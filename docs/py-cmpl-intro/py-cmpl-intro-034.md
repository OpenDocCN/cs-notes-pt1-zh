Python超全入门教程：P34：关键字参数详解 🧠

在本节课中，我们将要学习Python中的关键字参数。关键字参数是一种在调用函数时，通过指定参数名来传递参数的方式。我们将了解它的定义、优势、使用方法，并通过具体示例来加深理解。

![](img/a333a25cc3dca4431fecef82dea2556e_1.png)

---

![](img/a333a25cc3dca4431fecef82dea2556e_2.png)

![](img/a333a25cc3dca4431fecef82dea2556e_3.png)

![](img/a333a25cc3dca4431fecef82dea2556e_4.png)

### 什么是关键字参数？🔍

![](img/a333a25cc3dca4431fecef82dea2556e_6.png)

![](img/a333a25cc3dca4431fecef82dea2556e_8.png)

上一节我们介绍了函数参数的基本概念。本节中，我们来看看一种特殊的参数传递方式——关键字参数。

![](img/a333a25cc3dca4431fecef82dea2556e_10.png)

![](img/a333a25cc3dca4431fecef82dea2556e_12.png)

一个**关键字参数**是一个前面带有标识符（即参数名）的参数。它的核心语法是 `参数名=值`。

![](img/a333a25cc3dca4431fecef82dea2556e_14.png)

### 关键字参数的优势 ✨

![](img/a333a25cc3dca4431fecef82dea2556e_16.png)

以下是使用关键字参数的两个主要好处：

![](img/a333a25cc3dca4431fecef82dea2556e_18.png)

1.  **提高代码可读性**：通过参数名，可以清晰地知道每个值对应的含义。
2.  **参数顺序无关紧要**：只要使用了关键字参数，传递参数的顺序可以与函数定义时参数的顺序不同。

![](img/a333a25cc3dca4431fecef82dea2556e_20.png)

### 从位置参数到关键字参数 🔄

![](img/a333a25cc3dca4431fecef82dea2556e_22.png)

![](img/a333a25cc3dca4431fecef82dea2556e_23.png)

假设我们有一个用于显示问候信息的函数 `hello`。

![](img/a333a25cc3dca4431fecef82dea2556e_25.png)

```python
def hello(greeting, title, first, last):
    print(f"{greeting} {title}. {first} {last}")
```

![](img/a333a25cc3dca4431fecef82dea2556e_27.png)

目前，我们使用**位置参数**调用它：

![](img/a333a25cc3dca4431fecef82dea2556e_29.png)

![](img/a333a25cc3dca4431fecef82dea2556e_30.png)

```python
hello("Hello", "Mr.", "Spongebob", "Squarepants")
# 输出：Hello Mr. Spongebob Squarepants
```

![](img/a333a25cc3dca4431fecef82dea2556e_31.png)

![](img/a333a25cc3dca4431fecef82dea2556e_32.png)

位置参数的顺序至关重要。如果调换参数顺序，输出结果就会混乱。

![](img/a333a25cc3dca4431fecef82dea2556e_34.png)

![](img/a333a25cc3dca4431fecef82dea2556e_35.png)

现在，我们可以将其转换为**关键字参数**调用：

```python
hello(greeting="Hello", title="Mr.", first="Spongebob", last="Squarepants")
```

![](img/a333a25cc3dca4431fecef82dea2556e_37.png)

![](img/a333a25cc3dca4431fecef82dea2556e_38.png)

![](img/a333a25cc3dca4431fecef82dea2556e_39.png)

使用关键字参数后，参数的顺序就不再重要了。例如，以下调用方式依然正确：

![](img/a333a25cc3dca4431fecef82dea2556e_41.png)

```python
hello(title="Mr.", last="Squarepants", first="Spongebob", greeting="Hello")
```

![](img/a333a25cc3dca4431fecef82dea2556e_43.png)

![](img/a333a25cc3dca4431fecef82dea2556e_44.png)

### 混合使用位置参数与关键字参数 ⚠️

![](img/a333a25cc3dca4431fecef82dea2556e_46.png)

在混合使用时，必须确保**所有位置参数在所有关键字参数之前**。

![](img/a333a25cc3dca4431fecef82dea2556e_48.png)

![](img/a333a25cc3dca4431fecef82dea2556e_49.png)

*   **正确示例**：`hello("Hello", title="Mr.", first="Spongebob", last="Squarepants")`
*   **错误示例**：`hello(title="Mr.", "Hello", first="Spongebob", last="Squarepants")` （会导致语法错误）

![](img/a333a25cc3dca4431fecef82dea2556e_51.png)

### 关键字参数的实际应用示例 💡

![](img/a333a25cc3dca4431fecef82dea2556e_52.png)

![](img/a333a25cc3dca4431fecef82dea2556e_54.png)

#### 示例1：澄清参数含义

![](img/a333a25cc3dca4431fecef82dea2556e_56.png)

当参数值本身含义模糊时，关键字参数能有效避免混淆。

![](img/a333a25cc3dca4431fecef82dea2556e_58.png)

![](img/a333a25cc3dca4431fecef82dea2556e_60.png)

```python
# 不使用关键字参数，容易混淆哪个是名，哪个是姓
hello("Hello", "Mr.", "John", "James")

# 使用关键字参数，含义一目了然
hello(greeting="Hello", title="Mr.", first="James", last="John")
```

![](img/a333a25cc3dca4431fecef82dea2556e_62.png)

![](img/a333a25cc3dca4431fecef82dea2556e_63.png)

#### 示例2：使用内置函数的关键字参数

Python 许多内置函数都提供了有用的关键字参数。例如 `print()` 函数：

![](img/a333a25cc3dca4431fecef82dea2556e_65.png)

![](img/a333a25cc3dca4431fecef82dea2556e_66.png)

![](img/a333a25cc3dca4431fecef82dea2556e_67.png)

*   **`end` 参数**：指定打印结束后的字符，默认为换行符 `\n`。

    ```python
    for x in range(1, 11):
        print(x, end=" ")  # 用空格代替换行
    # 输出：1 2 3 4 5 6 7 8 9 10
    ```

![](img/a333a25cc3dca4431fecef82dea2556e_69.png)

![](img/a333a25cc3dca4431fecef82dea2556e_71.png)

*   **`sep` 参数**：指定多个打印项之间的分隔符，默认为空格。

    ```python
    print("1", "2", "3", "4", "5", sep="-")
    # 输出：1-2-3-4-5
    ```

![](img/a333a25cc3dca4431fecef82dea2556e_73.png)

### 综合练习：生成电话号码 📞

![](img/a333a25cc3dca4431fecef82dea2556e_75.png)

![](img/a333a25cc3dca4431fecef82dea2556e_77.png)

让我们创建一个生成电话号码格式字符串的函数，并练习使用关键字参数。

![](img/a333a25cc3dca4431fecef82dea2556e_79.png)

```python
def get_phone(country_code, area_code, first_digits, last_digits):
    return f"{country_code}-{area_code}-{first_digits}-{last_digits}"

![](img/a333a25cc3dca4431fecef82dea2556e_81.png)

![](img/a333a25cc3dca4431fecef82dea2556e_83.png)

# 使用关键字参数调用，顺序可以任意
phone_number = get_phone(country_code="1", area_code="123", first_digits="456", last_digits="7890")
print(phone_number)  # 输出：1-123-456-7890

![](img/a333a25cc3dca4431fecef82dea2556e_85.png)

# 也可以保持与定义一致的顺序，但使用关键字形式
phone_number = get_phone(country_code="1", area_code="123", first_digits="456", last_digits="7890")
```

![](img/a333a25cc3dca4431fecef82dea2556e_86.png)

![](img/a333a25cc3dca4431fecef82dea2556e_88.png)

---

### 总结 📝

本节课中我们一起学习了Python的关键字参数。

![](img/a333a25cc3dca4431fecef82dea2556e_90.png)

![](img/a333a25cc3dca4431fecef82dea2556e_91.png)

*   **定义**：关键字参数是通过 `参数名=值` 形式传递的参数。
*   **优势**：**提高代码可读性**，并且**调用时参数的顺序可以任意**。
*   **规则**：当混合使用位置参数和关键字参数时，所有位置参数必须位于关键字参数之前。
*   **应用**：广泛用于内置函数（如 `print()` 的 `end` 和 `sep`）和自定义函数中，使代码意图更清晰。

![](img/a333a25cc3dca4431fecef82dea2556e_92.png)

![](img/a333a25cc3dca4431fecef82dea2556e_93.png)

![](img/a333a25cc3dca4431fecef82dea2556e_94.png)

![](img/a333a25cc3dca4431fecef82dea2556e_95.png)

关键字参数是编写清晰、易维护Python代码的重要工具之一。