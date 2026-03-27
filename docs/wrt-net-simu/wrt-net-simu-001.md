# 001：C语言编写网络安全网络映射器

![](img/d2acdde8394d43c332a2e6f3f3cf9289_0.png)

![](img/d2acdde8394d43c332a2e6f3f3cf9289_0.png)

在本节课中，我们将要学习如何使用C语言编写一个基础的网络映射器。网络映射器是一种安全工具，用于扫描网络，发现活跃的主机及其开放的端口。我们将从最简单的版本开始，逐步构建一个功能完整的工具。

## 概述

网络映射器，有时也称为端口扫描器，是系统管理员和安全人员用来检查网络安全的工具。它可以扫描指定IP地址范围内的主机，尝试连接到特定的TCP端口（如SSH的22端口），并记录哪些端口是开放的。本节课我们将创建一个名为“Nema”的网络映射器。

## 项目结构与准备

在开始编码之前，我们需要建立项目结构并准备一些工具库。我们将创建一个名为 `birchutils` 的通用工具库，用于存放项目中反复使用的函数。

### 创建工具库

首先，我们为工具库创建一个 `Makefile` 文件，用于编译和构建库。

```makefile
# birchutils/Makefile
CFLAGS = -O3 -Wall -std=c2x
LDFLAGS = -shared -fPIC

birchutils.o: birchutils.c
	$(CC) $(CFLAGS) -c birchutils.c -o birchutils.o

birchutils.so: birchutils.o
	$(CC) $(CFLAGS) $(LDFLAGS) birchutils.o -o birchutils.so

all: clean birchutils.so

clean:
	rm -f birchutils.o birchutils.so
```

### 创建网络映射器项目

接下来，我们为网络映射器项目创建目录和 `Makefile`。我们将开发多个版本，因此需要组织好代码结构。

```makefile
# netmap/Makefile
CFLAGS = -O3 -Wall -std=c2x
LDFLAGS =

netmap_naive.o: netmap_naive.c
	$(CC) $(CFLAGS) -c netmap_naive.c -o netmap_naive.o

netmap_naive: netmap_naive.o
	$(CC) $(CFLAGS) netmap_naive.o -o netmap_naive $(LDFLAGS)

all: clean netmap_naive

clean:
	rm -f netmap_naive.o netmap_naive
```

## 编写基础代码框架

现在，让我们开始编写网络映射器的基础代码。我们将首先处理命令行参数，并定义一些必要的数据类型和全局变量。

### 包含头文件与类型定义

在 `netmap_naive.c` 文件中，我们首先包含必要的头文件，并定义一些常用的数据类型。

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include "netmap.h"

typedef unsigned char u8;
typedef unsigned short u16;
typedef unsigned int u32;
```

### 处理命令行参数

我们的工具需要接受命令行参数，包括端口号、起始IP地址和结束IP地址。以下是处理这些参数的代码。

```c
int main(int argc, char *argv[]) {
    if (argc < 2) {
        fprintf(stderr, "Usage: %s <port> [start_ip] [end_ip]\n", argv[0]);
        fprintf(stderr, "If only port is provided, IPs will be read from stdin.\n");
        fprintf(stderr, "If start_ip is provided without end_ip, scan to 255.255.255.255.\n");
        return -1;
    }

    u16 port = (u16)atoi(argv[1]);
    struct in_addr current_ip, end_ip;

    // 解析起始IP地址
    if (argc >= 3) {
        if (inet_aton(argv[2], &current_ip) == 0) {
            fprintf(stderr, "Invalid start IP address.\n");
            return -1;
        }
    } else {
        // 如果没有提供起始IP，则从标准输入读取
        // 此处留待后续实现
    }

    // 解析结束IP地址
    if (argc >= 4) {
        if (inet_aton(argv[3], &end_ip) == 0) {
            fprintf(stderr, "Invalid end IP address.\n");
            return -1;
        }
    } else {
        // 如果没有提供结束IP，则默认扫描到255.255.255.255
        end_ip.s_addr = 0xFFFFFFFF;
    }

    // 简单的范围检查
    if (current_ip.s_addr > end_ip.s_addr) {
        fprintf(stderr, "Start IP must be less than or equal to end IP.\n");
        return -1;
    }

    printf("Scanning port %d from %s to %s\n", 
           port, 
           inet_ntoa(current_ip), 
           inet_ntoa(end_ip));

    // 后续扫描逻辑将在这里实现
    return 0;
}
```

### IP地址生成器

为了遍历IP地址范围，我们需要一个生成器函数。我们将使用全局变量来跟踪当前IP地址。

```c
// 全局变量，存储当前和结束IP地址
struct in_addr current_ip;
struct in_addr end_ip;

// IP地址生成器函数
struct in_addr generate_ip() {
    struct in_addr next_ip = current_ip;

    // 如果当前IP已经达到或超过结束IP，则返回0表示结束
    if (current_ip.s_addr >= end_ip.s_addr) {
        next_ip.s_addr = 0;
        return next_ip;
    }

    // 将当前IP地址加1
    current_ip.s_addr = htonl(ntohl(current_ip.s_addr) + 1);
    return next_ip;
}
```

## 实现简单的扫描逻辑

上一节我们介绍了如何解析参数和生成IP地址，本节中我们来看看如何实现最基础的扫描逻辑。我们将使用阻塞式套接字，逐个尝试连接目标IP和端口。

### 扫描单个IP地址

首先，我们编写一个函数，用于尝试连接指定的IP地址和端口。

```c
int scan_single_ip(struct in_addr ip, u16 port) {
    int sockfd;
    struct sockaddr_in target_addr;

    // 创建TCP套接字
    sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd < 0) {
        perror("Socket creation failed");
        return -1;
    }

    // 设置目标地址结构
    target_addr.sin_family = AF_INET;
    target_addr.sin_port = htons(port);
    target_addr.sin_addr = ip;

    // 设置连接超时（可选，但建议）
    struct timeval timeout;
    timeout.tv_sec = 2; // 2秒超时
    timeout.tv_usec = 0;
    setsockopt(sockfd, SOL_SOCKET, SO_SNDTIMEO, &timeout, sizeof(timeout));

    // 尝试连接
    if (connect(sockfd, (struct sockaddr *)&target_addr, sizeof(target_addr)) < 0) {
        close(sockfd);
        return 0; // 连接失败，端口可能关闭
    }

    // 连接成功，端口开放
    printf("Port %d is OPEN on %s\n", port, inet_ntoa(ip));
    close(sockfd);
    return 1; // 端口开放
}
```

### 主扫描循环

接下来，我们在 `main` 函数中集成IP生成器和扫描函数，实现主扫描循环。

```c
int main(int argc, char *argv[]) {
    // ... [之前的参数解析和初始化代码] ...

    printf("Starting naive network mapper scan...\n");

    int open_ports = 0;
    struct in_addr target_ip;

    // 初始化当前IP
    current_ip = start_ip; // 假设start_ip已从参数解析

    while (1) {
        target_ip = generate_ip();
        if (target_ip.s_addr == 0) {
            break; // 生成器返回0，表示扫描结束
        }

        if (scan_single_ip(target_ip, port) > 0) {
            open_ports++;
        }
    }

    printf("Scan completed. Found %d open ports.\n", open_ports);
    return 0;
}
```

## 总结

本节课中我们一起学习了如何使用C语言编写一个基础版的网络映射器。我们完成了以下工作：

1.  **建立项目结构**：创建了工具库和主项目的 `Makefile`。
2.  **处理命令行参数**：使工具能够接受端口号、起始和结束IP地址作为输入。
3.  **实现IP地址生成器**：编写了函数来遍历指定的IP地址范围。
4.  **实现基础扫描逻辑**：使用阻塞式套接字逐个尝试连接目标主机的指定端口，并报告开放端口。

这个初始版本非常简单，并且由于使用阻塞式调用和顺序扫描，速度会很慢。然而，它为我们理解网络扫描的基本原理奠定了坚实的基础。在接下来的课程中，我们将逐步改进这个工具，引入非阻塞套接字、多线程等技术，使其变得更高效、更实用。

![](img/d2acdde8394d43c332a2e6f3f3cf9289_2.png)

---
![](img/d2acdde8394d43c332a2e6f3f3cf9289_2.png)