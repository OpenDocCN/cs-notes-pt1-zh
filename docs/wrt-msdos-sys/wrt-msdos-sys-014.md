# 014：PowerBasic圣诞特辑 🎄

在本节课中，我们将学习如何使用PowerBasic编译器，在MS-DOS环境下编写一个简单的圣诞贺卡程序。我们将涵盖图形绘制、动画、音乐播放和文本输出等基础概念。

## 概述与背景

上一节我们介绍了MS-DOS下的编程环境。本节中，我们将探索一款名为PowerBasic的BASIC语言编译器，并利用它创建一个具有节日气氛的图形程序。

PowerBasic是一款由Bob Zale开发、最初源自Borland Turbo Basic的BASIC编译器。它能够生成独立的`.EXE`可执行文件，在90年代的德国颇为流行。与解释型的GW-BASIC或QBasic不同，PowerBasic将代码编译为机器码，因此执行速度更快。

## 环境准备与安装

首先，我们需要在MS-DOS系统中安装PowerBasic。以下是安装步骤：

1.  将包含PowerBasic的3.5英寸软盘插入驱动器（例如A:）。
2.  在硬盘上创建一个新目录，用于存放PowerBasic文件。
    ```dos
    mkdir C:\PBASIC
    ```
3.  切换到该目录，并使用`XCOPY`命令复制软盘上的所有文件（包括子目录）。
    ```dos
    cd C:\PBASIC
    xcopy A:\*.* /S
    ```
4.  复制完成后，运行`PBRT.EXE`即可启动PowerBasic运行时编译器。

## 第一个图形程序：绘制彩虹圆圈

![](img/55c78dc961b6edf6630a71a6f58ebf7b_1.png)

成功启动PowerBasic后，我们可以开始编写第一个图形程序。以下是一个简单的例子，它在屏幕上绘制一系列彩色的同心圆。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_3.png)

```basic
SCREEN 7
FOR i = 0 TO 15
    CIRCLE (160, 100), i * 5 + 5, i
NEXT i
```

![](img/55c78dc961b6edf6630a71a6f58ebf7b_5.png)

**代码解释**：
*   `SCREEN 7`：将图形模式设置为EGA 320x200，16色。
*   `FOR...NEXT`循环：循环变量`i`从0到15，代表16种颜色。
*   `CIRCLE`命令：用于画圆。参数依次是圆心的X坐标、Y坐标、半径和颜色。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_7.png)

运行这段代码，你将在屏幕中央看到一组彩色的同心圆，就像一个彩虹。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_9.png)

## 创建动画：飘落的雪花 ❄️

![](img/55c78dc961b6edf6630a71a6f58ebf7b_11.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_13.png)

静态图形很有趣，但动画更能吸引人。接下来，我们让一些“雪花”像素点从屏幕顶部随机位置飘落。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_15.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_17.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_19.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_21.png)

实现动画需要以下几个步骤：
1.  初始化：创建数组来存储大量雪花的位置（X和Y坐标），并为其赋予随机起始值。
2.  主循环：在循环中不断更新每个雪花的位置（主要是Y坐标增加，模拟下落）。
3.  绘制与擦除：在雪花的新位置绘制一个白点，并在其旧位置绘制一个黑点（或背景色）以擦除痕迹，从而产生运动效果。
4.  边界检测与重置：当雪花落到屏幕底部时，将其重置到顶部的一个新随机位置。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_23.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_25.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_27.png)

以下是核心代码框架：

![](img/55c78dc961b6edf6630a71a6f58ebf7b_29.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_31.png)

```basic
RANDOMIZE TIMER
DIM snowX(49), snowY(49)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_33.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_35.png)

' 初始化雪花位置
FOR i = 0 TO 49
    snowX(i) = INT(RND * 320)
    snowY(i) = INT(RND * 200)
NEXT i

![](img/55c78dc961b6edf6630a71a6f58ebf7b_37.png)

SCREEN 7

![](img/55c78dc961b6edf6630a71a6f58ebf7b_39.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_41.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_43.png)

' 主动画循环
DO
    FOR i = 0 TO 49
        ' 擦除旧雪花
        PSET (snowX(i), snowY(i)), 0
        ' 更新位置
        snowY(i) = snowY(i) + 1
        ' 检查是否落地
        IF snowY(i) >= 200 THEN
            snowY(i) = 0
            snowX(i) = INT(RND * 320)
        END IF
        ' 绘制新雪花
        PSET (snowX(i), snowY(i)), 15
    NEXT i
    ' 检测按键退出
LOOP UNTIL INKEY$ <> ""
```

![](img/55c78dc961b6edf6630a71a6f58ebf7b_45.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_47.png)

**关键概念**：
*   `RANDOMIZE TIMER`：用系统时间初始化随机数种子。
*   `RND`函数：生成一个0到1之间的随机浮点数。
*   `PSET (x, y), color`：在指定坐标`(x, y)`绘制一个指定颜色的像素点。
*   `INKEY$`函数：检测是否有按键被按下。循环会一直运行，直到用户按下任意键。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_49.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_51.png)

## 添加背景音乐 🎵

一个完整的节日程序怎么能缺少音乐呢？PowerBasic提供了`PLAY`命令，可以方便地通过PC扬声器播放音乐。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_53.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_55.png)

`PLAY`命令使用一个特殊的字符串来定义音符、音长和节奏。以下是一段《铃儿响叮当》(Jingle Bells)的示例：

![](img/55c78dc961b6edf6630a71a6f58ebf7b_57.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_59.png)

```basic
PLAY "MB T200 L16 O2 E L4 E L2 E L4 E L4 E L2 E L4 G L4 C L4 D L1 E"
```

![](img/55c78dc961b6edf6630a71a6f58ebf7b_61.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_63.png)

**字符串解释**：
*   `MB`：在后台播放音乐，这样程序可以同时做其他事情（如动画）。
*   `T200`：设置节奏（速度）。
*   `L16`：设置默认音长为16分音符。
*   `O2`：设置音阶为第2个八度。
*   后面的`E`、`G`、`C`、`D`等字母代表音符。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_65.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_67.png)

为了让音乐在播放完毕后自动重复，我们可以检查音乐缓冲区。以下是如何将音乐整合到主循环中：

![](img/55c78dc961b6edf6630a71a6f58ebf7b_69.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_71.png)

```basic
' 设置更大的声音缓冲区
SOUND 4000, 0

![](img/55c78dc961b6edf6630a71a6f58ebf7b_73.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_75.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_77.png)

' 在程序开始处启动音乐
PLAY "MB T200 L16 O2 E L4 E L2 E L4 E L4 E L2 E L4 G L4 C L4 D L1 E"

![](img/55c78dc961b6edf6630a71a6f58ebf7b_79.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_81.png)

DO
    ' ... (这里是雪花动画的代码) ...

    ' 检查音乐是否快播完了，如果是则重新开始
    IF PLAY(0) < 2 THEN
        PLAY "MB T200 L16 O2 E L4 E L2 E L4 E L4 E L2 E L4 G L4 C L4 D L1 E"
    END IF
LOOP UNTIL INKEY$ <> ""
```

## 添加节日问候文本

![](img/55c78dc961b6edf6630a71a6f58ebf7b_83.png)

最后，让我们在屏幕上显示一句节日祝福。在图形模式下，我们可以使用`PRINT`语句，但需要手动定位。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_85.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_87.png)

由于PowerBasic的图形模式没有直接的文本光标定位命令，我们可以通过打印多个空行和空格来模拟居中效果：

![](img/55c78dc961b6edf6630a71a6f58ebf7b_89.png)

```basic
COLOR 5  ' 设置文本颜色为洋红色
FOR i = 1 TO 16
    PRINT  ' 打印16个空行，将光标移动到屏幕下半部分
NEXT i
PRINT "                                    Merry Christmas!"  ' 使用空格进行水平居中
```

![](img/55c78dc961b6edf6630a71a6f58ebf7b_91.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_93.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_95.png)

## 程序整合与运行

现在，我们将所有部分组合起来：彩虹圆圈作为背景，飘落的雪花作为动画，循环播放的《铃儿响叮当》作为背景音乐，以及屏幕上的节日祝福文本。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_97.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_99.png)

![](img/55c78dc961b6edf6630a71a6f58ebf7b_101.png)

运行这个完整的程序，你将看到一个充满节日气氛的MS-DOS圣诞贺卡。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_103.png)

## 总结与思考

![](img/55c78dc961b6edf6630a71a6f58ebf7b_105.png)

本节课中我们一起学习了如何使用PowerBasic在MS-DOS下进行编程。我们完成了一个包含图形、动画、音乐和文本的综合项目，回顾了以下核心技能：
*   设置图形模式（`SCREEN`）。
*   使用`CIRCLE`、`PSET`等命令进行图形绘制。
*   利用数组和循环创建简单动画。
*   使用`PLAY`命令播放音乐。
*   在图形模式下输出文本。

PowerBasic作为一款历史悠久的编译器，对于体验复古编程和了解BASIC语言很有帮助。然而，对于希望深入学习系统编程或开发高性能应用的现代学习者，C或汇编语言可能是更合适的选择。不过，这次怀旧之旅无疑是一次有趣且富有教育意义的体验。

![](img/55c78dc961b6edf6630a71a6f58ebf7b_107.png)

祝你编程愉快，并预祝节日快乐！