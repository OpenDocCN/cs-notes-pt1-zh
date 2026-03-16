# 032：Python函数其实很简单 🐍

在本节课中，我们将要学习Python中一个非常核心的概念：**函数**。函数可以被看作是一段可重复使用的代码块，它能帮助我们避免编写重复的代码，让程序结构更清晰、更易于维护。

![](img/bee196f05b79316ff548e06f4c7ff8b8_1.png)

## 什么是函数？🤔

![](img/bee196f05b79316ff548e06f4c7ff8b8_3.png)

函数是一个可重复调用的代码块。你可以将函数想象成一个预先打包好的工具，每当需要执行特定任务时，只需“调用”这个工具即可，而无需每次都重写一遍工具内部的运作步骤。

![](img/bee196f05b79316ff548e06f4c7ff8b8_4.png)

要调用一个函数，你需要在函数名后面加上一对圆括号 `()`。

## 为什么需要函数？🎯

让我们通过一个场景来理解。假设我需要唱三遍“生日快乐歌”。如果不使用函数，我可能需要重复写三遍相同的代码，或者使用循环。

![](img/bee196f05b79316ff548e06f4c7ff8b8_6.png)

以下是重复代码的示例：
```python
print("Happy birthday to you")
print("Happy birthday to you")
print("Happy birthday to you")
```

![](img/bee196f05b79316ff548e06f4c7ff8b8_7.png)

![](img/bee196f05b79316ff548e06f4c7ff8b8_8.png)

![](img/bee196f05b79316ff548e06f4c7ff8b8_9.png)

虽然这样可以完成任务，但代码显得冗长且重复。更好的方法是：将这段代码写一次，然后在需要时重复使用它。这正是函数的作用。

## 如何定义和调用函数？🔧

要定义一个函数，你需要使用 `def` 关键字，后跟一个唯一的函数名、一对圆括号和一个冒号 `:`。属于该函数的代码需要缩进在下方。

以下是定义一个名为 `happy_birthday` 的函数的示例：
```python
def happy_birthday():
    print("Happy birthday to you")
```

要调用这个函数，只需写下函数名并加上圆括号：
```python
happy_birthday()
```

这样，函数内的代码就会被执行一次。如果需要执行三次，只需调用三次即可：
```python
happy_birthday()
happy_birthday()
happy_birthday()
```

## 向函数传递数据：参数与实参 📤

![](img/bee196f05b79316ff548e06f4c7ff8b8_11.png)

函数的一个强大之处在于，你可以向它传递数据，这些数据被称为**实参**。函数内部接收这些数据的临时变量则被称为**形参**。

![](img/bee196f05b79316ff548e06f4c7ff8b8_13.png)

你可以在调用函数时，将数据放在圆括号内传递进去。例如，我们修改 `happy_birthday` 函数，让它接收一个名字：

![](img/bee196f05b79316ff548e06f4c7ff8b8_15.png)

![](img/bee196f05b79316ff548e06f4c7ff8b8_17.png)

```python
def happy_birthday(name):
    print(f"Happy birthday to {name}")
```

现在，我们可以向不同的人唱生日歌：
```python
happy_birthday("Bro")
happy_birthday("Steve")
happy_birthday("Joe")
```

![](img/bee196f05b79316ff548e06f4c7ff8b8_19.png)

## 传递多个参数 📦

函数可以接收多个参数。你需要确保调用时传递的实参数量与函数定义时的形参数量匹配，并且顺序一致。

让我们扩展生日歌函数，同时接收名字和年龄：
```python
def happy_birthday(name, age):
    print(f"Happy birthday to {name}")
    print(f"You are {age} years old")
```

![](img/bee196f05b79316ff548e06f4c7ff8b8_21.png)

![](img/bee196f05b79316ff548e06f4c7ff8b8_22.png)

调用时传递两个参数：
```python
happy_birthday("Bro", 20)
happy_birthday("Steve", 30)
happy_birthday("Joe", 40)
```

**请注意**：形参和实参的顺序非常重要。如果顺序错乱，结果也会出错。

![](img/bee196f05b79316ff548e06f4c7ff8b8_24.png)

![](img/bee196f05b79316ff548e06f4c7ff8b8_25.png)

## 一个更实用的例子：发票函数 💰

让我们创建一个更实用的函数来生成发票。这个函数接收用户名、金额和到期日三个参数。

```python
def display_invoice(username, amount, due_date):
    print(f"Hello {username},")
    print(f"Your bill of ${amount:.2f} is due on {due_date}")
```

![](img/bee196f05b79316ff548e06f4c7ff8b8_27.png)

调用示例：
```python
display_invoice("BroCode", 42.50, "January 1")
display_invoice("Joe Schmo", 100.01, "January 2")
```

## 从函数返回数据：return语句 🔄

`return` 语句用于结束函数的执行，并将一个结果“发送回”给函数的调用者。这使得函数可以计算一个值，并将这个值用于程序的其他部分。

例如，我们创建一些基本的数学运算函数：

```python
def add(x, y):
    z = x + y
    return z

def subtract(x, y):
    z = x - y
    return z

def multiply(x, y):
    z = x * y
    return z

def divide(x, y):
    z = x / y
    return z
```

![](img/bee196f05b79316ff548e06f4c7ff8b8_29.png)

![](img/bee196f05b79316ff548e06f4c7ff8b8_31.png)

调用这些函数并将返回值赋给变量：
```python
result = add(1, 2)
print(result) # 输出：3

result = subtract(1, 2)
print(result) # 输出：-1

result = multiply(1, 2)
print(result) # 输出：2

result = divide(1, 2)
print(result) # 输出：0.5
```

你可以将函数调用想象成：函数执行完毕后，它本身“变成”了 `return` 语句后面的值。

## 综合示例：创建全名函数 📝

让我们创建一个函数，它接收名和姓，将其首字母大写，然后组合成完整的姓名并返回。

```python
def create_name(first, last):
    first = first.capitalize()
    last = last.capitalize()
    return first + " " + last
```

调用这个函数：
```python
full_name = create_name("bro", "code")
print(full_name) # 输出：Bro Code

full_name = create_name("spongebob", "squarepants")
print(full_name) # 输出：Spongebob Squarepants
```

## 总结 📚

本节课中我们一起学习了Python函数的核心知识：

1.  **函数定义**：使用 `def` 关键字定义函数，函数体需要缩进。
2.  **函数调用**：通过函数名加圆括号 `()` 来执行函数内的代码。
3.  **参数与实参**：调用函数时可以传递数据（实参），函数通过形参来接收这些数据。数量和顺序必须匹配。
4.  **返回值**：使用 `return` 语句可以将函数计算的结果发送回调用处，这个值可以被赋值给变量或用于其他操作。

![](img/bee196f05b79316ff548e06f4c7ff8b8_33.png)

![](img/bee196f05b79316ff548e06f4c7ff8b8_34.png)

![](img/bee196f05b79316ff548e06f4c7ff8b8_35.png)

函数是构建模块化、可读性强且高效代码的基石。掌握了函数，你就掌握了让代码变得整洁和强大的关键工具。在后续的学习中，我们还会经常使用并深化对函数的理解。