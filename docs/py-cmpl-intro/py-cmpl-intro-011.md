Python超全入门教程：P11：条件表达式 🎯

在本节课中，我们将要学习Python中的条件表达式。这是一种简洁的语法，可以在一行代码中实现简单的条件判断，是`if-else`语句的快捷方式。

---

### 什么是条件表达式？ 🤔

条件表达式是一种单行的快捷方式，用于替代`if-else`语句。如果你熟悉其他编程语言，它也被称为**三元运算符**。其核心行为是根据一个条件，在两个值中选择一个进行打印或赋值。

其基本公式如下：

![](img/e58cbf877cdc2bb205e37d41a6c36e36_1.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_2.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_3.png)

```
X if condition else Y
```

![](img/e58cbf877cdc2bb205e37d41a6c36e36_4.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_5.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_6.png)

如果条件为真，则返回`X`；如果条件为假，则返回`Y`。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_7.png)

---

![](img/e58cbf877cdc2bb205e37d41a6c36e36_9.png)

### 基础示例：判断数字正负 🔢

![](img/e58cbf877cdc2bb205e37d41a6c36e36_11.png)

让我们从一个简单的例子开始。我们将创建一个变量`num`，并判断它是正数还是负数。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_12.png)

```python
num = 5
print("positive" if num > 0 else "negative")
```

![](img/e58cbf877cdc2bb205e37d41a6c36e36_14.png)

在这个例子中，条件`num > 0`为真，因此会打印`"positive"`。如果`num`是`-5`，条件为假，则会打印`"negative"`。

---

### 判断数字奇偶性 🔄

![](img/e58cbf877cdc2bb205e37d41a6c36e36_16.png)

上一节我们介绍了如何判断正负，本节中我们来看看如何判断一个数字是奇数还是偶数。这次，我们将把结果赋值给一个变量。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_18.png)

```python
num = 6
result = "even" if num % 2 == 0 else "odd"
print(result)
```

![](img/e58cbf877cdc2bb205e37d41a6c36e36_20.png)

这里，`num % 2 == 0`检查`num`是否能被2整除。如果能，则`result`被赋值为`"even"`，否则为`"odd"`。当`num`为6时，打印`"even"`；如果`num`为5，则会打印`"odd"`。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_22.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_24.png)

---

### 比较两个数字的大小 ⚖️

![](img/e58cbf877cdc2bb205e37d41a6c36e36_26.png)

接下来，我们使用条件表达式来找出两个数字中的最大值和最小值。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_28.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_29.png)

```python
a = 6
b = 7

![](img/e58cbf877cdc2bb205e37d41a6c36e36_31.png)

max_num = a if a > b else b
print(max_num)  # 输出 7

![](img/e58cbf877cdc2bb205e37d41a6c36e36_33.png)

min_num = a if a < b else b
print(min_num)  # 输出 6
```

第一个表达式判断`a`是否大于`b`，是则返回`a`，否则返回`b`，从而得到最大值。第二个表达式则用于获取最小值。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_35.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_36.png)

---

### 根据年龄判断状态 👶👨

![](img/e58cbf877cdc2bb205e37d41a6c36e36_38.png)

条件表达式也可以用于处理更复杂的逻辑，例如根据年龄判断一个人是成年人还是儿童。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_40.png)

```python
age = 25
status = "adult" if age >= 18 else "child"
print(status)  # 输出 adult
```

![](img/e58cbf877cdc2bb205e37d41a6c36e36_42.png)

如果`age`是13，条件`age >= 18`为假，`status`将被赋值为`"child"`。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_44.png)

---

![](img/e58cbf877cdc2bb205e37d41a6c36e36_46.png)

### 根据温度描述天气 🌡️

![](img/e58cbf877cdc2bb205e37d41a6c36e36_48.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_50.png)

让我们看一个根据温度描述天气的例子。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_52.png)

```python
temperature = 30
weather = "hot" if temperature > 20 else "cold"
print(weather)  # 输出 hot
```

![](img/e58cbf877cdc2bb205e37d41a6c36e36_54.png)

如果`temperature`是20，条件`temperature > 20`为假，`weather`将被赋值为`"cold"`。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_56.png)

---

![](img/e58cbf877cdc2bb205e37d41a6c36e36_58.png)

### 根据用户角色设置访问权限 🔐

![](img/e58cbf877cdc2bb205e37d41a6c36e36_60.png)

最后一个例子，我们将根据用户的角色来决定其访问权限级别。

![](img/e58cbf877cdc2bb205e37d41a6c36e36_62.png)

```python
user_role = "admin"
access_level = "full access" if user_role == "admin" else "limited access"
print(access_level)  # 输出 full access
```

![](img/e58cbf877cdc2bb205e37d41a6c36e36_64.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_66.png)

如果`user_role`是`"guest"`，条件`user_role == "admin"`为假，`access_level`将被赋值为`"limited access"`。

---

### 总结 📝

本节课中我们一起学习了Python中的条件表达式。它是一种单行的快捷方式，用于替代`if-else`语句，类似于其他编程语言中的三元运算符。通过条件表达式，我们可以根据一个条件，在两个值中选择一个进行打印或赋值。其核心公式是：

![](img/e58cbf877cdc2bb205e37d41a6c36e36_68.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_69.png)

```
X if condition else Y
```

![](img/e58cbf877cdc2bb205e37d41a6c36e36_70.png)

![](img/e58cbf877cdc2bb205e37d41a6c36e36_71.png)

我们通过判断数字正负、奇偶性、比较大小、判断年龄状态、描述天气以及设置用户权限等多个例子，实践了条件表达式的用法。掌握这个语法能让你的代码更加简洁明了。