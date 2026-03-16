Python超全入门教程：P54：鸭子类型 🦆

在本节课中，我们将要学习Python中一个重要的概念——鸭子类型。这是一种实现多态性的方式，它不依赖于传统的继承机制，而是关注对象的行为。

## 概述

鸭子类型是Python实现多态性的一种方式。其核心思想是：**一个对象的类型由其行为（拥有的属性和方法）决定，而不是由其声明的类决定**。这遵循一句谚语：“如果它看起来像鸭子，走起路来像鸭子，叫起来也像鸭子，那么它就是鸭子。”

![](img/fd124d4f4fdba10a181c73ffd5d33915_1.png)

![](img/fd124d4f4fdba10a181c73ffd5d33915_2.png)

![](img/fd124d4f4fdba10a181c73ffd5d33915_3.png)

## 通过继承实现多态

![](img/fd124d4f4fdba10a181c73ffd5d33915_4.png)

在深入鸭子类型之前，我们先回顾一下如何使用继承来实现多态。我们创建几个类来演示。

首先，定义一个基础的 `Animal` 类，它有一个表示生物状态的属性 `alive`。

```python
class Animal:
    alive = True
```

接着，创建 `Dog` 类继承自 `Animal` 类，并为其添加一个 `speak` 方法。

```python
class Dog(Animal):
    def speak(self):
        print("Woof")
```

同样，创建 `Cat` 类也继承自 `Animal` 类，并定义自己的 `speak` 方法。

```python
class Cat(Animal):
    def speak(self):
        print("Meow")
```

![](img/fd124d4f4fdba10a181c73ffd5d33915_6.png)

现在，我们可以创建一个包含不同动物对象的列表，并让它们依次“说话”。由于 `Dog` 和 `Cat` 都继承自 `Animal` 并拥有 `speak` 方法，它们可以被统一处理。

![](img/fd124d4f4fdba10a181c73ffd5d33915_8.png)

```python
animals = [Dog(), Cat()]

![](img/fd124d4f4fdba10a181c73ffd5d33915_10.png)

for animal in animals:
    animal.speak()
```

![](img/fd124d4f4fdba10a181c73ffd5d33915_12.png)

执行上述代码，输出结果为：
```
Woof
Meow
```

上一节我们介绍了通过继承实现多态，本节中我们来看看如何不依赖继承，仅通过行为来实现相同的效果。

![](img/fd124d4f4fdba10a181c73ffd5d33915_14.png)

![](img/fd124d4f4fdba10a181c73ffd5d33915_16.png)

## 引入鸭子类型

现在，我们引入一个与 `Animal` 毫无继承关系的 `Car` 类。按照传统思路，它不应该被放入动物列表中。

![](img/fd124d4f4fdba10a181c73ffd5d33915_18.png)

```python
class Car:
    def horn(self):
        print("Honk")
```

![](img/fd124d4f4fdba10a181c73ffd5d33915_20.png)

如果我们尝试将 `Car` 对象加入 `animals` 列表并调用 `speak` 方法，程序会报错，因为 `Car` 类没有 `speak` 方法。

![](img/fd124d4f4fdba10a181c73ffd5d33915_22.png)

```python
animals = [Dog(), Cat(), Car()] # 这里会出错
```

错误信息是：`AttributeError: 'Car' object has no attribute 'speak'`。

![](img/fd124d4f4fdba10a181c73ffd5d33915_24.png)

这就是鸭子类型发挥作用的地方。我们不需要让 `Car` 继承 `Animal`，只需要让它“看起来”像动物——即拥有动物所需的最小必要行为。

我们修改 `Car` 类，将 `horn` 方法改名为 `speak`。

![](img/fd124d4f4fdba10a181c73ffd5d33915_26.png)

![](img/fd124d4f4fdba10a181c73ffd5d33915_27.png)

```python
class Car:
    def speak(self):  # 将方法名改为 speak
        print("Honk")
    alive = False     # 同时添加 alive 属性
```

现在，`Car` 类拥有了 `speak` 方法和 `alive` 属性。尽管它在类定义上与 `Animal` 无关，但它满足了被当作“动物”处理的最低要求。

![](img/fd124d4f4fdba10a181c73ffd5d33915_29.png)

![](img/fd124d4f4fdba10a181c73ffd5d33915_30.png)

再次运行循环：

```python
animals = [Dog(), Cat(), Car()]

![](img/fd124d4f4fdba10a181c73ffd5d33915_32.png)

for animal in animals:
    animal.speak()
    print(animal.alive)
```

![](img/fd124d4f4fdba10a181c73ffd5d33915_34.png)

输出结果为：
```
Woof
True
Meow
True
Honk
False
```

![](img/fd124d4f4fdba10a181c73ffd5d33915_36.png)

`Car` 对象成功地被当作“动物”处理了，因为它“叫起来像鸭子”（有 `speak` 方法），并且“看起来像鸭子”（有 `alive` 属性）。

![](img/fd124d4f4fdba10a181c73ffd5d33915_38.png)

## 核心概念总结

![](img/fd124d4f4fdba10a181c73ffd5d33915_40.png)

以下是鸭子类型的核心要点：

![](img/fd124d4f4fdba10a181c73ffd5d33915_41.png)

*   **关注行为，而非类型**：Python不检查对象的类型（是否继承自某个父类），只检查对象是否拥有需要的方法或属性。
*   **最小必要接口**：只要对象实现了预期的方法（如 `speak`）和属性（如 `alive`），它就可以被当作特定类型的对象使用。
*   **提高灵活性**：代码不再紧密耦合于特定的类层次结构，任何对象只要符合接口，就可以无缝接入。

![](img/fd124d4f4fdba10a181c73ffd5d33915_43.png)

其核心思想可以用一个简单的条件逻辑来描述：

![](img/fd124d4f4fdba10a181c73ffd5d33915_44.png)

```python
# 伪代码逻辑
if hasattr(object, ‘speak’) and callable(object.speak):
    # 那么可以把它当作会“说话”的对象来处理
    object.speak()
```

## 本节课总结

本节课中我们一起学习了Python的鸭子类型。

*   我们首先回顾了通过**继承**实现多态的传统方法。
*   然后，我们引入了**鸭子类型**的概念，它通过对象的行为（拥有的方法和属性）来实现多态，而不强制要求继承关系。
*   通过 `Car` 类的例子，我们演示了只要一个对象拥有所需的 `speak` 方法和 `alive` 属性，即使它不是一个真正的“动物”，也可以被相关代码处理。
*   鸭子类型体现了Python的动态性和灵活性，是“协议”或“接口”编程思想的体现，在Python标准库和众多框架中广泛应用。

![](img/fd124d4f4fdba10a181c73ffd5d33915_46.png)

![](img/fd124d4f4fdba10a181c73ffd5d33915_47.png)

记住这句格言：**“如果它走起来像鸭子，叫起来也像鸭子，那么它就可以被当作鸭子。”** 这就是鸭子类型的精髓。