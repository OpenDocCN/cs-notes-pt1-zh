# 016：一小时内仅用BusyBox构建一个极简Linux系统 🚀

在本节课中，我们将学习如何构建一个极其精简的Linux发行版。这个系统非常小，大约只有12兆字节。与完整的“Linux From Scratch”项目不同，我们今天的目标是创建一个仅能提供Shell环境、用于浏览文件系统和启动程序的最小化系统。为了实现这个目标，我们将只构建两个核心包：Linux内核和BusyBox。

## 概述 📋

一个典型的Linux系统需要两样东西：一个内核（Kernel）和一个根文件系统（Root Filesystem）。内核是系统的核心，负责管理硬件和进程。根文件系统则包含了内核启动后需要运行的所有程序、配置文件和驱动程序。在本教程中，我们的根文件系统将主要由BusyBox构成。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_1.png)

BusyBox是一个特殊的工具，它被称为“瑞士军刀”式的嵌入式Linux工具。它将许多常见的Unix工具（如`ls`、`cp`、`echo`等）的功能集成到了一个单一的可执行文件中。这极大地减少了系统所需的空间和依赖。

接下来，我们将分步完成内核编译、BusyBox静态编译以及根文件系统的构建，最终将它们组合成一个可以运行的Linux系统。

## 第一步：准备构建脚本与环境 🛠️

我们将创建一个脚本来自动化整个构建过程。首先，定义我们要使用的软件版本，并设置一些变量。

```bash
#!/bin/bash

# 定义要使用的版本
KERNEL_VERSION="5.15.6"
BUSYBOX_VERSION="1.34.1"

# 提取内核的主版本号（例如从“5.15.6”中提取“5”）
KERNEL_MAJOR_VERSION=$(echo $KERNEL_VERSION | sed -E 's/^([0-9]+)[^0-9].*$/\1/')
```

我们创建`src`目录作为工作空间，并进入该目录。

```bash
# 创建并进入源码目录
mkdir -p src
cd src
```

## 第二步：下载与编译Linux内核 ⚙️

首先，我们需要下载指定版本的Linux内核源代码。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_3.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_5.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_7.png)

```bash
# 下载并解压Linux内核
wget -q --show-progress https://cdn.kernel.org/pub/linux/kernel/v$KERNEL_MAJOR_VERSION.x/linux-$KERNEL_VERSION.tar.xz
tar -xf linux-$KERNEL_VERSION.tar.xz
cd linux-$KERNEL_VERSION
```

![](img/04be878bb5e6eddf97ce4ab1efee7b35_9.png)

进入内核源码目录后，我们使用默认配置并开始编译。`make defconfig`会生成一个适合当前架构的默认配置。`make -j8`会启动并行编译以加快速度。

```bash
# 配置并编译Linux内核
make defconfig
make -j8 || exit 1
```

![](img/04be878bb5e6eddf97ce4ab1efee7b35_11.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_13.png)

编译完成后，我们将生成的内核映像文件`arch/x86/boot/bzImage`复制到项目根目录。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_15.png)

```bash
# 复制编译好的内核到项目根目录
cp arch/x86/boot/bzImage ../../kernel.img
cd ..
```

上一节我们完成了内核的编译，本节中我们来看看如何构建BusyBox。

## 第三步：下载与静态编译BusyBox 📦

![](img/04be878bb5e6eddf97ce4ab1efee7b35_17.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_19.png)

现在，我们来处理BusyBox。首先下载并解压源代码。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_21.png)

```bash
# 下载并解压BusyBox
wget -q --show-progress https://busybox.net/downloads/busybox-$BUSYBOX_VERSION.tar.bz2
tar -xf busybox-$BUSYBOX_VERSION.tar.bz2
cd busybox-$BUSYBOX_VERSION
```

我们的目标系统没有动态链接库（如`glibc`），因此需要将BusyBox编译成静态二进制文件，将所有依赖库都打包进可执行文件内部。

在基于Arch Linux的系统（如Manjaro）上，直接静态编译可能会遇到库缺失的问题。一个解决方案是使用`musl libc`这个替代的C标准库。我们需要安装相关工具链。

```bash
# 对于Manjaro/Arch系统，安装musl工具链（在其他发行版上可能不需要）
sudo pacman -S --noconfirm musl binutils-musl 2>/dev/null || true
```

接下来，我们生成默认配置，并修改配置以启用静态编译。我们使用`sed`命令自动修改配置文件。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_23.png)

```bash
# 生成默认配置并修改为静态编译
make defconfig
sed -i 's/^CONFIG_STATIC.*$/CONFIG_STATIC=y/' .config
```

然后，我们使用`musl-gcc`编译器（如果已安装）来执行静态编译。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_25.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_27.png)

```bash
# 使用musl-gcc进行静态编译（如果可用），否则使用系统gcc
if command -v musl-gcc &> /dev/null; then
    make CC=musl-gcc -j8 || exit 1
else
    make -j8 || exit 1
fi
```

![](img/04be878bb5e6eddf97ce4ab1efee7b35_29.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_31.png)

编译成功后，BusyBox的可执行文件`busybox`就生成了。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_33.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_34.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_36.png)

## 第四步：构建初始内存盘（initrd）文件系统 🗂️

内核需要一个根文件系统来挂载。我们将创建一个初始内存盘（initrd），它是一个临时的根文件系统，在系统启动早期被加载到内存中。

首先，创建一个目录结构来模拟根文件系统。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_38.png)

```bash
# 创建initrd目录结构
cd ../..
mkdir -p initrd
cd initrd
mkdir -p bin dev proc sys
```

将编译好的静态`busybox`二进制文件复制到`bin`目录下。

```bash
# 复制busybox到bin目录
cp ../src/busybox-$BUSYBOX_VERSION/busybox bin/
```

![](img/04be878bb5e6eddf97ce4ab1efee7b35_40.png)

BusyBox的一个巧妙特性是：它会根据自己被调用的名字（argv[0]）来执行不同的功能。例如，如果创建一个指向`busybox`的名为`ls`的符号链接，那么执行这个链接就相当于执行`ls`命令。

以下是创建所有必要符号链接的步骤：

![](img/04be878bb5e6eddf97ce4ab1efee7b35_42.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_44.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_46.png)

```bash
# 进入bin目录并为busybox创建所有applet的符号链接
cd bin
for applet in $(./busybox --list); do
    ln -s busybox $applet
done
cd ..
```

![](img/04be878bb5e6eddf97ce4ab1efee7b35_48.png)

现在，我们需要创建一个初始化脚本`init`。这个脚本是内核启动后运行的第一个用户空间进程（PID 1）。

```bash
# 创建初始化脚本 init
cat > init << 'EOF'
#!/bin/sh
# 挂载必要的虚拟文件系统
mount -t devtmpfs devtmpfs /dev
mount -t proc proc /proc
mount -t sysfs sysfs /sys

# 设置内核日志级别
echo 0 > /proc/sys/kernel/printk

# 启动一个shell
exec /bin/sh
EOF

# 使init脚本可执行
chmod +x init
```

![](img/04be878bb5e6eddf97ce4ab1efee7b35_50.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_52.png)

最后，使用`cpio`命令将整个`initrd`目录打包成一个镜像文件。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_54.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_56.png)

```bash
# 将initrd目录打包成cpio归档文件（镜像）
find . | cpio -o -H newc > ../initrd.img
cd ..
```

![](img/04be878bb5e6eddf97ce4ab1efee7b35_58.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_60.png)

## 第五步：使用QEMU运行系统 🖥️

![](img/04be878bb5e6eddf97ce4ab1efee7b35_62.png)

现在，我们拥有了两个关键文件：`kernel.img`（内核）和`initrd.img`（初始内存盘）。我们可以使用QEMU虚拟机来启动这个系统。

以下是用QEMU启动系统的命令：

![](img/04be878bb5e6eddf97ce4ab1efee7b35_64.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_66.png)

```bash
# 使用QEMU运行构建好的系统（图形界面模式）
qemu-system-x86_64 -kernel kernel.img -initrd initrd.img
```

![](img/04be878bb5e6eddf97ce4ab1efee7b35_68.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_70.png)

如果你想在终端中直接与系统交互，而不弹出图形窗口，可以使用以下命令：

![](img/04be878bb5e6eddf97ce4ab1efee7b35_72.png)

![](img/04be878bb5e6eddf97ce4ab1efee7b35_74.png)

```bash
# 使用QEMU运行构建好的系统（无图形界面，使用串口控制台）
qemu-system-x86_64 -kernel kernel.img -initrd initrd.img -nographic -append "console=ttyS0"
```

![](img/04be878bb5e6eddf97ce4ab1efee7b35_76.png)

在Shell中，你可以尝试一些基本命令，如`ls`、`cd`、`echo`等。要退出QEMU，在图形界面中可以关闭窗口，在无图形模式下的终端中，可以按`Ctrl+A`，然后按`X`来强制退出。

![](img/04be878bb5e6eddf97ce4ab1efee7b35_78.png)

## 总结 🎉

![](img/04be878bb5e6eddf97ce4ab1efee7b35_80.png)

在本节课中，我们一起完成了一个极简Linux系统的构建。我们学习了：

![](img/04be878bb5e6eddf97ce4ab1efee7b35_82.png)

1.  **下载和编译Linux内核**：使用`make defconfig`和`make -j8`。
2.  **静态编译BusyBox**：理解了静态链接与动态链接的区别，并解决了在特定发行版上的编译问题。
3.  **构建初始内存盘（initrd）**：创建了包含BusyBox和基本目录结构的根文件系统，并编写了启动脚本`init`。
4.  **使用QEMU测试系统**：将内核和initrd组合，在虚拟机中成功启动了一个功能完整的微型Linux环境。

整个构建过程被浓缩在了一个60多行的脚本中，这充分展示了Linux模块化设计的强大与简洁。通过这个实践，你不仅得到了一个可运行的系统，更深入理解了Linux系统启动的基本原理和核心组件是如何协同工作的。希望这为你进一步探索操作系统世界打下了坚实的基础。