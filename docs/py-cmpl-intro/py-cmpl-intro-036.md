Python超全入门教程：P36：可迭代对象详解 🐍

在本节课中，我们将要学习Python中一个核心概念——可迭代对象。我们将了解什么是可迭代对象，以及如何在不同类型的数据结构（如列表、元组、集合、字符串和字典）中使用循环来遍历它们。

---

![](img/6e8c40c8113edca802f95e776a7965eb_1.png)

![](img/6e8c40c8113edca802f95e776a7965eb_2.png)

### **什么是可迭代对象？**

任何能够一次返回一个元素的对象或集合，都被视为可迭代对象。如果一个对象或集合被认为是可迭代的，那么它就可以在循环中被遍历。

---

### **列表的迭代**

![](img/6e8c40c8113edca802f95e776a7965eb_4.png)

![](img/6e8c40c8113edca802f95e776a7965eb_6.png)

列表是典型的可迭代对象。我们可以使用`for`循环来遍历列表中的每个元素。

在`for`循环的上下文中，我们会逐个获得列表中的元素。对于正在处理的每个元素，我们可以给它一个临时的、描述性的名称。

![](img/6e8c40c8113edca802f95e776a7965eb_8.png)

以下是创建和遍历一个数字列表的示例：

![](img/6e8c40c8113edca802f95e776a7965eb_9.png)

```python
numbers = [1, 2, 3, 4, 5]
for number in numbers:
    print(number)
```
这段代码将输出数字1到5，每个数字占一行。

为当前元素命名时，应使用能清晰描述其内容的名称。例如，使用`item`也是一个不错的选择：

```python
for item in numbers:
    print(item)
```

![](img/6e8c40c8113edca802f95e776a7965eb_11.png)

---

![](img/6e8c40c8113edca802f95e776a7965eb_13.png)

### **反向迭代**

你可以使用`reversed()`函数来反向遍历一个可迭代对象。

```python
for number in reversed(numbers):
    print(number)
```
这将输出数字5、4、3、2、1。

---

![](img/6e8c40c8113edca802f95e776a7965eb_15.png)

### **控制打印输出**

![](img/6e8c40c8113edca802f95e776a7965eb_17.png)

如果你不希望每个元素都打印在新的一行，可以修改`print`函数的`end`参数。

默认情况下，`print`函数以换行符结束。我们可以将其替换为空格或其他字符。

例如，用空格分隔每个元素：

![](img/6e8c40c8113edca802f95e776a7965eb_19.png)

![](img/6e8c40c8113edca802f95e776a7965eb_21.png)

```python
for number in numbers:
    print(number, end=' ')
```

或者，用“ - ”来分隔：

![](img/6e8c40c8113edca802f95e776a7965eb_23.png)

```python
for number in numbers:
    print(number, end=' - ')
```

![](img/6e8c40c8113edca802f95e776a7965eb_25.png)

---

![](img/6e8c40c8113edca802f95e776a7965eb_27.png)

### **元组的迭代**

![](img/6e8c40c8113edca802f95e776a7965eb_29.png)

元组也是可迭代对象。我们可以像遍历列表一样遍历元组。

```python
numbers_tuple = (1, 2, 3, 4, 5)
for number in numbers_tuple:
    print(number)
```

![](img/6e8c40c8113edca802f95e776a7965eb_31.png)

![](img/6e8c40c8113edca802f95e776a7965eb_33.png)

---

![](img/6e8c40c8113edca802f95e776a7965eb_35.png)

### **集合的迭代**

集合同样是可迭代的。但需要注意的是，集合是无序的，并且**不能**使用`reversed()`函数进行反向迭代，尝试这样做会引发`TypeError`。

![](img/6e8c40c8113edca802f95e776a7965eb_37.png)

以下是遍历集合的示例：

![](img/6e8c40c8113edca802f95e776a7965eb_39.png)

```python
fruits = {"apple", "orange", "banana", "coconut"}
for fruit in fruits:
    print(fruit)
```

---

### **字符串的迭代**

字符串也是可迭代对象，遍历字符串会逐个返回其中的字符。

```python
name = "YourChannelName"
for character in name:
    print(character, end=' ')
```

![](img/6e8c40c8113edca802f95e776a7965eb_41.png)

---

![](img/6e8c40c8113edca802f95e776a7965eb_43.png)

### **字典的迭代**

![](img/6e8c40c8113edca802f95e776a7965eb_45.png)

字典的迭代稍微复杂一些。默认情况下，直接迭代字典会返回所有的**键**，而不是值。

![](img/6e8c40c8113edca802f95e776a7965eb_47.png)

```python
my_dict = {'a': 1, 'b': 2, 'c': 3}
for key in my_dict:
    print(key)  # 输出：a, b, c
```

**获取字典的值：**
要获取值，需要使用字典的`.values()`方法。

```python
for value in my_dict.values():
    print(value)  # 输出：1, 2, 3
```

**同时获取键和值：**
要同时获取键和值，需要使用字典的`.items()`方法，它会返回一个包含键值对的可迭代对象。

```python
for key, value in my_dict.items():
    print(f"{key} = {value}")
```
输出结果为：`a = 1`, `b = 2`, `c = 3`。

![](img/6e8c40c8113edca802f95e776a7965eb_49.png)

---

### **总结**

![](img/6e8c40c8113edca802f95e776a7965eb_51.png)

本节课中，我们一起学习了Python中的可迭代对象。可迭代对象是指能够一次返回一个元素的对象或集合，这意味着它们可以在`for`循环中被遍历。我们实践了如何遍历列表、元组、集合、字符串和字典，并学习了如何控制输出格式以及处理字典的键和值。掌握可迭代对象是理解Python循环和数据处理的基础。