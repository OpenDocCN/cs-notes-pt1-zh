# 005：chroot环境搭建 🛠️

在本节课中，我们将继续构建LFS系统，重点完成第6章核心工具的编译，并最终进入关键的chroot环境。这是从“宿主系统”过渡到“目标系统”的重要一步。

上一节我们完成了第5章临时工具的编译。本节中，我们将编译第6章的工具链，并学习如何设置和进入chroot环境。

## 编译第6章工具链

第6章需要编译一系列核心工具，这些工具是后续构建目标系统的基础。以下是需要编译的软件包列表：

*   M4
*   Ncurses
*   Bash
*   Coreutils
*   Diffutils
*   File
*   Findutils
*   Gawk
*   Grep
*   Gzip
*   Make
*   Patch
*   Sed
*   Tar
*   Xz
*   Binutils (再次编译)
*   GCC (再次编译)

![](img/68b32610a30b12db3c3c627eb143ea15_1.png)

![](img/68b32610a30b12db3c3c627eb143ea15_3.png)

![](img/68b32610a30b12db3c3c627eb143ea15_5.png)

我们使用脚本来批量处理这些包的编译。脚本的核心逻辑是检查并清理旧的源码目录，然后执行标准的配置、编译和安装流程。例如，对于M4包，其安装脚本结构如下：

![](img/68b32610a30b12db3c3c627eb143ea15_7.png)

```bash
#!/bin/bash
# 设置版本等变量
# 检查并删除旧的源码目录
# 解压源码
# 进入源码目录并执行配置、编译、安装
./configure --prefix=/usr   \
            --host=$LFS_TGT \
            --build=$(build-aux/config.guess)
make
make DESTDIR=$LFS install
```

在编译M4时，我们遇到了一个关于过时编译器参数`-Wabi`的错误。解决方法是修改编译标志，在配置或编译步骤中传递正确的参数。我们通过设置`CFLAGS`环境变量来实现：

![](img/68b32610a30b12db3c3c627eb143ea15_9.png)

![](img/68b32610a30b12db3c3c627eb143ea15_11.png)

```bash
# 在配置或编译前设置
export CFLAGS="-Wno-error -fpermissive"
```

![](img/68b32610a30b12db3c3c627eb143ea15_13.png)

![](img/68b32610a30b12db3c3c627eb143ea15_15.png)

![](img/68b32610a30b12db3c3c627eb143ea15_16.png)

![](img/68b32610a30b12db3c3c627eb143ea15_18.png)

应用此修复后，M4及其他工具包得以成功编译。

## 备份工作成果

在进入chroot之前，强烈建议对已构建的系统进行备份。这可以防止后续操作失误导致前功尽弃。可以使用`tar`命令创建备份：

```bash
cd $LFS
tar -cJpf /path/to/backup/lfs-temp-tools-$(date +%s).tar.xz .
```

## 准备chroot环境

chroot（change root）能将当前进程的根目录切换到指定位置（这里是`$LFS`），从而创建一个隔离的系统环境。在进入之前，需要进行一系列准备。

以下是准备步骤的脚本概要：

1.  **权限设置**：将`$LFS`下除`sources`外的所有目录所有者改为`root`，确保环境安全。
    ```bash
    chown -R root:root $LFS/{usr,lib,var,etc,bin,sbin,tools}
    ```
2.  **创建虚拟文件系统目录**：在`$LFS`下创建`/dev`， `/proc`， `/sys`， `/run`等目录，这些是系统运行时必需的。
3.  **挂载宿主系统目录**：使用`--bind`选项将宿主系统的`/dev`， `/dev/pts`， `/proc`， `/sys`， `/run`目录绑定到`$LFS`下的对应位置，以便在chroot环境中访问设备和控制台。
    ```bash
    mount --bind /dev $LFS/dev
    mount --bind /dev/pts $LFS/dev/pts
    mount -t proc proc $LFS/proc
    mount -t sysfs sysfs $LFS/sys
    mount -t tmpfs tmpfs $LFS/run
    ```
4.  **创建设备节点**：使用`mknod`命令创建设备文件，例如控制台（`/dev/console`）和空设备（`/dev/null`）。
    ```bash
    mknod -m 600 $LFS/dev/console c 5 1
    mknod -m 666 $LFS/dev/null c 1 3
    ```
5.  **处理共享内存**：检查宿主系统的共享内存位置（通常是`/dev/shm`），并在chroot环境中创建相应的目录或符号链接。

## 进入chroot环境

完成准备工作后，即可使用`chroot`命令进入隔离环境。为了拥有一个完整可用的shell环境，我们需要设置正确的环境变量。

以下是进入chroot的脚本命令：

```bash
chroot "$LFS" /usr/bin/env -i \
    HOME=/root \
    TERM="$TERM" \
    PS1='(lfs chroot) \u:\w\$ ' \
    PATH=/usr/bin:/usr/sbin \
    /bin/bash --login +h
```

*   `/usr/bin/env -i`：启动一个干净的环境。
*   `HOME=/root`：设置家目录。
*   `TERM`：保留终端类型。
*   `PS1`：设置shell提示符，这里我们将其设为`(lfs chroot) \u:\w\$ `以便清晰识别当前环境。
*   `PATH`：设置命令搜索路径，指向我们新编译的工具。
*   `/bin/bash --login +h`：启动bash登录shell，并启用历史记录功能（`+h`）。

![](img/68b32610a30b12db3c3c627eb143ea15_20.png)

![](img/68b32610a30b12db3c3c627eb143ea15_22.png)

![](img/68b32610a30b12db3c3c627eb143ea15_24.png)

![](img/68b32610a30b12db3c3c627eb143ea15_26.png)

![](img/68b32610a30b12db3c3c627eb143ea15_28.png)

为了验证是否成功进入chroot环境，可以在内部执行一个简单的测试命令，例如创建一个文件：

![](img/68b32610a30b12db3c3c627eb143ea15_30.png)

![](img/68b32610a30b12db3c3c627eb143ea15_32.png)

![](img/68b32610a30b12db3c3c627eb143ea15_34.png)

![](img/68b32610a30b12db3c3c627eb143ea15_36.png)

![](img/68b32610a30b12db3c3c627eb143ea15_38.png)

![](img/68b32610a30b12db3c3c627eb143ea15_40.png)

```bash
echo “test” > /test.txt
```

![](img/68b32610a30b12db3c3c627eb143ea15_42.png)

退出chroot后，检查`$LFS/test.txt`文件是否存在且内容正确，即可确认chroot环境运行正常。

![](img/68b32610a30b12db3c3c627eb143ea15_44.png)

![](img/68b32610a30b12db3c3c627eb143ea15_46.png)

![](img/68b32610a30b12db3c3c627eb143ea15_48.png)

![](img/68b32610a30b12db3c3c627eb143ea15_50.png)

## 总结

![](img/68b32610a30b12db3c3c627eb143ea15_52.png)

本节课中我们一起学习了如何编译LFS第6章的核心工具链，解决了编译过程中遇到的参数错误。更重要的是，我们详细讲解了chroot环境的原理、准备步骤和进入方法，并成功进入了为目标系统准备的隔离构建环境。这是构建独立Linux系统的关键里程碑。下一节，我们将在chroot环境中继续构建更多工具，并最终开始构建最终的目标系统。