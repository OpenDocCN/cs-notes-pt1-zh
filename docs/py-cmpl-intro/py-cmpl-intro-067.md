# 067：Python排序详解 🧑‍💻

在本节课中，我们将学习Python中对不同数据结构进行排序的方法。我们将涵盖列表、元组、字典和自定义对象，并详细解释每种情况下的排序技巧。

## 列表排序 📋

![](img/4819c5f43593a74f62af660809ee2da6_1.png)

![](img/4819c5f43593a74f62af660809ee2da6_3.png)

列表是Python中最基础的数据结构之一，对其进行排序也最为直接。Python为列表提供了内置的`sort()`方法。

![](img/4819c5f43593a74f62af660809ee2da6_5.png)

以下是列表排序的步骤：

![](img/4819c5f43593a74f62af660809ee2da6_7.png)

![](img/4819c5f43593a74f62af660809ee2da6_9.png)

1.  创建一个列表。
2.  调用列表的`sort()`方法。
3.  如需逆序，可传入参数`reverse=True`。

![](img/4819c5f43593a74f62af660809ee2da6_11.png)

**代码示例：**
```python
# 对字符串列表排序
fruits = ['banana', 'orange', 'apple', 'coconut']
fruits.sort()
print(fruits)  # 输出: ['apple', 'banana', 'coconut', 'orange']

![](img/4819c5f43593a74f62af660809ee2da6_12.png)

![](img/4819c5f43593a74f62af660809ee2da6_14.png)

# 对数字列表排序
numbers = [3, 1, 5, 2, 4]
numbers.sort()
print(numbers)  # 输出: [1, 2, 3, 4, 5]

![](img/4819c5f43593a74f62af660809ee2da6_16.png)

# 逆序排序
fruits.sort(reverse=True)
print(fruits)  # 输出: ['orange', 'coconut', 'banana', 'apple']
```

![](img/4819c5f43593a74f62af660809ee2da6_18.png)

`sort()`方法会直接修改原列表，使其元素按指定顺序排列。

![](img/4819c5f43593a74f62af660809ee2da6_20.png)

---

![](img/4819c5f43593a74f62af660809ee2da6_22.png)

上一节我们介绍了如何使用`sort()`方法对列表进行原地排序。本节中我们来看看如何对元组进行排序。

![](img/4819c5f43593a74f62af660809ee2da6_24.png)

## 元组排序 📦

![](img/4819c5f43593a74f62af660809ee2da6_26.png)

元组是不可变序列，因此它没有`sort()`方法。对元组排序需要使用内置的`sorted()`函数，该函数会返回一个新的排序后的列表。

以下是元组排序的步骤：

![](img/4819c5f43593a74f62af660809ee2da6_28.png)

![](img/4819c5f43593a74f62af660809ee2da6_29.png)

1.  使用`sorted()`函数对元组进行排序，得到一个列表。
2.  如需保留元组类型，可将`sorted()`的结果用`tuple()`函数转换。
3.  使用`reverse=True`参数实现逆序。

**代码示例：**
```python
fruits_tuple = ('banana', 'orange', 'apple', 'coconut')

![](img/4819c5f43593a74f62af660809ee2da6_31.png)

# 使用sorted函数排序，返回列表
sorted_list = sorted(fruits_tuple)
print(sorted_list)  # 输出: ['apple', 'banana', 'coconut', 'orange']

![](img/4819c5f43593a74f62af660809ee2da6_33.png)

# 将排序结果转换回元组
sorted_tuple = tuple(sorted(fruits_tuple))
print(sorted_tuple)  # 输出: ('apple', 'banana', 'coconut', 'orange')

# 逆序排序
reverse_sorted_tuple = tuple(sorted(fruits_tuple, reverse=True))
print(reverse_sorted_tuple)  # 输出: ('orange', 'coconut', 'banana', 'apple')
```

`sorted()`函数不会修改原始元组，而是返回一个新的排序后的序列。

![](img/4819c5f43593a74f62af660809ee2da6_35.png)

---

![](img/4819c5f43593a74f62af660809ee2da6_37.png)

了解了列表和元组的排序后，我们进入更复杂的部分。本节中我们来看看如何对字典进行排序。

## 字典排序 📖

字典由键值对组成，排序时需要指定是按`键`还是按`值`进行排序。字典本身是无序的（在Python 3.7之前），但我们可以通过`sorted()`函数获取排序后的视图或创建新的有序字典。

以下是字典排序的几种情况：

1.  **按键排序**：使用`sorted()`函数对字典的键进行排序。
2.  **按值排序**：使用`sorted()`函数并配合`key`参数，指定按值排序。
3.  **保留键值对**：使用`items()`方法获取键值对元组，排序后再重构字典。

![](img/4819c5f43593a74f62af660809ee2da6_39.png)

**代码示例：**
```python
fruits_dict = {'banana': 105, 'orange': 73, 'apple': 72, 'coconut': 354}

![](img/4819c5f43593a74f62af660809ee2da6_41.png)

![](img/4819c5f43593a74f62af660809ee2da6_43.png)

# 1. 按键排序（返回排序后的键列表）
sorted_keys = sorted(fruits_dict)
print(sorted_keys)  # 输出: ['apple', 'banana', 'coconut', 'orange']

# 2. 按键排序并保留键值对
sorted_dict_by_key = dict(sorted(fruits_dict.items()))
print(sorted_dict_by_key)
# 输出: {'apple': 72, 'banana': 105, 'coconut': 354, 'orange': 73}

![](img/4819c5f43593a74f62af660809ee2da6_45.png)

# 3. 按键逆序排序
reverse_sorted_dict_by_key = dict(sorted(fruits_dict.items(), reverse=True))
print(reverse_sorted_dict_by_key)
# 输出: {'orange': 73, 'coconut': 354, 'banana': 105, 'apple': 72}

![](img/4819c5f43593a74f62af660809ee2da6_47.png)

# 4. 按值排序（升序）
sorted_dict_by_value = dict(sorted(fruits_dict.items(), key=lambda item: item[1]))
print(sorted_dict_by_value)
# 输出: {'apple': 72, 'orange': 73, 'banana': 105, 'coconut': 354}

![](img/4819c5f43593a74f62af660809ee2da6_49.png)

# 5. 按值逆序排序（降序）
reverse_sorted_dict_by_value = dict(sorted(fruits_dict.items(), key=lambda item: item[1], reverse=True))
print(reverse_sorted_dict_by_value)
# 输出: {'coconut': 354, 'banana': 105, 'orange': 73, 'apple': 72}
```

**核心概念解释：**
*   `dict.items()`：返回一个包含字典所有`(键, 值)`元组的视图对象。
*   `key=lambda item: item[1]`：这是一个`lambda`函数，它告诉`sorted()`函数使用每个元组（即每个键值对）的第二个元素（索引1，即“值”）作为排序依据。`item[0]`代表键。

![](img/4819c5f43593a74f62af660809ee2da6_51.png)

![](img/4819c5f43593a74f62af660809ee2da6_53.png)

---

字典的排序涉及对键值对的操作。本节中我们将应用类似的`lambda`函数概念，来对自定义对象列表进行排序。

## 对象排序 🍎

当我们有一个由自定义类实例组成的列表时，排序需要指定依据对象的哪个属性。这同样通过`sorted()`函数的`key`参数配合`lambda`函数来实现。

以下是对象排序的步骤：

1.  定义一个类，例如`Fruit`，包含`name`和`calories`属性。
2.  创建该类的对象列表。
3.  使用`sorted()`函数，并通过`key`参数指定排序依据的属性（如`lambda fruit: fruit.name`）。

![](img/4819c5f43593a74f62af660809ee2da6_55.png)

![](img/4819c5f43593a74f62af660809ee2da6_57.png)

![](img/4819c5f43593a74f62af660809ee2da6_59.png)

**代码示例：**
```python
class Fruit:
    def __init__(self, name, calories):
        self.name = name
        self.calories = calories

    def __repr__(self):
        return f"{self.name}: {self.calories}"

![](img/4819c5f43593a74f62af660809ee2da6_61.png)

# 创建水果对象列表
fruits = [
    Fruit('banana', 105),
    Fruit('apple', 72),
    Fruit('orange', 73),
    Fruit('coconut', 354)
]

![](img/4819c5f43593a74f62af660809ee2da6_63.png)

# 1. 按名称属性排序
sorted_by_name = sorted(fruits, key=lambda fruit: fruit.name)
print(sorted_by_name)
# 输出: [apple: 72, banana: 105, coconut: 354, orange: 73]

# 2. 按名称逆序排序
reverse_sorted_by_name = sorted(fruits, key=lambda fruit: fruit.name, reverse=True)
print(reverse_sorted_by_name)
# 输出: [orange: 73, coconut: 354, banana: 105, apple: 72]

![](img/4819c5f43593a74f62af660809ee2da6_65.png)

# 3. 按热量属性排序
sorted_by_calories = sorted(fruits, key=lambda fruit: fruit.calories)
print(sorted_by_calories)
# 输出: [apple: 72, orange: 73, banana: 105, coconut: 354]

![](img/4819c5f43593a74f62af660809ee2da6_67.png)

![](img/4819c5f43593a74f62af660809ee2da6_69.png)

# 4. 按热量逆序排序
reverse_sorted_by_calories = sorted(fruits, key=lambda fruit: fruit.calories, reverse=True)
print(reverse_sorted_by_calories)
# 输出: [coconut: 354, banana: 105, orange: 73, apple: 72]
```

![](img/4819c5f43593a74f62af660809ee2da6_71.png)

![](img/4819c5f43593a74f62af660809ee2da6_73.png)

**核心概念解释：**
*   `__repr__`方法：定义对象的官方字符串表示形式，便于打印查看。
*   `key=lambda fruit: fruit.calories`：`lambda`函数从每个`Fruit`对象中提取`calories`属性作为排序键。

![](img/4819c5f43593a74f62af660809ee2da6_74.png)

![](img/4819c5f43593a74f62af660809ee2da6_76.png)

---

![](img/4819c5f43593a74f62af660809ee2da6_78.png)

## 总结 📝

![](img/4819c5f43593a74f62af660809ee2da6_80.png)

![](img/4819c5f43593a74f62af660809ee2da6_82.png)

本节课中我们一起学习了Python中多种数据结构的排序方法：

*   **列表**：使用内置的`list.sort()`方法进行原地排序。
*   **元组**：使用`sorted()`函数生成新的排序列表，可转换为元组。
*   **字典**：使用`sorted()`函数配合`items()`方法和`key`参数，可以按键或按值排序，并重构字典。
*   **自定义对象**：使用`sorted()`函数配合`key`参数和`lambda`函数，指定依据对象的某个属性进行排序。

![](img/4819c5f43593a74f62af660809ee2da6_84.png)

![](img/4819c5f43593a74f62af660809ee2da6_85.png)

对于字典和对象的排序，关键在于理解并使用`key=lambda x: ...`来指定排序所依据的元素或属性。掌握这些技巧，你就能灵活应对Python中的各种排序需求。