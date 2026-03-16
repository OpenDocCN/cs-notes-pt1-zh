# 057：嵌套类 🏗️

在本节课中，我们将要学习Python中的嵌套类。嵌套类是指定义在另一个类内部的类。我们将探讨它的用途、好处，并通过一个完整的示例来演示如何创建和使用嵌套类。

---

## 嵌套类的概念与好处

![](img/9e9b040187b388b4c9a73869056ddbef_1.png)

嵌套类是一个定义在另一个类内部的类。使用嵌套类主要有以下几个好处：

![](img/9e9b040187b388b4c9a73869056ddbef_3.png)

*   **逻辑分组**：可以将紧密相关的类组织在一起。
*   **信息封装**：可以封装那些对外部类不重要的私有细节。
*   **保持命名空间整洁**：有助于减少命名冲突的可能性。

例如，如果直接定义两个同名的类，会产生命名冲突。

```python
class Employee:
    print("这是第一个类")

![](img/9e9b040187b388b4c9a73869056ddbef_5.png)

![](img/9e9b040187b388b4c9a73869056ddbef_6.png)

![](img/9e9b040187b388b4c9a73869056ddbef_8.png)

class Employee:
    print("这是第二个类")
```

![](img/9e9b040187b388b4c9a73869056ddbef_10.png)

运行上述代码，两个类都会被执行，这清楚地表明了存在命名冲突。在大型项目中，频繁导入导出文件时，可能不易察觉此类冲突。嵌套类可以帮助我们避免这个问题。

---

## 嵌套类示例：公司与员工

上一节我们介绍了嵌套类的基本概念，本节中我们来看看如何在实际场景中应用它。

![](img/9e9b040187b388b4c9a73869056ddbef_12.png)

假设我们有两个组织：一个营利性公司和一个非营利组织。它们都有员工，但员工的属性可能不同。我们可以为每个组织创建一个外部类，并在其中定义同名的内部`Employee`类。

![](img/9e9b040187b388b4c9a73869056ddbef_13.png)

![](img/9e9b040187b388b4c9a73869056ddbef_14.png)

```python
class Company:
    class Employee:
        pass

![](img/9e9b040187b388b4c9a73869056ddbef_16.png)

![](img/9e9b040187b388b4c9a73869056ddbef_17.png)

class Nonprofit:
    class Employee:
        pass
```

这样，两个`Employee`类虽然同名，但由于处于不同的作用域（分别属于`Company`和`Nonprofit`），因此不会产生冲突，实现了命名空间的整洁和类的复用。

![](img/9e9b040187b388b4c9a73869056ddbef_19.png)

接下来，我们将构建一个更完整的例子，创建属于公司对象的员工对象。

![](img/9e9b040187b388b4c9a73869056ddbef_21.png)

---

![](img/9e9b040187b388b4c9a73869056ddbef_23.png)

## 构建公司类（外部类）

首先，我们定义外部类`Company`。它有一个构造函数，用于接收公司名称并初始化一个用于存储员工的空列表。

![](img/9e9b040187b388b4c9a73869056ddbef_25.png)

```python
class Company:
    def __init__(self, company_name):
        self.company_name = company_name
        self.employees = []  # 用于存储员工对象的列表
```

![](img/9e9b040187b388b4c9a73869056ddbef_27.png)

我们还为`Company`类定义了两个方法框架：`add_employee`用于添加员工，`list_employees`用于列出所有员工。

![](img/9e9b040187b388b4c9a73869056ddbef_29.png)

```python
    def add_employee(self, name, position):
        pass  # 稍后实现

    def list_employees(self):
        pass  # 稍后实现
```

![](img/9e9b040187b388b4c9a73869056ddbef_31.png)

现在，我们可以测试公司名称是否设置成功。

![](img/9e9b040187b388b4c9a73869056ddbef_33.png)

```python
company1 = Company("蟹堡王")
print(company1.company_name)  # 输出：蟹堡王
```

![](img/9e9b040187b388b4c9a73869056ddbef_35.png)

---

![](img/9e9b040187b388b4c9a73869056ddbef_37.png)

## 构建员工类（内部类）并实现关联

![](img/9e9b040187b388b4c9a73869056ddbef_39.png)

上一节我们创建了公司类的基本结构，本节中我们来完善内部的员工类，并实现将员工添加到公司的方法。

![](img/9e9b040187b388b4c9a73869056ddbef_41.png)

![](img/9e9b040187b388b4c9a73869056ddbef_43.png)

首先，在`Company`类内部定义`Employee`类。它有自己的构造函数和获取详细信息的方法。

![](img/9e9b040187b388b4c9a73869056ddbef_45.png)

```python
class Company:
    # ... 之前的 __init__ 方法 ...

    class Employee:
        def __init__(self, name, position):
            self.name = name
            self.position = position

        def get_details(self):
            return f"{self.name} - {self.position}"
```

![](img/9e9b040187b388b4c9a73869056ddbef_47.png)

现在，回到`Company`类的`add_employee`方法。我们需要在这里创建`Employee`对象。要访问内部类，需要使用`self.Employee`（`self`指代当前的公司对象）。

![](img/9e9b040187b388b4c9a73869056ddbef_48.png)

![](img/9e9b040187b388b4c9a73869056ddbef_50.png)

```python
    def add_employee(self, name, position):
        # 创建新的员工对象
        new_employee = self.Employee(name, position)
        # 将员工对象添加到公司员工列表中
        self.employees.append(new_employee)
```

![](img/9e9b040187b388b4c9a73869056ddbef_52.png)

![](img/9e9b040187b388b4c9a73869056ddbef_54.png)

---

![](img/9e9b040187b388b4c9a73869056ddbef_56.png)

## 列出员工与代码演示

我们已经实现了添加员工的功能，现在来实现`list_employees`方法，并演示整个流程。

`list_employees`方法将遍历员工列表，并调用每个员工的`get_details`方法。

```python
    def list_employees(self):
        # 使用列表推导式返回所有员工的详细信息
        return [emp.get_details() for emp in self.employees]
```

![](img/9e9b040187b388b4c9a73869056ddbef_58.png)

现在，让我们使用这个结构来创建公司和员工。

![](img/9e9b040187b388b4c9a73869056ddbef_60.png)

```python
# 创建第一个公司
company1 = Company("蟹堡王")

# 为第一个公司添加员工
company1.add_employee("尤金·蟹老板", "经理")
company1.add_employee("海绵宝宝", "厨师")
company1.add_employee("章鱼哥", "收银员")

![](img/9e9b040187b388b4c9a73869056ddbef_62.png)

# 列出第一个公司的所有员工
print(f"{company1.company_name}的员工：")
for employee in company1.list_employees():
    print(employee)
```

运行上述代码，输出结果如下：
```
蟹堡王的员工：
尤金·蟹老板 - 经理
海绵宝宝 - 厨师
章鱼哥 - 收银员
```

![](img/9e9b040187b388b4c9a73869056ddbef_64.png)

![](img/9e9b040187b388b4c9a73869056ddbef_65.png)

---

![](img/9e9b040187b388b4c9a73869056ddbef_67.png)

## 演示类的可重用性

![](img/9e9b040187b388b4c9a73869056ddbef_68.png)

为了展示嵌套类带来的命名空间整洁和类的可重用性，我们可以轻松创建第二个公司及其独立的员工列表。

![](img/9e9b040187b388b4c9a73869056ddbef_70.png)

![](img/9e9b040187b388b4c9a73869056ddbef_72.png)

```python
# 创建第二个公司
company2 = Company("海之霸")

# 为第二个公司添加员工
company2.add_employee("谢尔顿·痞老板", "经理")
company2.add_employee("凯伦", "助理")

![](img/9e9b040187b388b4c9a73869056ddbef_74.png)

# 列出第二个公司的所有员工
print(f"\n{company2.company_name}的员工：")
for employee in company2.list_employees():
    print(employee)
```

![](img/9e9b040187b388b4c9a73869056ddbef_76.png)

运行后，输出结果如下：
```
海之霸的员工：
谢尔顿·痞老板 - 经理
凯伦 - 助理
```

![](img/9e9b040187b388b4c9a73869056ddbef_78.png)

可以看到，两个公司对象完全独立，它们内部的`Employee`类互不干扰，完美复用了类的结构。

---

## 总结 🎯

本节课中我们一起学习了Python中的嵌套类。

![](img/9e9b040187b388b4c9a73869056ddbef_80.png)

*   **嵌套类**是一个定义在另一个类（外部类）内部的类（内部类）。
*   它的主要**好处**包括：
    1.  对紧密相关的类进行**逻辑分组**（如将`Employee`置于`Company`内）。
    2.  **封装**外部类不需要知道的私有细节。
    3.  保持**命名空间整洁**，**减少命名冲突**（可以在不同外部类中使用同名的内部类）。
*   我们通过构建`Company`和内部`Employee`类的完整示例，演示了如何创建嵌套类、在外部类中实例化内部类对象，以及如何利用这种结构管理不同实体的数据。

![](img/9e9b040187b388b4c9a73869056ddbef_81.png)

![](img/9e9b040187b388b4c9a73869056ddbef_82.png)

![](img/9e9b040187b388b4c9a73869056ddbef_83.png)

通过使用嵌套类，你可以写出更有组织性、更模块化且不易出错的代码。