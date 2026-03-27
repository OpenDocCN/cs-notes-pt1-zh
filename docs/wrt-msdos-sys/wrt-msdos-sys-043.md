# 043：编写 Adlib 背景音乐播放器

在本节课中，我们将学习如何利用 Reality Adlib Tracker 工具及其附带的汇编播放器，在 MS-DOS 环境下播放 Adlib 背景音乐。我们将从加载音乐文件开始，逐步实现一个使用可编程中断定时器（PIT）进行精确 50Hz 播放的 C 语言程序。

---

## 概述：Reality Adlib Tracker 简介

上一节我们介绍了 Adlib 的旋律和打击乐编程。本节中，我们来看看一个更接近真实音乐创作的工具：Reality Adlib Tracker。

![](img/671902fd1b0d9aac742cd0b899d31d5c_1.png)

这是一个由演示场景小组 Reality 在 1995 年制作的音乐追踪器程序。它的优势在于：
*   它可以在低端的 286 机器上流畅运行。
*   与 Pro Tracker 等使用采样音频的追踪器不同，它直接使用 Adlib OPL2 芯片的 FM 合成功能来生成音乐。
*   它附带了一个用汇编语言编写的简单播放器例程及其源代码，我们可以将其集成到自己的程序中。

这个程序拥有 9 个旋律通道（将鼓点也作为旋律乐器处理）、乐器编辑器、模式编辑器和顺序列表。音乐数据以 `.rad` 格式存储，文件非常小巧（通常在 2KB 到 18KB 之间），因为只需要存储振荡器参数，无需音频采样，非常适合在慢速机器上播放。

---

![](img/671902fd1b0d9aac742cd0b899d31d5c_3.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_5.png)

## 第一步：加载音乐文件

要播放音乐，首先需要将 `.rad` 文件加载到内存中。以下是加载函数的关键步骤：

1.  **打开文件**：以二进制读取模式打开指定的音乐文件。
2.  **获取文件大小**：使用 `fseek` 和 `ftell` 函数来确定文件长度。
3.  **分配内存**：使用 `allocmem` 函数（而非 `malloc`）在段边界上分配内存，因为汇编播放器要求音乐数据位于段边界地址。
4.  **读取数据**：将整个文件读入分配好的缓冲区。
5.  **关闭文件并返回指针**：操作完成后关闭文件句柄，将缓冲区指针返回给调用者。

核心代码逻辑如下：
```c
unsigned char *load_music(const char *filename) {
    FILE *fp = fopen(filename, "rb");
    if (!fp) return NULL;

    fseek(fp, 0, SEEK_END);
    long size = ftell(fp);
    fseek(fp, 0, SEEK_SET);

    unsigned int segment;
    unsigned char *buffer = (unsigned char *)allocmem((size + 15) >> 4, &segment);
    if (!buffer) {
        fclose(fp);
        return NULL;
    }

    fread(buffer, 1, size, fp);
    fclose(fp);
    return buffer;
}
```

---

## 第二步：集成汇编播放器

Reality Tracker 附带的播放器包含三个主要的汇编函数，我们需要在 C 语言中声明并调用它们。

以下是需要在头文件中声明的函数原型：
```c
int init_player(unsigned int music_segment);
void play_music(void);
void end_player(void);
```
*   `init_player`：初始化播放器，传入音乐数据所在的**段地址**。成功返回 0，失败返回 1。
*   `play_music`：这是需要以 50Hz 频率调用的核心例程，负责处理音乐数据的播放。
*   `end_player`：停止播放并重置 Adlib 芯片。

汇编文件 (`player.asm`) 使用特定的调用约定。为了从 C 中调用，函数名需要添加下划线前缀并被声明为 `PUBLIC`。函数参数通过栈传递。

![](img/671902fd1b0d9aac742cd0b899d31d5c_7.png)

在 C 主程序中，初始化流程如下：
```c
unsigned char *music_data = load_music("ALLORUN.RAD");
if (music_data) {
    // 获取音乐数据所在的段地址
    unsigned int music_segment = FP_SEG(music_data);
    if (init_player(music_segment) == 0) {
        // 初始化成功，可以开始播放
    }
}
```

![](img/671902fd1b0d9aac742cd0b899d31d5c_9.png)

---

## 第三步：实现精确的 50Hz 定时播放

![](img/671902fd1b0d9aac742cd0b899d31d5c_11.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_12.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_14.png)

最简单的播放方式是循环调用 `play_music`，但这样无法控制速度。为了实现精确的 50Hz 播放，我们需要使用 PC 的可编程中断定时器。

![](img/671902fd1b0d9aac742cd0b899d31d5c_16.png)

### 1. 理解 PC 定时器系统
IBM PC 使用一个 8253/8254 PIT 芯片。其中：
*   **计数器 0** 用于系统定时器，默认以大约 18.206 Hz 的频率触发 **INT 8h** 硬件中断。
*   **INT 8h** 的中断服务程序会调用一个软件中断 **INT 1Ch**，这是一个供用户程序使用的“定时器滴答”钩子。

![](img/671902fd1b0d9aac742cd0b899d31d5c_18.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_20.png)

我们的策略是：接管 **INT 1Ch** 中断向量，在自己的中断处理函数中调用 `play_music`，并重新编程 PIT 的计数器 0，使其以 50Hz 触发中断。

![](img/671902fd1b0d9aac742cd0b899d31d5c_22.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_24.png)

### 2. 编写中断处理函数
中断处理函数需要使用 `interrupt` 关键字声明，并保存和恢复旧的向量。
```c
void interrupt (*old_timer_handler)(...);
void interrupt new_timer_handler(...) {
    static unsigned long t = 0;
    // 禁用中断以确保原子操作
    disable();
    // 调用音乐播放例程
    play_music();
    // 累加时间计数器，用于在50Hz下正确调用旧的18.206Hz处理程序
    t += TIMER_FREQ_OLD; // TIMER_FREQ_OLD = 18206
    if (t >= TIMER_FREQ_NEW) { // TIMER_FREQ_NEW = 10000 (对应50Hz两次滴答)
        t -= TIMER_FREQ_NEW;
        if (old_timer_handler) {
            old_timer_handler();
        }
    }
    // 启用中断
    enable();
}
```

### 3. 设置和恢复中断向量
使用 DOS 提供的 `getvect` 和 `setvect` 函数来管理中断向量。
```c
void set_interrupt(void) {
    disable();
    old_timer_handler = getvect(0x1C);
    setvect(0x1C, new_timer_handler);
    set_timer(50); // 将PIT设置为50Hz
    enable();
}

![](img/671902fd1b0d9aac742cd0b899d31d5c_26.png)

void reset_interrupt(void) {
    disable();
    setvect(0x1C, old_timer_handler);
    reset_timer(); // 将PIT恢复为默认的18.206Hz
    enable();
}
```

![](img/671902fd1b0d9aac742cd0b899d31d5c_28.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_30.png)

### 4. 编程 PIT 芯片
PIT 的计数器 0 由端口 `0x40`（数据）和 `0x43`（控制字）控制。我们需要将其设置为模式 3（方波发生器），并写入新的计数值。
```c
#define TIMER_PORT0 0x40
#define TIMER_MODE_CTRL 0x43
#define PIT_CLOCK_RATE 1193181L // PIT 的基准时钟频率

![](img/671902fd1b0d9aac742cd0b899d31d5c_32.png)

void set_timer(unsigned int hz) {
    unsigned int counter = (unsigned int)(PIT_CLOCK_RATE / hz);
    // 设置控制字：选择计数器0，读写低/高字节，模式3，二进制计数
    outportb(TIMER_MODE_CTRL, 0x36);
    // 写入计数值（先低字节，后高字节）
    outportb(TIMER_PORT0, counter & 0xFF);
    outportb(TIMER_PORT0, (counter >> 8) & 0xFF);
}

![](img/671902fd1b0d9aac742cd0b899d31d5c_34.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_36.png)

void reset_timer(void) {
    // 恢复为默认的18.206 Hz (计数值为0，相当于65536)
    outportb(TIMER_MODE_CTRL, 0x36);
    outportb(TIMER_PORT0, 0);
    outportb(TIMER_PORT0, 0);
}
```
公式 `counter = PIT_CLOCK_RATE / desired_hz` 计算了产生指定频率所需的分频计数值。

---

## 第四步：主程序流程

将所有部分组合起来，主程序的逻辑如下：
1.  加载 `.rad` 音乐文件。
2.  调用 `init_player` 初始化汇编播放器。
3.  调用 `set_interrupt` 安装新的 50Hz 定时器中断处理程序。此后，音乐将在后台自动播放。
4.  主程序可以进入一个循环，等待用户按下退出键（如 ESC）。
5.  用户退出时，调用 `end_player` 停止音乐，调用 `reset_interrupt` 恢复系统定时器，并释放内存。

![](img/671902fd1b0d9aac742cd0b899d31d5c_38.png)

一个有趣的扩展是，在设置好中断后，可以调用 `system("COMMAND.COM");` 来启动一个 DOS 命令行外壳。这样，你可以在音乐背景下执行其他命令，直到退出命令行，程序才会继续执行清理步骤并结束。

![](img/671902fd1b0d9aac742cd0b899d31d5c_40.png)

---

![](img/671902fd1b0d9aac742cd0b899d31d5c_42.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_44.png)

## 总结

![](img/671902fd1b0d9aac742cd0b899d31d5c_46.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_48.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_50.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_52.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_54.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_56.png)

本节课中我们一起学习了如何在 MS-DOS 环境下创建一个 Adlib 背景音乐播放器。我们首先利用 Reality Adlib Tracker 工具生成小巧的 `.rad` 音乐文件，然后通过 C 语言程序加载这些文件。接着，我们集成了现成的汇编语言播放器例程。最后，为了实现精确的 50Hz 播放，我们深入探讨了 PC 的可编程中断定时器系统，编写了中断服务程序，并编程了 8253 PIT 芯片以改变系统定时器的频率。

![](img/671902fd1b0d9aac742cd0b899d31d5c_58.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_60.png)

![](img/671902fd1b0d9aac742cd0b899d31d5c_62.png)

通过本教程，你掌握了将硬件定时、中断处理和外部汇编模块结合到 C 程序中的方法，为创建具有背景音乐的 DOS 游戏或演示程序奠定了基础。你可以尝试使用 Reality Adlib Tracker 创作自己的音乐，并利用这个播放器框架将其融入你的项目中。