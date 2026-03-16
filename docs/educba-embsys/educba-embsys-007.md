# 007：scanf练习实现 第二部分 🖥️

在本节课中，我们将继续学习如何使用 `scanf` 函数，并解决程序执行后终端窗口立即关闭的问题。我们将探讨如何让程序等待用户输入后再退出，并学习如何优化代码结构。

---

## 程序暂停问题与 `getchar` 函数

上一节我们介绍了使用 `scanf` 读取多个输入。但在程序执行最后的 `printf` 后，控制台窗口会立即关闭，导致用户看不到输出结果。

为了解决这个问题，我们需要让程序在打印结果后暂停。一个常见的方法是使用 `getchar` 函数。`getchar` 函数会使程序等待，直到用户从键盘按下任意键。当用户按下键时，该函数会捕获这个字符，然后程序可以继续执行或退出。

![](img/e2df4fa2f61bc21fdee706fa89193f41_1.png)

![](img/e2df4fa2f61bc21fdee706fa89193f41_2.png)

![](img/e2df4fa2f61bc21fdee706fa89193f41_4.png)

以下是使用 `getchar` 的基本语法：
```c
getchar();
```

![](img/e2df4fa2f61bc21fdee706fa89193f41_5.png)

将此语句放在打印平均值的 `printf` 语句之后，程序就会在显示结果后暂停。

---

![](img/e2df4fa2f61bc21fdee706fa89193f41_7.png)

![](img/e2df4fa2f61bc21fdee706fa89193f41_8.png)

## 初次尝试与问题

我们首先在打印平均值的代码后添加一个 `getchar()`。

```c
printf("The average is: %f\n", average);
getchar(); // 等待用户按键
```

构建并运行程序后，我们输入数字。然而，程序在显示平均值后并没有如预期般暂停，而是直接退出了。这是因为之前的 `scanf` 函数在读取数字后，在输入缓冲区中留下了一个换行符 `\n`。第一个 `getchar()` 会立刻读取这个残留的换行符，因此不会等待用户的新输入。

![](img/e2df4fa2f61bc21fdee706fa89193f41_10.png)

---

## 解决方案：使用多个 `getchar` 或循环

![](img/e2df4fa2f61bc21fdee706fa89193f41_12.png)

一个直接的解决方法是使用两个 `getchar()` 调用。第一个用于消耗缓冲区中残留的换行符，第二个则真正等待用户的新输入。

```c
printf("The average is: %f\n", average);
printf("Press any key to exit the application.\n");
getchar(); // 消耗残留的换行符
getchar(); // 等待用户按键
```

![](img/e2df4fa2f61bc21fdee706fa89193f41_14.png)

这种方法有效，但并非通用解决方案，因为它依赖于特定的输入情况。

![](img/e2df4fa2f61bc21fdee706fa89193f41_15.png)

![](img/e2df4fa2f61bc21fdee706fa89193f41_17.png)

![](img/e2df4fa2f61bc21fdee706fa89193f41_18.png)

![](img/e2df4fa2f61bc21fdee706fa89193f41_20.png)

更通用的方法是使用一个循环。我们可以让程序持续等待，直到用户输入一个非换行符的键。

![](img/e2df4fa2f61bc21fdee706fa89193f41_21.png)

```c
printf("Press any key to exit the application.\n");
while(getchar() != '\n') {
    // 循环等待，直到输入的字符不是换行符
    // 此处可以留空或添加其他语句
}
getchar(); // 等待用户最后一次按键确认
```

![](img/e2df4fa2f61bc21fdee706fa89193f41_23.png)

这个 `while` 循环会清空输入缓冲区中所有字符，直到遇到换行符。然后，最后一个 `getchar()` 会等待用户进行一次新的按键操作，从而实现稳定的暂停效果。

![](img/e2df4fa2f61bc21fdee706fa89193f41_25.png)

---

## 优化输入：单次 `scanf` 读取多个值

在之前的练习中，我们使用了多个 `printf` 和 `scanf` 语句来分别提示和读取四个数字。实际上，我们可以简化这个过程。

我们可以使用一个 `printf` 进行提示，然后用一个 `scanf` 语句同时读取所有四个数字。

以下是优化后的代码示例：

```c
#include <stdio.h>

int main() {
    float num1, num2, num3, num4, average;

    // 单次提示
    printf("Enter four numbers separated by space: ");
    // 单次scanf读取四个值
    scanf("%f %f %f %f", &num1, &num2, &num3, &num4);

    average = (num1 + num2 + num3 + num4) / 4;

    printf("The average is: %f\n", average);

    // 通用暂停方法
    printf("Press any key to exit the application.\n");
    while(getchar() != '\n'); // 清空输入缓冲区
    getchar(); // 等待用户按键

    return 0;
}
```

![](img/e2df4fa2f61bc21fdee706fa89193f41_27.png)

这种方法大大减少了代码行数，使程序更加简洁高效。用户可以在提示后一次性输入所有数字（用空格分隔），然后按回车键。

---

## 总结

本节课中我们一起学习了以下内容：
1.  **使用 `getchar` 暂停程序**：解决了程序输出后立即关闭的问题。
2.  **处理输入缓冲区**：理解了残留换行符对 `getchar` 的影响，并学会了使用循环来清空缓冲区，从而获得更稳健的暂停逻辑。
3.  **优化 `scanf` 的使用**：掌握了如何用单个 `scanf` 语句读取多个输入值，使代码更加简洁明了。

![](img/e2df4fa2f61bc21fdee706fa89193f41_29.png)

![](img/e2df4fa2f61bc21fdee706fa89193f41_30.png)

通过尝试和修改这些程序，你可以更深入地理解C语言中标准输入/输出函数的工作方式。请尝试运行这些示例，并观察它们的行为。我们将在后续课程中继续探索更多嵌入式系统编程的基础知识。