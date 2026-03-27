# 047：让我们编写MS-DOS 0x2E - VGA可重定义字符集

在本节课中，我们将学习如何在MS-DOS的文本模式下，利用VGA显卡的可重定义字符集功能，结合调色板修改和双缓冲技术，创建一个动态的“等离子”效果和滚动字幕。我们将使用Turbo C 2.0进行编程，并深入探讨一些底层VGA编程技巧。

## 概述与目标

上一节我们介绍了图形模式下的效果。本节中，我们来看看如何在文本模式下实现类似的效果。VGA显卡在文本模式下拥有一些有趣的特性，例如可重定义字符集和调色板修改功能。我们将利用这些特性，在80x25的文本屏幕上创建动态视觉效果。

## 核心步骤与函数

以下是实现效果的主要步骤，我们将在后续小节中逐一详细讲解。

1.  **设置调色板**：将16色调色板重新映射到VGA的调色板寄存器。
2.  **禁用光标**：通过BIOS中断调用隐藏屏幕光标。
3.  **设置字符宽度**：将默认的9列字符模式改为8列，以获得更紧凑的显示。
4.  **定义自定义字符**：创建一组从小到大的方块字符，用于表示不同强度的“等离子”点。
5.  **绘制等离子效果**：使用正弦函数生成动态颜色值，并映射到自定义字符上。
6.  **绘制滚动字幕**：在屏幕上实现一个从右向左滚动的文本。
7.  **实现双缓冲**：利用文本模式的多页面特性消除闪烁。
8.  **应用运行时补丁**：修复Turbo C 2.0中一个长达35年的BUG，以正确加载自定义字符。

![](img/01bb7f76fdf39419dd533cc7fe70087b_1.png)

## 设置调色板

![](img/01bb7f76fdf39419dd533cc7fe70087b_3.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_5.png)

首先，我们需要设置一个16色的调色板。VGA显卡的16色文本模式使用独立的调色板寄存器，其映射关系并非连续。

![](img/01bb7f76fdf39419dd533cc7fe70087b_7.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_9.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_11.png)

我们定义了一个`palette`数组，它是对火焰调色板进行16色采样的结果。然后，我们需要一个映射数组`palette_reg`，将逻辑颜色索引对应到实际的VGA调色板寄存器号。

![](img/01bb7f76fdf39419dd533cc7fe70087b_13.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_15.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_16.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_17.png)

```c
unsigned char palette[48] = { ... }; // 16色火焰调色板数据
unsigned char palette_reg[16] = {0,1,2,3,4,5,0x14,7,0x38,0x39,0x3A,0x3B,0x3C,0x3D,0x3E,0x3F};
```

![](img/01bb7f76fdf39419dd533cc7fe70087b_19.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_21.png)

设置调色板的函数`set_palette`会遍历这16种颜色。与256色模式直接写入颜色值不同，这里需要先写入要设置的寄存器索引，再写入RGB颜色值。

```c
void set_palette() {
    int i;
    for(i=0; i<16; i++) {
        // 选择要设置的调色板寄存器
        outportb(PALETTE_INDEX, palette_reg[i]);
        // 写入该寄存器的RGB值
        outportb(PALETTE_DATA, palette[i*3]);
        outportb(PALETTE_DATA, palette[i*3+1]);
        outportb(PALETTE_DATA, palette[i*3+2]);
    }
}
```

![](img/01bb7f76fdf39419dd533cc7fe70087b_23.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_24.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_26.png)

## 禁用光标

![](img/01bb7f76fdf39419dd533cc7fe70087b_28.png)

为了获得干净的显示效果，我们需要隐藏文本光标。这可以通过BIOS中断`0x10`的功能`0x01`（设置光标类型）来实现。通过将光标的结束扫描线设置为小于起始扫描线，即可使其消失。

![](img/01bb7f76fdf39419dd533cc7fe70087b_30.png)

以下是禁用光标的函数实现：

![](img/01bb7f76fdf39419dd533cc7fe70087b_32.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_34.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_36.png)

```c
void disable_cursor() {
    union REGS regs;
    regs.h.ah = 0x01; // 功能号：设置光标类型
    regs.h.ch = 0x3F; // 设置起始扫描线为63（二进制00111111）
    regs.h.cl = 0x00; // 设置结束扫描线为0
    int86(0x10, &regs, &regs); // 调用视频中断
}
```

代码中使用了`union REGS`结构来设置CPU寄存器。`AH`寄存器存放功能号，`CH`和`CL`寄存器分别控制光标的起始和结束扫描线。将其设置为`0x3F`和`0x00`即可禁用光标。

## 设置字符宽度

VGA文本模式默认使用9像素宽的字符（80列 * 9像素 = 720像素水平分辨率）。为了获得更紧凑的方块效果，我们将其切换为8像素宽（640像素）。这需要直接对VGA硬件寄存器进行编程。

![](img/01bb7f76fdf39419dd533cc7fe70087b_38.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_40.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_41.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_42.png)

以下是设置字符宽度的关键步骤：

1.  读取并修改“杂项输出寄存器”的时钟选择位，以选择640像素模式。
2.  通过序列器（Sequencer）的时钟模式寄存器，确认字符宽度为8点每字符。
3.  配置属性控制器（Attribute Controller）以确保像素对齐。

```c
void set_char_width_8() {
    unsigned char x;
    // 1. 设置杂项输出寄存器，选择640像素模式
    x = inportb(MISC_OUTPUT_READ) & 0xF3;
    x |= 0x04; // 设置位2和3，选择9列模式（实际为了后续步骤）
    outportb(MISC_OUTPUT_WRITE, x);

    // 2. 设置序列器时钟模式，启用8点每字符
    outportb(SEQ_INDEX, RESET_REG); // 选择复位寄存器
    outportb(SEQ_DATA, 0x00); // 解除复位
    outportb(SEQ_INDEX, CLOCKING_MODE_REG);
    outportb(SEQ_DATA, 0x01); // 清除位0，启用8点每字符

    // 3. 配置属性控制器像素平移
    x = inportb(INPUT_STATUS_1); // 读取状态以切换属性控制器到索引模式
    outportb(ATTRIBUTE_CONTROLLER_INDEX, 0x20 | PIXEL_PANNING_REG); // 选择像素平移寄存器
    outportb(ATTRIBUTE_CONTROLLER_DATA, 0x08); // 设置像素平移为0
    outportb(ATTRIBUTE_CONTROLLER_INDEX, x); // 恢复之前的状态
}
```

这个过程涉及多个VGA寄存器，具体位定义可以参考VGA编程手册。执行后，屏幕水平分辨率将从720像素变为640像素。

![](img/01bb7f76fdf39419dd533cc7fe70087b_44.png)

## 定义自定义字符

![](img/01bb7f76fdf39419dd533cc7fe70087b_46.png)

这是本教程的核心。VGA允许我们重新定义字符发生器中的字符形状。我们计划用128-132这五个字符代码来表示五个不同大小的实心方块。

![](img/01bb7f76fdf39419dd533cc7fe70087b_48.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_50.png)

首先，我们需要定义字符的点阵数据。每个字符由16行、每行8位（1字节）组成，位为1表示点亮像素。

![](img/01bb7f76fdf39419dd533cc7fe70087b_52.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_53.png)

```c
unsigned char char_table[] = {
    // 字符 128: 空方块
    0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
    0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
    // 字符 129: 2x2方块
    0x00, 0x00, 0x00, 0x18, 0x18, 0x00, 0x00, 0x00,
    0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
    // 字符 130: 4x4方块 (0x3C = 00111100)
    0x00, 0x00, 0x3C, 0x3C, 0x3C, 0x3C, 0x00, 0x00,
    0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
    // 字符 131: 6x6方块 (0x7E = 01111110)
    0x00, 0x7E, 0x7E, 0x7E, 0x7E, 0x7E, 0x7E, 0x00,
    0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00,
    // 字符 132: 8x8方块 (0xFF = 11111111)
    0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF,
    0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00
};
```

![](img/01bb7f76fdf39419dd533cc7fe70087b_55.png)

接下来，我们需要调用BIOS中断`0x10`的功能`0x11`（用户字符加载）来上传这些数据。这个功能需要设置`ES:BP`寄存器对指向我们的字符数据。然而，Turbo C 2.0标准的`int86`函数不支持设置`ES`和`BP`寄存器。为此，我们使用了一个特殊的`intr`函数和`REGS`联合体。

![](img/01bb7f76fdf39419dd533cc7fe70087b_57.png)

但这里存在一个Turbo C 2.0运行时的BUG：`intr`函数生成的代码错误地计算了`BP`寄存器的偏移量。社区成员Charlie发现并提供了一个运行时补丁函数`patch_intr`，它会在内存中搜索特定的指令序列并将其修复。

```c
// 补丁函数，修复intr的BUG
int patch_intr() {
    unsigned char *ptr = (unsigned char*)intr; // 获取intr函数地址
    unsigned char patch[] = {0xC6, 0x46, 0xF4, 0xE2}; // 要查找的错误指令
    while(ptr < (unsigned char*)intr + 1024) { // 在函数代码段中搜索
        if(memcmp(ptr, patch, 4) == 0) {
            ptr[3] = 0xDE; // 将错误的偏移0xE2改为正确的0xDE
            printf("Patched intr at %p\n", ptr);
            return (ptr - (unsigned char*)intr);
        }
        ptr++;
    }
    return -1; // 未找到，可能已修复
}
```

应用补丁后，我们可以正确调用中断来定义字符：

```c
void define_chars(int num, int first, int table, int height, int len, void *buffer) {
    union REGS regs;
    // 设置寄存器参数
    regs.h.ah = 0x11; // 功能：用户字符加载
    regs.h.al = 0x00; // 子功能：加载用户表
    regs.h.bh = height; // 每个字符的字节数（行数）
    regs.h.bl = table; // 字符生成器表
    regs.x.cx = num;   // 要定义的字符数量
    regs.x.dx = first; // 第一个字符的ASCII码
    // 设置ES:BP指向字符数据缓冲区
    regs.x.es = FP_SEG(buffer);
    regs.x.bp = FP_OFF(buffer);
    // 调用中断（此时intr已被补丁修复）
    intr(0x10, &regs);
}
```

![](img/01bb7f76fdf39419dd533cc7fe70087b_59.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_61.png)

## 绘制等离子效果

等离子效果的原理是组合多个在空间上变化的正弦波，生成平滑变化的颜色值。我们将颜色值（0-255）映射到之前定义的5个方块字符（128-132）上，同时利用前景色和背景色来丰富色彩层次。

以下是绘制等离子效果的核心循环：

```c
void draw_plasma(int x, int y, int width, int height) {
    int i, j;
    unsigned char c, c1, c2, c3, c4;
    int v1 = sintab[(t) % 256];
    int v2 = sintab[(32 + t*2) % 256];

    for(j=y; j<y+height; j++) {
        int ysin = sintab[(j*6 + t) % 256];
        for(i=x; i<x+width; i++) {
            // 计算四个不同的正弦波分量
            c1 = (i * 5);
            c2 = (i * v1 + j * v2) / (height+2);
            c3 = sintab[((i + ysin) % 256)];
            c4 = sintab[((i - ysin) % 256)];
            // 合并分量并得到最终颜色索引 (0-255)
            c = (unsigned char)((c1 + c2 + c3 + c4) >> 2);
            // 设置“像素”：字符索引 + 颜色属性
            set_pixel(i, j, c);
        }
    }
}
```

`set_pixel`函数负责将计算出的颜色值`c`写入视频内存。它做了两件事：
1.  将颜色值`c`除以51，映射到0-4的范围，加上128得到对应的方块字符代码。
2.  将颜色值`c`的高4位作为前景色，次高3位作为背景色，组合成一个文本属性字节。

```c
void set_pixel(int x, int y, unsigned char c) {
    int offset = (y * VGA_WIDTH + x) * 2 + back_page * VGA_PAGE_SIZE;
    // 写入字符代码
    vga_buffer[offset] = 128 + (c / 51); // 映射到字符128-132
    // 写入颜色属性：前景色 + 背景色
    vga_buffer[offset + 1] = (c >> 4) + ((c >> 5) << 4);
}
```

![](img/01bb7f76fdf39419dd533cc7fe70087b_63.png)

## 绘制滚动字幕

![](img/01bb7f76fdf39419dd533cc7fe70087b_65.png)

滚动字幕的原理是不断改变文本在屏幕上的起始X坐标。我们使用一个静态变量`time_step`来控制滚动速度，当文本完全滚出屏幕左侧时，将其重置到屏幕右侧的一个随机行。

```c
void draw_scroller() {
    static int time_step = 0;
    int x, index;
    int offset = time_step >> 1; // 控制滚动速度
    static int scroller_y = 12;
    int len = strlen(scroll_text);

    time_step--;
    if(offset < -len) { // 如果文本完全滚出屏幕
        time_step = VGA_WIDTH * 2; // 重置到右侧
        scroller_y = rand() % 25; // 随机新行
        offset = time_step >> 1;
    }

    // 计算文本在屏幕上的可见范围
    int start_x = max(0, offset);
    int end_x = min(VGA_WIDTH, offset + len);

    for(x = start_x; x < end_x; x++) {
        int pos = (scroller_y * VGA_WIDTH + x) * 2 + back_page * VGA_PAGE_SIZE;
        // 写入字符
        vga_buffer[pos] = scroll_text[x - offset];
        // 写入属性（白色）
        vga_buffer[pos + 1] = 0x0F;
    }
}
```

## 实现双缓冲与主循环

文本模式支持多个显示页面。我们可以使用一个页面（如前页，page 0）进行显示，同时在另一个页面（后页，page 1）进行绘制，完成后再切换页面，从而实现无闪烁的双缓冲。

主循环的流程如下：
1.  初始化（设置模式、禁用光标、定义字符、设置调色板）。
2.  进入循环，直到有按键按下。
3.  在每一帧中，先在后台页面绘制等离子效果。
4.  然后在同一后台页面绘制滚动字幕（覆盖部分等离子）。
5.  切换显示页面到后台页面。
6.  交换前后台页面索引，为下一帧做准备。

```c
void main() {
    // ... 初始化变量
    patch_intr(); // 应用运行时补丁
    init_sintab(); // 初始化正弦表
    set_mode(TEXT_MODE); // 设置文本模式
    disable_cursor(); // 禁用光标
    set_char_width_8(); // 设置8列字符
    define_chars(5, 128, 0, 16, 5*16, char_table); // 定义自定义字符
    set_palette(); // 设置调色板

    back_page = 1; // 从页面1开始绘制
    while(!kbhit()) { // 主循环，直到按键
        draw_plasma(0, 0, VGA_WIDTH, VGA_HEIGHT); // 绘制等离子
        draw_scroller(); // 绘制滚动字幕
        set_display_page(back_page); // 切换显示页面
        back_page ^= 1; // 交换前后台页面索引
        t++; // 更新时间
    }
    // ... 恢复文本模式并退出
}
```

![](img/01bb7f76fdf39419dd533cc7fe70087b_67.png)

![](img/01bb7f76fdf39419dd533cc7fe70087b_69.png)

## 总结

本节课中我们一起学习了如何在MS-DOS的VGA文本模式下创造动态视觉效果。我们涵盖了以下核心内容：

1.  **VGA调色板重映射**：学习了16色文本模式下调色板寄存器的特殊映射方式。
2.  **底层VGA编程**：通过直接操作硬件寄存器来禁用光标和修改字符宽度。
3.  **可重定义字符集**：利用BIOS中断上传自定义字符形状，并修复了Turbo C 2.0的一个历史BUG。
4.  **算法生成效果**：使用正弦函数生成“等离子”效果，并将连续的颜色值离散化到有限的字符和颜色上。
5.  **文本模式双缓冲**：利用多页面显示实现无闪烁动画。
6.  **滚动字幕实现**：掌握了在固定缓冲区中实现物体运动的基本方法。

通过结合这些技术，我们能够在有限的文本模式下创造出令人印象深刻的动画效果，这展示了早期计算机编程中硬件直接控制的强大能力和创意空间。