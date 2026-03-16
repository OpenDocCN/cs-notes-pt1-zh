# 007：编写一个简易计算器 🧮

在本节课中，我们将学习如何使用Python的基础知识，特别是`if`语句，来编写一个简单的命令行计算器程序。我们将从用户那里获取输入，执行基本的算术运算，并处理一些常见的错误。

![](img/896cf68bbf4345f93aa6e564a1107929_1.png)

![](img/896cf68bbf4345f93aa6e564a1107929_2.png)

![](img/896cf68bbf4345f93aa6e564a1107929_3.png)

---

## 概述

我们将创建一个程序，允许用户选择一种算术运算符（加、减、乘、除），然后输入两个数字。程序会根据用户选择的运算符计算结果并输出。我们还将学习如何处理无效的输入。

---

## 获取用户输入

首先，我们需要从用户那里获取三个信息：运算符、第一个数字和第二个数字。

![](img/896cf68bbf4345f93aa6e564a1107929_5.png)

以下是获取用户输入的代码：

![](img/896cf68bbf4345f93aa6e564a1107929_7.png)

```python
operator = input("Enter an operator (+, -, *, /): ")
num1 = float(input("Enter the first number: "))
num2 = float(input("Enter the second number: "))
```

**注意**：我们使用`float()`函数将输入的字符串转换为浮点数。这是因为`input()`函数返回的是字符串类型，直接进行数学运算会导致字符串拼接，而不是数值计算。例如，`"10" + "11"`的结果是`"1011"`，而不是`21`。

![](img/896cf68bbf4345f93aa6e564a1107929_9.png)

---

![](img/896cf68bbf4345f93aa6e564a1107929_10.png)

## 使用条件语句判断运算

![](img/896cf68bbf4345f93aa6e564a1107929_12.png)

上一节我们介绍了如何获取用户输入。本节中，我们来看看如何使用`if-elif-else`语句来判断用户选择了哪种运算符，并执行相应的计算。

![](img/896cf68bbf4345f93aa6e564a1107929_14.png)

以下是判断和计算的逻辑结构：

![](img/896cf68bbf4345f93aa6e564a1107929_16.png)

```python
if operator == "+":
    result = num1 + num2
elif operator == "-":
    result = num1 - num2
elif operator == "*":
    result = num1 * num2
elif operator == "/":
    result = num1 / num2
else:
    print(f"{operator} is not a valid operator.")
```

![](img/896cf68bbf4345f93aa6e564a1107929_18.png)

**代码解释**：
*   `if`和`elif`语句检查`operator`变量的值。
*   如果匹配到有效的运算符（`+`, `-`, `*`, `/`），则进行相应的计算并将结果存储在`result`变量中。
*   如果`operator`的值不匹配任何有效选项，则执行`else`块中的代码，向用户提示输入无效。

![](img/896cf68bbf4345f93aa6e564a1107929_20.png)

---

![](img/896cf68bbf4345f93aa6e564a1107929_22.png)

![](img/896cf68bbf4345f93aa6e564a1107929_24.png)

## 输出结果与格式化

![](img/896cf68bbf4345f93aa6e564a1107929_26.png)

在计算出结果后，我们需要将其输出给用户。为了使结果更易读，我们可以使用`round()`函数对结果进行四舍五入。

![](img/896cf68bbf4345f93aa6e564a1107929_28.png)

以下是输出结果的代码：

![](img/896cf68bbf4345f93aa6e564a1107929_30.png)

```python
if operator == "+":
    result = num1 + num2
    print(round(result, 3))
elif operator == "-":
    result = num1 - num2
    print(round(result, 3))
elif operator == "*":
    result = num1 * num2
    print(round(result, 3))
elif operator == "/":
    result = num1 / num2
    print(round(result, 3))
else:
    print(f"{operator} is not a valid operator.")
```

![](img/896cf68bbf4345f93aa6e564a1107929_32.png)

**公式/函数**：
*   `round(number, ndigits)`：将`number`四舍五入到`ndigits`位小数。如果省略`ndigits`，则四舍五入到最接近的整数。
*   例如，`round(5.6789, 2)` 返回 `5.68`。

![](img/896cf68bbf4345f93aa6e564a1107929_34.png)

![](img/896cf68bbf4345f93aa6e564a1107929_36.png)

---

![](img/896cf68bbf4345f93aa6e564a1107929_38.png)

## 完整代码示例

将以上所有部分组合起来，就得到了我们简易计算器的完整代码。

![](img/896cf68bbf4345f93aa6e564a1107929_40.png)

```python
# 获取用户输入
operator = input("Enter an operator (+, -, *, /): ")
num1 = float(input("Enter the first number: "))
num2 = float(input("Enter the second number: "))

# 根据运算符进行计算并输出结果
if operator == "+":
    result = num1 + num2
    print(round(result, 3))
elif operator == "-":
    result = num1 - num2
    print(round(result, 3))
elif operator == "*":
    result = num1 * num2
    print(round(result, 3))
elif operator == "/":
    result = num1 / num2
    print(round(result, 3))
else:
    print(f"{operator} is not a valid operator.")
```

![](img/896cf68bbf4345f93aa6e564a1107929_42.png)

![](img/896cf68bbf4345f93aa6e564a1107929_44.png)

---

## 总结

![](img/896cf68bbf4345f93aa6e564a1107929_46.png)

本节课中我们一起学习了如何构建一个基础的Python计算器。我们实践了以下几个核心概念：
1.  使用`input()`函数获取用户输入。
2.  使用`float()`进行类型转换，确保进行数值运算。
3.  使用`if-elif-else`条件语句来控制程序流程，根据不同的输入执行不同的代码块。
4.  使用`round()`函数格式化输出结果。
5.  通过`else`分支处理无效的用户输入，使程序更加健壮。

![](img/896cf68bbf4345f93aa6e564a1107929_48.png)

![](img/896cf68bbf4345f93aa6e564a1107929_49.png)

这个程序是理解Python基础控制流和用户交互的一个绝佳起点。你可以尝试在此基础上增加更多功能，例如支持更多运算符（如求余`%`、幂运算`**`），或者加入循环让用户可以连续计算。