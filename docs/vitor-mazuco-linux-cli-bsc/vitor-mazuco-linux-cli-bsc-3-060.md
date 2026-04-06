# 060：在Ansible中使用Jinja模板 🧩

![](img/ab0653c53ea045cc12c2f32369572f4a_1.png)

在本节课中，我们将学习如何在Ansible中使用Jinja2模板引擎。Jinja2是一个面向Python编程的Web模板引擎，它与Ansible完全兼容。我们将通过一个创建服务器维护通知消息的实例，来掌握使用模板复制文件到多台主机的核心方法。

## 概述

Ansible的`copy`模块常用于将文件复制到被管理的主机上。然而，当需要向多台机器分发文件时，手动操作效率低下。Ansible的模板功能，特别是结合Jinja2，可以高效地解决这个问题。本节我们将创建一个Jinja2模板，并利用它向所有服务器分发统一的维护通知。

![](img/ab0653c53ea045cc12c2f32369572f4a_3.png)

## 创建模板目录

![](img/ab0653c53ea045cc12c2f32369572f4a_5.png)

首先，我们需要在Ansible项目目录下创建一个名为`templates`的专用文件夹。这个文件夹名称是固定的，Ansible会在此寻找模板文件。

![](img/ab0653c53ea045cc12c2f32369572f4a_7.png)

```bash
mkdir templates
```

![](img/ab0653c53ea045cc12c2f32369572f4a_9.png)

## 编写Jinja2模板文件

接下来，在`templates`目录中创建我们的模板文件，例如`motd.j2`。Jinja2模板使用特定的语法结构，而非简单的键值对。

以下是模板文件的基本结构元素：

*   **注释**：使用 `{# ... #}` 包裹，内容不会被渲染。
*   **表达式**：使用 `{% ... %}` 包裹，用于设置变量、控制流程等。
*   **变量渲染**：使用 `{{ ... }}` 包裹，用于输出变量或事实（facts）的值。

![](img/ab0653c53ea045cc12c2f32369572f4a_11.png)

![](img/ab0653c53ea045cc12c2f32369572f4a_13.png)

现在，我们来编写一个生成维护消息的模板：

```jinja2
{# 这是一个注释，不会被包含在最终文件中 #}

{# 设置日期和时间变量 #}
{% set maintenance_date = '2022-05-08' %}
{% set start_time = '02:00:00' %}
{% set end_time = '03:00:00' %}

{# 定义日期时间格式 #}
{% set date_format = '%Y-%m-%d' %}
{% set time_format = '%H:%M:%S' %}

{# 输出维护消息 #}
服务器 {{ ansible_facts['fqdn'] }} (IP: {{ ansible_facts['default_ipv4']['address'] }}) 将于
{{ maintenance_date }} 的 {{ start_time }} 至 {{ end_time }} (UTC-8) 进行计划维护。
请提前做好安排。
```

![](img/ab0653c53ea045cc12c2f32369572f4a_15.png)

**代码解释**：
*   `{% set ... %}`：用于在模板内定义变量，如`maintenance_date`。
*   `{{ ansible_facts[‘fqdn’] }}`：引用Ansible收集的“完全限定域名”事实。
*   `{{ ansible_facts[‘default_ipv4’][‘address’] }}`：引用Ansible收集的“默认IPv4地址”事实。

![](img/ab0653c53ea045cc12c2f32369572f4a_17.png)

## 创建Ansible Playbook

![](img/ab0653c53ea045cc12c2f32369572f4a_19.png)

模板本身不会执行，我们需要一个Playbook来调用它。在Ansible项目根目录（与`templates`目录同级）创建Playbook文件，例如`update_motd.yml`。

![](img/ab0653c53ea045cc12c2f32369572f4a_21.png)

```yaml
---
- name: 使用模板更新服务器消息
  hosts: all  # 针对所有在inventory中定义的主机
  become: yes # 使用root权限，因为/etc/motd是系统文件
  tasks:
    - name: 将模板渲染并复制到目标主机
      template:
        src: motd.j2                # 源模板文件，位于templates目录下
        dest: /etc/motd             # 目标路径，即消息文件
        owner: root
        group: root
        mode: '0644'
```

**代码解释**：
*   `hosts: all`：任务将针对清单文件（`hosts`）中定义的所有主机执行。
*   `become: yes`：以特权用户（如root）运行，因为`/etc/motd`文件通常需要权限。
*   `template`模块：这是核心模块，它读取`src`指定的Jinja2模板，渲染其中的变量，然后将生成的内容复制到`dest`指定的远程主机路径。

## 运行Playbook并验证结果

使用`ansible-playbook`命令执行我们编写的Playbook。

![](img/ab0653c53ea045cc12c2f32369572f4a_23.png)

```bash
ansible-playbook update_motd.yml
```

![](img/ab0653c53ea045cc12c2f32369572f4a_25.png)

执行成功后，Playbook会输出任务执行摘要，显示在所有主机上更改成功。

![](img/ab0653c53ea045cc12c2f32369572f4a_27.png)

![](img/ab0653c53ea045cc12c2f32369572f4a_28.png)

![](img/ab0653c53ea045cc12c2f32369572f4a_30.png)

为了验证，我们可以登录到任意一台被管理的主机（或使用Ansible的`shell`模块）查看`/etc/motd`文件的内容：

![](img/ab0653c53ea045cc12c2f32369572f4a_32.png)

![](img/ab0653c53ea045cc12c2f32369572f4a_34.png)

```bash
# 示例：查看web1服务器的消息
ansible web1 -m shell -a "cat /etc/motd"
```

你应该能看到根据模板生成的、包含该服务器特定FQDN和IP地址的维护通知消息。

## 总结

![](img/ab0653c53ea045cc12c2f32369572f4a_36.png)

![](img/ab0653c53ea045cc12c2f32369572f4a_38.png)

本节课我们一起学习了Ansible中Jinja2模板的核心用法。我们了解到，通过将静态文件转换为模板（`.j2`），并利用`{{ }}`插入动态变量（如主机事实），可以轻松实现一份配置、多处复用的目标。`template`模块负责最终的渲染和分发工作。这种方法极大地简化了向大批量服务器分发配置文件或脚本的管理工作，是Ansible实现自动化配置的关键技能之一。