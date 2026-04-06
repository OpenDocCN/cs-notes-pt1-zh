# 041：安装额外Apache模块 🔧

在本节课中，我们将学习如何为Apache Web服务器安装额外的模块。这些模块能为你的网站提供额外的功能和支持，例如运行PHP或Python程序。

## 概述

Apache模块是增强服务器功能的附加组件。它们通常在安装Apache后单独安装，用于支持特定的编程语言或技术，如PHP或Python。Ubuntu系统提供了专门的命令来管理这些模块。

![](img/857502d2209511b80df565cab886e639_1.png)

![](img/857502d2209511b80df565cab886e639_2.png)

## 查看可用模块

![](img/857502d2209511b80df565cab886e639_4.png)

![](img/857502d2209511b80df565cab886e639_6.png)

在安装之前，我们可以先查看系统中有哪些Apache模块可用。Ubuntu的官方软件仓库通常包含了常用的模块。

![](img/857502d2209511b80df565cab886e639_7.png)

![](img/857502d2209511b80df565cab886e639_9.png)

以下是查看已安装和可用模块的命令：

![](img/857502d2209511b80df565cab886e639_11.png)

![](img/857502d2209511b80df565cab886e639_13.png)

*   `a2enmod`：用于启用一个模块。
*   `a2dismod`：用于禁用一个模块。
*   `apt search apache2` 或 `apt search libapache2-mod`：用于搜索可安装的Apache模块。

![](img/857502d2209511b80df565cab886e639_15.png)

![](img/857502d2209511b80df565cab886e639_17.png)

例如，你可以搜索与PHP或Python相关的模块。

![](img/857502d2209511b80df565cab886e639_19.png)

## 安装模块

假设我们需要为网站添加PHP支持。我们将以安装PHP 7.4版本模块为例进行说明。

安装特定模块的命令格式为：`sudo apt install libapache2-mod-php7.4`

**请注意**：安装一个模块时，系统会自动安装其依赖的其他软件包（如JSON库、Apache运行时库等），以确保模块能正常工作。你无需手动安装这些依赖。

## 管理模块状态

![](img/857502d2209511b80df565cab886e639_21.png)

安装模块后，我们需要启用它，并重新启动Apache服务使配置生效。

![](img/857502d2209511b80df565cab886e639_23.png)

以下是管理模块的步骤：

1.  **启用模块**：使用命令 `sudo a2enmod php7.4`。如果模块已启用，系统会提示“Module php7.4 already enabled”。
2.  **重启Apache**：使用命令 `sudo systemctl restart apache2` 来应用更改。
3.  **验证状态**：你可以再次运行 `sudo a2enmod php7.4` 或列出所有已启用模块来确认。
4.  **禁用模块**：如果需要禁用某个模块，使用命令 `sudo a2dismod php7.4`，然后同样需要重启Apache。

## 其他常用模块

除了PHP，Python也是常用的Web开发语言。你可以类似地搜索和安装Python模块：

![](img/857502d2209511b80df565cab886e639_25.png)

*   搜索命令：`apt search libapache2-mod-python`
*   安装命令：`sudo apt install libapache2-mod-python`

![](img/857502d2209511b80df565cab886e639_27.png)

![](img/857502d2209511b80df565cab886e639_28.png)

安装Python模块后，你就可以配置Apache来运行Django或Flask等Python Web框架了。

![](img/857502d2209511b80df565cab886e639_30.png)

![](img/857502d2209511b80df565cab886e639_32.png)

## 总结

![](img/857502d2209511b80df565cab886e639_34.png)

![](img/857502d2209511b80df565cab886e639_36.png)

本节课我们一起学习了Apache额外模块的安装与管理。关键点包括：使用`apt`命令搜索和安装模块（如`libapache2-mod-php7.4`），使用`a2enmod`和`a2dismod`来启用或禁用模块，并且记住在更改配置后必须**重启Apache服务**。掌握这些操作，你就能根据网站需求灵活扩展Apache服务器的功能了。