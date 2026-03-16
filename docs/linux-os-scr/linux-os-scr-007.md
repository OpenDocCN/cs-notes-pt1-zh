# 007：最终构建系统

## 概述
在本节课中，我们将继续构建LFS系统，完成第七章的收尾工作。我们将进入一个新的chroot环境，安装剩余的软件包，并处理在此过程中遇到的一些问题。

---

## 进入新的chroot环境

上一节我们完成了基础系统的构建，并创建了必要的用户和组。本节中，我们将按照LFS手册的建议，进入一个新的chroot环境来继续安装软件包。

LFS手册建议在初始的chroot环境中再启动一个bash shell。这是因为第一个chroot环境中的bash以root身份运行，但此时`/etc/passwd`文件可能尚未完全就绪，导致用户ID没有对应的用户信息。启动第二个bash可以避免由此产生的问题。

![](img/77264a30aa8a858ec63338e684f04aba_1.png)

![](img/77264a30aa8a858ec63338e684f04aba_2.png)

虽然我们不确定为何不直接在初始脚本中完成这些操作，但我们将遵循手册的指导。我们的做法是：退出当前的chroot环境，然后通过一个循环脚本再次进入。

以下是创建和运行新chroot环境脚本的步骤：

```bash
# 创建 chroot2.sh 脚本
cat > chroot2.sh << "EOF"
#!/bin/bash
set -e

![](img/77264a30aa8a858ec63338e684f04aba_4.png)

# 设置LFS变量为空，因为我们在chroot环境中，路径已经是根“/”
export LFS=""

![](img/77264a30aa8a858ec63338e684f04aba_6.png)

![](img/77264a30aa8a858ec63338e684f04aba_8.png)

# 进入源代码目录
cd /sources

![](img/77264a30aa8a858ec63338e684f04aba_10.png)

![](img/77264a30aa8a858ec63338e684f04aba_12.png)

# 准备chroot环境并运行编译脚本
./prepare_chroot.sh
./package_install_check_7.sh

![](img/77264a30aa8a858ec63338e684f04aba_14.png)

![](img/77264a30aa8a858ec63338e684f04aba_16.png)

EOF

![](img/77264a30aa8a858ec63338e684f04aba_18.png)

# 为脚本添加执行权限
chmod +x chroot2.sh
```

![](img/77264a30aa8a858ec63338e684f04aba_20.png)

![](img/77264a30aa8a858ec63338e684f04aba_22.png)

---

![](img/77264a30aa8a858ec63338e684f04aba_24.png)

## 安装第七章的软件包

在新的chroot环境中，我们需要安装一系列新的软件包。以下是需要安装的软件包列表：
*   libstdc++
*   gettext
*   bison
*   perl
*   python
*   texinfo
*   util-linux

![](img/77264a30aa8a858ec63338e684f04aba_26.png)

![](img/77264a30aa8a858ec63338e684f04aba_28.png)

我们创建了一个名为`package_install_check_7.sh`的脚本来自动化安装过程。该脚本会为每个软件包创建目录、解压源码、编译并安装。

![](img/77264a30aa8a858ec63338e684f04aba_29.png)

![](img/77264a30aa8a858ec63338e684f04aba_31.png)

---

## 处理构建过程中的问题

![](img/77264a30aa8a858ec63338e684f04aba_33.png)

![](img/77264a30aa8a858ec63338e684f04aba_35.png)

在运行安装脚本时，我们遇到了一些错误，并逐一解决了它们。

![](img/77264a30aa8a858ec63338e684f04aba_37.png)

![](img/77264a30aa8a858ec63338e684f04aba_39.png)

![](img/77264a30aa8a858ec63338e684f04aba_41.png)

### 问题一：找不到XZ软件包
脚本报错提示找不到`xz`文件。检查发现，问题在于软件包列表中的名称与实际下载的压缩包名称不匹配。我们修正了软件包名称，确保其与压缩包文件名一致。

![](img/77264a30aa8a858ec63338e684f04aba_43.png)

![](img/77264a30aa8a858ec63338e684f04aba_45.png)

### 问题二：文件移动遗漏隐藏文件
在解压某些软件包（如`perl`）时，其源码目录内包含以点`.`开头的隐藏文件。我们原有的移动命令`mv * ../`不会移动这些隐藏文件，导致编译失败。

**解决方案**：修改移动命令，显式包含隐藏文件。
```bash
# 修改前
mv * ../

# 修改后
mv .* * ../
```

### 问题三：链接器找不到库文件
在编译`util-linux`时，出现错误`cannot find -lncursesw`。这表明链接器在寻找名为`libncursesw.so`的共享库文件时失败。

![](img/77264a30aa8a858ec63338e684f04aba_47.png)

检查发现，库文件实际存在于`/usr/lib`目录，但链接器可能只在`/lib`目录中查找。

**解决方案**：在`/lib`目录中为所需的库文件创建一个硬链接。
```bash
# 进入lib目录
cd /lib
# 创建硬链接
ln /usr/lib/libncursesw.so.6 libncursesw.so
```
创建硬链接后，编译得以继续进行。

---

## 第七章收尾工作

所有软件包安装完成后，LFS手册建议进行一些清理工作以节省空间，例如删除临时工具的文档和调试符号。不过，考虑到节省的空间（约120MB）相对于整个系统（约5GB）来说比例很小，我们可以选择跳过此步骤。

![](img/77264a30aa8a858ec63338e684f04aba_49.png)

![](img/77264a30aa8a858ec63338e684f04aba_51.png)

![](img/77264a30aa8a858ec63338e684f04aba_53.png)

![](img/77264a30aa8a858ec63338e684f04aba_54.png)

最后，在结束工作前，我们需要卸载虚拟文件系统。
```bash
# 卸载虚拟文件系统
umount $LFS/dev/pts
umount $LFS/dev
umount $LFS/proc
umount $LFS/sys
umount $LFS/run
```
如果卸载失败，重启系统后再执行上述命令即可。

![](img/77264a30aa8a858ec63338e684f04aba_56.png)

---

## 总结
本节课中，我们一起学习了如何进入新的chroot环境来完成LFS第七章的构建。我们安装了`libstdc++`、`gettext`等多个核心软件包，并解决了构建过程中遇到的“找不到软件包”、“遗漏隐藏文件”和“库文件链接”等问题。最后，我们完成了第七章的收尾工作，为进入下一阶段的构建做好了准备。