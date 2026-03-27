# 040：AdLib OPL2旋律编程教程

![](img/6fcfce86e56c5fbc5593c5681c169ec6_1.png)

在本节课中，我们将学习如何为AdLib OPL2合成器编写旋律程序。我们将定义乐器结构，实现音符播放功能，并将PC键盘的一部分映射为钢琴键盘，从而创建一个简单的旋律合成器。

## 概述

![](img/6fcfce86e56c5fbc5593c5681c169ec6_3.png)

上一节我们实现了一个基于OPL2合成器的简单鼓机。本节我们将专注于旋律编程。我们将使用OPL2的旋律模式，该模式提供6个旋律声道和5个打击乐声道。核心任务是创建一个可以定义乐器和播放音符的系统。

## 核心概念与准备工作

![](img/6fcfce86e56c5fbc5593c5681c169ec6_5.png)

首先，我们需要理解OPL2合成器的几个核心概念。所有声音都基于一个正弦波，该波形以只读方式存储了四分之一波，并通过镜像复制来生成完整的波形。

![](img/6fcfce86e56c5fbc5593c5681c169ec6_7.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_9.png)

声音的包络由**ADSR（Attack, Decay, Sustain, Release）** 生成器控制。`Key On`和`Key Off`信号触发包络的不同阶段。特别地，**Sustain（持续）位** 决定了在按下琴键期间是否使用持续电平。如果该位关闭，则在衰减阶段后会立即进入释放阶段，类似于钢琴上不踩下延音踏板的效果。

![](img/6fcfce86e56c5fbc5593c5681c169ec6_11.png)

此外，调制器（Modulator）可以调制载波器（Carrier），产生频率调制（FM）效果。**颤音（Vibrato）** 和**震音（Tremolo/Amplitude Modulation）** 功能可以进一步丰富音色。

![](img/6fcfce86e56c5fbc5593c5681c169ec6_13.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_15.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_17.png)

为了编程，我们需要定义一些数据结构和查找表。

![](img/6fcfce86e56c5fbc5593c5681c169ec6_19.png)

### 定义声道与振荡器映射

![](img/6fcfce86e56c5fbc5593c5681c169ec6_21.png)

在旋律模式下，6个旋律声道各自使用两个特定的振荡器（操作器）。以下是它们的映射关系，我们将用一个二维数组来存储：

![](img/6fcfce86e56c5fbc5593c5681c169ec6_23.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_25.png)

```c
static int adlib_voice_oscillators[6][2] = {
    {0x00, 0x03}, // 声道 0 使用振荡器 1 和 4
    {0x01, 0x04}, // 声道 1 使用振荡器 2 和 5
    {0x02, 0x05}, // 声道 2 使用振荡器 3 和 6
    {0x08, 0x0B}, // 声道 3 使用振荡器 7 和 10
    {0x09, 0x0C}, // 声道 4 使用振荡器 8 和 11
    {0x0A, 0x0D}  // 声道 5 使用振荡器 9 和 12
};
```

### 定义音符频率表

OPL2使用一个10位的频率数值（F-Number）和一个3位的区块数值（Block Number，代表八度）来确定音高。我们可以预先计算一个八度内所有半音的频率数值。计算公式基于标准音高A4（440Hz）。这里我们计算出一个八度的频率数值：

```c
unsigned int adlib_notes[12] = {
    172, 182, 193, 205, 217, // C, C#, D, D#, E
    230, 243, 258, 273, 290, // F, F#, G, G#, A
    307, 325 // A#, B
};
```

通过改变区块数值，我们可以用这组数据生成所有八度的音符。

![](img/6fcfce86e56c5fbc5593c5681c169ec6_27.png)

## 定义乐器结构

![](img/6fcfce86e56c5fbc5593c5681c169ec6_29.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_31.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_33.png)

一个乐器由两个振荡器（载波器和调制器）及其连接方式定义。我们首先定义单个振荡器的参数结构：

```c
typedef struct {
    unsigned char attack_rate;
    unsigned char decay_rate;
    unsigned char sustain_level;
    unsigned char release_rate;
    unsigned char tremolo;      // 振幅调制（震音）
    unsigned char vibrato;      // 频率调制（颤音）
    unsigned char sustaining;   // 持续开关
    unsigned char key_scale;    // 键位缩放
    unsigned char total_level;  // 总电平（衰减）
    unsigned char wave_select;  // 波形选择
} Adlib_Oscillator;
```

接着，定义完整的乐器结构，包含两个振荡器和一些全局设置：

```c
typedef struct {
    Adlib_Oscillator modulator; // 调制器
    Adlib_Oscillator carrier;   // 载波器
    unsigned char connection;   // 连接方式：0为FM合成，1为加法合成
    unsigned char feedback;     // 反馈强度（0-7）
} Adlib_Instrument;
```

## 实现设置乐器函数

![](img/6fcfce86e56c5fbc5593c5681c169ec6_35.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_36.png)

`adlib_set_instrument` 函数接收一个乐器结构和一个声道号，并将所有参数写入对应的OPL2寄存器。这涉及大量的位操作。

以下是设置攻击率（Attack Rate）和衰减率（Decay Rate）的示例代码：

![](img/6fcfce86e56c5fbc5593c5681c169ec6_38.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_40.png)

```c
void adlib_set_instrument(Adlib_Instrument *instrument, int voice) {
    // 获取该声道对应的两个振荡器编号
    int op0 = adlib_voice_oscillators[voice][0]; // 调制器
    int op1 = adlib_voice_oscillators[voice][1]; // 载波器

    // 设置调制器的攻击率和衰减率（寄存器 0x20 - 0x35）
    unsigned char value = (instrument->modulator.attack_rate & 0xF) << 4;
    value |= (instrument->modulator.decay_rate & 0xF);
    adlib_register(0x20 + op0, value);

    // 设置载波器的攻击率和衰减率
    value = (instrument->carrier.attack_rate & 0xF) << 4;
    value |= (instrument->carrier.decay_rate & 0xF);
    adlib_register(0x20 + op1, value);

    // ... 类似地设置 Sustain Level & Release Rate (0x40-0x55),
    // Tremolo/Vibrato/Sustain/KSR/Multi (0x60-0x75),
    // Key Scale & Total Level (0x80-0x95),
    // Waveform Select (0xE0-0xF5) 等寄存器
}
```

![](img/6fcfce86e56c5fbc5593c5681c169ec6_42.png)

我们需要为每个振荡器重复此过程，设置所有相关参数，如持续电平、释放率、震音、颤音、键位缩放、总电平和波形选择。

![](img/6fcfce86e56c5fbc5593c5681c169ec6_44.png)

最后，设置声道的连接方式和反馈参数（寄存器 0xC0 - 0xC8）：

```c
    unsigned char value = (instrument->feedback & 0x7) << 1;
    value |= (instrument->connection & 0x1);
    adlib_register(0xC0 + voice, value);
```

## 实现播放音符函数

`adlib_play_note` 函数负责在指定声道上播放一个音符。它需要处理频率设置和 `Key On/Off` 事件。

为了在关闭一个音符时不改变其音高，我们需要记录每个声道当前正在播放的音符频率。我们使用一个静态数组来存储这些信息：

```c
static unsigned int current_note_freq[6] = {0};
```

函数逻辑如下：
1.  首先，发送 `Key Off` 信号来停止当前音符（如果正在播放）。这通过清除对应寄存器中的 `Key On` 位（第5位）来实现，同时保留原有的区块和高位频率数值。
2.  然后，设置新的频率数值（F-Number）到低位和高位寄存器。
3.  最后，发送 `Key On` 信号，并结合新区块数值写入高位寄存器，同时更新 `current_note_freq` 数组。

```c
void adlib_play_note(int voice, unsigned int note) {
    // 1. Key Off: 关闭当前音符，保持原有音高
    unsigned char key_off_value = (current_note_freq[voice] >> 8) & 0x1F; // 清除Key On位
    adlib_register(0xB0 + voice, key_off_value);

    // 2. 设置新的频率数值（低8位）
    adlib_register(0xA0 + voice, note & 0xFF);

    // 3. Key On: 结合区块数值（例如第3八度）和频率高2位，并设置Key On位
    unsigned char block = 3; // 示例：使用第3八度
    unsigned char key_on_value = 0x20; // Key On 位
    key_on_value |= (block << 2);
    key_on_value |= ((note >> 8) & 0x03);
    adlib_register(0xB0 + voice, key_on_value);

    // 4. 记录当前音符频率
    current_note_freq[voice] = note;
}
```

## 创建键盘映射与主程序逻辑

现在，我们将PC键盘的一部分键映射为一个八度的钢琴键盘（12个半音）。我们使用以下键位布局，模拟钢琴的白键和黑键：

```
A  -> C
W  -> C#
S  -> D
E  -> D#
D  -> E
F  -> F
T  -> F#
G  -> G
Y  -> G#
H  -> A
U  -> A#
J  -> B
```

在主循环中，我们检测按键，并将按下的键映射到对应的音符频率数值（结合之前计算的 `adlib_notes` 和区块数值）。然后，我们需要一个简单的声部管理策略来将音符分配给6个可用的旋律声道。

一个简单的方法是使用一个循环计数器，依次将新音符分配给下一个可用声道，实现基本的复音效果：

```c
int current_voice = 0;
// ... 在按键处理循环内 ...
if (key_pressed) {
    unsigned int note_freq = adlib_notes[note_index] | (block << 10);
    adlib_play_note(current_voice, note_freq);
    current_voice = (current_voice + 1) % 6; // 循环使用6个声道
}
```

我们还可以在屏幕上显示当前哪些声道正在播放音符，例如用 `#` 号表示。

## 定义示例乐器并初始化

在程序开始时，我们需要定义并设置一个乐器。以下是一个示例乐器配置，它创建了一个具有FM合成特性的音色：

```c
Adlib_Instrument my_instrument;

// 配置载波器
my_instrument.carrier.tremolo = 1;
my_instrument.carrier.vibrato = 1;
my_instrument.carrier.sustaining = 1;
my_instrument.carrier.attack_rate = 0x06;
my_instrument.carrier.decay_rate = 0x06;
my_instrument.carrier.sustain_level = 0x0F;
my_instrument.carrier.release_rate = 0x06;
my_instrument.carrier.total_level = 0x00;
my_instrument.carrier.wave_select = 0; // 正弦波

// 配置调制器（参数类似，数值可不同以产生不同效果）
my_instrument.modulator.tremolo = 0;
// ... 设置其他调制器参数 ...

![](img/6fcfce86e56c5fbc5593c5681c169ec6_46.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_48.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_50.png)

// 设置连接方式为FM合成（0），反馈强度为0
my_instrument.connection = 0;
my_instrument.feedback = 0;

![](img/6fcfce86e56c5fbc5593c5681c169ec6_52.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_54.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_55.png)

// 为所有6个声道设置这个乐器
for (int i = 0; i < 6; i++) {
    adlib_set_instrument(&my_instrument, i);
}
```

![](img/6fcfce86e56c5fbc5593c5681c169ec6_57.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_59.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_61.png)

你可以通过调整这些参数（如攻击/释放时间、波形、反馈强度等）来创造各种各样的音色，例如风琴、钢琴或更电子化的声音。

![](img/6fcfce86e56c5fbc5593c5681c169ec6_63.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_65.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_67.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_69.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_71.png)

## 总结

本节课我们一起学习了AdLib OPL2合成器的旋律编程。我们从理解ADSR包络和FM合成等核心概念开始，然后逐步实现了定义乐器结构、设置合成器参数、播放音符以及管理多声道复音的功能。通过将PC键盘映射为钢琴键盘，我们创建了一个可以交互演奏的简单旋律合成器。

![](img/6fcfce86e56c5fbc5593c5681c169ec6_73.png)

![](img/6fcfce86e56c5fbc5593c5681c169ec6_75.png)

你现在已经掌握了使用OPL2芯片进行音乐编程的基础知识。可以在此基础上进行扩展，例如实现更复杂的声部管理、读取音乐文件、或者创建一个图形化的乐器编辑器。