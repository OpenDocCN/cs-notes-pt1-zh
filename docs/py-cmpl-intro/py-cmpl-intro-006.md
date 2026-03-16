# 006：Python中的条件语句（if, elif, else） 🐍

在本节课中，我们将要学习Python中的条件语句。条件语句是编程中进行决策的基础工具，它允许程序根据特定条件的真假来执行不同的代码块。

## 概述

![](img/26574ae142fbb06f2cf03bdeb4f00980_1.png)

![](img/26574ae142fbb06f2cf03bdeb4f00980_3.png)

条件语句的核心是`if`语句。它的基本逻辑是：**如果**某个条件为真，**那么**执行一段代码；**否则**，可以执行另一段代码。我们还将学习如何使用`elif`来检查多个条件，以及如何利用布尔值来简化条件判断。

---

## 基本if语句

![](img/26574ae142fbb06f2cf03bdeb4f00980_5.png)

上一节我们介绍了条件语句的概念，本节中我们来看看最基本的`if`语句结构。

首先，我们通过一个示例来获取用户的年龄：

```python
age = int(input("Enter your age: "))
```

假设用户想申请信用卡，但规定年龄必须大于或等于18岁。我们可以使用`if`语句来检查这个条件：

![](img/26574ae142fbb06f2cf03bdeb4f00980_7.png)

```python
if age >= 18:
    print("You are now signed up.")
```

**代码解释**：
*   `if age >= 18:`：这是一个条件检查。`>=`是比较运算符，表示“大于或等于”。如果`age`变量的值满足这个条件，则执行下方缩进的代码。
*   注意`:`和代码缩进。`if`语句后的冒号`:`是必须的，而所有属于该`if`块的代码都必须**缩进**（通常是4个空格或一个Tab键）。

![](img/26574ae142fbb06f2cf03bdeb4f00980_9.png)

运行代码，如果输入21，条件为真，程序会打印“You are now signed up.”。如果输入13，条件为假，则`if`块内的代码会被跳过，程序没有任何输出。

---

## 使用else处理其他情况

如果条件不满足时，我们想执行另一段代码，该怎么办呢？这时就需要`else`语句。

![](img/26574ae142fbb06f2cf03bdeb4f00980_11.png)

![](img/26574ae142fbb06f2cf03bdeb4f00980_12.png)

以下是`else`语句的用法：

![](img/26574ae142fbb06f2cf03bdeb4f00980_13.png)

```python
if age >= 18:
    print("You are now signed up.")
else:
    print("You must be 18 plus to sign up.")
```

![](img/26574ae142fbb06f2cf03bdeb4f00980_15.png)

现在，如果用户年龄小于18岁，程序会打印“You must be 18 plus to sign up.”。`else`就像一个“备用方案”，当所有`if`条件都不满足时执行。

![](img/26574ae142fbb06f2cf03bdeb4f00980_17.png)

---

![](img/26574ae142fbb06f2cf03bdeb4f00980_19.png)

![](img/26574ae142fbb06f2cf03bdeb4f00980_21.png)

## 使用elif检查多个条件

![](img/26574ae142fbb06f2cf03bdeb4f00980_23.png)

有时我们需要检查一系列条件，而不仅仅是“是”或“否”。这时可以使用`elif`（`else if`的缩写）。

以下是添加多个`elif`语句的示例：

![](img/26574ae142fbb06f2cf03bdeb4f00980_25.png)

![](img/26574ae142fbb06f2cf03bdeb4f00980_27.png)

```python
if age >= 100:
    print("You are too old to sign up.")
elif age >= 18:
    print("You are now signed up.")
elif age < 0:
    print("You haven‘t been born yet.")
else:
    print("You must be 18 plus to sign up.")
```

![](img/26574ae142fbb06f2cf03bdeb4f00980_29.png)

**重要提示**：条件检查是**从上到下依次进行**的。一旦某个条件为真，就会执行对应的代码块，并跳过后面所有的`elif`和`else`。因此，条件的**顺序**非常重要。在上面的例子中，我们首先检查年龄是否过大，然后检查是否成年，最后检查是否为无效的负数。

---

![](img/26574ae142fbb06f2cf03bdeb4f00980_31.png)

## 更多条件语句示例

![](img/26574ae142fbb06f2cf03bdeb4f00980_33.png)

为了加深理解，我们来看几个不同的应用场景。

![](img/26574ae142fbb06f2cf03bdeb4f00980_35.png)

### 示例1：是/否回答

![](img/26574ae142fbb06f2cf03bdeb4f00980_37.png)

以下是询问用户是否需要食物的示例：

![](img/26574ae142fbb06f2cf03bdeb4f00980_39.png)

```python
response = input("Would you like food? (Y/N): ")

if response == "Y":
    print("Have some food.")
else:
    print("No food for you.")
```

![](img/26574ae142fbb06f2cf03bdeb4f00980_41.png)

**核心概念**：注意这里使用了双等号`==`。在Python中，`=`是赋值运算符，用于给变量赋值。而`==`是**比较运算符**，用于检查两个值是否相等。在条件判断中，必须使用`==`。

![](img/26574ae142fbb06f2cf03bdeb4f00980_43.png)

### 示例2：检查输入是否为空

![](img/26574ae142fbb06f2cf03bdeb4f00980_45.png)

以下是检查用户是否输入了名字的示例：

![](img/26574ae142fbb06f2cf03bdeb4f00980_47.png)

```python
name = input("Enter your name: ")

if name == "":
    print("You did not type in your name.")
else:
    print(f"Hello {name}")
```

这里，`name == ““`检查`name`变量是否等于空字符串。如果用户直接按回车键，条件为真，程序会提示用户未输入名字。

---

## 在if语句中使用布尔变量

布尔值（`True`或`False`）可以直接用在`if`语句中，因为条件表达式本身的结果就是布尔值。

以下是使用布尔变量的示例：

![](img/26574ae142fbb06f2cf03bdeb4f00980_49.png)

```python
for_sale = True

![](img/26574ae142fbb06f2cf03bdeb4f00980_51.png)

if for_sale:
    print("This item is for sale.")
else:
    print("This item is not for sale.")
```

因为`for_sale`本身就是`True`，所以`if for_sale:`等价于`if True:`，条件为真，执行第一条打印语句。如果将`for_sale`改为`False`，则会执行`else`块。

另一个例子：

![](img/26574ae142fbb06f2cf03bdeb4f00980_53.png)

![](img/26574ae142fbb06f2cf03bdeb4f00980_55.png)

```python
online = False

if online:
    print("The user is online.")
else:
    print("The user is offline.")
```

![](img/26574ae142fbb06f2cf03bdeb4f00980_57.png)

---

![](img/26574ae142fbb06f2cf03bdeb4f00980_59.png)

## 总结

本节课中我们一起学习了Python条件语句的核心用法：
1.  **基本结构**：使用`if`、`elif`和`else`来构建决策分支。
2.  **语法要点**：条件后要加冒号`:`，执行代码块必须正确缩进。
3.  **比较运算符**：使用`==`来判断相等，使用`>=`、`<`等进行比较。
4.  **执行逻辑**：程序从上到下检查条件，第一个为真的条件块会被执行，后续块被跳过。
5.  **布尔值应用**：可以直接将布尔变量作为条件进行判断。

![](img/26574ae142fbb06f2cf03bdeb4f00980_61.png)

条件语句是控制程序流程的基石，熟练掌握它将为你后续学习循环、函数等更复杂的编程概念打下坚实基础。