# 008：构建目标系统 🛠️

在本节课中，我们将正式开始构建最终可用的Linux系统。上一节我们完成了所有辅助工具的准备工作，本节我们将进入`chroot`环境，手动编译并安装目标系统的核心软件包。

## 概述

我们已经完成了第7章，这意味着所有构建最终系统所需的预备工具都已就绪。现在，我们将进入`chroot`环境，开始构建最终将包含在可用系统中的软件。为了更深入地理解过程，我们将采用更手动的方式，而不是完全依赖自动化脚本。

## 进入Chroot环境

为了更手动地操作系统，我们首先需要创建一个能进入`chroot`环境的脚本。

以下是创建`chroot`环境并启动`bash`的脚本步骤：

1.  **准备环境**：首先运行`prepare_chroot`脚本，挂载必要的虚拟文件系统。
2.  **启动交互式Shell**：在准备好的`chroot`环境中启动一个交互式的`bash`会话。
3.  **控制会话**：确保我们能控制这个新启动的`bash`会话。

对应的脚本核心命令如下：
```bash
# 准备chroot环境
./prepare_chroot $LFS
# 进入chroot并启动bash
chroot "$LFS" /usr/bin/env -i \
    HOME=/root \
    TERM="$TERM" \
    PS1='(lfs chroot) \u:\w\$ ' \
    PATH=/usr/bin:/usr/sbin \
    /bin/bash --login
```

![](img/143940e667a93e098fdacfeaaaddec72_1.png)

![](img/143940e667a93e098fdacfeaaaddec72_3.png)

![](img/143940e667a93e098fdacfeaaaddec72_5.png)

![](img/143940e667a93e098fdacfeaaaddec72_7.png)

![](img/143940e667a93e098fdacfeaaaddec72_8.png)

![](img/143940e667a93e098fdacfeaaaddec72_9.png)

## 退出与清理

![](img/143940e667a93e098fdacfeaaaddec72_11.png)

![](img/143940e667a93e098fdacfeaaaddec72_13.png)

操作完成后，我们需要一个脚本来正确地卸载虚拟文件系统并清理`chroot`环境。

以下是清理步骤，与准备步骤顺序相反：

![](img/143940e667a93e098fdacfeaaaddec72_15.png)

![](img/143940e667a93e098fdacfeaaaddec72_17.png)

1.  **卸载虚拟文件系统**：按顺序卸载`/dev/pts`和`/dev`。
2.  **恢复目录所有权**：将`$LFS`目录下的`dev`目录所有权恢复给原用户。
3.  **删除临时目录**：删除在`$LFS`下创建的`dev`、`proc`、`sys`、`run`等目录。

![](img/143940e667a93e098fdacfeaaaddec72_19.png)

![](img/143940e667a93e098fdacfeaaaddec72_21.png)

![](img/143940e667a93e098fdacfeaaaddec72_23.png)

对应的脚本核心命令如下：
```bash
# 卸载文件系统
umount $LFS/dev/pts
umount $LFS/dev
# 恢复所有权（假设在sudo环境下获取原用户和组）
chown -R $(id -un):$(id -gn) $LFS/dev
# 删除临时目录
rm -rf $LFS/{dev,proc,sys,run}
```

![](img/143940e667a93e098fdacfeaaaddec72_25.png)

## 开始构建软件包

成功进入`chroot`环境后，我们就可以开始按照第8章的指南构建软件包了。我们将从一些基础包开始。

![](img/143940e667a93e098fdacfeaaaddec72_27.png)

![](img/143940e667a93e098fdacfeaaaddec72_29.png)

![](img/143940e667a93e098fdacfeaaaddec72_31.png)

以下是前几个需要安装的软件包列表：

![](img/143940e667a93e098fdacfeaaaddec72_33.png)

![](img/143940e667a93e098fdacfeaaaddec72_35.png)

*   **Man-pages**：包含系统手册页。安装命令通常是简单的`make install`。
*   **Tcl**：一种高级编程语言。使用`make -j8`命令可以利用多核处理器加速编译。
*   **Expect**：一个用于自动化交互式应用程序的工具。
*   **DejaGNU**：一个测试框架。
*   **IANA-etc**：提供网络协议和服务的数据文件，安装过程主要是复制文件。
*   **Glibc**：系统的C语言库，是核心组件之一。在安装时，我们需要配置系统的时区信息。

![](img/143940e667a93e098fdacfeaaaddec72_37.png)

![](img/143940e667a93e098fdacfeaaaddec72_39.png)

## 配置系统时区

![](img/143940e667a93e098fdacfeaaaddec72_41.png)

在安装Glibc等包时，需要配置本地时区。例如，将时区设置为`Europe/Berlin`的命令如下：
```bash
ln -sf /usr/share/zoneinfo/Europe/Berlin /etc/localtime
```

## 总结与展望

![](img/143940e667a93e098fdacfeaaaddec72_43.png)

![](img/143940e667a93e098fdacfeaaaddec72_45.png)

本节课中，我们一起学习了如何手动进入`chroot`环境，并开始了目标系统软件包的构建。我们完成了前六个包的安装，包括Man-pages、Tcl、Expect等。第8章总共包含约81个软件包，构建整个系统是一个漫长的过程。

![](img/143940e667a93e098fdacfeaaaddec72_47.png)

![](img/143940e667a93e098fdacfeaaaddec72_48.png)

![](img/143940e667a93e098fdacfeaaaddec72_50.png)

为了保持教程的效率和趣味性，在后续课程中，我们可能不会对每个包都进行如此详细的手动演示，但核心方法和原理是相通的。我们已经建立了进入和操作`chroot`环境的能力，这是构建LFS系统最关键的一步。

下一节课，我们将继续这个构建之旅。