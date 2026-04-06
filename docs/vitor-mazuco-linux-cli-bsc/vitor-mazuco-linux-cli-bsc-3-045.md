# 045：安装Ansible 🚀

在本节课中，我们将学习如何在Linux系统上安装自动化工具Ansible。我们将以基于Red Hat的系统（如Fedora）为例进行演示，并涵盖必要的准备工作。

---

## 概述

Ansible是一款强大的自动化工具，它可以帮助我们高效地管理多台服务器。在开始使用Ansible之前，我们需要先将其安装到我们的系统上。本节课将指导你完成在基于Red Hat的Linux发行版上安装Ansible的完整步骤。

---

![](img/40d59a16efe3945cb6ed86f09d6a4f64_1.png)

![](img/40d59a16efe3945cb6ed86f09d6a4f64_2.png)

## 系统与Python准备

上一节我们介绍了Ansible的基本概念，本节中我们来看看安装前的准备工作。Ansible基于Python运行，因此首先需要确保系统中已安装Python 3。

![](img/40d59a16efe3945cb6ed86f09d6a4f64_4.png)

![](img/40d59a16efe3945cb6ed86f09d6a4f64_5.png)

如果你使用的是基于Red Hat的系统（如Fedora、CentOS），可以使用以下命令安装Python 3：

![](img/40d59a16efe3945cb6ed86f09d6a4f64_7.png)

```bash
sudo dnf install python3
```

如果你使用的是基于Debian的系统（如Ubuntu），则使用对应的包管理器命令：

```bash
sudo apt install python3
```

![](img/40d59a16efe3945cb6ed86f09d6a4f64_9.png)

![](img/40d59a16efe3945cb6ed86f09d6a4f64_10.png)

安装完成后，你可以运行以下命令来验证Python 3是否已成功安装及其版本：

![](img/40d59a16efe3945cb6ed86f09d6a4f64_11.png)

```bash
python3 -V
```

![](img/40d59a16efe3945cb6ed86f09d6a4f64_13.png)

此命令将输出已安装的Python版本号，确认安装成功。

![](img/40d59a16efe3945cb6ed86f09d6a4f64_15.png)

---

![](img/40d59a16efe3945cb6ed86f09d6a4f64_17.png)

## 启用EPEL仓库

![](img/40d59a16efe3945cb6ed86f09d6a4f64_19.png)

在基于Red Hat的系统中，某些额外的软件包存放在EPEL（Extra Packages for Enterprise Linux）仓库中。Ansible可能位于此仓库，因此我们需要启用它。

如果你的系统尚未启用EPEL仓库，可以运行以下命令进行安装：

![](img/40d59a16efe3945cb6ed86f09d6a4f64_21.png)

```bash
sudo dnf install epel-release
```

![](img/40d59a16efe3945cb6ed86f09d6a4f64_23.png)

![](img/40d59a16efe3945cb6ed86f09d6a4f64_24.png)

如果系统已经启用了EPEL仓库，命令会提示该软件包已安装。启用EPEL仓库能确保我们可以访问到Ansible及其依赖包。

![](img/40d59a16efe3945cb6ed86f09d6a4f64_26.png)

---

![](img/40d59a16efe3945cb6ed86f09d6a4f64_28.png)

## 安装Ansible

![](img/40d59a16efe3945cb6ed86f09d6a4f64_30.png)

完成上述准备工作后，安装Ansible本身非常简单。

![](img/40d59a16efe3945cb6ed86f09d6a4f64_31.png)

只需执行以下命令即可：

```bash
sudo dnf install ansible
```

安装过程会自动处理所有依赖。安装完成后，系统会提示安装成功。

![](img/40d59a16efe3945cb6ed86f09d6a4f64_33.png)

为了验证Ansible是否正确安装，可以查看其版本信息：

![](img/40d59a16efe3945cb6ed86f09d6a4f64_34.png)

![](img/40d59a16efe3945cb6ed86f09d6a4f64_35.png)

```bash
ansible --version
```

![](img/40d59a16efe3945cb6ed86f09d6a4f64_37.png)

此命令会输出Ansible的详细版本信息，包括其依赖的Python版本、可执行文件路径、模块路径和配置文件位置等。这些信息对于后续的配置和故障排查非常重要。

![](img/40d59a16efe3945cb6ed86f09d6a4f64_38.png)

---

![](img/40d59a16efe3945cb6ed86f09d6a4f64_40.png)

![](img/40d59a16efe3945cb6ed86f09d6a4f64_41.png)

## 核心文件与下节预告

![](img/40d59a16efe3945cb6ed86f09d6a4f64_43.png)

安装Ansible后，最重要的部分是其配置文件。良好的配置结构是高效使用Ansible进行DevOps管理的基础，尤其是在管理规模增长时，能避免混乱。

![](img/40d59a16efe3945cb6ed86f09d6a4f64_44.png)

在下一节课中，我们将专门探讨Ansible的配置文件。我们将深入了解：
*   配置文件的最佳实践和存放位置。
*   如何结构化地组织配置，以适应不断增长的管理需求。
*   配置文件的具体内容和工作原理。

![](img/40d59a16efe3945cb6ed86f09d6a4f64_46.png)

本节课中我们一起学习了在Linux系统上安装Ansible的完整流程，从检查Python环境、启用必要的软件仓库到执行安装命令并验证结果。现在，你的系统已经准备好了Ansible运行环境。