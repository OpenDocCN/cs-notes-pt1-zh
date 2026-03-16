# 045：使用Python编写一个老虎机游戏 🎰

![](img/1247bd61a278dcd91103893c45c78f49_1.png)

![](img/1247bd61a278dcd91103893c45c78f49_2.png)

在本节课中，我们将学习如何使用Python创建一个适合初学者的老虎机游戏。我们将把项目分解为几个核心功能模块，并逐一实现它们，包括旋转转轮、显示结果和计算奖金。

---

![](img/1247bd61a278dcd91103893c45c78f49_4.png)

## 项目结构与核心函数

首先，我们需要规划程序的主要功能。以下是几个核心函数：

*   `spin_row()`：用于生成一行随机的符号。
*   `print_row()`：用于将生成的符号行美观地打印出来。
*   `get_payout()`：用于计算并返回玩家赢得的奖金。

![](img/1247bd61a278dcd91103893c45c78f49_6.png)

![](img/1247bd61a278dcd91103893c45c78f49_7.png)

我们将把大部分代码逻辑写在 `main()` 函数中，并在程序末尾使用 `if __name__ == "__main__":` 来调用它，这是一种良好的编程实践。

![](img/1247bd61a278dcd91103893c45c78f49_9.png)

![](img/1247bd61a278dcd91103893c45c78f49_11.png)

---

![](img/1247bd61a278dcd91103893c45c78f49_13.png)

## 初始化游戏变量

![](img/1247bd61a278dcd91103893c45c78f49_15.png)

现在，让我们开始编写 `main()` 函数。首先，我们需要初始化一些游戏运行所需的变量。

![](img/1247bd61a278dcd91103893c45c78f49_17.png)

以下是需要声明的变量：
*   `balance`：玩家的初始余额，我们设置为100。
*   `symbols`：一个包含游戏符号的列表，我们将使用一些表情符号来代表水果和常见图标。

```python
import random

![](img/1247bd61a278dcd91103893c45c78f49_19.png)

![](img/1247bd61a278dcd91103893c45c78f49_21.png)

def main():
    balance = 100
    symbols = ["🍒", "🍉", "🍋", "🔔", "⭐"]
```

![](img/1247bd61a278dcd91103893c45c78f49_23.png)

---

## 游戏主循环与下注逻辑

![](img/1247bd61a278dcd91103893c45c78f49_25.png)

上一节我们初始化了游戏变量，本节中我们来看看游戏的主循环和下注逻辑。游戏将在玩家余额大于0时持续运行。

![](img/1247bd61a278dcd91103893c45c78f49_27.png)

![](img/1247bd61a278dcd91103893c45c78f49_29.png)

以下是游戏主循环中的关键步骤：
1.  打印欢迎信息和当前余额。
2.  提示玩家输入下注金额。
3.  验证下注金额是否有效（是否为数字、是否大于0、是否不超过当前余额）。
4.  如果验证通过，则从余额中扣除下注金额。

![](img/1247bd61a278dcd91103893c45c78f49_31.png)

```python
    while balance > 0:
        print(f"当前余额：${balance}")
        bet = input("请输入下注金额：")

        if not bet.isdigit():
            print("请输入一个有效的数字。")
            continue
        bet = int(bet)

        if bet > balance:
            print("余额不足。")
            continue
        if bet <= 0:
            print("下注金额必须大于0。")
            continue

        balance -= bet
```

---

## 实现旋转与显示功能

在成功处理下注后，我们需要让老虎机“旋转”起来并显示结果。这需要用到我们之前定义的 `spin_row()` 和 `print_row()` 函数。

首先，我们来实现 `spin_row()` 函数。它的目标是生成一个包含三个随机符号的列表。

```python
def spin_row():
    symbols = ["🍒", "🍉", "🍋", "🔔", "⭐"]
    return [random.choice(symbols) for _ in range(3)]
```

![](img/1247bd61a278dcd91103893c45c78f49_33.png)

接着，我们来实现 `print_row()` 函数。它的作用是将传入的列表（即一行符号）以更美观的格式打印出来。

![](img/1247bd61a278dcd91103893c45c78f49_35.png)

![](img/1247bd61a278dcd91103893c45c78f49_37.png)

```python
def print_row(row):
    print(" | ".join(row))
```

![](img/1247bd61a278dcd91103893c45c78f49_39.png)

现在，回到主循环中，在扣除下注金额后调用这些函数。

```python
        row = spin_row()
        print("旋转中...\n")
        print_row(row)
```

![](img/1247bd61a278dcd91103893c45c78f49_41.png)

![](img/1247bd61a278dcd91103893c45c78f49_42.png)

---

## 计算奖金与更新余额

![](img/1247bd61a278dcd91103893c45c78f49_44.png)

显示结果后，我们需要判断玩家是否中奖并计算奖金。这是 `get_payout()` 函数的工作。

![](img/1247bd61a278dcd91103893c45c78f49_45.png)

该函数接收两个参数：`row`（符号行列表）和 `bet`（下注金额）。如果三个符号相同，则根据匹配的符号类型返回相应的倍数奖金；否则返回0。

```python
def get_payout(row, bet):
    if row[0] == row[1] == row[2]:
        if row[0] == "🍒":
            return bet * 3
        elif row[0] == "🍉":
            return bet * 4
        elif row[0] == "🍋":
            return bet * 5
        elif row[0] == "🔔":
            return bet * 10
        elif row[0] == "⭐":
            return bet * 20
    return 0
```

![](img/1247bd61a278dcd91103893c45c78f49_47.png)

在主循环中，我们调用 `get_payout()` 函数，根据返回值更新玩家余额并给出提示信息。

```python
        payout = get_payout(row, bet)
        if payout > 0:
            print(f"恭喜！你赢得了 ${payout}！")
        else:
            print("很遗憾，本轮未中奖。")
        balance += payout
```

![](img/1247bd61a278dcd91103893c45c78f49_49.png)

![](img/1247bd61a278dcd91103893c45c78f49_51.png)

---

## 添加继续游戏选项与结束处理

为了让玩家能自主控制游戏进程，我们添加一个是否继续游戏的选项。

在每一轮结束后，询问玩家是否继续。如果输入的不是“Y”（无论大小写），则退出游戏循环。

![](img/1247bd61a278dcd91103893c45c78f49_53.png)

```python
        play_again = input("是否继续游戏？ (Y/N): ").upper()
        if play_again != "Y":
            break
```

当游戏循环结束（余额为0或玩家选择退出）时，打印最终余额和结束语。

![](img/1247bd61a278dcd91103893c45c78f49_55.png)

```python
    print(f"游戏结束！你的最终余额为：${balance}")
```

![](img/1247bd61a278dcd91103893c45c78f49_57.png)

![](img/1247bd61a278dcd91103893c45c78f49_59.png)

最后，别忘了设置程序的入口点。

```python
if __name__ == "__main__":
    main()
```

![](img/1247bd61a278dcd91103893c45c78f49_61.png)

---

![](img/1247bd61a278dcd91103893c45c78f49_63.png)

## 总结

![](img/1247bd61a278dcd91103893c45c78f49_65.png)

本节课中我们一起学习了如何使用Python构建一个完整的老虎机游戏。我们实现了以下核心功能：
1.  **游戏初始化**：设置余额和游戏符号。
2.  **下注验证**：确保玩家输入有效的下注金额。
3.  **随机生成**：使用 `random.choice()` 和列表生成式来模拟转轮旋转。
4.  **奖金计算**：通过条件判断，根据匹配的符号类型计算奖金倍数。
5.  **用户交互**：提供了继续游戏的选择，并友好地显示游戏结果。

![](img/1247bd61a278dcd91103893c45c78f49_67.png)

![](img/1247bd61a278dcd91103893c45c78f49_69.png)

通过这个项目，你实践了函数定义、循环控制、条件判断和列表操作等多个Python基础概念。记住，赌博有害，编程快乐！