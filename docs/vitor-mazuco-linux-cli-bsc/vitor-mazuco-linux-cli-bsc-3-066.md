# 066：使用Chocolatey 🍫

## 概述
在本节课中，我们将学习如何在Windows系统上使用Chocolatey。这是一个强大的开源包管理器程序，它能极大地简化程序的管理工作。

## 安装Chocolatey
上一节我们介绍了Ansible的基础，本节中我们来看看如何为Windows系统配置一个强大的包管理工具。首先，我们需要在Windows系统上安装Chocolatey。

安装需要通过PowerShell进行。请确保你使用的是PowerShell 7或更高版本。以下是安装步骤：

1.  以管理员身份打开PowerShell。
2.  复制并执行以下命令：

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

执行该命令后，Chocolatey将自动完成安装。安装完成后，你就可以使用`choco`命令来管理程序了。

## 验证安装与基本使用
安装完成后，我们可以验证Chocolatey是否正常工作。在PowerShell中输入`choco`并按下回车，如果显示版本信息，则说明安装成功。

**公式/代码示例：**
```powershell
choco --version
```

Chocolatey连接到一个庞大的软件仓库，其中包含大量程序包。大部分常用软件都可以在这里找到。你可以通过社区仓库浏览可用的软件包。

## 通过Ansible远程管理软件
现在，我们将结合Ansible，演示如何远程管理Windows机器上的软件。这能让你通过一条命令，同时为多台机器安装或更新软件，非常高效。

![](img/3866def51e335647d60df08a67e10710_1.png)

![](img/3866def51e335647d60df08a67e10710_3.png)

我们将创建Ansible剧本（playbook）来安装两个软件：Google Chrome和Java。

![](img/3866def51e335647d60df08a67e10710_5.png)

### 安装Google Chrome
首先，我们创建一个Ansible剧本来安装Google Chrome。你需要知道软件在Chocolatey仓库中的准确名称。

![](img/3866def51e335647d60df08a67e10710_7.png)

以下是创建剧本的步骤：

![](img/3866def51e335647d60df08a67e10710_9.png)

![](img/3866def51e335647d60df08a67e10710_11.png)

1.  创建一个名为`google_chrome.yml`的YAML文件。
2.  在文件中输入以下内容：

![](img/3866def51e335647d60df08a67e10710_13.png)

![](img/3866def51e335647d60df08a67e10710_15.png)

![](img/3866def51e335647d60df08a67e10710_17.png)

![](img/3866def51e335647d60df08a67e10710_19.png)

```yaml
---
- name: Install Google Chrome
  hosts: windows
  tasks:
    - name: Ensure Google Chrome is installed
      win_chocolatey:
        name: googlechrome
        state: present
```

**关键参数解释：**
*   `name: googlechrome`: 指定要安装的软件包名称。
*   `state: present`: 确保软件处于“已安装”状态。

保存文件后，在命令行中运行此剧本：
```bash
ansible-playbook google_chrome.yml
```
Ansible将连接至目标Windows主机，并通过Chocolatey完成Google Chrome的安装。

![](img/3866def51e335647d60df08a67e10710_21.png)

### 安装Java Runtime
Java经常需要更新，手动操作很繁琐。使用Chocolatey和Ansible可以轻松实现批量更新。

![](img/3866def51e335647d60df08a67e10710_23.png)

接下来，我们创建另一个剧本来安装Java Runtime 8。

![](img/3866def51e335647d60df08a67e10710_25.png)

![](img/3866def51e335647d60df08a67e10710_27.png)

![](img/3866def51e335647d60df08a67e10710_29.png)

1.  创建一个名为`java.yml`的新文件。
2.  输入以下内容：

```yaml
---
- name: Install Java Runtime
  hosts: windows
  tasks:
    - name: Ensure Java Runtime 8 is installed
      win_chocolatey:
        name: javaruntime
        state: present
```

![](img/3866def51e335647d60df08a67e10710_31.png)

同样，运行这个剧本：
```bash
ansible-playbook java.yml
```
安装过程可能需要一些时间，但Ansible会自动处理所有步骤。完成后，Java Runtime 8就会被安装到目标机器上。

![](img/3866def51e335647d60df08a67e10710_33.png)

## 总结
本节课我们一起学习了Chocolatey的安装与基本使用。我们了解到Chocolatey是一个强大的Windows包管理器，能简化软件的安装、更新和移除。更重要的是，我们学会了如何将Chocolatey与Ansible结合，实现Windows程序的远程自动化管理。通过编写简单的YAML剧本，我们可以轻松地让多台机器保持一致的软件环境，极大地提升了运维效率。