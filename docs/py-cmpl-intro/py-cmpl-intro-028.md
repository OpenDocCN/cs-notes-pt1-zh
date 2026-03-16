# 028：编写一个数字猜谜游戏 🎮

在本节课中，我们将使用Python创建一个数字猜谜游戏。这是一个面向初学者的项目，通过完成它，可以帮助我们巩固对之前所学主题的理解。

![](img/c4b89b28761031c14dd4b1973d1bd265_1.png)

## 概述与准备

我们将从导入必要的模块和定义游戏变量开始。

首先，我们需要导入 `random` 模块，因为它将帮助我们生成一个随机数字。

```python
import random
```

接下来，定义游戏数字的范围。我们将使用两个变量来存储最小值和最大值。

```python
lowest_number = 1
highest_number = 100
```

你可以根据需要自由选择不同的范围。我们将范围设置为1到100。一个随机数将在这个范围内被选中，并存储在名为 `answer` 的变量中。

```python
answer = random.randint(lowest_number, highest_number)
```

为了测试，我们可以先打印出答案。

```python
print(answer)
```

现在，我们需要一些额外的变量来跟踪游戏状态。`guesses` 变量用于记录猜测次数，`is_running` 是一个布尔变量，用于控制游戏主循环。

![](img/c4b89b28761031c14dd4b1973d1bd265_3.png)

```python
guesses = 0
is_running = True
```

![](img/c4b89b28761031c14dd4b1973d1bd265_5.png)

## 游戏主循环与用户交互

![](img/c4b89b28761031c14dd4b1973d1bd265_7.png)

上一节我们设置了游戏的基础变量，本节中我们来看看如何构建游戏的主循环并与用户交互。

![](img/c4b89b28761031c14dd4b1973d1bd265_9.png)

首先，打印一个欢迎信息。

```python
print(f"Python数字猜谜游戏！\n请选择一个介于 {lowest_number} 和 {highest_number} 之间的数字。")
```

现在，我们需要一个 `while` 循环来持续运行游戏的每一轮。

![](img/c4b89b28761031c14dd4b1973d1bd265_11.png)

```python
while is_running:
```

在循环内部，我们将提示用户输入他们的猜测。

![](img/c4b89b28761031c14dd4b1973d1bd265_13.png)

```python
    guess = input("请输入你的猜测：")
```

## 输入验证

获取用户输入后，我们需要验证其有效性。以下是需要检查的情况：

![](img/c4b89b28761031c14dd4b1973d1bd265_15.png)

*   **输入是否为数字**：用户可能输入非数字内容，如“pizza”。
*   **数字是否在有效范围内**：用户可能输入一个超出定义范围的数字。

![](img/c4b89b28761031c14dd4b1973d1bd265_17.png)

![](img/c4b89b28761031c14dd4b1973d1bd265_19.png)

首先，检查输入是否为数字。

```python
    if guess.isdigit():
        # 暂时用pass占位，稍后填充逻辑
        pass
    else:
        print("无效猜测。")
        print(f"请选择一个介于 {lowest_number} 和 {highest_number} 之间的数字。")
```

![](img/c4b89b28761031c14dd4b1973d1bd265_21.png)

如果输入是数字，我们需要将其转换为整数类型，并增加猜测次数计数器。

![](img/c4b89b28761031c14dd4b1973d1bd265_23.png)

```python
    if guess.isdigit():
        guess = int(guess)
        guesses += 1
```

然后，检查转换后的数字是否在有效范围内。

![](img/c4b89b28761031c14dd4b1973d1bd265_25.png)

![](img/c4b89b28761031c14dd4b1973d1bd265_26.png)

```python
        if guess < lowest_number or guess > highest_number:
            print("该数字超出范围。")
            print(f"请选择一个介于 {lowest_number} 和 {highest_number} 之间的数字。")
```

![](img/c4b89b28761031c14dd4b1973d1bd265_28.png)

## 游戏逻辑判断

![](img/c4b89b28761031c14dd4b1973d1bd265_30.png)

处理完输入验证后，接下来是实现游戏的核心逻辑：判断猜测的数字与正确答案的关系。

![](img/c4b89b28761031c14dd4b1973d1bd265_32.png)

如果猜测的数字小于正确答案，我们提示“太低”。

![](img/c4b89b28761031c14dd4b1973d1bd265_34.png)

```python
        elif guess < answer:
            print("太低了，再试一次。")
```

![](img/c4b89b28761031c14dd4b1973d1bd265_36.png)

![](img/c4b89b28761031c14dd4b1973d1bd265_38.png)

如果猜测的数字大于正确答案，我们提示“太高”。

![](img/c4b89b28761031c14dd4b1973d1bd265_40.png)

```python
        elif guess > answer:
            print("太高了，再试一次。")
```

![](img/c4b89b28761031c14dd4b1973d1bd265_42.png)

如果猜测的数字既不小于也不大于正确答案，那么它就是正确答案。

```python
        else:
            print(f"正确！答案是 {answer}。")
            print(f"猜测次数：{guesses}。")
```

![](img/c4b89b28761031c14dd4b1973d1bd265_44.png)

当用户猜中答案后，我们需要将 `is_running` 变量设置为 `False` 以退出 `while` 循环，结束游戏。

![](img/c4b89b28761031c14dd4b1973d1bd265_46.png)

![](img/c4b89b28761031c14dd4b1973d1bd265_48.png)

```python
            is_running = False
```

## 完整代码与总结

将以上所有部分组合起来，就得到了完整的数字猜谜游戏代码。

![](img/c4b89b28761031c14dd4b1973d1bd265_50.png)

![](img/c4b89b28761031c14dd4b1973d1bd265_51.png)

```python
import random

![](img/c4b89b28761031c14dd4b1973d1bd265_53.png)

lowest_number = 1
highest_number = 100
answer = random.randint(lowest_number, highest_number)

![](img/c4b89b28761031c14dd4b1973d1bd265_55.png)

guesses = 0
is_running = True

print(f"Python数字猜谜游戏！\n请选择一个介于 {lowest_number} 和 {highest_number} 之间的数字。")

![](img/c4b89b28761031c14dd4b1973d1bd265_57.png)

while is_running:
    guess = input("请输入你的猜测：")

    if guess.isdigit():
        guess = int(guess)
        guesses += 1

        if guess < lowest_number or guess > highest_number:
            print("该数字超出范围。")
            print(f"请选择一个介于 {lowest_number} 和 {highest_number} 之间的数字。")
        elif guess < answer:
            print("太低了，再试一次。")
        elif guess > answer:
            print("太高了，再试一次。")
        else:
            print(f"正确！答案是 {answer}。")
            print(f"猜测次数：{guesses}。")
            is_running = False
    else:
        print("无效猜测。")
        print(f"请选择一个介于 {lowest_number} 和 {highest_number} 之间的数字。")
```

![](img/c4b89b28761031c14dd4b1973d1bd265_59.png)

本节课中我们一起学习了如何构建一个完整的Python数字猜谜游戏。我们实践了模块导入、变量定义、循环控制、条件判断和用户输入处理等多个基础概念。你可以将此作为一个小型项目，通过修改数字范围或添加更多功能（如难度选择、猜测历史记录等）来进一步练习和巩固你的Python技能。