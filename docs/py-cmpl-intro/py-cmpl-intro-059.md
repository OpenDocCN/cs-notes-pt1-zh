Python超全入门教程：P59：类方法详解 🧑‍🏫

在本节课中，我们将学习Python中的类方法。我们将了解类方法的定义、它与实例方法的区别，并通过一个学生管理系统的例子来演示如何使用类方法来操作类级别的数据。

---

### 类方法与实例方法的区别

上一节我们介绍了实例方法，它用于操作类的具体对象。本节中我们来看看类方法。

![](img/5bfb5bb56c14c748bea4c8745236b37a_1.png)

类方法用于执行与类本身相关的操作，而不是与类的某个特定实例相关。定义类方法时，使用装饰器 `@classmethod`，并且其第一个参数是 `cls`（代表类本身），而不是 `self`（代表实例）。

以下是两种方法的核心区别：
*   **实例方法**：第一个参数是 `self`，用于访问和修改**实例属性**。
*   **类方法**：第一个参数是 `cls`，用于访问和修改**类属性**。

![](img/5bfb5bb56c14c748bea4c8745236b37a_3.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_5.png)

---

![](img/5bfb5bb56c14c748bea4c8745236b37a_7.png)

### 创建示例类：Student

我们将创建一个 `Student` 类来演示类方法的应用。这个类将记录学生总数和所有学生的总GPA。

![](img/5bfb5bb56c14c748bea4c8745236b37a_9.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_11.png)

首先，定义类的构造方法 `__init__` 和两个类变量：
```python
class Student:
    count = 0  # 类变量，用于统计学生总数
    total_gpa = 0.0  # 类变量，用于累计所有学生的GPA总和

    def __init__(self, name, gpa):
        self.name = name  # 实例属性：学生姓名
        self.gpa = gpa    # 实例属性：学生GPA
        Student.count += 1  # 每创建一个学生，总数加1
        Student.total_gpa += gpa  # 将新学生的GPA加入总和
```
在构造方法中，每当创建一个新的 `Student` 对象时，我们都会更新类变量 `count` 和 `total_gpa`。

---

### 定义实例方法

![](img/5bfb5bb56c14c748bea4c8745236b37a_13.png)

实例方法用于获取单个学生的信息。以下是 `get_info` 方法的定义：
```python
    def get_info(self):  # 实例方法，参数为 self
        return f"学生姓名：{self.name}, GPA：{self.gpa}"
```
这个方法通过 `self` 访问当前实例的属性。

![](img/5bfb5bb56c14c748bea4c8745236b37a_15.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_17.png)

---

![](img/5bfb5bb56c14c748bea4c8745236b37a_19.png)

### 定义类方法

![](img/5bfb5bb56c14c748bea4c8745236b37a_21.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_23.png)

现在，我们来定义类方法。类方法用于操作类级别的数据。

以下是获取学生总数的类方法 `get_count`：
```python
    @classmethod  # 类方法装饰器
    def get_count(cls):  # 类方法，第一个参数是 cls
        return f"学生总人数：{cls.count}"
```
要调用类方法，我们使用类名而不是实例名：`Student.get_count()`。

![](img/5bfb5bb56c14c748bea4c8745236b37a_25.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_27.png)

以下是计算平均GPA的类方法 `get_average_gpa`：
```python
    @classmethod
    def get_average_gpa(cls):
        if cls.count == 0:  # 避免除零错误
            return 0
        average = cls.total_gpa / cls.count  # 计算公式：总和 / 人数
        return f"平均GPA：{average:.2f}"  # 格式化为两位小数
```
这个方法使用类变量 `total_gpa` 和 `count` 来计算所有学生的平均GPA。

![](img/5bfb5bb56c14c748bea4c8745236b37a_29.png)

---

### 测试代码

![](img/5bfb5bb56c14c748bea4c8745236b37a_31.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_33.png)

让我们创建几个学生对象并测试我们的方法：
```python
# 调用类方法，此时还没有学生
print(Student.get_count())

![](img/5bfb5bb56c14c748bea4c8745236b37a_35.png)

# 创建三个学生对象
student1 = Student("海绵宝宝", 3.2)
student2 = Student("派大星", 2.0)
student3 = Student("珊迪", 4.0)

![](img/5bfb5bb56c14c748bea4c8745236b37a_37.png)

# 再次调用类方法查看总数
print(Student.get_count())

![](img/5bfb5bb56c14c748bea4c8745236b37a_39.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_41.png)

# 调用实例方法查看某个学生的信息
print(student1.get_info())

![](img/5bfb5bb56c14c748bea4c8745236b37a_43.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_45.png)

# 调用类方法查看平均GPA
print(Student.get_average_gpa())
```
运行上述代码，输出结果将依次显示学生总数从0变为3，并显示第一个学生的信息以及所有学生的平均GPA。

![](img/5bfb5bb56c14c748bea4c8745236b37a_47.png)

---

### 方法使用场景总结

![](img/5bfb5bb56c14c748bea4c8745236b37a_49.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_50.png)

本节课中我们一起学习了Python中三种主要方法的使用场景：

![](img/5bfb5bb56c14c748bea4c8745236b37a_52.png)

1.  **实例方法**：最适合对**类的实例（对象）** 进行操作。它们可以访问和修改对象的属性。
2.  **静态方法**（本课未详述）：最适合作为**通用工具函数**，它们不需要访问类或实例的任何数据。
3.  **类方法**：最适合处理**类级别的数据**。当你需要访问或修改类变量，或者操作与类本身相关而不是与特定实例相关的逻辑时，就应该使用类方法。其标志是使用 `@classmethod` 装饰器和 `cls` 参数。

![](img/5bfb5bb56c14c748bea4c8745236b37a_53.png)

![](img/5bfb5bb56c14c748bea4c8745236b37a_55.png)

简而言之，记住这个核心区别：操作对象用 `self`（实例方法），操作类本身用 `cls`（类方法）。