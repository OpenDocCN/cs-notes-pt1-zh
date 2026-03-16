# 008：核心概念与应用

![](img/374be2cbd51ec16d9f49f0c4f9645404_0.png)

![](img/374be2cbd51ec16d9f49f0c4f9645404_1.png)

在本节课中，我们将要学习 C++ 中一个非常强大且现代的特性：可变参数模板和参数包。它们是现代 C++ 设计的基石，不仅支撑了许多标准库和第三方库的实现，也能帮助我们创建灵活且高效的自定义工具。

## 模板基础回顾

在深入可变参数模板之前，我们先简要回顾一下模板的基础知识。相信大家对以下语法都很熟悉：类模板和函数模板。

```cpp
template <typename T, size_t Capacity>
class StaticVector {
    // ... 实现细节
};
```

这里我们有一个 `StaticVector` 类模板。它有两个模板参数：一个是类型参数 `T`，另一个是非类型模板参数 `Capacity`，用于指定容器预分配存储的最大元素数量。

当我们实例化模板并提供参数时，编译器会用这些参数替换模板体中的参数名，从而生成一个具体的类。例如，`StaticVector<int, 10>` 会生成一个能存储最多10个整数的具体类。

在 C++11 之前，模板可以拥有多个参数，但无法拥有任意数量的参数。虽然有一些技术可以绕过这个限制，但在语言层面并不支持。C++11 引入了可变参数模板和参数包，解决了这个问题。

## 可变参数模板与参数包

可变参数模板允许模板接受任意数量的参数。其语法如下：

```cpp
template <typename... Ts>
void func(Ts... args) {
    // ... 处理参数包
}
```

这里，`typename... Ts` 是一个**模板参数包**，它代表一组类型。`Ts... args` 是一个**函数参数包**，它代表一组值，这些值对应着实例化函数时提供的参数。

关于参数包，有几个关键点：
*   参数包是一个命名实体，代表一组参数（类型或值）。
*   参数包可以为空（包含零个或多个元素）。
*   在模板体内，参数包必须被**显式展开**才能使用。编译器不会像处理普通模板参数那样自动替换它们。

接下来，我们将通过示例探索参数包的各种展开方式和使用场景。

## 参数包的展开上下文

参数包可以在多种语言指定的上下文中展开。以下是几个最常用的例子。

### 示例：包装 `std::tuple`

假设我们要实现一个包装 `std::tuple` 的容器。

```cpp
template <typename... Ts>
class TupleWrapper {
    std::tuple<Ts...> storage; // 上下文1：展开到模板实参列表
public:
    TupleWrapper(Ts... args)   // 上下文2：展开到函数形参列表
        : storage(args...) {   // 上下文3：展开到初始化器列表
    }
};
```

在这个例子中，我们展示了三种展开上下文：
1.  **模板实参列表**：`std::tuple<Ts...>` 将类型包 `Ts` 展开，作为 `tuple` 的模板参数。
2.  **函数形参列表**：构造函数 `TupleWrapper(Ts... args)` 将类型包展开为函数的形参类型。
3.  **成员初始化器列表**：`storage(args...)` 将函数参数包 `args` 展开，用于初始化 `storage` 成员。

当我们实例化 `TupleWrapper<int, float>(1, 2.0f)` 时，编译器会进行如下替换：
*   `Ts...` 被替换为 `int, float`
*   构造函数签名变为 `TupleWrapper(int, float)`
*   初始化变为 `storage(1, 2.0f)`

### 使用操作进行展开

展开参数包时，我们可以对每个元素应用操作，而不仅仅是简单列出。

```cpp
template <typename... Ts>
auto make_tuple(Ts&&... args) {
    return std::tuple<Ts...>(std::forward<Ts>(args)...);
}
```

这里，`std::forward<Ts>(args)...` 是一个展开模式。它同时展开了类型包 `Ts` 和函数参数包 `args`，对每个参数进行完美转发。

### 使用 `sizeof...` 操作符

`sizeof...` 是一个特殊的操作符，用于获取参数包中元素的数量。

```cpp
template <typename T, typename... Args>
auto make_array(Args&&... args) -> std::array<T, sizeof...(Args)> {
    return { T(std::forward<Args>(args))... };
}
```

在这个 `make_array` 函数中，`sizeof...(Args)` 在编译时计算出参数包 `Args` 的大小，并将其用作返回的 `std::array` 的大小。同时，`T(std::forward<Args>(args))...` 将每个参数完美转发并构造为类型 `T` 的对象。

## 展开到初始化列表

将参数包展开到初始化列表是一种非常实用的技术，它允许我们在函数体内以统一的方式处理参数，例如使用循环。

### 示例：求和函数

```cpp
template <typename T, typename... Args>
constexpr auto sum(T first, Args... rest) {
    // C++14 起，初始化列表可用于常量表达式
    std::common_type_t<T, Args...> result = first;
    for (const auto& val : {rest...}) { // 展开到初始化列表
        result += val;
    }
    return result;
}
```

这里，`{rest...}` 将参数包展开为一个初始化列表。由于初始化列表要求所有元素类型相同，我们使用 `std::common_type_t` 来获取一个公共类型。从 C++14 开始，这种写法可以在常量表达式中使用。

### 对每个参数执行操作

有时我们需要对参数包中的每个参数执行某个操作（例如打印），而这些参数的类型可能各不相同。

```cpp
template <typename... Args>
void print_all(Args&&... args) {
    (void)std::initializer_list<int>{ (std::cout << std::forward<Args>(args) << ‘ ‘, 0)... };
}
```

这个技巧利用了逗号运算符：`(std::cout << arg << ‘ ‘, 0)`。逗号运算符会执行左边的表达式（输出到标准输出），然后丢弃其结果，最终整个表达式的结果是右边的 `0`。这些 `0` 被收集到初始化列表 `std::initializer_list<int>` 中（其值被忽略）。这样，我们就对每个参数执行了输出操作，而无需关心它们的具体类型。

## C++17 折叠表达式

上一节我们介绍了使用初始化列表处理参数包的方法。在 C++17 中，引入了一种更优雅、更强大的工具来处理参数包：**折叠表达式**。它可以直接对参数包应用二元运算符。

使用折叠表达式，之前的求和函数可以简化为：

```cpp
template <typename... Args>
auto sum(Args... args) {
    return (args + ...); // 一元右折叠
}
// 或者带有初始值
template <typename T, typename... Args>
auto sum(T first, Args... args) {
    return (first + ... + args); // 二元左折叠
}
```

折叠表达式 `(args + ...)` 是一个**一元右折叠**。对于调用 `sum(1, 2, 3)`，它会被展开为 `1 + (2 + 3)`。
而 `(first + ... + args)` 是一个**二元左折叠**，对于同样的调用，它会被展开为 `((1 + 2) + 3)`。

对于空参数包，只有三个运算符在折叠表达式中是有效的，它们有默认值：
*   逻辑与 `(&& ...)` 求值为 `true`
*   逻辑或 `(|| ...)` 求值为 `false`
*   逗号运算符 `(, ...)` 求值为 `void()`

### 折叠表达式的复杂应用

折叠表达式可以用于实现更复杂的逻辑。例如，查找第一个满足条件的参数：

```cpp
template <typename Pred, typename... Args>
constexpr auto find_first_if(Pred pred, Args&&... args) -> std::common_type_t<Args...> {
    std::common_type_t<Args...> result{};
    ( (pred(args) ? (result = args, true) : false) || ... );
    return result;
}
// C++20 可选版本
template <typename Pred, typename... Args>
constexpr auto find_first_if(Pred pred, Args&&... args) -> std::optional<std::common_type_t<Args...>> {
    std::optional<std::common_type_t<Args...>> result;
    ( (pred(args) ? (result = args, true) : false) || ... );
    return result;
}
```

这个函数使用折叠表达式配合逻辑或 `||` 和三元运算符进行短路求值。一旦谓词 `pred` 对某个参数返回 `true`，就会将该参数赋值给 `result`，并且由于逻辑或的短路特性，后续参数不会再被求值。C++20 的 `std::optional` 让我们可以返回一个可能为空的值，避免了类型必须可默认构造的限制。

## 类型列表与参数包存储

有时，我们需要将参数包（特别是类型包）存储起来以备后用。一个简单的方法是使用一个空的可变参数模板类，它被称为**类型列表**。

```cpp
template <typename...>
struct TypeList {};
```

`TypeList<int, float, char>` 就是一个包含了 `int, float, char` 三种类型的类型列表。我们可以用它来存储一组类型。

### 示例：事件调度器

假设我们有一个调度器，它支持调度特定类型的事件。

```cpp
template <typename... Events>
class Scheduler {
    using SupportedEvents = TypeList<Events...>; // 存储支持的事件类型
    // ... 其他实现
};

template <typename SchedulerT, typename Event>
void schedule_event(const SchedulerT& sched, const Event& ev) {
    static_assert(is_in_typelist_v<Event, typename SchedulerT::SupportedEvents>,
                  “Event type not supported by this scheduler”);
    // ... 调度事件
}
```

这里，调度器内部使用 `TypeList` 存储了它支持的事件类型。`schedule_event` 函数在编译时检查要调度的事件类型是否在支持的类型列表中。

### 实现 `is_in_typelist`

`is_in_typelist` 的功能是检查一个类型是否在类型列表中。随着 C++ 标准的演进，它的实现方式也在不断简化。

**C++11 递归版本**：受限于常量表达式的功能，需要使用递归模板元编程。
**C++14 版本**：可以利用 `constexpr` 函数和普通循环在编译期计算。
**C++17 版本**：使用折叠表达式，代码变得非常简洁。

```cpp
// C++17 使用折叠表达式
template <typename T, typename... Ts>
struct IsInTypeList {
    static constexpr bool value = (std::is_same_v<T, Ts> || ...);
};
template <typename T, typename... Ts>
inline constexpr bool is_in_typelist_v = IsInTypeList<T, Ts...>::value;
```

## 现代 C++ 中的增强

C++ 标准的发展为可变参数模板带来了更多便利。

### `std::apply` 与元组

`std::tuple` 本身就是一个可变参数模板类。`std::apply` 函数可以将一个元组的元素展开，作为参数调用一个可调用对象。

```cpp
auto tup = std::make_tuple(1, 2.0, “hello”);
std::apply([](auto&&... args) {
    // args... 就是元组中的每个元素
    ( (std::cout << args << ‘ ‘), ... );
}, tup);
```

这让我们可以方便地将存储在元组中的值转换回参数包进行处理。

### C++20 简写模板语法与概念

C++20 引入了简写函数模板语法和概念，它们同样适用于可变参数模板。

```cpp
// 简写语法
void print(auto&&... args) {
    ( (std::cout << args << ‘ ‘), ... );
}

// 使用概念约束参数包中的所有类型必须相同
void print_same(std::same_as<int> auto... args) {
    // 所有 args 都是 int 类型
    ( (std::cout << args << ‘ ‘), ... );
}
```

在 C++20 之前，要强制函数的所有参数类型相同，需要复杂的 SFINAE 或静态断言技巧。现在，使用 `std::same_as` 概念可以轻松实现这一约束。

### C++26 展望：包索引与可变结构化绑定

未来的 C++26 标准计划引入更多操作参数包的能力：

1.  **包索引**：允许直接通过索引访问参数包中的特定元素。
    ```cpp
    template <typename... Ts>
    using SecondType = __type_pack_element<1, Ts...>; // 获取类型包中第二个类型
    ```
2.  **可变结构化绑定**：可以将一个结构化绑定的所有元素捕获到一个参数包中。
    ```cpp
    template <typename T>
    void print_fields(T&& obj) {
        auto&& [...elems] = std::forward<T>(obj); // 可变结构化绑定
        ( (std::cout << elems << ‘ ‘), ... ); // 打印所有字段
    }
    ```
    这使得编写通用的、支持自省功能的代码变得更加容易。

## 总结

本节课我们一起深入探讨了 C++ 可变参数模板和参数包的核心概念与应用。我们从模板基础回顾开始，学习了参数包的定义与基本特性。接着，我们探索了多种参数包的展开上下文，包括模板实参列表、函数形参列表、初始化列表以及 C++17 引入的强大工具——折叠表达式。我们还了解了如何使用类型列表来存储和操作类型包，并看到了现代 C++（C++20/26）如何通过简写语法、概念和新的语言特性来简化可变参数模板的使用。

![](img/374be2cbd51ec16d9f49f0c4f9645404_3.png)

可变参数模板是编写灵活、通用和高效 C++ 代码的关键工具。掌握它们，将极大地提升你进行现代 C++ 库设计和元编程的能力。