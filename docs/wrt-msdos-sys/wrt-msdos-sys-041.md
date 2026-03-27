# 041：扩展内存 (EMS) 教程 💾

在本节课中，我们将要学习如何在 MS-DOS 环境下使用扩展内存。扩展内存是一种突破早期 PC 640KB 常规内存限制的技术，允许程序访问更多内存，用于存储图形、声音或关卡数据，从而加快加载速度。

## 背景与原理

上一节我们介绍了 VGA 图形编程的基础。本节中我们来看看如何突破内存限制。

最初的 PC 使用 8088 或 8086 CPU，它们有 20 条地址线，最多可寻址 **2^20 字节**，即 1 MB 内存。其中，高端的 384 KB 被用于 ROM 扩展和 BIOS，留给程序的可用内存只有 640 KB。这是一个严重的限制。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_1.png)

后来的 80286 和 80386 CPU 将寻址能力提升到了 16 MB 和 4 GB。但对于已经存在的旧软件，640KB 的限制依然存在。为了解决这个问题，Lotus、Intel 和 Microsoft 三家公司联合制定了 **LIM 标准**，定义了所谓的“扩展内存”。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_3.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_5.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_7.png)

扩展内存通常位于一块额外的 ISA 插卡上，带有数 MB 的 RAM。这块卡通过“分页切换”技术，将内存“窗口”映射到常规内存中一个未使用的段（通常在 `D000:0000` 到 `E000:0000` 之间）。程序通过一个标准化的 API（通过中断 `0x67` 访问）与这块卡通信。你可以分配内存、释放内存，并将扩展内存中的 **16 KB 页面** 切换到常规内存的“页框”中查看。后来，MS-DOS 自带了一个名为 **EMM386** 的扩展内存管理器，它通过软件模拟硬件卡的功能，允许旧程序在 386 及以上 CPU 上使用扩展内存。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_9.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_11.png)

使用 EMS 的好处是，它可以在从第一台 PC 开始的所有系统上工作，让你能编写使用超过 640KB 内存的 DOS 程序，同时又无需处理保护模式等复杂概念。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_13.png)

## 核心功能与实现

![](img/ce8475ab5dbd1304bea8baf2cdafce72_15.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_16.png)

以下是使用扩展内存需要了解的核心功能，我们将通过代码示例来逐一讲解。

### 1. 检测 EMS 驱动程序

![](img/ce8475ab5dbd1304bea8baf2cdafce72_18.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_19.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_21.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_22.png)

首先，需要检查系统是否安装了 EMS 驱动程序。可以通过检查中断 `0x67` 的处理程序地址处是否存在特定字符串来实现。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_24.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_26.png)

```c
#define EMS_INT 0x67
#define EMS_NAME "EMMXXXX0"

![](img/ce8475ab5dbd1304bea8baf2cdafce72_28.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_30.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_32.png)

int ems_installed() {
    union REGS regs;
    struct SREGS sregs;
    regs.h.ah = 0x35; // DOS 功能号：获取中断向量
    regs.h.al = EMS_INT;
    intdosx(®s, ®s, &sregs); // 调用 DOS 中断
    // 检查中断处理程序地址处是否有 “EMMXXXX0” 字符串
    if (memcmp(MK_FP(sregs.es, 0x0A), EMS_NAME, strlen(EMS_NAME)) == 0) {
        return 1; // 已安装
    }
    return 0; // 未安装
}
```

![](img/ce8475ab5dbd1304bea8baf2cdafce72_34.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_36.png)

### 2. 获取 EMS 版本

![](img/ce8475ab5dbd1304bea8baf2cdafce72_38.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_40.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_42.png)

获取驱动程序的版本号，返回值是二进制编码的十进制数。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_44.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_46.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_48.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_50.png)

```c
int ems_version() {
    union REGS regs;
    regs.h.ah = 0x46; // 功能号：获取版本
    int86(EMS_INT, ®s, ®s);
    if (regs.h.ah != 0) return -1; // 出错
    // 将 BCD 码转换为十进制数
    int version = ((regs.h.al >> 4) * 10) + (regs.h.al & 0x0F);
    return version;
}
```

![](img/ce8475ab5dbd1304bea8baf2cdafce72_52.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_54.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_56.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_58.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_60.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_62.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_63.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_65.png)

### 3. 查询页面信息

扩展内存以 16 KB 的“页面”为单位进行管理。需要查询系统总共有多少页面，以及有多少空闲页面。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_67.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_69.png)

```c
// 获取总页面数
int ems_page_count() {
    union REGS regs;
    regs.h.ah = 0x42; // 功能号：获取未分配页面数
    int86(EMS_INT, ®s, ®s);
    if (regs.h.ah != 0) return -1; // 出错
    return regs.x.dx; // 总页面数在 DX 中
}

![](img/ce8475ab5dbd1304bea8baf2cdafce72_71.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_73.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_75.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_77.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_78.png)

// 获取空闲页面数
int ems_free_pages() {
    union REGS regs;
    regs.h.ah = 0x42;
    int86(EMS_INT, ®s, ®s);
    if (regs.h.ah != 0) return -1;
    return regs.x.bx; // 空闲页面数在 BX 中
}
```

![](img/ce8475ab5dbd1304bea8baf2cdafce72_80.png)

### 4. 获取页框段地址

页框是常规内存中用于映射扩展内存页面的窗口，通常是一个段地址。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_82.png)

```c
unsigned short ems_frame_segment() {
    union REGS regs;
    regs.h.ah = 0x41; // 功能号：获取页框基地址
    int86(EMS_INT, ®s, ®s);
    if (regs.h.ah != 0) return 0; // 出错
    return regs.x.bx; // 段地址在 BX 中
}
```

### 5. 分配与释放内存

程序需要先分配一定数量的页面，并获得一个用于标识该内存块的“句柄”。

```c
// 分配页面
int ems_allocate(int num_pages) {
    union REGS regs;
    regs.h.ah = 0x43; // 功能号：分配页面
    regs.x.bx = num_pages; // BX = 请求的页面数
    int86(EMS_INT, ®s, ®s);
    if (regs.h.ah != 0) return -1; // 出错
    return regs.x.dx; // 返回分配句柄 (在 DX 中)
}

// 释放页面
int ems_free(int handle) {
    union REGS regs;
    regs.h.ah = 0x45; // 功能号：释放页面
    regs.x.dx = handle; // DX = 要释放的句柄
    int86(EMS_INT, ®s, ®s);
    return (regs.h.ah == 0) ? 0 : -1; // 返回成功或失败
}
```
**重要提示**：与常规的 `malloc` 不同，EMS 分配的内存不会在程序退出时自动释放。你必须显式调用 `ems_free`，否则会造成内存泄漏。

### 6. 映射页面

这是核心操作：将扩展内存中已分配的逻辑页面，映射到页框中的物理页窗口（共4个，编号0-3），使其在常规内存中可见。

```c
int ems_map(int handle, int logical_page, int physical_page) {
    union REGS regs;
    regs.h.ah = 0x44; // 功能号：映射页面
    regs.h.al = physical_page; // AL = 物理页号 (0-3)
    regs.x.bx = logical_page;  // BX = 逻辑页号
    regs.x.dx = handle;        // DX = 句柄
    int86(EMS_INT, ®s, ®s);
    return (regs.h.ah == 0) ? 0 : -1; // 返回成功或失败
}
```

![](img/ce8475ab5dbd1304bea8baf2cdafce72_84.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_86.png)

### 7. 访问映射内存

映射后，可以通过页框段地址和偏移量来访问数据。以下是一个辅助宏，用于获取指向特定物理页的远指针。

```c
#define EMS_PAGE_SIZE 16384 // 16 KB
unsigned short ems_frame_seg; // 存储获取到的页框段地址

#define EMS_PAGE(n) (MK_FP(ems_frame_seg, (n) * EMS_PAGE_SIZE))
// 使用示例：将数据复制到物理页0
_fmemcpy(EMS_PAGE(0), source_data, copy_size);
```

![](img/ce8475ab5dbd1304bea8baf2cdafce72_88.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_90.png)

## 实战示例：加载并播放动画

让我们将这些功能组合起来，实现一个将多帧动画图片加载到 EMS 内存，然后循环播放的程序。

以下是程序的主要步骤：

1.  **初始化与检测**：检查 EMS 驱动，获取版本和页面信息。
2.  **分配内存**：根据动画帧数和每帧大小（例如 VGA Mode 13h 的 64000 字节），计算所需页面数并进行分配。
3.  **加载数据**：循环读取每个动画帧文件（如 GIF），解码后，通过映射和内存拷贝，将像素数据存入 EMS 中预先分配好的连续逻辑页面。
    *   优化技巧：可以一次映射4个连续的物理页，然后执行一次大内存拷贝，而不是每16KB映射拷贝一次。
4.  **播放循环**：
    *   进入图形模式。
    *   对于每一帧动画：
        *   将该帧对应的 EMS 逻辑页面映射到物理页窗口。
        *   使用 `_fmemcpy` 将数据从页框（如 `EMS_PAGE(0)`）快速拷贝到 VGA 显示内存（`0xA000:0000`）。
        *   设置该帧的调色板。
        *   延时以控制帧率。
5.  **清理退出**：播放完毕或用户中断后，释放所有分配的 EMS 内存，并返回文本模式。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_92.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_94.png)

通过这种方式，即使动画数据总量远超 640KB，也能在传统的 DOS PC 上流畅预加载和播放，避免了播放时从磁盘实时解码的缓慢过程。

## 总结

![](img/ce8475ab5dbd1304bea8baf2cdafce72_96.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_98.png)

本节课中我们一起学习了 MS-DOS 扩展内存的基本概念和使用方法。

扩展内存通过一个位于常规内存 `D000:0000` 附近的**页框窗口**和**分页切换**机制工作，兼容从 8088 到 386 的所有 PC。其驱动程序可通过中断 `0x67` 的向量地址处是否存在 **“EMMXXXX0”** 字符串来检测。核心操作遵循固定模式：将功能号放入 `AH` 寄存器，参数放入其他寄存器，然后调用中断 `0x67`。我们必须管理**内存的分配、映射和释放**，并特别注意**手动释放**已分配的内存以防止泄漏。

![](img/ce8475ab5dbd1304bea8baf2cdafce72_100.png)

![](img/ce8475ab5dbd1304bea8baf2cdafce72_102.png)

利用 EMS，我们可以为 DOS 程序赋予访问数 MB 内存的能力，非常适合用于存储游戏中的大型资源，是 DOS 时代突破内存瓶颈的关键技术之一。