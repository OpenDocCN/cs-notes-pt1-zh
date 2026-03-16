# 051：管道与FIFO 🚰

![](img/01642b111612d01dc2ff6ff1cc7fad99_1.png)

在本节课中，我们将学习UNIX系统中两种最古老且最常用的进程间通信（IPC）机制：管道（pipe）和命名管道（FIFO）。它们是UNIX哲学中“构建小型工具，处理文本流”理念的基础。

## 管道（Pipe）简介

上一节我们讨论了System V IPC，本节中我们来看看日常使用最频繁的IPC形式——管道。管道是一种内核数据结构，它通过提供一个读端和一个写端来实现单向通信。

![](img/01642b111612d01dc2ff6ff1cc7fad99_3.png)

创建管道的系统调用是 `pipe`，其函数原型如下：

![](img/01642b111612d01dc2ff6ff1cc7fad99_5.png)

```c
int pipe(int pipefd[2]);
```

![](img/01642b111612d01dc2ff6ff1cc7fad99_7.png)

调用 `pipe` 后，数组 `pipefd` 中的两个文件描述符将分别连接到新创建管道的读端（`pipefd[0]`）和写端（`pipefd[1]`）。但管道本身并不能直接用于进程间通信，因为它只存在于单个进程的上下文中。

## 使用管道进行进程间通信

为了进行进程间通信，我们需要另一个进程。这通常通过 `fork` 系统调用来实现。`fork` 创建的子进程是父进程的近乎完全相同的副本，因此它也继承了管道的文件描述符。

然而，管道是单向的，因此父子进程必须约定好谁负责读、谁负责写。按照惯例，每个进程会关闭它不使用的那个文件描述符端。例如，父进程关闭读端，只保留写端；子进程关闭写端，只保留读端。这样就建立了一个从父进程到子进程的单向通信通道。

![](img/01642b111612d01dc2ff6ff1cc7fad99_9.png)

![](img/01642b111612d01dc2ff6ff1cc7fad99_11.png)

![](img/01642b111612d01dc2ff6ff1cc7fad99_13.png)

以下是使用管道通信的一个简单示例，它模拟了 `cat` 命令通过管道传输数据：

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <string.h>

int main() {
    int pipefd[2];
    char buf[100];
    pid_t pid;

    pipe(pipefd); // 创建管道
    pid = fork(); // 创建子进程

    if (pid > 0) { // 父进程
        close(pipefd[0]); // 关闭读端
        printf(“Parent (PID: %d) sending message.\n”, getpid());
        write(pipefd[1], “Hello from parent!\n”, 20); // 写入管道
        close(pipefd[1]); // 关闭写端
        wait(NULL); // 等待子进程
    } else if (pid == 0) { // 子进程
        close(pipefd[1]); // 关闭写端
        printf(“Child (PID: %d) reading message.\n”, getpid());
        read(pipefd[0], buf, 100); // 从管道读取
        write(STDOUT_FILENO, buf, strlen(buf)); // 输出到标准输出
        close(pipefd[0]); // 关闭读端
    }
    return 0;
}
```

## 缓冲与执行顺序

![](img/01642b111612d01dc2ff6ff1cc7fad99_15.png)

运行上述程序时，你可能会注意到输出的顺序与代码顺序不一致。这是因为 `printf` 使用的是带缓冲的I/O，而 `write` 是无缓冲的。当标准输出连接到终端时，它是行缓冲的；但当它连接到管道时，会采用默认的完全缓冲模式，导致数据刷新时机不同。

![](img/01642b111612d01dc2ff6ff1cc7fad99_17.png)

此外，`fork` 之后，子进程和父进程的执行顺序是不确定的。但在我们的例子中，通信逻辑强制了某种顺序：子进程必须等待父进程写入数据后才能读取，而父进程在写入数据后，通过 `wait` 调用显式等待子进程完成。

如果父进程不等待子进程（注释掉 `wait(NULL)`），父进程可能先于子进程退出。此时，子进程会成为孤儿进程，并被 `init` 进程（PID 1）接管。这解释了为什么子进程有时会报告其父进程PID为1。

![](img/01642b111612d01dc2ff6ff1cc7fad99_19.png)

![](img/01642b111612d01dc2ff6ff1cc7fad99_21.png)

## 与外部程序通信：`popen`

![](img/01642b111612d01dc2ff6ff1cc7fad99_23.png)

![](img/01642b111612d01dc2ff6ff1cc7fad99_25.png)

![](img/01642b111612d01dc2ff6ff1cc7fad99_27.png)

![](img/01642b111612d01dc2ff6ff1cc7fad99_29.png)

通常，我们不想自己实现管道两端的代码，而是希望与另一个完全独立的程序通信。例如，执行一个外部程序并将数据通过管道传递给它，让它从标准输入读取数据。

UNIX C库提供了一个便捷的函数 `popen` 来处理这种常见场景。`popen` 会创建一个管道，启动一个shell来执行给定的命令，并返回一个文件流（`FILE*`），供你读取（如果命令输出）或写入（如果命令输入）。

`popen` 的函数原型如下：

![](img/01642b111612d01dc2ff6ff1cc7fad99_31.png)

![](img/01642b111612d01dc2ff6ff1cc7fad99_33.png)

```c
FILE *popen(const char *command, const char *type);
```

![](img/01642b111612d01dc2ff6ff1cc7fad99_35.png)

![](img/01642b111612d01dc2ff6ff1cc7fad99_37.png)

参数 `type` 指定了管道的方向：`”r”` 表示从命令读取，`”w”` 表示向命令写入。

![](img/01642b111612d01dc2ff6ff1cc7fad99_39.png)

以下是一个使用 `popen` 将文件内容通过管道传递给分页程序（如 `more`）的示例：

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    FILE *fp, *pager_fp;
    char buf[1024];
    char *pager = getenv(“PAGER”); // 从环境变量获取分页程序
    if (!pager) pager = “/usr/bin/more”; // 默认使用 more

    if (argc != 2) {
        fprintf(stderr, “Usage: %s <file>\n”, argv[0]);
        exit(1);
    }

    fp = fopen(argv[1], “r”);
    if (!fp) {
        perror(“fopen”);
        exit(1);
    }

    // 使用 popen 打开分页程序，准备向其写入数据
    pager_fp = popen(pager, “w”);
    if (!pager_fp) {
        perror(“popen”);
        exit(1);
    }

    while (fgets(buf, sizeof(buf), fp) != NULL) {
        fputs(buf, pager_fp); // 将文件内容写入管道，即分页程序的标准输入
    }

    fclose(fp);
    pclose(pager_fp); // 关闭管道，等待命令结束
    return 0;
}
```

![](img/01642b111612d01dc2ff6ff1cc7fad99_41.png)

**重要安全警告**：`popen` 通过shell执行命令，这意味着如果命令字符串来自不可信的输入（如用户），则可能造成命令注入漏洞。攻击者可以通过注入shell元字符（如 `;`， `|`， `&`）来执行任意命令。因此，在生产代码中应避免使用 `popen`，或对其输入进行严格的验证和清理。一些系统（如NetBSD）提供了更安全的变体 `popenve`。

![](img/01642b111612d01dc2ff6ff1cc7fad99_43.png)

## 命名管道（FIFO）

![](img/01642b111612d01dc2ff6ff1cc7fad99_45.png)

管道只能在有亲缘关系的进程（如父子进程）之间使用。如果希望两个完全不相关的进程进行通信，可以使用命名管道，也称为FIFO。

FIFO在文件系统中以一个特殊类型的文件（类型为 `p`）存在，因此任何知道其路径的进程都可以打开它进行读写。数据通过FIFO流动，但并不会实际存储到磁盘上。

![](img/01642b111612d01dc2ff6ff1cc7fad99_47.png)

使用 `mkfifo` 命令或 `mkfifo()` 系统调用可以创建FIFO：

```bash
$ mkfifo myfifo
```

或者用C代码：

```c
#include <sys/types.h>
#include <sys/stat.h>
int mkfifo(const char *pathname, mode_t mode);
```

FIFO的一个典型用途是“分流”（tee）数据。假设你想将一个命令的输出同时发送给两个不同的程序处理。普通的管道无法做到这一点。但你可以结合 `tee` 命令和FIFO来实现：

![](img/01642b111612d01dc2ff6ff1cc7fad99_49.png)

1.  创建一个FIFO：`mkfifo mypipe`
2.  启动第一个消费者进程，从FIFO读取：`consumer1 < mypipe &`
3.  使用 `tee` 命令将原始命令的输出同时写入标准输出和FIFO：`producer | tee mypipe | consumer2`

这样，`producer` 的输出会同时传递给 `consumer2` 和从FIFO读取的 `consumer1`。

## 管道与FIFO的行为细节

![](img/01642b111612d01dc2ff6ff1cc7fad99_51.png)

以下是关于管道和FIFO的一些关键点总结：

*   **缓冲**：连接到管道的标准I/O流不是行缓冲，而是完全缓冲。
*   **多读者/写者**：理论上，通过多次 `fork`，一个管道可以有多个读者或写者。管道保证小于 `PIPE_BUF` 大小的写入是原子的，不会与其他写入者的数据交错。但这种情况很少见，通常更好的设计是使用多个管道或其他IPC。
*   **关闭管道端**：
    *   如果写端被关闭，读者在读完所有数据后，下一次 `read` 将返回0（EOF）。
    *   如果读端被关闭，写者尝试写入时，内核会向其发送 `SIGPIPE` 信号（默认行为是终止进程）。如果信号被捕获或忽略，则 `write` 调用会失败，并设置 `errno` 为 `EPIPE`。

## 总结

本节课中我们一起学习了UNIX中两种基础的进程间通信机制：
1.  **管道（Pipe）**：用于有亲缘关系进程间的单向通信。它是UNIX工具链和过滤器模式的核心。
2.  **命名管道（FIFO）**：以文件形式存在于文件系统中，允许任意进程间进行通信，常用于构建复杂的数据流处理管道。

![](img/01642b111612d01dc2ff6ff1cc7fad99_53.png)

我们了解了它们的基本用法、缓冲特性、执行顺序问题，以及使用 `popen` 与外部程序交互的便利性与安全风险。管道和FIFO是理解更高级IPC机制（如下节课将介绍的套接字）的重要基础。