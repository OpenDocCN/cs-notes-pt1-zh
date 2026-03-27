# 002：用C编写简单的网络映射器

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_1.png)

在本节课中，我们将继续构建一个简易的网络映射器。这个工具的主要功能是发现网络上的服务器和节点，并检查其服务的版本等信息。上一节我们完成了基础框架，本节我们将实现IP地址生成和TCP连接功能。

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_3.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_5.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_7.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_9.png)

## 概述与回顾

上一节我们创建了主函数，它接收一个端口号、一个起始IP地址和一个结束IP地址作为参数。程序将扫描这个IP地址范围。如果省略结束IP地址，则只扫描起始地址所在的子网。如果两个IP地址都省略，程序将从标准输入读取IP地址。

## 实现IP地址生成器

首先，我们需要完善`generate`函数，使其能够处理从标准输入读取IP地址的情况。

以下是`generate`函数的核心逻辑，当起始和结束地址均为0时，从标准输入读取：

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_11.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_13.png)

```c
in_addr_t generate(...) {
    if (current == 0 && ending == 0) {
        // 从标准输入读取IP地址
        in8 buff[16];
        zero(buff, 16);
        fgets((char*)buff, 15, stdin);
        // ... 处理buff，转换为in_addr_t并返回
    }
    // ... 原有的范围生成逻辑
}
```

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_15.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_17.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_19.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_21.png)

为了辅助内存清零，我们创建了一个工具函数`zero`，并将其放入自定义的工具库`birch_utils`中。

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_23.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_25.png)

```c
// birch_utils.h 或类似头文件中
#ifndef BIRCH_UTILS_H
#define BIRCH_UTILS_H

#include <stdio.h>
#include <string.h>

typedef unsigned char in8;
typedef unsigned short in16;
typedef unsigned int in32;

void zero(in8* str, in16 size);

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_27.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_29.png)

#endif
```

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_31.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_33.png)

```c
// zero函数实现
void zero(in8* str, in16 size) {
    in16 n = 0;
    in8* p = str;
    while (n < size) {
        *p = 0;
        p++;
        n++;
    }
}
```

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_35.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_36.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_38.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_40.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_42.png)

## 实现TCP连接功能

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_44.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_46.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_48.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_50.png)

有了IP地址生成器，下一步是实现一个函数来尝试连接到给定的IP地址和端口。

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_52.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_54.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_56.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_58.png)

我们创建一个名为`tcp_connect`的函数，它接收一个`in_addr_t`类型的IP地址和一个`in16`类型的端口号。

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_60.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_61.png)

以下是该函数的基本步骤：

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_63.png)

1.  创建套接字。
2.  填充`sockaddr_in`结构体。
3.  尝试连接。
4.  如果连接成功，则尝试读取服务横幅（banner）；如果失败，则关闭套接字并返回。

```c
bool tcp_connect(in_addr_t ip, in16 port) {
    int s = socket(AF_INET, SOCK_STREAM, 0);
    if (s < 0) {
        // 处理套接字创建失败
        return false;
    }

    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(port);
    addr.sin_addr.s_addr = ip;

    int ret = connect(s, (struct sockaddr*)&addr, sizeof(addr));
    if (ret != 0) {
        close(s);
        return false; // 连接失败
    }

    // 连接成功，读取横幅
    read_header(s, ip);
    close(s);
    return true;
}
```

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_65.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_67.png)

## 读取服务横幅

连接建立后，我们尝试读取服务可能发送的初始数据（例如SSH或HTTP服务的欢迎信息），这有助于识别服务类型和版本。

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_69.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_71.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_72.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_74.png)

我们创建一个`read_header`函数来执行此操作：

```c
void read_header(int s, in_addr_t ip) {
    in8 buff[256];
    zero(buff, 256);
    int i = read(s, buff, 255);

    if (i < 1) {
        // 没有读到数据，只打印IP地址
        printf("IP: %x\n", ip);
    } else {
        // 处理读取到的数据，例如去除换行符
        in8* p = buff + i - 1;
        if (*p == '\n' || *p == '\r') {
            *p = 0;
        }
        printf("IP: %x | Header: %s\n", ip, buff);
    }
}
```

## 整合主循环

最后，我们在主函数中整合所有部分，形成一个完整的扫描循环。

```c
int main(int argc, char* argv[]) {
    // ... 参数解析，获取端口、起始IP、结束IP
    in_addr_t ip;
    while ((ip = generate(...)) != 0) {
        tcp_connect(ip, port);
    }
    return 0;
}
```

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_76.png)

## 测试与展望

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_78.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_80.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_82.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_84.png)

现在，我们的简易网络映射器已经可以工作了。我们可以通过管道将IP地址列表传递给它进行测试，例如：

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_86.png)

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_88.png)

```bash
echo "127.0.0.1" | ./naive_scanner 80
```

程序会尝试连接本机的80端口并打印结果。

当前的实现使用的是阻塞式套接字，在扫描多个地址时速度会很慢，因为每次连接尝试都会等待超时或响应。在下一节课中，我们将着手改进这一点。

## 总结

![](img/e47078a6dc7c2d22a36e3072a2a75fd0_90.png)

本节课中我们一起学习了如何完善一个简易网络映射器。我们实现了从标准输入和IP范围生成地址的逻辑，创建了进行TCP连接并尝试读取服务横幅的核心功能，并将所有模块整合到一个可以运行的程序中。虽然当前版本是“朴素”且低效的，但它为我们后续实现更快速、更强大的版本（例如使用非阻塞I/O或设置连接超时）奠定了坚实的基础。