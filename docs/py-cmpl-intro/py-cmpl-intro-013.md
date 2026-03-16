# 013：字符串索引 📍

在本节课中，我们将要学习Python中的字符串索引。索引允许我们使用方括号（索引操作符）来访问字符串中的特定字符或子串。我们将从基础概念开始，逐步学习如何指定起始位置、结束位置和步长，并通过信用卡号的实例来掌握这些技巧。

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_1.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_2.png)

## 索引操作符基础

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_3.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_4.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_5.png)

索引操作符是一对方括号 `[]`，跟在字符串变量名之后。通过它，我们可以访问字符串序列中的元素。计算机的计数从0开始，因此字符串的第一个字符索引是0。

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_7.png)

例如，我们定义一个信用卡号字符串：

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_9.png)

```python
credit_number = "1234-5678-9012-3456"
```

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_11.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_12.png)

要访问第一个字符，我们可以这样做：

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_13.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_15.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_16.png)

```python
print(credit_number[0])  # 输出: 1
```

将索引改为1，则访问第二个字符：

```python
print(credit_number[1])  # 输出: 2
```

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_18.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_19.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_21.png)

## 使用起始和结束索引切片

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_23.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_25.png)

上一节我们介绍了如何访问单个字符，本节中我们来看看如何获取字符串的一部分，即“切片”。

切片语法是在方括号内使用冒号分隔起始和结束索引：`[start:end]`。起始索引是包含的，而结束索引是排除的。

例如，要获取前四个数字：

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_27.png)

```python
print(credit_number[0:4])  # 输出: 1234
# 或者省略起始索引0，效果相同
print(credit_number[:4])   # 输出: 1234
```

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_29.png)

要获取第二组数字“5678”，我们需要找到正确的索引位置：

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_31.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_32.png)

```python
print(credit_number[5:9])  # 输出: 5678
```

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_34.png)

如果需要从某个位置开始直到字符串末尾的所有字符，可以省略结束索引：

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_36.png)

```python
print(credit_number[5:])   # 输出: 5678-9012-3456
```

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_38.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_40.png)

## 使用负索引

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_42.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_43.png)

除了从左边开始计数，Python还支持负索引，从字符串末尾开始计数，-1代表最后一个字符。

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_45.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_47.png)

以下是负索引的用法示例：

```python
print(credit_number[-1])   # 输出: 6 (最后一个字符)
print(credit_number[-2])   # 输出: 5 (倒数第二个字符)
```

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_49.png)

## 理解步长参数

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_51.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_52.png)

到目前为止，我们学习了如何获取连续的子串。现在，让我们看看如何使用步长来间隔地获取字符。

步长是索引操作符中的第三个参数，语法为 `[start:end:step]`。如果省略起始和结束，只指定步长，则需要两个冒号 `[::step]`。

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_54.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_55.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_56.png)

例如，获取字符串中每隔一个的字符：

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_58.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_60.png)

```python
print(credit_number[::2])  # 输出: 13-68 91 35
```

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_62.png)

将步长改为3，则每隔两个字符取一个：

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_64.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_66.png)

```python
print(credit_number[::3])  # 输出: 14-7-26
```

## 实践应用示例

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_68.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_70.png)

让我们将所学知识应用到两个实际场景中。

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_72.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_74.png)

**场景一：获取信用卡号的后四位**

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_76.png)

这在隐藏敏感信息时很常用。我们可以使用负索引轻松实现：

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_78.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_80.png)

```python
last_digits = credit_number[-4:]
print(f"XXXX-XXXX-XXXX-{last_digits}")
# 输出: XXXX-XXXX-XXXX-3456
```

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_82.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_83.png)

**场景二：反转字符串**

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_85.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_87.png)

通过将步长设置为-1，我们可以轻松地反转整个字符串：

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_89.png)

```python
reversed_number = credit_number[::-1]
print(reversed_number)
# 输出: 6543-2109-8765-4321
```

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_90.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_92.png)

## 总结

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_94.png)

本节课中我们一起学习了Python字符串索引的核心知识。我们了解到：

1.  索引操作符 `[]` 用于访问字符串中的元素。
2.  基本索引从0开始，`字符串[索引]` 获取单个字符。
3.  切片语法 `[起始:结束]` 用于获取子串，起始包含，结束排除。
4.  负索引 `[-1]` 用于从字符串末尾开始访问。
5.  步长参数 `[::步长]` 用于以特定间隔获取字符，设置步长为-1可以反转字符串。

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_96.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_97.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_98.png)

![](img/6226ceffd1877cb6d4ca89bad8eb77a8_99.png)

通过理解和练习这些概念，你将能够灵活地操作和处理Python中的任何字符串数据。