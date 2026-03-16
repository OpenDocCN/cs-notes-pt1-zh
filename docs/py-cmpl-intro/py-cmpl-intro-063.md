Python超全入门教程：P63：Lambda函数详解 🐍

在本节课中，我们将要学习Python中的Lambda函数。Lambda函数是一种小型、匿名的函数，通常用于一次性操作。我们将了解其语法、用途以及如何在实际编程中应用它们。

---

### **什么是Lambda函数？**

Lambda函数是一种小型、匿名的函数，通常用于一次性操作。它们可以接受任意数量的参数，但只能包含一个表达式。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_1.png)

Lambda函数的主要优点包括：
*   **保持命名空间清洁**：由于它们是匿名的，我们无需为仅使用一次的函数费心起名。
*   **与高阶函数配合使用**：它们常与`sort`、`map`、`filter`和`reduce`等高阶函数结合使用，这将在后续课程中详细讨论。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_2.png)

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_3.png)

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_4.png)

Lambda函数的基本语法如下：
```python
lambda 参数: 表达式
```
你可能会在`map`等高阶函数中看到这种写法。不过，本节课我们主要聚焦于Lambda函数本身的语法。

---

### **Lambda函数语法详解**

上一节我们介绍了Lambda函数的基本概念，本节中我们来看看如何具体定义和使用它。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_6.png)

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_7.png)

虽然可以将Lambda函数赋值给一个变量，但这并非其主要用途。它们更常见于高阶函数内部。不过，为了演示语法，我们先从赋值给变量开始。

以下是一个将数字翻倍的Lambda函数示例：
```python
double = lambda x: x * 2
```
我们创建了一个名为`double`的变量，并将一个Lambda函数赋值给它。这个函数接受一个参数`x`，并返回`x * 2`的结果。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_9.png)

现在，`double`变量包含了一个函数，我们可以像调用普通函数一样调用它。
```python
print(double(2))  # 输出：4
print(double(3))  # 输出：6
print(double(4))  # 输出：8
```

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_10.png)

---

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_12.png)

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_13.png)

### **多参数Lambda函数**

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_15.png)

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_17.png)

Lambda函数可以接受多个参数。接下来，我们创建一个将两个数字相加的Lambda函数。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_19.png)

以下是实现两个数字相加的Lambda函数：
```python
add = lambda x, y: x + y
```
这个函数接受两个参数`x`和`y`，并返回它们的和。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_21.png)

我们可以这样调用它：
```python
print(add(2, 3))  # 输出：5
```

---

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_23.png)

### **使用条件表达式**

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_25.png)

我们也可以在Lambda函数的表达式中使用条件逻辑。让我们编写一个找出两个数中较大值的函数。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_27.png)

以下是找出两个数中较大值的Lambda函数：
```python
max_value = lambda x, y: x if x > y else y
```
这个函数比较`x`和`y`，如果`x`大于`y`则返回`x`，否则返回`y`。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_29.png)

调用示例：
```python
print(max_value(4, 5))  # 输出：5
print(max_value(6, 5))  # 输出：6
```

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_31.png)

类似地，我们可以找出较小值：
```python
min_value = lambda x, y: x if x < y else y
print(min_value(6, 7))  # 输出：6
print(min_value(8, 7))  # 输出：7
```

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_33.png)

---

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_35.png)

### **字符串操作与逻辑判断**

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_37.png)

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_39.png)

Lambda函数同样适用于字符串操作和更复杂的逻辑判断。

**1. 拼接字符串**
以下Lambda函数用于拼接姓和名：
```python
full_name = lambda first, last: first + " " + last
print(full_name("Spongebob", "Squarepants"))  # 输出：Spongebob Squarepants
```

**2. 判断数字是否为偶数**
以下Lambda函数检查一个数字是否为偶数：
```python
is_even = lambda x: x % 2 == 0
print(is_even(4))  # 输出：True
print(is_even(5))  # 输出：False
```
这里使用了取模运算符`%`，`x % 2 == 0`表示`x`除以2的余数为0，即为偶数。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_41.png)

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_43.png)

**3. 年龄验证**
以下Lambda函数用于进行简单的年龄验证：
```python
age_check = lambda age: True if age >= 18 else False
print(age_check(21))  # 输出：True
print(age_check(12))  # 输出：False
```

---

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_45.png)

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_47.png)

### **总结**

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_49.png)

本节课中我们一起学习了Python中的Lambda函数。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_50.png)

我们了解到，Lambda函数是小型、匿名的函数，主要用于一次性操作。其核心语法是`lambda 参数: 表达式`。它们可以接受任意数量的参数，但只能包含一个表达式。

使用Lambda函数有助于保持代码命名空间的清洁，因为我们无需为临时函数命名。它们在高阶函数（如`sort`、`map`、`filter`和`reduce`）中尤其有用，这将是后续课程的重点。

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_52.png)

![](img/ee0d25f7d46e9e6970b7daf7e6cfb000_53.png)

通过多个示例，我们练习了如何使用Lambda函数进行数学运算、字符串拼接以及逻辑判断。