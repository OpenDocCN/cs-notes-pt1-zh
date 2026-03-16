# 012：缺失（以及未来）的 C++ 范围概念

![](img/3b6621316fabf78322edb660cfbf6abe_0.png)

![](img/3b6621316fabf78322edb660cfbf6abe_0.png)

在本教程中，我们将探讨 C++ 标准库中缺失的一些范围概念，以及未来可能的发展方向。我们将重点关注性能优化和元编程这两个核心问题，并介绍一些已在 `think-cell` 库中实现的概念，这些概念有望被纳入标准。

## 概述

本次课程将分为两个主要部分。首先，我们将讨论如何通过新的概念和定制点来优化范围算法的性能，特别是针对迭代器状态机带来的开销。其次，我们将探讨如何在编译时利用范围进行元编程，处理依赖于类型的操作。最后，我们会简要提及标准中关于无限范围定义的一些悬而未决的问题。

## 问题一：性能优化

上一节我们概述了课程内容，本节中我们来看看第一个核心问题：性能优化。当前基于迭代器的范围模型在某些场景下会引入不必要的性能开销。

### 示例：文件处理与连接视图

考虑一个常见的任务：读取文件并将所有制表符（`\t`）替换为四个空格。使用范围管道可以简洁地实现：

```cpp
auto result = std::ranges::to<std::string>(
    read_file(path)
    | std::views::transform([](char c) -> std::string_view {
        return c == '\t' ? "    " : std::string_view(&c, 1);
      })
    | std::views::join
);
```

`read_file` 函数返回一个输入范围。`transform` 视图将每个字符映射为一个 `std::string_view`（单个字符或四个空格）。`join` 视图将这些字符串视图扁平化为一个字符范围。最后，`std::ranges::to` 将惰性范围急切地转换为 `std::string`。

#### 大小信息的丢失与近似大小范围

`std::ranges::to` 在构造字符串时，如果输入范围是 **sized** 的，它可以预先分配足够内存，效率很高。然而，经过 `transform` 和 `join` 后，我们失去了大小信息，因为 `join` 需要求和内部各个字符串的大小，而这是未知的。

如果文件大部分不含制表符，那么输出大小近似等于输入大小。C++26 引入了 **approximately sized range** 概念和 `size_hint` 函数来解决这类问题。

以下是 `size_hint` 的简化实现思路：
```cpp
template<typename R>
constexpr auto size_hint(const R& r) -> std::optional<std::ranges::range_size_t<R>> {
    if constexpr (std::ranges::sized_range<R>) {
        return std::ranges::size(r);
    } else if constexpr (requires { r.size_hint(); }) { // ADL 查找
        return r.size_hint();
    } else {
        return std::nullopt;
    }
}
```

许多视图（如 `transform`、`take`）可以传播近似大小信息，但 `join` 通常不能。我们可以创建一个包装器来手动传播大小：

```cpp
// 一个简单的包装器，存储范围及其近似大小
template<std::ranges::input_range R>
struct approximately_sized_view : std::ranges::view_interface<...> {
    R base_;
    std::optional<std::size_t> size_hint_;

    // ... 迭代器实现需转发 size_hint
};

// 使用示例（破坏了管道流畅性）
auto base_range = read_file(path) | std::views::transform(transformer);
auto sized_view = approximately_sized_view(base_range, file_size);
auto result = std::ranges::to<std::string>(sized_view | std::views::join);
```

为了保持管道风格，可以定义一个 `join_with_unchanged_size` 适配器，但其实现涉及复杂的闭包对象，错误信息不友好。这暗示此类功能或许更适合作为语言特性。

### 迭代器状态机的开销

当前的范围模型基于 **拉取模型（pull model）**，消费者通过迭代器完全控制迭代过程。但对于像 `std::ranges::for_each` 这样的算法，消费者只需要所有元素，并不需要这种控制力。

迭代器本质上是一个状态机。例如，`join` 视图的迭代器需要维护外层迭代器和内层迭代器两个状态，并在内层结束时切换到下一个外层元素。这比简单的嵌套循环要复杂。经过多层视图变换后，会形成一个深层嵌套的状态机，编译器需要大量内联和优化才能将其恢复为高效的循环代码。

更好的方案是 **推送模型（push model）** 或直接定制循环。生产者主动将值推送给消费者，消费者通过回调函数处理每个元素。

#### 定制点：`for_each_while`

我们可以引入一个名为 `for_each_while` 的定制点。它接受一个范围和一个“接收器（sink）”可调用对象，遍历范围并对每个元素调用接收器。如果接收器返回 `false`，则停止迭代。

其默认实现基于迭代器：
```cpp
template<std::ranges::input_range R, typename Sink>
constexpr bool for_each_while(R&& r, Sink sink) {
    for (auto&& elem : r) {
        if (!sink(std::forward<decltype(elem)>(elem))) {
            return false;
        }
    }
    return true;
}
```

关键在于，视图可以为此定制点提供更高效的实现：
*   **`transform` 视图**：直接在基范围的循环中应用变换函数。
*   **`join` 视图**：实现为两个嵌套循环，分别遍历外层和内层范围，完全避免状态机。
*   **`read_file` 视图**：直接嵌入读取缓冲区和遍历缓冲区的命令式代码。

当算法（如 `for_each`、`copy`、`transform`）使用 `for_each_while` 而不是基于迭代器的循环时，经过编译器内联，最终能得到与手写命令式代码几乎相同的优化结果。

#### 处理连续内存块

许多范围虽然不是完全连续的，但由连续的“块”组成，例如 `deque`、拼接的向量、或 `read_file` 的缓冲区。如果能以块为单位处理，就可以使用 `memcpy` 等高效操作。

`for_each_while` 定制点可以扩展，让接收器优先处理整个块。我们修改 `for_each_while`，让它首先尝试调用接收器的 `.chunk()` 成员函数（如果存在），并传入一个代表连续块的子范围。如果接收器不支持块操作，则回退到逐元素调用。

例如，字符串追加器可以实现为：
```cpp
struct string_appender {
    std::string& str;
    // 处理单个字符
    bool operator()(char c) { str.push_back(c); return true; }
    // 处理连续字符块（更高效）
    bool chunk(std::span<const char> s) { str.append(s.data(), s.size()); return true; }
};
```

当 `join` 视图的 `for_each_while` 实现遇到一个连续块（如 `std::span`）时，它会调用 `string_appender::chunk`，从而一次性追加整个块，避免了逐字符的 `push_back`。

通过结合 `for_each_while` 和块处理，我们可以编写出既表达力强又极其高效的代码。

## 问题二：元编程与编译时计算

上一节我们探讨了运行时性能优化，本节中我们来看看如何利用范围在编译时进行元编程。

### 编译时字符串处理

假设我们需要在编译时根据类型描述生成 SQL 语句。字符串字面量在常量表达式函数中作为参数时，其值（`const char*`）不是常量表达式，但它的类型（`char[N]`）是。因此，我们需要将字符串编码到类型系统中。

**字面量范围（literal range）** 将一系列值编码在模板参数中：
```cpp
template<auto... Values>
struct literal_range {
    // 提供迭代器，迭代时从静态存储中返回值
    // size() 是编译时常量
};
```

使用用户定义字面量可以方便地创建此类范围：`"table_name"_tc`。这样，我们就能在常量表达式函数内部获取其大小等信息。

### 编译时连接视图

我们希望在编译时连接多个字符串：
```cpp
constexpr auto create_table_sql = concat_to_array(
    "CREATE TABLE "_tc,
    table_name,
    " (id INT)"_tc
);
```

`concat_to_array` 需要知道结果数组的大小，这依赖于输入范围的大小。在 C++26 之前，由于参数不是常量表达式，无法在函数体内直接计算其大小。C++26 放宽了约束，只要不访问参数的 **odr-used** 值，就可以在常量表达式中使用局部对象和参数。

因此，我们可以定义 **constant-sized range** 概念，表示即使只有该类型的运行时引用，也能在常量表达式中获取其大小。
```cpp
template<typename R>
concept constant_sized_range = std::ranges::sanged_range<R> &&
    requires { { std::type_identity<std::ranges::size(std::declval<R&>())>() } -> constant; };
```

利用这一点，可以实现 `concat_to_array`：
```cpp
template<constant_sized_range... Rs>
constexpr auto concat_to_array(Rs&&... rs) {
    constexpr std::size_t total_size = (std::ranges::size(rs) + ...);
    std::array<char, total_size> result{};
    // ... 复制逻辑
    return result;
}
```

### 异构生成器与元编程

标准范围要求所有元素类型相同。但为了编译时元编程，我们需要处理类型序列。**异构生成器（heterogeneous generator）** 只提供 `for_each_while` 接口，其元素类型可以不同。

`std::tuple` 就是一个典型的异构生成器。我们可以为其实现 `for_each_while`，对元组中的每个元素（可能类型不同）调用接收器。

这开启了强大的编译时元编程能力：
*   **过滤类型**：可以创建一个过滤视图，根据类型特征（如 `std::is_integral_v`）在编译时跳过某些类型的元素。
*   **编译时索引**：结合 `std::index_sequence`，可以在迭代时获得编译时索引值。
*   **类型列表算法**：将类型列表视为范围，使用范围算法进行变换、过滤等操作。

例如，解码所有 256 个字节码的操作函数：
```cpp
constexpr auto opcode_table = std::views::iota(0, 256)
    | std::views::transform([]<std::uint8_t I> {
        return +[] { /* 执行操作码 I 的函数体 */ };
      })
    | to_array; // 生成 std::array<FuncPtr, 256>
```

### 实现 `concat_nonempty_with`

回到同事的问题：连接一系列字符串，忽略空字符串，并用分隔符连接。最初的实现基于 `join` 和 `filter`，但丢失了编译时大小信息。

通过引入 **元组生成器（tuple generator）** 概念（一种支持编译时索引访问的异构生成器），我们可以为 `filter` 和 `join` 视图实现编译时大小计算：
*   `filter` 的编译时大小：基于类型谓词，在编译时计算满足条件的类型数量。
*   `join` 的编译时大小：对于元组生成器，在编译时对每个内部范围的大小求和。

最终，`concat` 可以被视为 `join` 的特例（连接一个元组中的多个范围）。经过一系列库基础设施的更新后，`concat_nonempty_with` 可以用纯粹的范围算法实现，并且完全在编译期工作，代码与手写的元编程版本逻辑等价，但更清晰。

## 标准中的未决问题：无限范围

标准中关于“范围”的定义要求存在一个哨兵（sentinel），并且通过有限次递增迭代器可以到达该哨兵。根据此定义，像 `std::views::iota(0u)` 这样的无限范围是 **无效范围**，对其调用任何范围算法（包括 `.begin()`）理论上都是未定义行为。

这显然不符合直觉和实际使用。我们需要更精细的定义来区分：
1.  **真正无限范围**：如 `std::views::repeat(42)`，`std::views::iota(0u)`（无符号整数不会溢出）。
2.  **有界但未知范围**：如 `std::views::iota(0)`（`int` 会溢出，行为未定义），或指向数组的指针与 `unreachable_sentinel` 构成的子范围。
3.  **无效范围**：如两个无关容器的迭代器构成的子范围。

澄清这些定义有助于：
*   使 `std::views::iota` 等常用视图合法化。
*   为并行算法提供优化可能（例如，当连接一个有限范围和一个无限范围时，结果大小就是有限范围的大小）。
*   允许视图进行优化（例如，对无限范围进行 `take` 操作时可以省略边界检查）。

## 总结

本节课中我们一起学习了 C++ 范围库可能的发展方向。

我们首先探讨了通过引入 `for_each_while` 定制点和块处理机制来优化性能，这能将复杂的状态机转换为高效的循环，并支持对连续内存块进行批量操作。

接着，我们研究了如何利用 C++26 的常量表达式放宽约束，实现编译时大小范围，并结合异构生成器进行强大的编译时元编程，使得像 `concat_nonempty_with` 这样的操作可以用优雅的范围语法表达并在编译期完成。

最后，我们指出了当前标准中关于无限范围定义的模糊之处，以及澄清该定义带来的好处。

一些概念，如 `conditional_range` 和 `for_each_while`，非常实用且易于集成，有望进入标准。而像完整的异构生成器这样的概念，由于与现有模型差异较大，可能更适合通过未来的反射功能来实现。

![](img/3b6621316fabf78322edb660cfbf6abe_2.png)

![](img/3b6621316fabf78322edb660cfbf6abe_2.png)