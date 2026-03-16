# 009：讨论软件包与继续编译 🛠️

![](img/7620c7e229704c2a7caa021fe15f39fe_1.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_3.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_5.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_7.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_9.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_10.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_11.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_12.png)

在本节课中，我们将继续构建最终的LFS系统，并深入了解正在安装的一系列核心软件包。上一节我们完成了Glibc等基础库的安装，本节中我们将批量编译更多工具，并探讨每个包的功能。

## 概述

![](img/7620c7e229704c2a7caa021fe15f39fe_14.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_16.png)

我们将创建一系列安装脚本，并批量编译从 `man-pages` 到 `vim` 的数十个软件包。过程中，我们会解释每个包的作用，这对于理解一个最小Linux系统的构成至关重要。

## 创建批量安装脚本

![](img/7620c7e229704c2a7caa021fe15f39fe_18.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_20.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_22.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_24.png)

为了避免重复劳动，我们将为所有待安装的软件包预先创建空的脚本文件。以下是需要创建脚本的软件包列表：

![](img/7620c7e229704c2a7caa021fe15f39fe_26.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_28.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_30.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_32.png)

*   man-pages
*   tcl
*   expect
*   dejagnu
*   iana-etc
*   glibc
*   zlib
*   bzip2
*   xz
*   zstd
*   file
*   readline
*   m4
*   bc
*   flex
*   binutils
*   gmp
*   mpfr
*   mpc
*   attr
*   acl
*   libcap
*   shadow
*   gcc
*   pkg-config
*   ncurses
*   sed
*   psmisc
*   gettext
*   bison
*   grep
*   bash
*   libtool
*   gdbm
*   gperf
*   expat
*   inetutils
*   perl
*   xml::parser
*   intltool
*   autoconf
*   automake
*   kmod
*   libelf
*   libffi
*   openssl
*   python
*   ninja
*   meson
*   coreutils
*   check
*   diffutils
*   gawk
*   findutils
*   groff
*   grub
*   less
*   gzip
*   iproute2
*   kbd
*   libpipeline
*   make
*   patch
*   man-db
*   tar
*   texinfo
*   vim

`zlib` 的脚本已提前编写好。对于 `m4` 包，我们将采用之前视频中使用的解决方案来规避已知问题。

![](img/7620c7e229704c2a7caa021fe15f39fe_34.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_36.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_38.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_40.png)

## 安装与编译过程

进入 `chroot` 环境后，我们开始执行第一批七个包的安装脚本：`man-pages`, `tcl`, `expect`, `dejagnu`, `iana-etc`, `glibc`, `zlib`。

在编译进行的同时，让我们来讨论这些软件包的具体作用。

## 软件包功能详解

### 已安装的软件包

![](img/7620c7e229704c2a7caa021fe15f39fe_42.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_44.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_46.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_48.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_49.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_51.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_53.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_55.png)

以下是第一批安装的软件包及其功能：

![](img/7620c7e229704c2a7caa021fe15f39fe_57.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_59.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_61.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_63.png)

*   **man-pages**: 包含超过2200个程序、源代码和头文件的帮助文档。
*   **tcl**: 工具命令语言，一种高级脚本解释器。
*   **expect**: 用于自动化测试的程序，可以预设期望的输入输出。
*   **dejagnu**: 运行自动化测试的框架。
*   **iana-etc**: 提供 `/etc/services` 和 `/etc/protocols` 文件，定义了TCP端口和协议的标准列表。
*   **glibc**: GNU C库，是系统最核心的库之一，处理本地化、日期时间格式化等基础功能。
*   **zlib**: 提供数据压缩功能的库。

![](img/7620c7e229704c2a7caa021fe15f39fe_65.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_67.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_69.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_71.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_73.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_75.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_77.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_79.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_81.png)

### 正在编译的软件包

![](img/7620c7e229704c2a7caa021fe15f39fe_83.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_85.png)

编译队列中的软件包同样关键：

![](img/7620c7e229704c2a7caa021fe15f39fe_87.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_89.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_91.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_93.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_95.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_97.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_99.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_101.png)

*   **bzip2, xz, zstd**: 都是数据压缩工具和库。
*   **file**: 用于判断文件类型的工具。它不仅依赖扩展名，还会分析文件的魔数（magic number），并能检测文件编码。
    ```bash
    file script.sh # 输出: script.sh: Bourne-Again shell script, ASCII text executable
    ```
*   **readline**: 一个库，为命令行程序提供行编辑和历史记录功能（例如按上箭头查看历史命令）。
*   **m4**: 宏处理器，类似于C/C++预处理器，用于扩展宏定义。
*   **bc**: 一个任意精度计算器语言。
    ```bash
    echo “1 + 1” | bc # 输出: 2
    ```
*   **flex**: 词法分析器生成器。它根据规则将输入流（如源代码）转换为标记流，常与 `bison` 配合使用来构建编译器。
*   **binutils**: 包含二进制工具集，如链接器 `ld`、汇编器 `as`，以及分析目标文件的工具 `readelf`、`objdump`。
*   **gmp, mpfr, mpc**: 高精度数学运算库，被GCC及其生成的程序广泛使用。
*   **attr**: 提供 `setfattr` 等工具，用于管理文件系统对象的扩展属性。
*   **acl**: 访问控制列表库，用于为不同用户设置精细的文件访问权限。
*   **libcap**: 将root超级权限划分为一系列独立权限的库，允许普通用户执行部分特权操作。
*   **shadow**: 用于管理用户、组和密码的套件，是登录系统的核心。
*   **gcc**: GNU编译器集合，包含C、C++等编译器，是Linux世界的基石。

![](img/7620c7e229704c2a7caa021fe15f39fe_103.png)

## 继续安装更多工具

![](img/7620c7e229704c2a7caa021fe15f39fe_105.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_107.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_109.png)

在 `gcc` 编译完成后，我们继续安装后续一批软件包，直到 `bash`。

![](img/7620c7e229704c2a7caa021fe15f39fe_111.png)

*   **pkg-config**: 在软件包配置和编译阶段，向构建工具传递头文件路径和库路径的工具。
*   **ncurses**: 用于控制文本终端颜色、光标定位的库，是许多文本界面程序的基础。
*   **sed**: 流编辑器，是功能最强大的Linux工具之一。它用于处理和转换文本流。
    ```bash
    echo “foo bar” | sed ‘s/o/a/g’ # 将所有的’o’替换为’a’，输出: faa bar
    ```
    `sed` 支持正则表达式和捕获组，分隔符不限于 `/`，可以使用任何字符。
    ```bash
    echo “foo bar” | sed ‘s:o:a:g’ # 使用冒号作为分隔符，输出: faa bar
    ```
*   **psmisc**: 进程管理工具集，包含 `pstree`（以树状显示进程）、`killall` 等。
*   **gettext**: 国际化与本地化框架，用于程序的翻译。
*   **bison**: 语法分析器生成器，与 `flex` 配合，用于构建编程语言的解析器。
*   **grep**: 强大的文本搜索工具，使用正则表达式过滤行。
    ```bash
    echo -e “foo\nbar\nbaz” | grep “ba.” # 匹配包含”ba”及后接一个字符的行，输出: bar, baz
    ```
    `-v` 参数可以反向选择，即输出不匹配的行。
*   **bash**: Bourne-Again Shell，是绝大多数Linux系统的默认命令解释器，是系统的基础。

![](img/7620c7e229704c2a7caa021fe15f39fe_113.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_115.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_117.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_119.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_121.png)

## 最后一批核心工具

![](img/7620c7e229704c2a7caa021fe15f39fe_123.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_125.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_127.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_129.png)

我们创建了剩余所有包的脚本并开始编译。以下是其中部分重要工具的介绍：

![](img/7620c7e229704c2a7caa021fe15f39fe_131.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_133.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_135.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_137.png)

*   **libtool**: 封装了使用共享库的复杂性，提供一致、便携的接口。
*   **gdbm**: GNU数据库管理器，提供存储键值对、搜索和检索数据的原语。
*   **gperf**: 根据一组键生成完美哈希函数的工具，可用于优化字符串查找。
*   **expat**: 用于解析XML的C语言库。
*   **inetutils**: 基础网络工具集，如 `ping`, `telnet`, `ftp`, `traceroute`。
*   **perl**: 实用提取和报告语言，一种高级脚本语言。
*   **xml::parser**: Perl的XML解析接口（基于expat）。
*   **intltool**: 用于自动化国际化工作的工具。
*   **autoconf & automake**: GNU自动构建工具，用于生成 `configure` 脚本和 `Makefile`。
*   **kmod**: 用于管理内核模块的工具（如 `modprobe`, `insmod`）。
*   **libelf**: 用于处理ELF（可执行与可链接格式）文件的库。
*   **libffi**: 外部函数接口库，用于以可移植的方式调用不同约定的函数。
*   **openssl**: 实现SSL/TLS协议，用于加密网络连接的重要库。
*   **python**: 流行的解释型高级编程语言。
*   **ninja**: 一个注重速度的现代化构建系统（如Chrome项目使用）。
*   **meson**: 另一个构建系统，语法简洁。
*   **coreutils**: 包含最核心的Unix命令行工具，如 `ls`, `cp`, `mkdir`, `cat`, `echo` 等上百个命令。可以说，没有它，系统几乎无法操作。
*   **check**: C语言的单元测试框架。
*   **diffutils**: 包含 `diff` 和 `patch` 工具。`diff` 比较文件差异，`patch` 应用差异文件。这是版本控制系统（如Git）的核心机制。
*   **gawk**: GNU Awk，一种强大的文本处理和数据提取编程语言，语法类似C。
*   **findutils**: 包含 `find` 命令，用于在目录树中查找文件，功能极其强大。
    ```bash
    find . -name “*.sh” # 查找当前目录及子目录中所有.sh文件
    ```
*   **groff**: GNU troff文档格式化系统，用于排版和生成PDF等格式文档。
*   **grub**: GRand Unified Bootloader，我们将用它来引导最终的系统。
*   **less**: 一个功能丰富的文件查看器。
*   **gzip**: 经典的压缩工具。
*   **iproute2**: 高级网络配置工具集，用于管理路由表、策略路由等。
*   **kbd**: 键盘工具，用于加载键盘布局。
*   **libpipeline**: 用于以灵活方便的方式操作子进程管道的库。
*   **make**: 经典的自动化构建工具，通过定义目标和依赖关系来组织编译过程，功能非常强大。
*   **patch**: 用于将 `diff` 生成的补丁文件应用到原始文件上。
*   **man-db**: 包含查找和查看手册页的工具，如 `man` 命令。
*   **tar**: 经典的归档工具，用于打包和解包文件。
*   **texinfo**: 用于阅读、编写和转换Info格式文档的工具。
*   **vim**: 一款功能强大、高度可配置的文本编辑器，以其模态编辑（普通模式、插入模式等）著称。
    ```
    vim file.txt # 打开文件，默认为普通模式
    i # 进入插入模式，开始编辑
    <Esc> # 返回普通模式
    :wq # 保存并退出
    ```

## 总结

![](img/7620c7e229704c2a7caa021fe15f39fe_139.png)

![](img/7620c7e229704c2a7caa021fe15f39fe_141.png)

本节课中我们一起学习了如何批量创建安装脚本，并编译安装了LFS系统所需的大量核心软件包。我们详细探讨了从基础库（如glibc）、开发工具（如gcc、flex、bison）、系统工具（如coreutils、findutils）到编辑器（vim）等各类包的功能。这个过程虽然耗时（约10小时），但让我们深刻理解了一个可运行的Linux系统所依赖的众多组件。下一节，我们将进入初始化系统（System V init 或 systemd）的选择和配置阶段，并向最终可启动的系统迈进。