# 002：不要被 C++ 重载集淹没

![](img/850194d5ca28e415c2cdfff332ac5be1_1.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_3.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_4.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_6.png)

在本节课中，我们将要学习 C++ 中一个强大但有时令人困惑的特性：函数重载集。我们将从基础概念开始，逐步深入到重载解析的规则、名称查找、以及一些高级技巧和常见陷阱。通过本教程，你将理解什么是重载集，如何找到它们，以及编译器如何选择调用哪个函数。

![](img/850194d5ca28e415c2cdfff332ac5be1_8.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_9.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_11.png)

## 什么是重载集？🤔

![](img/850194d5ca28e415c2cdfff332ac5be1_13.png)

函数重载是 C++ 从 C 语言继承并增强的一个强大特性。它允许我们定义多个同名但参数列表不同的函数。这些同名函数构成的集合，就称为“重载集”。

让我们看一个简单的例子。在 C 标准库中，计算自然对数的函数有三个变体：
*   `log`：接受 `double` 参数，返回 `double`。
*   `logf`：接受 `float` 参数，返回 `float`。
*   `logl`：接受 `long double` 参数，返回 `long double`。

![](img/850194d5ca28e415c2cdfff332ac5be1_15.png)

在 C 中，你必须根据参数类型选择正确的函数名。而在 C++ 中，我们有一个更优雅的解决方案：

```cpp
// C++ 标准库中的 log 函数重载集
double log(double);
float log(float);
long double log(long double);
template <typename T> double log(T); // 用于整数类型，返回 double
```

![](img/850194d5ca28e415c2cdfff332ac5be1_17.png)

现在，你只需要调用 `std::log`，编译器会根据你传入的参数类型自动选择正确的函数版本。这就是重载集的便利之处。

![](img/850194d5ca28e415c2cdfff332ac5be1_19.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_20.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_21.png)

## 重载的规则与限制 📏

![](img/850194d5ca28e415c2cdfff332ac5be1_23.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_25.png)

上一节我们介绍了重载集的基本概念，本节中我们来看看重载需要遵循哪些规则。

首先，重载函数必须通过参数类型（数量或类型）来区分。**仅返回值类型不同的函数不能重载**。这是因为在底层，函数签名（用于链接的名称）通常不包含返回类型信息。

![](img/850194d5ca28e415c2cdfff332ac5be1_27.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_28.png)

```cpp
// 错误：仅返回值不同，无法重载
float fromString(const std::string&);
double fromString(const std::string&);
long double fromString(const std::string&);
```

![](img/850194d5ca28e415c2cdfff332ac5be1_30.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_31.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_32.png)

但是，有一个有趣的例外：函数模板。模板允许你通过模板参数来指定返回类型，因为模板本身是一种特殊的“配方”，用于生成具体的函数。

构造函数是重载的常见场景。以 `std::vector` 为例：

![](img/850194d5ca28e415c2cdfff332ac5be1_34.png)

```cpp
std::vector<int> v1;          // 调用默认构造函数
std::vector<int> v2(5);       // 调用填充构造函数，创建5个元素（值为0）
std::vector<int> v3{1,2,3,4,5}; // 调用初始化列表构造函数
```

![](img/850194d5ca28e415c2cdfff332ac5be1_36.png)

注意 `v2(5)` 和 `v3{...}` 的区别。使用圆括号 `()` 调用的是填充构造函数，而使用花括号 `{}` 调用的是初始化列表构造函数。当使用空花括号 `{}` 时，会优先调用默认构造函数。我们稍后在讨论重载解析优先级时会再次看到这一点。

![](img/850194d5ca28e415c2cdfff332ac5be1_38.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_40.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_41.png)

## 深入理解“集合”概念 🧩

“重载集”的核心在于“集”这个字。它不是一个单一的实体，而是通过名称查找找到的一组同名函数。

![](img/850194d5ca28e415c2cdfff332ac5be1_43.png)

考虑以下代码：

```cpp
namespace A {
    void foo(int) {}
    namespace B {
        void foo(char) {}
        void foo(long) {}
        void bar(int x) {
            foo(x); // 错误：调用歧义！int 可以转换为 char 或 long
        }
    }
}
```

在函数 `bar` 内部调用 `foo(x)` 时，编译器会在当前作用域（`namespace B`）内查找名称 `foo`，并找到了两个候选函数（`foo(char)` 和 `foo(long)`）。这就是 `foo` 在此上下文中的重载集。由于 `int` 可以隐式转换为 `char` 或 `long`，编译器无法决定使用哪一个，因此报错。

我们可以通过强制类型转换来消除歧义：

![](img/850194d5ca28e415c2cdfff332ac5be1_45.png)

```cpp
foo(static_cast<long>(x)); // 明确选择 foo(long)
```

![](img/850194d5ca28e415c2cdfff332ac5be1_47.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_49.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_50.png)

如果 `bar` 在另一个命名空间 `C` 中，而 `C` 中没有 `foo`，那么名称查找会向上层命名空间 `A` 搜索，并找到 `foo(int)`，调用就不会有歧义。

![](img/850194d5ca28e415c2cdfff332ac5be1_52.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_53.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_55.png)

**重载集本身不是一个可以直接操作的类型**。例如，你不能直接获取一个重载集的类型信息：

![](img/850194d5ca28e415c2cdfff332ac5be1_57.png)

```cpp
// 错误：无法解析对重载函数的引用
auto t = typeid(A::B::foo);
// 正确：必须通过调用或强制转换来指定具体函数
auto t = typeid(static_cast<void(*)(long)>(A::B::foo));
```

![](img/850194d5ca28e415c2cdfff332ac5be1_59.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_61.png)

## 名称查找：如何找到重载集？🔍

在编译器决定调用哪个函数之前，它必须先找到候选函数集，即重载集。这个过程称为“名称查找”。

![](img/850194d5ca28e415c2cdfff332ac5be1_63.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_65.png)

基本规则是：从调用点所在的当前作用域开始，逐级向外层作用域搜索，直到找到该名称的声明。**一旦在某个作用域找到了该名称，搜索就会停止**，并将该作用域内所有该名称的函数（以及通过参数依赖查找找到的函数，见下文）加入重载集。

对于类成员函数的调用（如 `obj.foo()` 或 `ptr->foo()`），搜索范围限定在该类及其基类中。

![](img/850194d5ca28e415c2cdfff332ac5be1_67.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_68.png)

### 参数依赖查找（ADL）

ADL 是 C++ 中一个强大且有时令人惊讶的特性。对于未限定的函数调用（如 `foo(arg)`），除了常规的作用域搜索，编译器还会检查函数参数类型的所属命名空间，并在那些命名空间中搜索同名函数，将它们也加入重载集。

![](img/850194d5ca28e415c2cdfff332ac5be1_70.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_72.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_74.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_76.png)

```cpp
namespace Other {
    struct S {};
    void foo(S) {} // (3)
}
namespace A {
    void foo(int) {} // (1)
    namespace B {
        void foo(char) {} // (2a)
        void foo(long) {} // (2b)
        void bar(Other::S s) {
            foo(s); // 重载集包含 (2a), (2b), (3)
        }
    }
}
```

![](img/850194d5ca28e415c2cdfff332ac5be1_78.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_80.png)

在 `bar` 中调用 `foo(s)`，首先在 `namespace B` 中找到 `foo(char)` 和 `foo(long)`。然后，因为参数 `s` 的类型 `Other::S` 属于 `Other` 命名空间，ADL 会将该命名空间中的 `foo(Other::S)` 也加入重载集。最终，`foo(Other::S)` 是精确匹配，会被调用。

![](img/850194d5ca28e415c2cdfff332ac5be1_81.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_83.png)

ADL 是实现**运算符重载**和**泛型编程**的关键。例如，`std::cout << myType` 能正常工作，正是因为 `operator<<` 通过 ADL 在 `myType` 所在的命名空间中被找到。

![](img/850194d5ca28e415c2cdfff332ac5be1_85.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_86.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_88.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_89.png)

## 重载解析：如何选择最佳函数？🥇

![](img/850194d5ca28e415c2cdfff332ac5be1_91.png)

找到重载集后，编译器需要从中选出“最佳匹配”的函数。选择过程基于一系列优先级规则：

![](img/850194d5ca28e415c2cdfff332ac5be1_93.png)

以下是匹配优先级从高到低的排序：
1.  **精确匹配**：参数类型与形参类型完全一致。
2.  **提升**：不会丢失精度的内置类型转换（如 `bool`/`char`/`short` 提升为 `int`）。
3.  **标准转换**：其他内置隐式转换（如 `int` 转 `double`，数值类型转换等）。
4.  **用户定义转换**：通过构造函数或转换运算符实现的转换（如 `std::string` 转 `std::string_view`）。
5.  **省略号匹配**：匹配 C 风格的可变参数 `...`（应尽量避免）。

让我们通过一个例子来理解：

![](img/850194d5ca28e415c2cdfff332ac5be1_95.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_97.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_98.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_99.png)

```cpp
void select(int) {}      // 1. 精确匹配 int
void select(short) {}    // 1. 精确匹配 short
void select(double) {}   // 3. 标准转换 (float->double)
void select(std::string_view) {} // 4. 用户定义转换 (char*->string_view)
void select(...) {}      // 5. 最后的选择

![](img/850194d5ca28e415c2cdfff332ac5be1_101.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_103.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_105.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_107.png)

int main() {
    select(42);          // 调用 select(int)，精确匹配
    short s = 42;
    select(s);           // 调用 select(short)，精确匹配
    select('A');         // 调用 select(int)，字符提升为 int
    select(3.14f);       // 调用 select(double)，float 标准转换为 double
    select("hello");     // 调用 select(string_view)，用户定义转换
    struct Mystery{};
    select(Mystery{});   // 调用 select(...)，无其他匹配
}
```

![](img/850194d5ca28e415c2cdfff332ac5be1_109.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_110.png)

### 花括号初始化的特殊规则

![](img/850194d5ca28e415c2cdfff332ac5be1_112.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_114.png)

使用花括号 `{}` 进行初始化（列表初始化）有特殊的优先级：
*   如果一个函数接受 `std::initializer_list` 参数，并且调用时使用了花括号，那么只要列表中的元素类型可以转换，这个函数将**优先于其他所有函数**被选择。
*   对于构造函数，使用空花括号 `{}` 会**优先调用默认构造函数**，而不是接受 `std::initializer_list` 的构造函数。

![](img/850194d5ca28e415c2cdfff332ac5be1_116.png)

这解释了为什么 `std::vector<int> v{5}` 创建一个包含单个元素 `5` 的向量，而 `std::vector<int> v(5)` 创建五个值为 `0` 的元素。

![](img/850194d5ca28e415c2cdfff332ac5be1_118.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_120.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_122.png)

## 当重载遇到模板 🌀

![](img/850194d5ca28e415c2cdfff332ac5be1_124.png)

模板函数也参与重载解析，但规则更复杂一些。核心思想是：**函数模板是生成重载集的“配方”**。

![](img/850194d5ca28e415c2cdfff332ac5be1_126.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_127.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_129.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_131.png)

选择过程分为两步：
1.  **模板特化选择**：对于所有匹配的模板，选出“最特化”的那个。简单理解是，能接受参数类型范围更小的模板更特化。
2.  **重载解析**：将上一步选出的最特化模板实例化后的函数，与所有普通（非模板）函数放在一起，按照之前的优先级规则进行重载解析。

![](img/850194d5ca28e415c2cdfff332ac5be1_133.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_134.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_135.png)

**一个重要规则**：在重载解析排名相同时，非模板函数优先于模板特化。

![](img/850194d5ca28e415c2cdfff332ac5be1_137.png)

考虑以下例子：

![](img/850194d5ca28e415c2cdfff332ac5be1_139.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_141.png)

```cpp
template<typename T> T sqrt(T); // (1) 通用模板
template<typename T> std::complex<T> sqrt(std::complex<T>); // (2) 针对 complex 的模板
float sqrt(float); // (3) 普通函数

![](img/850194d5ca28e415c2cdfff332ac5be1_143.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_145.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_147.png)

std::complex<float> z;
auto r1 = sqrt(42);    // 调用 (1)，int 匹配 T
auto r2 = sqrt(3.14f); // 调用 (3)，普通函数优于模板 (1)
auto r3 = sqrt(z);     // 调用 (2)，模板 (2) 比模板 (1) 更特化（只接受 complex）
```

### 模板重载的陷阱

一个常见的陷阱是，过于通用的模板可能会“劫持”你期望的调用。

```cpp
// 一个设计不佳的“可选类型”
struct MaybeInt {
    int value;
    bool valid = false;
    operator bool() const { return valid; }
    operator int() const { return value; } // 隐式转换到 int
    operator std::optional<int>() const { return valid ? std::optional{value} : std::nullopt; }
};

std::optional<int> o = MaybeInt{}; // 我们期望调用 operator optional<int>()，得到一个空 optional
// 但实际上可能调用了 optional 的通用构造函数 template <typename U> optional(U&&)，然后通过 operator int() 得到了 optional(0)
```

这里，`std::optional` 有一个通用构造函数模板，可以接受任何类型 `U`。`MaybeInt` 到 `U` 是精确匹配（模板参数推导），而到 `std::optional<int>` 需要一次用户定义转换。根据优先级，精确匹配的模板被选中，导致了非预期的行为。

## 成员函数重载与继承 👨‍👦

对于类的成员函数，重载规则基本类似，但增加了作用域（类内）和继承的复杂性。

### 名称隐藏

在继承体系中，派生类中定义的函数会**隐藏**基类中同名的函数（即使参数列表不同），而不是重载它们。

![](img/850194d5ca28e415c2cdfff332ac5be1_149.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_151.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_153.png)

```cpp
struct Base {
    void foo(int) {}
};
struct Derived : Base {
    void foo(double) {} // 隐藏了 Base::foo(int)
};
Derived d;
d.foo(42); // 调用 Derived::foo(double)，int 被转换为 double
// d.foo(42); 如果想调用基类的，需要显式指定：d.Base::foo(42);
```

![](img/850194d5ca28e415c2cdfff332ac5be1_155.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_157.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_159.png)

要引入基类的重载集，需要在派生类中使用 `using` 声明：

![](img/850194d5ca28e415c2cdfff332ac5be1_161.png)

```cpp
struct Derived2 : Base {
    using Base::foo; // 引入 Base 中的 foo 重载集
    void foo(double) {}
};
Derived2 d2;
d2.foo(42); // 现在重载集包含 foo(int) 和 foo(double)，调用 foo(int)
```

![](img/850194d5ca28e415c2cdfff332ac5be1_163.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_165.png)

### const 和引用限定符

成员函数可以通过 `const`、`volatile` 以及引用限定符（`&`、`&&`）进行重载，这允许根据对象的常量性或值类别来提供不同的实现。

```cpp
class DataHolder {
    std::vector<char> data;
public:
    // const 重载
    std::vector<char> data() const { return data; } // 返回副本
    std::vector<char>& data() { return data; }      // 返回可修改的引用

    // 引用限定符重载 (较少使用)
    std::vector<char> data() && { return std::move(data); } // 临时对象，可以移动数据
};
```

![](img/850194d5ca28e415c2cdfff332ac5be1_167.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_168.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_170.png)

## 实用技巧与总结 🛠️

![](img/850194d5ca28e415c2cdfff332ac5be1_172.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_174.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_176.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_178.png)

最后，我们来看一些关于设计和使用重载集的实用技巧。

![](img/850194d5ca28e415c2cdfff332ac5be1_180.png)

以下是几个关键建议：
*   **使用默认参数替代重载**：如果函数区别仅在于某些参数是否有默认值，使用默认参数更简洁。
*   **避免令人困惑的重载**：只对语义基本相同的操作进行重载。例如，`open(file)` 和 `open(gate)` 虽然都是“打开”，但行为迥异，应使用不同名称。
*   **使用标签分发进行行为控制**：当你需要根据某种“策略”选择不同实现，但又想保持接口统一时，可以使用空结构体作为“标签”参数。标准库中的执行策略（如 `std::execution::par`）就是例子。
*   **谨慎处理继承中的重载**：避免在派生类中添加与基类虚函数同名的非虚重载，这容易导致混淆。尽量保持虚函数的重载集在基类中完整。
*   **使用概念约束模板**：对于函数模板，使用 C++20 的概念可以更清晰、更安全地约束模板参数，避免意外的模板匹配。
*   **极端情况：禁用重载**：如果你真的不希望一个函数被重载，可以将其定义为 lambda 并赋值给一个变量。变量名在名称查找中只会找到这一个实体，不会形成重载集。（这是一个小众技巧，谨慎使用。）

![](img/850194d5ca28e415c2cdfff332ac5be1_182.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_183.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_185.png)

---

![](img/850194d5ca28e415c2cdfff332ac5be1_187.png)

本节课中我们一起学习了 C++ 重载集的方方面面。我们从基本概念出发，探讨了名称查找（包括 ADL）如何构建重载集，以及重载解析的详细规则如何选出最佳函数。我们还深入研究了模板、继承带来的复杂性，并分享了一些避免陷阱的实用技巧。

![](img/850194d5ca28e415c2cdfff332ac5be1_189.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_190.png)

![](img/850194d5ca28e415c2cdfff332ac5be1_192.png)

理解重载集是掌握 C++ 强大表达能力的关键。虽然规则有时显得复杂，但遵循良好的设计准则（如语义一致性、优先使用默认参数等）可以让你有效地利用这一特性，同时保持代码的清晰和可维护性。希望本教程能帮助你在 C++ 的海洋中，不被“重载集”淹没。