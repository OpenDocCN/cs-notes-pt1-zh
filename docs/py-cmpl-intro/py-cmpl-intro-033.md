# 033：默认参数详解 🎯

在本节课中，我们将要学习Python函数中一个非常实用的特性——默认参数。默认参数可以为函数参数提供预设值，从而简化函数调用，使代码更加灵活和简洁。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_1.png)

上一节我们介绍了位置参数，本节中我们来看看如何为参数设置默认值。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_2.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_3.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_4.png)

## 什么是默认参数？ 🤔

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_6.png)

默认参数是为函数参数指定的一个默认值。当调用函数时，如果省略了该参数，则会自动使用这个默认值。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_8.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_10.png)

## 一个计算净价的例子 💰

让我们通过一个计算商品净价的函数来理解默认参数。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_12.png)

首先，我们定义一个函数 `net_price`，它接收三个参数：商品原价 `list_price`、折扣 `discount` 和销售税 `tax`。其计算公式为：

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_13.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_14.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_15.png)

**净价 = 原价 × (1 - 折扣) × (1 + 税率)**

用Python代码表示如下：

```python
def net_price(list_price, discount, tax):
    return list_price * (1 - discount) * (1 + tax)
```

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_17.png)

假设我购买一台标价500美元的PlayStation 5，没有折扣，税率为5%（0.05），调用方式如下：

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_18.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_19.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_20.png)

```python
print(net_price(500, 0, 0.05))
```
输出结果为 `525.0`。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_21.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_23.png)

## 引入默认参数 ✨

现在，假设在大多数情况下（比如90%），折扣都为0，税率也固定为5%。每次都传入这两个相同的值显得冗余。这时，我们可以使用默认参数来优化函数。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_25.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_26.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_27.png)

以下是优化后的函数定义，为 `discount` 和 `tax` 参数设置了默认值：

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_28.png)

```python
def net_price(list_price, discount=0, tax=0.05):
    return list_price * (1 - discount) * (1 + tax)
```

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_30.png)

现在，在通常没有折扣和固定税率的情况下，我们只需要传入原价即可：

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_32.png)

```python
print(net_price(500))  # 输出 525.0
```

## 默认参数的灵活性 🔧

使用默认参数的好处在于，当情况有变时，我们仍然可以传入新的值来覆盖默认值。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_34.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_35.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_36.png)

例如，如果顾客有10%的折扣券：

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_37.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_38.png)

```python
print(net_price(500, 0.1))  # 输出 472.5
```

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_40.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_42.png)

如果该商品免税：

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_44.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_45.png)

```python
print(net_price(500, 0.1, 0))  # 输出 450.0
```

这使得函数既能处理常见情况，又能灵活应对特例。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_47.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_48.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_50.png)

## 实践练习：创建一个倒计时器 ⏱️

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_52.png)

接下来，我们通过创建一个倒计时函数来巩固对默认参数的理解。

我们将使用 `time` 模块。这个函数将从 `start` 数到 `end`，每秒打印一个数字。

```python
import time

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_54.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_55.png)

def count(end, start=0):
    for x in range(start, end + 1):
        print(x)
        time.sleep(1)
    print("Done!")
```

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_57.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_59.png)

**注意**：在定义函数时，**所有默认参数必须放在非默认参数（位置参数）的后面**。这是Python的语法规则。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_61.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_62.png)

现在调用函数：
- 大多数时候我们从0开始计数，所以只需传入结束值：

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_64.png)

```python
count(10)  # 从0数到10
```

- 如果想从15开始数到30：

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_66.png)

```python
count(30, 15)  # 从15数到30
```

## 总结 📝

本节课中我们一起学习了Python的默认参数。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_68.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_69.png)

- **定义**：默认参数是为函数参数预设的值，在调用时若省略该参数则自动使用。
- **作用**：它们能使函数调用更简洁，减少需要传递的参数数量，尤其是在某些参数值在大多数情况下都保持一致时。
- **语法规则**：在函数定义中，默认参数必须位于所有非默认参数（位置参数）之后。

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_70.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_71.png)

![](img/4c1bb0f301ca3e307a1ee0fc96b39fd0_72.png)

默认参数是编写清晰、灵活且易于使用的函数的重要工具。下一节，我们将探讨另一种参数类型——关键字参数。