# 014：第8章收尾与第9章系统配置 🛠️

![](img/c2c4b16d856edd337379bb6f72e89a15_1.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_3.png)

在本节课中，我们将完成第8章剩余软件包的构建，并开始进入第9章的系统配置环节。我们将重点学习如何配置网络、设备管理、时钟、控制台以及系统服务等基础设置。

![](img/c2c4b16d856edd337379bb6f72e89a15_4.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_5.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_6.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_7.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_8.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_9.png)

---

![](img/c2c4b16d856edd337379bb6f72e89a15_11.png)

## 第8章收尾工作

![](img/c2c4b16d856edd337379bb6f72e89a15_13.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_15.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_16.png)

上一节我们完成了第8章大部分软件包的构建。本节中，我们来处理最后几个软件包，以完成第8章的全部内容。

![](img/c2c4b16d856edd337379bb6f72e89a15_18.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_19.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_21.png)

首先，我们需要创建并运行安装脚本。以下是需要执行的命令：

![](img/c2c4b16d856edd337379bb6f72e89a15_23.png)

```bash
# 创建并运行安装脚本
./script_name.sh
```

![](img/c2c4b16d856edd337379bb6f72e89a15_25.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_27.png)

在运行脚本前，需要检查软件包的下载位置。由于本教程最初基于System V版本的Linux From Scratch，最后几个软件包可能需要单独添加。检查后发现，需要添加的包主要是`systemd`和`dbus`。

![](img/c2c4b16d856edd337379bb6f72e89a15_29.png)

确认无误后，我们开始运行安装过程：

```bash
# 挂载必要的驱动器
mount /dev/sdX /mnt
mount /dev/sdY /mnt/boot
```

在安装`systemd`时，可能会遇到文档解压失败的问题。这通常不是关键错误，可以忽略。但为了确保构建日志完整，我们可以手动下载并放置缺失的man手册页。

```bash
# 手动下载并放置man手册页
wget [man_pages_url] -P /path/to/packages/
```

再次运行安装脚本后，检查日志文件。如果日志显示有1833个部分被顺序编译，则表明构建过程正常。

至此，第8章的所有软件包构建完成。

---

## 第9章：系统配置

完成第8章后，我们进入第9章的系统配置。与System V需要手动编写大量启动脚本不同，systemd采用更集成的设计理念，许多功能已内置，简化了配置工作。

### 网络配置

首先配置网络。我们使用静态设备名，并通过DHCP获取IP地址，这对于家庭路由器环境是通用且方便的方式。

1.  **设置网络设备名**：我们需要在配置文件中指定设备的MAC地址和期望的名称（如`enp0s3`）。
    ```bash
    # 示例：/etc/systemd/network/10-eth0.network
    [Match]
    MACAddress=xx:xx:xx:xx:xx:xx

    [Network]
    DHCP=ipv4
    ```

2.  **配置DNS**：systemd会生成自己的`resolv.conf`。为了兼容性，我们创建一个符号链接。
    ```bash
    ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
    ```

3.  **设置主机名**：将主机名写入`/etc/hostname`文件。
    ```bash
    echo "myhostname" > /etc/hostname
    ```

4.  **更新hosts文件**：至少需要添加localhost条目。
    ```bash
    # /etc/hosts
    127.0.0.1   localhost
    ::1         localhost
    ```

### 设备与模块管理概述

这是整个过程中最有趣的部分之一。设备节点并非由内核直接创建在`/dev`目录下，而是由`udev`服务根据内核通过`sysfs`提供的信息动态创建的。

`udev`规则可以解决设备名随机分配的问题。例如，两个网卡可能因为响应速度不同，在每次启动时被分配不同的名字（如eth0和eth1）。通过`udev`规则，可以将其固定。

然而，对于初次构建，我们可以暂时接受设备名可能变化的情况，跳过复杂的`udev`规则编写。

### 时钟配置

我们可以配置系统的时区和时间同步服务（如`systemd-timesyncd`），这对于保持系统时间准确很有帮助。本节提供了一些可选的设置项。

### 控制台配置

这里可以设置键盘布局。例如，德式键盘需要特定的映射。我们可以列出可用的区域设置：
```bash
locale -a
```
输出可能包含`de_DE.UTF-8`这样的条目。但为了简化，我们可以暂时使用默认的键盘布局。

`/etc/inputrc`文件用于定义终端按键行为，确保如Home、End、Delete等按键按预期工作。

### 系统Shell配置

`/etc/shells`文件列出了系统允许使用的合法登录shell（如`/bin/bash`、`/bin/zsh`）。这可以防止用户不小心将shell设置为`/bin/false`等导致无法登录的程序。

### 其他系统行为配置

1.  **禁用启动时清屏**：默认情况下，systemd在启动序列结束后会清屏。我们可以保留启动信息以便查看。
    ```bash
    systemctl disable systemd-vconsole-setup.service
    ```
    （注：具体命令可能需查阅手册，此处为概念示意）

2.  **临时目录**：`/tmp`目录默认被创建为`tmpfs`（内存文件系统），可以按需修改。

3.  **服务自动重启**：systemd可以配置为在服务崩溃后自动重启（例如30秒后），这比System V更健壮。此配置通常针对每个服务单独设置。

4.  **核心转储（Core Dump）限制**：可以限制核心转储文件的大小，防止占满磁盘空间。
    ```bash
    # 在 /etc/systemd/coredump.conf 中设置
    [Coredump]
    MaxSize=1G
    ```

运行完所有配置脚本后，检查生成的文件和链接（如`/etc/resolv.conf`的符号链接）是否正常。如果一切顺利，第9章的基础配置就完成了。

![](img/c2c4b16d856edd337379bb6f72e89a15_31.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_33.png)

---

![](img/c2c4b16d856edd337379bb6f72e89a15_35.png)

![](img/c2c4b16d856edd337379bb6f72e89a15_37.png)

## 总结

本节课中，我们一起完成了第8章剩余软件包的安装，并开始了第9章的系统配置。我们学习了如何配置网络（使用DHCP和静态设备名）、了解了`udev`设备管理的基本概念、并设置了时钟、控制台、Shell以及一些基本的系统行为。

![](img/c2c4b16d856edd337379bb6f72e89a15_39.png)

下一节课，我们将进入激动人心的第10章：编译并安装Linux内核以及引导程序GRUB，最终尝试启动我们亲手构建的系统。