# 029：编译器工具链（第四部分）🔗

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_1.png)

在本节课中，我们将完成对编译过程及编译器工具链的讨论，重点学习链接（Linking）这一最终步骤。我们将了解如何将目标文件与必要的库文件链接，生成最终的可执行程序。

在之前的视频中，我们已经了解了编译器如何通过`cpp`命令进行预处理，通过`cc`命令进行编译，以及通过`as`命令将中间代码汇编成目标文件（`.o`文件）。本节中，我们来看看最后一个步骤：链接。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_3.png)

链接阶段由`ld`命令执行，它将多个不同的文件组合成最终的输出文件（默认为`a.out`）。如下图所示，链接器所做的工作似乎不仅仅是使用我们生成的目标文件。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_1.png)

我们唯一能控制的文件是汇编阶段生成的`.o`文件。那么图中显示的其他文件是什么呢？让我们来探究一下。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_5.png)

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_7.png)

## 回顾与尝试链接

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_9.png)

首先，让我们回顾之前的步骤并尝试链接。我们调用了预处理器生成`.i`文件，然后通过`gcc`进行编译和代码生成，得到汇编代码`.s`文件，接着使用`as`生成`.o`文件。当我们尝试执行这个目标文件时，理所当然地失败了。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_11.png)

因此，我们尝试使用`ld`命令进行链接。
```bash
ld hello.o
```
然而，这并不顺利，我们遇到了错误。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_5.png)

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_13.png)

错误信息显示“未定义的引用：`printf`”。为什么会这样？

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_15.png)

## 链接标准C库

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_17.png)

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_19.png)

`printf`是标准C库（`libc`）提供的标准I/O函数。根据手册页，我们需要使用`-lc`标志来链接`libc`库。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_21.png)

让我们将这个标志添加到`ld`命令中：
```bash
ld hello.o -lc
```
这次看起来好多了，不再有关于`printf`的未定义引用错误，命令成功执行，只显示了一个警告。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_13.png)

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_23.png)

我们得到了可执行文件`a.out`。让我们运行它。程序执行失败了，这是为什么？

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_25.png)

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_27.png)

## 链接C运行时文件

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_29.png)

检查文件类型，看起来没问题。我们可能还缺少其他需要链接的库。回顾手册页，它提到了一个名为`crt0.o`的文件（`crt`代表C运行时）。这个文件提供了程序执行的启动例程，我们需要在链接过程中包含它。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_31.png)

但我们之前尝试过吗？让我们看看之前得到的警告。警告提示缺少符号`_start`。我们需要找到包含这个符号的`crt`文件。在`/usr/lib`目录中，我们找到了几个`crt`文件。使用`grep`或更好的工具`nm`（用于列出目标文件中的符号）来查找。

使用`nm`命令检查`/usr/lib/crt0.o`：
```bash
nm /usr/lib/crt0.o
```
输出显示符号`_start`被定义了（标记为`T`），但符号`_fini`是未定义的（标记为`U`）。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_33.png)

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_29.png)

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_35.png)

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_37.png)

而在文件`crti.o`中，我们找到了`_fini`的定义。因此，看起来我们需要同时链接`crt0.o`（提供`_start`）和`crti.o`（提供`_fini`）。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_39.png)

让我们尝试链接这两个文件：
```bash
ld hello.o /usr/lib/crt0.o /usr/lib/crti.o -lc
```
很好，没有警告了。让我们运行程序。结果提示“a.out: not found”。但文件明明在那里，为什么说找不到？

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_41.png)

## 动态链接器与解释器

使用`file`命令检查`a.out`：
```bash
file a.out
```
输出显示这是一个“动态链接的可执行文件”，并且使用了一个解释器`/lib/ld64.so.1`。“not found”信息指的不是`a.out`本身，而是指这个解释器`/lib/ld64.so.1`不存在。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_35.png)

让我们与一个已知能工作的可执行文件（如`/bin/ls`）进行比较。`ls`也是一个动态链接的可执行文件，但它使用的解释器是`/lib/ld-linux.so`。`ld-linux.so`是一个运行时链接编辑器，用于在程序执行时加载可执行文件和所有必需的对象。

因此，我们需要告诉`ld`使用正确的动态链接器。我们可以通过`-dynamic-linker`选项来指定。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_43.png)

## 完整的链接命令

与其手动摸索所有必需的库和启动文件，不如看看`gcc`在幕后是如何调用链接器的。使用`gcc -v`标志可以显示它实际运行的命令。
```bash
gcc -v hello.c -o hello
```
观察输出，我们可以看到`gcc`传递了许多标志给`ld`，包括动态链接器路径、多个`crt`对象文件以及其他启动和结束对象文件（如`crtbegin.o`, `crtend.o`），这些文件为程序提供了启动和从`main`函数返回所需的簿记代码。

将这些元素添加到我们的手动链接命令中，我们最终得到了一个可以工作的可执行文件。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_45.png)

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_47.png)

## 库搜索路径

为什么我们必须通过绝对路径指定`crt`文件，却可以简单地用`-lc`来链接`libc`？手册页指出，要链接一个名为`libNAME`的库，我们需要传递`-lNAME`，链接器会去寻找名为`libNAME.a`（静态库）或`libNAME.so`（共享库）的文件。

链接器在哪些目录中搜索这些库文件呢？我们可以使用`-L`标志指定额外的搜索目录。要查看链接器的默认搜索路径，可以运行：
```bash
ld --verbose | grep SEARCH_DIR
```
输出显示它默认在`/usr/lib`等目录中查找。因此，我们可以在`/usr/lib`下找到`libc`库（`libc.a`和`libc.so`）。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_49.png)

## 过程回顾与总结

本节课中我们一起学习了链接的完整过程。如果我们要手动完成所有阶段，需要：
1.  运行`cpp`预处理源文件。
2.  运行`gcc`将C代码编译成汇编。
3.  运行`as`将汇编代码转为目标代码。
4.  运行`ld`将目标文件与C运行时代码和标准C库链接，创建可执行文件。

当然，我们可以让编译器驱动程序`gcc`通过一个命令为我们执行所有这些步骤，这要简单得多。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_49.png)

正如你所看到的，这个看似简单的步骤序列实际上涉及多个独立的命令，每个命令都有其复杂性和众多选项。`gcc`驱动了整个流程，它将各种命令和选项按需传递给其他工具。

在这个过程中，理解标志（flags）的顺序可能很重要，这与大多数UNIX工具不同。此外，编译器的行为既受内置默认值影响，也常受环境变量影响。

虽然我们完成了对编译器的高级介绍，但你可能已经注意到，在这一系列视频中，我们偶尔使用了另一个工具`make`来帮助我们节省击键次数，并使用预定义的规则来构建文件。`make`是UNIX开发环境中极其重要且有用的工具，我们将在下一个视频中详细介绍它。

![](img/66f7d27f47acaf03f62deedcc5ae3f6d_51.png)

感谢观看。