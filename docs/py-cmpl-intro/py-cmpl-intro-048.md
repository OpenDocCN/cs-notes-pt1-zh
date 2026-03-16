Python超全入门教程：P48：Python类变量详解 🧩

在本节课中，我们将要学习Python中的类变量。类变量是一种在类的所有实例之间共享数据的变量。我们将通过创建一个学生类来演示类变量的定义、访问和使用，并与实例变量进行对比。

---

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_1.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_2.png)

### 类变量与实例变量

上一节我们介绍了类的基本概念，本节中我们来看看类变量和实例变量的区别。

类变量在类的所有实例之间共享。这意味着从一个类创建的所有对象都访问同一个类变量。相反，实例变量是每个对象独有的，定义在构造函数 `__init__` 内部。

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_4.png)

以下是核心概念的定义：
*   **类变量**：定义在类内部但在任何方法（包括构造函数）之外。所有实例共享。
*   **实例变量**：定义在 `__init__` 方法内部，使用 `self` 关键字。每个实例拥有自己的副本。

---

### 创建学生类与实例变量

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_6.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_7.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_8.png)

首先，我们创建一个 `Student` 类，并为其定义构造函数来初始化实例变量 `name` 和 `age`。

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_10.png)

```python
class Student:
    # 类变量将在下一步定义
    def __init__(self, name, age):
        self.name = name  # 实例变量
        self.age = age    # 实例变量
```

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_12.png)

构造函数 `__init__` 在创建对象时自动调用。参数 `self` 代表当前正在创建的对象。我们创建两个学生对象：

```python
student1 = Student("Sponongebob", 30)
student2 = Student("Patrick", 35)
```

现在，我们可以打印每个学生的实例变量：

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_14.png)

```python
print(student1.name, student1.age)  # 输出: Sponongebob 30
print(student2.name, student2.age)  # 输出: Patrick 35
```

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_16.png)

---

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_18.png)

### 定义与访问类变量

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_20.png)

现在，我们来添加一个类变量。类变量定义在类内部，但在所有方法之外。

我们在 `Student` 类中添加一个表示毕业年份的类变量 `class_year`：

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_22.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_23.png)

```python
class Student:
    class_year = 2024  # 类变量

    def __init__(self, name, age):
        self.name = name
        self.age = age
```

类变量可以被任何实例访问，也可以通过类名直接访问。以下是访问方式：

```python
# 通过实例访问（可行，但不推荐）
print(student1.class_year)  # 输出: 2024
print(student2.class_year)  # 输出: 2024

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_25.png)

# 通过类名访问（推荐做法）
print(Student.class_year)   # 输出: 2024
```

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_26.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_27.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_28.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_29.png)

**最佳实践是使用类名（如 `Student.class_year`）来访问类变量**。这样做代码意图更清晰，能明确区分类变量和实例变量。

---

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_31.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_32.png)

### 使用类变量跟踪对象数量

类变量的一个常见用途是跟踪从类创建了多少个对象。我们添加一个类变量 `num_students`，并在每次创建新学生时在构造函数中将其递增。

以下是修改后的类定义：

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_34.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_35.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_36.png)

```python
class Student:
    class_year = 2024
    num_students = 0  # 类变量，用于计数

    def __init__(self, name, age):
        self.name = name
        self.age = age
        Student.num_students += 1  # 通过类名修改类变量
```

注意，在构造函数中，我们使用 `Student.num_students` 而不是 `self.num_students` 来确保修改的是共享的类变量，而不是创建新的实例变量。

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_38.png)

现在，每创建一个学生，计数器就会增加。我们创建几个学生并打印总数：

```python
student1 = Student("Sponongebob", 30)
student2 = Student("Patrick", 35)
student3 = Student("Squiidward", 55)
student4 = Student("Sandy", 27)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_40.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_41.png)

print(f"学生总数为: {Student.num_students}")  # 输出: 学生总数为: 4
```

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_42.png)

---

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_44.png)

### 综合示例与输出

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_46.png)

让我们将所有内容结合起来，使用一个格式化的字符串打印毕业年份和学生名单。

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_48.png)

```python
# 使用f-string格式化输出
print(f"我的毕业班级是 {Student.class_year} 年，共有 {Student.num_students} 名学生。")
print(f"学生名单: {student1.name}, {student2.name}, {student3.name}, {student4.name}")
```

输出结果将是：
```
我的毕业班级是 2024 年，共有 4 名学生。
学生名单: Sponongebob, Patrick, Squiidward, Sandy
```

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_50.png)

你可以轻松修改类变量 `class_year`，所有相关的输出都会自动更新。

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_51.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_53.png)

---

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_55.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_57.png)

### 总结

本节课中我们一起学习了Python中的类变量。

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_59.png)

*   **类变量**定义在类内部、方法外部，使用格式为 `变量名 = 值`。
*   类变量在类的**所有实例之间共享**，这与每个对象独有的实例变量不同。
*   访问类变量的**推荐方式是通过类名**（例如 `ClassName.variable_name`），这使代码更清晰、更易读。
*   类变量非常适合用于存储**所有对象共有的数据**（如标准、设置）或**跟踪类的状态**（如创建的对象数量）。

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_60.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_61.png)

![](img/3755ff6b38dda80c5dd364d2e78c0bf6_62.png)

通过理解和运用类变量，你可以编写出更高效、组织性更好的面向对象程序。