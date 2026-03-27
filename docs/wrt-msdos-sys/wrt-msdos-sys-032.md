# 032：检测图形显卡 🖥️

在本节课中，我们将学习如何在 MS-DOS 环境下，通过编程自动检测系统中安装的图形显卡类型。这对于编写需要适配不同显示硬件的应用程序（如游戏）至关重要。

## 概述

早期的 IBM PC 及其兼容机（直到大约 90 年代中期）没有现代即插即用（Plug and Play）的硬件识别机制。显卡（如 CGA、Hercules、EGA、VGA）都是“哑”设备，不会主动向系统报告自身信息。因此，程序员需要编写代码来探测和识别系统中安装的显卡，尤其是当系统可能同时安装了一块彩色显卡和一块单色显卡时。

我们将学习如何从最新的 VGA 标准回溯到最老的 CGA 标准，逐步检测显卡。核心方法是利用 BIOS 中断调用和直接端口读写。

## 检测原理与方法

上一节我们概述了检测显卡的必要性，本节中我们来看看具体的检测顺序和原理。

检测应从最新的标准开始，逐步向旧标准回溯。因为较新的显卡（如 VGA、EGA）的 BIOS 提供了查询自身信息的功能，而较老的显卡（如 CGA、Hercules）则需要通过直接与硬件交互来探测。

以下是我们的检测流程：
1.  首先尝试检测 VGA 显卡。
2.  如果未检测到 VGA，则尝试检测 EGA 显卡。
3.  如果仍未检测到，则尝试检测基于 Motorola 6845 芯片的显卡（CGA 和 Hercules/MDA），并区分它们。

## 检测 VGA 显卡 🎯

![](img/b40b5f9afaa8c465d97b7773ada977a4_1.png)

VGA 及以上的显卡标准提供了 BIOS 中断来查询显示组合。这是最直接的方法。

![](img/b40b5f9afaa8c465d97b7773ada977a4_3.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_5.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_6.png)

我们使用 `INT 0x10`（视频服务中断）的子功能 `0x1A`（获取显示组合代码）。

![](img/b40b5f9afaa8c465d97b7773ada977a4_8.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_9.png)

**代码示例：**
```c
union REGS regs;
regs.x.ax = 0x1A00; // AH = 0x1A, AL = 0x00
int86(0x10, &regs, &regs);

![](img/b40b5f9afaa8c465d97b7773ada977a4_11.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_12.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_14.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_16.png)

if (regs.h.al == 0x1A) { // 功能调用成功
    // BL 寄存器包含主显示卡代码
    // BH 寄存器包含次显示卡代码
    primaryCard = vgaCodeToString(regs.h.bl);
    secondaryCard = vgaCodeToString(regs.h.bh);
    // 检测完成，可跳过后续检测
}
```
调用成功后，`BL` 和 `BH` 寄存器中的代码分别对应主显示卡和次显示卡。我们可以将这些代码转换为可读的字符串（如 “VGA with analog color display”）。

![](img/b40b5f9afaa8c465d97b7773ada977a4_18.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_20.png)

**注意：** 如果返回的代码表示是单色显示适配器（MDA），它可能是 Hercules 卡，因为 VGA BIOS 无法区分 MDA 和 Hercules。我们稍后需要专门检测 Hercules。

![](img/b40b5f9afaa8c465d97b7773ada977a4_22.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_23.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_25.png)

## 检测 EGA 显卡 🔍

![](img/b40b5f9afaa8c465d97b7773ada977a4_27.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_29.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_30.png)

如果 VGA 检测失败，我们接下来检测 EGA 显卡。EGA 也提供了 BIOS 功能来识别自身。

![](img/b40b5f9afaa8c465d97b7773ada977a4_32.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_34.png)

我们使用 `INT 0x10` 的子功能 `0x12`，并设置 `BL = 0x10`（获取视频子系统配置信息）。

![](img/b40b5f9afaa8c465d97b7773ada977a4_36.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_38.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_40.png)

**代码示例：**
```c
union REGS regs;
regs.h.ah = 0x12;
regs.h.bl = 0x10;
int86(0x10, &regs, &regs);

![](img/b40b5f9afaa8c465d97b7773ada977a4_42.png)

if (regs.h.bl != 0x10) { // 值被改变，说明 EGA BIOS 存在
    // 检测到 EGA 卡
    // CL 寄存器包含卡上的 DIP 开关设置
    unsigned char dipSettings = regs.h.cl >> 1;

    if (dipSettings == 5) {
        // EGA 单色模式
        primaryCard = “EGA Mono”;
    } else if (dipSettings == 3) {
        // EGA 彩色模式（连接 CGA 显示器）
        primaryCard = “EGA Color (CGA monitor)”;
    } else {
        // EGA 高分辨率彩色模式
        primaryCard = “EGA Enhanced Color”;
    }
}
```
通过读取 `CL` 寄存器中的 DIP 开关设置，我们可以判断 EGA 卡是工作在单色模式、标准彩色模式还是增强彩色模式。根据检测到的模式，我们可以推断系统中是否还可能存在其他类型的彩色或单色显卡。

## 检测 6845 芯片显卡（CGA/Hercules/MDA）🕹️

对于更老的 CGA 和 Hercules/MDA 显卡，它们基于 Motorola 6845 CRT 控制器（CRTC）。我们需要通过直接读写硬件端口来探测。

这些显卡的 CRTC 寄存器位于特定的 I/O 端口：
*   **CGA:** 索引端口 `0x3D4`，数据端口 `0x3D5`
*   **Hercules/MDA:** 索引端口 `0x3B4`，数据端口 `0x3B5`

检测思路是：向一个特定的、安全的 CRTC 寄存器（如光标低位寄存器，编号 `0x0F`）写入一个任意值，稍等片刻再读回。如果读回的值与我们写入的值相同，则说明该端口存在一个 6845 芯片。

**核心检测函数 `test6845` 代码逻辑：**
```c
int test6845(unsigned port) {
    unsigned char oldValue, newValue, testValue = 0x55; // 任意测试值
    // 选择光标低位寄存器 (0x0F)
    outportb(port, 0x0F);
    // 保存旧值
    oldValue = inportb(port + 1);
    // 写入测试值
    outportb(port + 1, testValue);
    // 短暂延迟
    delay(10);
    // 读回新值
    newValue = inportb(port + 1);
    // 恢复旧值
    outportb(port + 1, oldValue);
    // 比较并返回结果
    return (newValue == testValue);
}
```

### 区分 CGA

如果 `test6845(0x3D4)` 返回真，则检测到 CGA 显卡。它通常是主彩色显卡。

![](img/b40b5f9afaa8c465d97b7773ada977a4_44.png)

### 区分 Hercules 与 MDA

![](img/b40b5f9afaa8c465d97b7773ada977a4_46.png)

如果 `test6845(0x3B4)` 返回真，则检测到一个单色显卡，可能是 MDA 或 Hercules。

为了区分两者，我们需要检查 Hercules 卡独有的特性：其状态寄存器（端口 `0x3BA`）的第 7 位会在水平回扫期间切换，而 MDA 卡的这一位是固定的。

![](img/b40b5f9afaa8c465d97b7773ada977a4_48.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_50.png)

**区分代码逻辑：**
```c
if (test6845(0x3B4)) {
    // 至少是 MDA
    unsigned char status = inportb(0x3BA);
    // 检查第7位是否会在循环中发生变化
    for (int i = 0; i < 32767; i++) {
        if ((inportb(0x3BA) & 0x80) != (status & 0x80)) {
            // 位发生变化，是 Hercules 卡
            *card = “Hercules Graphics Card”;
            return;
        }
    }
    // 位未变化，是 MDA 卡
    *card = “MDA”;
}
```

![](img/b40b5f9afaa8c465d97b7773ada977a4_52.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_54.png)

## 总结与注意事项

![](img/b40b5f9afaa8c465d97b7773ada977a4_55.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_57.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_59.png)

本节课中我们一起学习了在 MS-DOS 环境下自动检测图形显卡的完整方法。

![](img/b40b5f9afaa8c465d97b7773ada977a4_61.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_63.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_64.png)

我们首先利用 VGA BIOS 的高级查询功能，这是最准确的方式。若不成功，则降级使用 EGA BIOS 的配置查询。对于更古老的 CGA 和 Hercules/MDA 显卡，我们通过直接探测 6845 CRTC 控制器端口并检查硬件特定行为来识别。

![](img/b40b5f9afaa8c465d97b7773ada977a4_66.png)

**重要提示：**
1.  **模拟器选择：** 此类底层硬件检测代码需要高精度的模拟器（如 86Box）或真实硬件才能可靠运行。DOSBox 等侧重于兼容性和速度的模拟器可能无法准确模拟 6845 芯片行为。
2.  **用户覆盖：** 自动检测并非 100% 可靠，特别是面对非标准或兼容性不佳的硬件时。因此，在商业软件中，提供一个允许用户手动选择显卡类型的配置选项始终是明智之举。

![](img/b40b5f9afaa8c465d97b7773ada977a4_68.png)

![](img/b40b5f9afaa8c465d97b7773ada977a4_70.png)

通过掌握这些技术，你可以编写出像《波斯王子》或《国王密使》那样能够自动识别并适配最佳图形模式的经典 DOS 程序。