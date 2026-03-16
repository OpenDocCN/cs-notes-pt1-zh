# 036：sizeof与strlen的区别 🔍

![](img/5c246e4893cc424dbf231a7b545367c4_1.png)

在本节课中，我们将深入探讨C语言中的`sizeof`运算符。许多初学者容易将其与`strlen`函数混淆，尤其是在处理缓冲区大小时。我们将通过实例和调试器观察，明确两者的根本区别，并解释为何在动态分配内存的场景下错误使用`sizeof`会导致严重问题。

## 1：常见误解与问题引入

![](img/5c246e4893cc424dbf231a7b545367c4_3.png)

上一节我们回顾了字符串操作的基础。本节中，我们来看看一个常见的编码误区：将`sizeof`等同于`strlen`。

![](img/5c246e4893cc424dbf231a7b545367c4_5.png)

许多代码错误地使用`sizeof`来确定缓冲区的大小。例如，`strncpy`的手册页提供了如下示例代码：

![](img/5c246e4893cc424dbf231a7b545367c4_7.png)

![](img/5c246e4893cc424dbf231a7b545367c4_9.png)

```c
strncpy(buf, src, sizeof(buf) - 1);
buf[sizeof(buf) - 1] = '\0';
```

![](img/5c246e4893cc424dbf231a7b545367c4_11.png)

这段代码之所以正确，是因为`buf`是一个在编译时已知大小的固定长度数组。`sizeof(buf)`返回的是整个数组的存储大小。

然而，更常见的情况是使用动态分配的内存：

![](img/5c246e4893cc424dbf231a7b545367c4_13.png)

```c
char *buf = malloc(SOME_SIZE);
strncpy(buf, src, sizeof(buf) - 1); // 错误！
```

![](img/5c246e4893cc424dbf231a7b545367c4_15.png)

![](img/5c246e4893cc424dbf231a7b545367c4_17.png)

![](img/5c246e4893cc424dbf231a7b545367c4_19.png)

如果在这里使用`sizeof`，将会出现问题。代码存在固有缺陷和风险。为了理解原因，我们需要探究`sizeof`的真正行为。

## 2：探究sizeof运算符的本质

`sizeof`是一个运算符，而非库函数，因此没有单独的手册页。要了解它，最好的方法是直接观察。

我们可以使用调试器来观察`sizeof`对各种数据类型的求值结果。以下是`sizeof`对基本数据类型的返回结果（在典型64位系统上）：

![](img/5c246e4893cc424dbf231a7b545367c4_21.png)

![](img/5c246e4893cc424dbf231a7b545367c4_23.png)

*   `sizeof(char)` 返回 **1**。
*   `sizeof(int)` 返回 **4**。
*   `sizeof(float)` 返回 **4**。
*   `sizeof(long)` 返回 **8**。
*   `sizeof(double)` 返回 **8**。
*   `sizeof(指针类型)`（如 `char*` 或 `void*`）返回 **8**。

`sizeof`返回的是传递给它的**数据类型或表达式所占用的存储空间大小（以字节为单位）**。

## 3：结构体与灵活数组成员

![](img/5c246e4893cc424dbf231a7b545367c4_25.png)

在C语言中，我们可以自定义结构体类型。`sizeof`对结构体的计算会包含**内存对齐填充**。

考虑以下结构体：
```c
struct s1 { char *p; };                 // sizeof 可能为 8
struct s2 { char *p; int i; };          // sizeof 可能为 16（含填充）
struct s3 { int i; int j; char *p; };   // sizeof 可能为 16
struct s4 { struct s1 a; struct s2 b; struct s3 c; }; // sizeof 为各成员之和（含填充）
```

![](img/5c246e4893cc424dbf231a7b545367c4_27.png)

C99引入了**灵活数组成员**，即结构体末尾大小未指定的数组。对于这样的结构体：
```c
struct flex { char c; char array[]; };
```
`sizeof(struct flex)` 仅返回成员 `c` 的大小（即1）。灵活数组成员在`sizeof`计算中视为零大小。这意味着对包含灵活数组成员的结构体使用`sizeof`无法获得其实际存储的数据长度。

## 4：sizeof与strlen的正面比较

![](img/5c246e4893cc424dbf231a7b545367c4_29.png)

现在，我们通过一个具体程序来对比`sizeof`和`strlen`。

以下是程序中不同变量的定义和初始化：
```c
char buff1[] = "ABC";                    // 数组，初始化
char buff2[1024];                        // 数组，未初始化
char buff3[1024] = "ABC";                // 数组，部分初始化
char *s1 = "ABC";                        // 指针，指向字符串字面量
char *s2 = buff3;                        // 指针，指向数组
char *s3;                                // 指针，未初始化
s3 = malloc(1024);                       // 指针，指向动态内存
strncpy(s3, "HelloWorld", 10);           // 向动态内存写入数据
```

![](img/5c246e4893cc424dbf231a7b545367c4_31.png)

![](img/5c246e4893cc424dbf231a7b545367c4_33.png)

![](img/5c246e4893cc424dbf231a7b545367c4_35.png)

观察结果如下：

![](img/5c246e4893cc424dbf231a7b545367c4_37.png)

![](img/5c246e4893cc424dbf231a7b545367c4_39.png)

![](img/5c246e4893cc424dbf231a7b545367c4_41.png)

*   对于数组`buff1`：
    *   `sizeof(buff1)` 为 **4**（包含结尾的`\0`）。
    *   `strlen(buff1)` 为 **3**（不包含结尾的`\0`）。
*   对于未初始化的数组`buff2`：
    *   `sizeof(buff2)` 为 **1024**（数组总大小）。
    *   `strlen(buff2)` **结果未定义**，会一直计数直到遇到内存中的第一个`\0`。
*   对于指针`s1`或`s3`：
    *   `sizeof(s1)` 始终为 **8**（指针本身的大小）。
    *   `strlen(s1)` 取决于指针所指向的字符串内容。

![](img/5c246e4893cc424dbf231a7b545367c4_43.png)

**核心区别总结**：
*   `sizeof` 是**编译时**运算符（大部分情况下），返回**数据类型或对象的存储大小**。
*   `strlen` 是**运行时**函数，计算**字符串中直到第一个空字符(`\0`)之前的字符数**。

![](img/5c246e4893cc424dbf231a7b545367c4_45.png)

## 5：关键结论与正确用法

回到最初的`strncpy`示例，我们可以得出关键结论：

![](img/5c246e4893cc424dbf231a7b545367c4_47.png)

1.  当操作对象是**固定大小的数组**（编译时已知大小）时，可以使用`sizeof`来获取缓冲区大小。
2.  当操作对象是**指针**（尤其是指向动态分配内存的指针）时，**绝不能**使用`sizeof`来获取缓冲区长度。`sizeof(指针)`永远返回指针变量本身的大小（如8字节），而非它所指向的内存块的大小。

![](img/5c246e4893cc424dbf231a7b545367c4_49.png)

![](img/5c246e4893cc424dbf231a7b545367c4_51.png)

对于动态分配的内存，你必须自己记录或知晓缓冲区的最大容量，并将其作为参数传递给`strncpy`等函数。

```c
// 正确做法
char *buf = malloc(SOME_SIZE);
strncpy(buf, src, SOME_SIZE - 1);
buf[SOME_SIZE - 1] = '\0';
```

`sizeof`常用于动态内存分配，特别是为结构体分配空间时：
```c
struct somestruct *ptr = malloc(sizeof(struct somestruct));
```
但务必将其与`strlen`区分开来。

## 总结

![](img/5c246e4893cc424dbf231a7b545367c4_53.png)

本节课中我们一起学习了`sizeof`运算符与`strlen`函数的根本区别。`sizeof`用于获取存储大小，在编译时（通常）确定；而`strlen`用于计算字符串长度，在运行时确定。混淆二者，尤其是在处理指针和动态内存时，会导致缓冲区溢出等严重错误。请务必根据上下文选择正确的工具。