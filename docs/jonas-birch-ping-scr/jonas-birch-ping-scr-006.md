# 006：用C语言实现以太网库

![](img/71193a65bb6b908f70e6c24891d2d8d3_0.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_2.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_4.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_0.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_5.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_7.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_9.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_11.png)

在本节课中，我们将开始实现以太网（Ethernet）协议。我们将深入到比IP层更低的网络层次。首先，我会介绍一个名为“Ping from scratch”的现有项目，它将作为我们实现的基础。该项目使用原始套接字（raw sockets）编写了一个ping工具，其中的代码非常适合用于我们的目的。

## 项目基础与设计思路

上一节我们提到了基础项目。本节中，我们来看看如何基于它构建我们的以太网库。

![](img/71193a65bb6b908f70e6c24891d2d8d3_13.png)

“Ping from scratch”项目中有一个接收IP数据包的函数，我将完全重写它。但其他部分代码很好，比如构造IP数据结构的函数。当我们有了包含IP地址等信息的结构后，可以调用 `eval_ip` 函数将其转换为能在网络上发送的格式。对于Ping协议涉及的ICMP，我也有类似的实现。

![](img/71193a65bb6b908f70e6c24891d2d8d3_15.png)

我希望为以太网实现类似的功能。我们将有一个构造函数，用于创建包含我们感兴趣信息的以太网数据结构。如果我们查看头文件，会发现每种协议都有两个数据结构：一个是只包含类型、源/目的IP地址等核心信息的“逻辑”结构；另一个是为了通过网络发送而需要的、包含更多字段的“原始”结构。`eval` 函数负责将前者转换为后者。

![](img/71193a65bb6b908f70e6c24891d2d8d3_17.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_19.png)

我的目标是为以太网实现同样的模式，并将其构建成一个简单的库，使我们能够通过直接读写网卡来发送和接收任何协议的数据包。这是总体的想法。

## 创建新项目

首先，我将复制整个“ping”项目文件夹。由于新项目将主要基于以太网（可能也涉及其他协议），我将其命名为“Ether”。

以下是创建新项目目录和重命名文件的步骤：
*   复制整个 `ping` 文件夹到新的 `ether` 文件夹。
*   清理不必要的文件。
*   将 `ping.c` 重命名为 `ether.c`。
*   将 `ping.h` 重命名为 `ether.h`。

接着，在代码编辑器中打开新项目，并修改Makefile以反映新的文件名。例如，将目标名称从 `ping` 改为 `ether`，并更新源文件和头文件的引用。

现在，开始清理 `ether.c` 文件。删除原有的 `receive_ip` 函数，因为我们将使用不同的接收方式。同时移除 `main` 函数和 `send_ping` 函数，只保留我们需要的框架。在头文件 `ether.h` 中也移除相应的函数声明。

## 设计以太网数据结构

![](img/71193a65bb6b908f70e6c24891d2d8d3_21.png)

清理完基础代码后，我们开始设计核心数据结构。

![](img/71193a65bb6b908f70e6c24891d2d8d3_23.png)

我们将定义一个结构体 `struct ether` 来表示以太网帧的逻辑视图。它需要包含以下信息：
*   一个类型变量，用于指示网络层协议（例如是IP还是其他协议）。
*   源MAC地址和目的MAC地址。
*   载荷（payload）数据及其大小。
*   帧类型（例如单播、广播等）。

我们使用 `__attribute__((packed))` 确保结构体成员紧密排列，没有填充字节。定义如下：
```c
struct __attribute__((packed)) ether {
    uint16_t type; // 协议类型，如IPv4
    mac_t dest;    // 目的MAC地址
    mac_t src;     // 源MAC地址
    uint8_t *payload; // 载荷数据指针
    size_t payload_size; // 载荷大小
    frame_type_t frame_type; // 帧类型（单播、广播等）
};
typedef struct ether ether_t;
```

接下来，我们需要定义 `frame_type_t` 枚举和 `mac_t` 类型。

`frame_type_t` 枚举定义了帧的发送方式：
```c
enum __attribute__((packed)) frame_type {
    FRAME_TYPE_NONE,     // 未设置
    FRAME_TYPE_UNICAST,  // 单播（发送给特定目标）
    FRAME_TYPE_BROADCAST,// 广播（发送给整个本地网络）
    FRAME_TYPE_MULTICAST,// 组播（发送给一组主机）
    FRAME_TYPE_UNKNOWN   // 未知（行为类似广播）
};
typedef enum frame_type frame_type_t;
```

## 实现MAC地址处理

![](img/71193a65bb6b908f70e6c24891d2d8d3_25.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_27.png)

MAC地址是48位的硬件地址，通常表示为六组十六进制数，如 `AA:BB:CC:DD:EE:FF`。在代码中，我们需要一种方式来表示和操作它。

![](img/71193a65bb6b908f70e6c24891d2d8d3_29.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_31.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_32.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_34.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_36.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_38.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_40.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_42.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_44.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_46.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_48.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_50.png)

我们定义一个结构体来存储MAC地址。由于C语言没有48位整数类型，我们使用一个64位整数，但只使用其低48位。
```c
struct __attribute__((packed)) mac {
    uint64_t addr:48; // 使用位域限定为48位
};
typedef struct mac mac_t;
```

![](img/71193a65bb6b908f70e6c24891d2d8d3_52.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_53.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_55.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_56.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_58.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_60.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_62.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_64.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_66.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_68.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_70.png)

为了方便调试和显示，我们实现一个 `show_mac` 函数，将 `mac_t` 结构体格式化为可读的字符串（采用类似Cisco的风格，如 `AABB.CCDD.EEFF`）。

![](img/71193a65bb6b908f70e6c24891d2d8d3_72.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_74.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_76.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_78.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_79.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_81.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_83.png)

函数实现思路是：从64位整数中分别提取出6个字节，然后用 `snprintf` 格式化成字符串。

![](img/71193a65bb6b908f70e6c24891d2d8d3_85.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_87.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_89.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_90.png)

更重要的是，我们需要能够从字符串或整数创建 `mac_t`。因此，我们实现两个函数：
*   `read_mac_int`: 从一个64位整数创建 `mac_t`。
*   `read_mac_str`: 从一个格式化的字符串（如 `"AABB.CCDD.EEFF"`）解析并创建 `mac_t`。

![](img/71193a65bb6b908f70e6c24891d2d8d3_92.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_94.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_96.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_98.png)

为了提供更友好的接口，我们使用C11的 `_Generic` 关键字创建一个泛型宏 `MAKE_MAC`，它能根据参数类型自动选择调用哪个函数。
```c
#define MAKE_MAC(x) _Generic((x), \
    uint64_t: read_mac_int,       \
    char*:    read_mac_str        \
)(x)
```

![](img/71193a65bb6b908f70e6c24891d2d8d3_100.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_102.png)

`read_mac_str` 函数的实现较为复杂，需要逐字节解析字符串，处理十六进制字符，并检查分隔符。我们使用一个宏来避免解析每个字节的重复代码。

![](img/71193a65bb6b908f70e6c24891d2d8d3_104.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_106.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_108.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_110.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_112.png)

在实现过程中，我们还需要一个辅助宏 `HEX`，用于将ASCII字符（‘0’-‘9’， ‘A’-‘F’， ‘a’-‘f’）转换为其对应的十六进制数值。

![](img/71193a65bb6b908f70e6c24891d2d8d3_114.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_115.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_117.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_119.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_121.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_123.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_125.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_127.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_129.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_131.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_133.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_135.png)

经过一系列编译、调试和错误修复（包括位域操作、类型转换、字符串解析逻辑等），我们最终成功实现了MAC地址的创建、解析和显示功能。

![](img/71193a65bb6b908f70e6c24891d2d8d3_136.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_138.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_140.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_142.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_144.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_145.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_147.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_149.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_151.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_153.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_155.png)

## 定义原始以太网帧结构

![](img/71193a65bb6b908f70e6c24891d2d8d3_157.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_159.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_160.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_161.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_162.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_163.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_165.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_166.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_168.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_170.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_171.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_173.png)

为了在网络上发送数据，我们需要定义原始的以太网帧结构，它对应着线缆上传输的比特序列。

![](img/71193a65bb6b908f70e6c24891d2d8d3_175.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_177.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_179.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_181.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_183.png)

根据维基百科的以太网帧格式，一个帧包括：
*   前导码（Preamble，7字节）
*   帧开始定界符（SFD，1字节）
*   目的MAC地址（6字节）
*   源MAC地址（6字节）
*   可选的802.1Q标签（4字节，可选）
*   以太网类型（EtherType，2字节，指示载荷协议）
*   载荷（Payload，46-1500字节）
*   帧校验序列（FCS，4字节）

![](img/71193a65bb6b908f70e6c24891d2d8d3_185.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_187.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_189.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_191.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_192.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_194.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_196.png)

然而，在实际编程中，通过AF_PACKET套接字或类似底层接口发送数据时，操作系统或网卡驱动通常会处理前导码、SFD和FCS。因此，我们实际需要构建的原始结构通常只包含目的地址、源地址、类型和载荷。

![](img/71193a65bb6b908f70e6c24891d2d8d3_198.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_200.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_202.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_204.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_206.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_208.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_210.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_212.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_214.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_216.png)

我们定义原始帧结构如下：
```c
struct __attribute__((packed)) raw_ether {
    mac_t dest;      // 目的MAC地址
    mac_t src;       // 源MAC地址
    uint16_t type;   // 协议类型
    uint8_t payload[]; // 柔性数组，存放载荷
};
typedef struct raw_ether raw_ether_t;
```
注意，这是一个变长结构，`payload` 的实际长度在运行时确定。

![](img/71193a65bb6b908f70e6c24891d2d8d3_218.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_220.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_222.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_224.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_226.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_228.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_230.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_232.png)

## 实现构造与评估函数

![](img/71193a65bb6b908f70e6c24891d2d8d3_234.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_236.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_238.png)

现在，我们模仿IP数据结构的处理模式，为以太网实现两个核心函数：

![](img/71193a65bb6b908f70e6c24891d2d8d3_240.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_242.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_243.png)

1.  **构造函数 `make_ether`**：根据给定的参数（目的MAC、源MAC、协议类型），创建一个逻辑上的 `ether_t` 结构体。
    ```c
    ether_t* make_ether(mac_t dest, mac_t src, uint16_t type) {
        // 分配内存
        ether_t* e = malloc(sizeof(ether_t));
        // 初始化各字段
        e->dest = dest;
        e->src = src;
        e->type = type;
        e->payload = NULL;
        e->payload_size = 0;
        e->frame_type = FRAME_TYPE_UNICAST; // 默认单播
        return e;
    }
    ```

2.  **评估函数 `eval_ether`**：将一个逻辑上的 `ether_t` 结构体，转换（序列化）为可以发送的原始字节流 `raw_ether_t`。
    ```c
    raw_ether_t* eval_ether(ether_t* e) {
        if (!e) return NULL;
        // 计算原始帧总大小：头部固定部分 + 载荷大小
        size_t total_size = sizeof(raw_ether_t) + e->payload_size;
        raw_ether_t* raw = malloc(total_size);
        // 填充头部字段
        raw->dest = e->dest;
        raw->src = e->src;
        raw->type = htons(e->type); // 注意网络字节序转换
        // 复制载荷数据
        if (e->payload && e->payload_size > 0) {
            memcpy(raw->payload, e->payload, e->payload_size);
        }
        return raw;
    }
    ```
    注意，`type` 字段需要使用 `htons` 函数转换为网络字节序（大端序）。

![](img/71193a65bb6b908f70e6c24891d2d8d3_245.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_247.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_249.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_251.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_253.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_255.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_257.png)

## 总结与下节预告

本节课中，我们一起学习了如何为以太网协议构建基础库。我们完成了以下工作：

![](img/71193a65bb6b908f70e6c24891d2d8d3_259.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_261.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_263.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_264.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_266.png)

*   **建立了项目基础**：基于已有的“Ping from scratch”项目创建了新项目“Ether”。
*   **设计了核心数据结构**：定义了表示逻辑视图的 `ether_t` 和表示原始帧的 `raw_ether_t`。
*   **实现了MAC地址处理**：创建了 `mac_t` 类型及相关函数，支持从字符串、整数创建MAC地址，并能格式化输出。
*   **实现了核心转换函数**：编写了 `make_ether` 构造函数和 `eval_ether` 评估函数，为发送以太网帧做好了数据准备。

![](img/71193a65bb6b908f70e6c24891d2d8d3_268.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_270.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_272.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_274.png)

![](img/71193a65bb6b908f70e6c24891d2d8d3_275.png)

现在，我们已经有了一个坚实的数据处理基础。在下一节课中，我们将实现关键的 **发送函数** ，学习如何通过底层网络接口将构建好的原始以太网帧数据真正发送到网络中。敬请期待！