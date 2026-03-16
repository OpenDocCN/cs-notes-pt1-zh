# 025：Python字典详解 📚

在本节课中，我们将要学习Python中一个非常重要的数据结构——字典。字典是Python四种基本集合类型之一，它通过键值对的形式存储数据，具有有序、可变的特性，并且不允许键重复。

![](img/db4e391228134f195e9685c04f60cfcd_1.png)

## 概述

![](img/db4e391228134f195e9685c04f60cfcd_2.png)

![](img/db4e391228134f195e9685c04f60cfcd_4.png)

字典由一系列**键值对**组成，每个键都映射到一个值。常见的例子包括ID与姓名、商品与价格等。本节课我们将以国家和首都为例，学习如何创建、访问和操作字典。

![](img/db4e391228134f195e9685c04f60cfcd_6.png)

---

![](img/db4e391228134f195e9685c04f60cfcd_8.png)

## 创建字典 🛠️

![](img/db4e391228134f195e9685c04f60cfcd_10.png)

![](img/db4e391228134f195e9685c04f60cfcd_11.png)

![](img/db4e391228134f195e9685c04f60cfcd_13.png)

要创建一个字典，需要使用一对花括号 `{}`，并在其中定义键值对。键和值之间用冒号 `:` 分隔，不同的键值对之间用逗号 `,` 分隔。

![](img/db4e391228134f195e9685c04f60cfcd_15.png)

以下是创建一个名为 `capitals` 的国家-首都字典的代码：

![](img/db4e391228134f195e9685c04f60cfcd_16.png)

![](img/db4e391228134f195e9685c04f60cfcd_18.png)

![](img/db4e391228134f195e9685c04f60cfcd_20.png)

```python
capitals = {
    "USA": "Washington, D.C.",
    "India": "New Delhi",
    "China": "Beijing",
    "Russia": "Moscow"
}
```

![](img/db4e391228134f195e9685c04f60cfcd_22.png)

## 查看字典的属性和方法 🔍

![](img/db4e391228134f195e9685c04f60cfcd_24.png)

![](img/db4e391228134f195e9685c04f60cfcd_26.png)

在深入学习之前，我们可以使用 `dir()` 函数来查看字典对象的所有属性和方法。

![](img/db4e391228134f195e9685c04f60cfcd_28.png)

```python
print(dir(capitals))
```

![](img/db4e391228134f195e9685c04f60cfcd_30.png)

如果想获得更详细的说明，可以使用 `help()` 函数。

![](img/db4e391228134f195e9685c04f60cfcd_32.png)

```python
help(capitals)
```

---

![](img/db4e391228134f195e9685c04f60cfcd_34.png)

## 访问字典的值 🔑

![](img/db4e391228134f195e9685c04f60cfcd_36.png)

![](img/db4e391228134f195e9685c04f60cfcd_37.png)

上一节我们介绍了如何创建字典，本节中我们来看看如何访问其中的值。要获取字典中某个键对应的值，可以使用 `get()` 方法。

![](img/db4e391228134f195e9685c04f60cfcd_39.png)

![](img/db4e391228134f195e9685c04f60cfcd_40.png)

```python
print(capitals.get("USA"))  # 输出: Washington, D.C.
print(capitals.get("India")) # 输出: New Delhi
```

![](img/db4e391228134f195e9685c04f60cfcd_42.png)

![](img/db4e391228134f195e9685c04f60cfcd_44.png)

如果查找的键不存在，`get()` 方法会返回 `None`。我们可以利用这一点进行条件判断。

![](img/db4e391228134f195e9685c04f60cfcd_46.png)

```python
if capitals.get("Japan"):
    print("That capital exists.")
else:
    print("That capital doesn‘t exist.")
```

![](img/db4e391228134f195e9685c04f60cfcd_48.png)

![](img/db4e391228134f195e9685c04f60cfcd_50.png)

---

![](img/db4e391228134f195e9685c04f60cfcd_52.png)

![](img/db4e391228134f195e9685c04f60cfcd_54.png)

## 更新字典 ✏️

![](img/db4e391228134f195e9685c04f60cfcd_56.png)

字典是可变的，这意味着我们可以添加新的键值对，或者修改已有的值。

![](img/db4e391228134f195e9685c04f60cfcd_58.png)

![](img/db4e391228134f195e9685c04f60cfcd_60.png)

以下是更新字典的几种方法：

*   **添加或更新键值对**：使用 `update()` 方法。
    ```python
    capitals.update({"Germany": "Berlin"})
    capitals.update({"USA": "Detroit"})  # 更新已有键的值
    print(capitals)
    ```

![](img/db4e391228134f195e9685c04f60cfcd_62.png)

![](img/db4e391228134f195e9685c04f60cfcd_63.png)

![](img/db4e391228134f195e9685c04f60cfcd_64.png)

*   **删除键值对**：
    *   使用 `pop()` 方法删除指定键。
        ```python
        capitals.pop("China")
        ```
    *   使用 `popitem()` 方法删除最后插入的键值对。
        ```python
        capitals.popitem()
        ```

![](img/db4e391228134f195e9685c04f60cfcd_66.png)

*   **清空字典**：使用 `clear()` 方法。
    ```python
    capitals.clear()
    ```

![](img/db4e391228134f195e9685c04f60cfcd_68.png)

![](img/db4e391228134f195e9685c04f60cfcd_69.png)

![](img/db4e391228134f195e9685c04f60cfcd_70.png)

---

![](img/db4e391228134f195e9685c04f60cfcd_72.png)

## 遍历字典 🔄

![](img/db4e391228134f195e9685c04f60cfcd_74.png)

字典提供了几种方法来获取其内容的视图，便于我们进行遍历操作。

![](img/db4e391228134f195e9685c04f60cfcd_76.png)

![](img/db4e391228134f195e9685c04f60cfcd_77.png)

![](img/db4e391228134f195e9685c04f60cfcd_79.png)

![](img/db4e391228134f195e9685c04f60cfcd_81.png)

以下是遍历字典的几种方式：

*   **获取所有键**：使用 `keys()` 方法。
    ```python
    for key in capitals.keys():
        print(key)
    ```

![](img/db4e391228134f195e9685c04f60cfcd_83.png)

*   **获取所有值**：使用 `values()` 方法。
    ```python
    for value in capitals.values():
        print(value)
    ```

![](img/db4e391228134f195e9685c04f60cfcd_85.png)

![](img/db4e391228134f195e9685c04f60cfcd_86.png)

![](img/db4e391228134f195e9685c04f60cfcd_88.png)

*   **同时获取键和值**：使用 `items()` 方法。这是最常用的遍历方式，它会返回一个包含键值对元组的可迭代对象。
    ```python
    for key, value in capitals.items():
        print(f"{key}: {value}")
    ```

![](img/db4e391228134f195e9685c04f60cfcd_90.png)

---

## 总结

本节课中我们一起学习了Python字典的核心知识。我们了解到字典是一种由**键值对**组成的、**有序**且**可变**的集合，其**键不允许重复**。

![](img/db4e391228134f195e9685c04f60cfcd_92.png)

![](img/db4e391228134f195e9685c04f60cfcd_93.png)

我们掌握了以下关键操作：
1.  使用花括号 `{}` 创建字典。
2.  使用 `get()` 方法安全地访问值。
3.  使用 `update()`、`pop()`、`popitem()` 和 `clear()` 方法来修改字典。
4.  使用 `keys()`、`values()` 和 `items()` 方法来遍历字典的不同部分。

![](img/db4e391228134f195e9685c04f60cfcd_94.png)

![](img/db4e391228134f195e9685c04f60cfcd_95.png)

![](img/db4e391228134f195e9685c04f60cfcd_96.png)

![](img/db4e391228134f195e9685c04f60cfcd_97.png)

字典在未来的编程项目中会非常有用，例如在游戏开发中存储角色属性或游戏状态。希望本教程能帮助你打下坚实的基础！