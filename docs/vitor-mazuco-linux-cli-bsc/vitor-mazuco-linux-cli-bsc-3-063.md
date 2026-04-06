# 063：使用Ansible Galaxy安装与配置NGINX 🚀

在本节课中，我们将学习如何使用Ansible Galaxy来安装和配置一个广泛使用的Web服务器——NGINX。我们将从Ansible Galaxy查找官方角色开始，完成NGINX的安装，并处理相关的防火墙配置，最终验证Web服务器是否成功运行。

## 查找与选择官方角色

上一节我们介绍了Ansible Galaxy的基本概念。本节中我们来看看如何从中查找和选择一个可靠的角色来安装软件。

![](img/0873a40d6119122282f38df508c0b385_1.png)

在Ansible Galaxy中搜索“nginx”，会找到许多由不同作者提供的角色。为了确保稳定性和可靠性，建议优先选择标记为“official”或下载量最高的官方角色。例如，我们可以选择一个名为 `nginxinc.nginx` 的官方角色。

![](img/0873a40d6119122282f38df508c0b385_3.png)

选择角色时，应查看其README部分，其中通常包含指向GitHub仓库的官方链接，以便确认其来源和详细信息。

![](img/0873a40d6119122282f38df508c0b385_5.png)

## 安装NGINX角色

找到合适的角色后，下一步就是将其安装到本地，以便在Playbook中使用。

![](img/0873a40d6119122282f38df508c0b385_7.png)

![](img/0873a40d6119122282f38df508c0b385_9.png)

安装过程非常简单。在Ansible Galaxy上，角色页面通常会提供安装命令。对于NGINX角色，命令可能类似于：
```bash
ansible-galaxy install nginxinc.nginx
```
执行此命令后，角色及其所有相关文件和配置模板将被下载到本地的Ansible角色目录中（通常是 `~/.ansible/roles/`）。我们可以使用 `ansible-galaxy list` 命令来查看已安装的所有角色。

## 创建Playbook安装NGINX

![](img/0873a40d6119122282f38df508c0b385_11.png)

角色安装完成后，我们需要编写一个Playbook来实际执行NGINX的安装和基础配置。

以下是创建一个名为 `nginx.yml` 的Playbook文件的步骤。该Playbook将调用我们安装的NGINX角色，并确保HTTP（端口80）和HTTPS（端口443）服务所需的端口是开放的。

![](img/0873a40d6119122282f38df508c0b385_13.png)

```yaml
---
- hosts: web_servers
  become: yes
  roles:
    - role: nginxinc.nginx
  tasks:
    - name: Ensure HTTP and HTTPS ports are open
      firewalld:
        port: "{{ item }}/tcp"
        permanent: yes
        state: enabled
      loop:
        - 80
        - 443
```
运行此Playbook的命令是：
```bash
ansible-playbook nginx.yml
```

## 处理操作系统兼容性与防火墙

![](img/0873a40d6119122282f38df508c0b385_15.png)

![](img/0873a40d6119122282f38df508c0b385_17.png)

在部署过程中，可能会遇到操作系统兼容性和防火墙配置的问题，我们需要妥善解决它们。

![](img/0873a40d6119122282f38df508c0b385_19.png)

首先，需要注意的是，并非所有Ansible Galaxy角色都支持全部Linux发行版。例如，某些角色可能明确支持Ubuntu、Red Hat和Debian，但不支持Alpine Linux。因此，在选用角色前，务必查阅其文档中的兼容性说明。

![](img/0873a40d6119122282f38df508c0b385_21.png)

其次，为了让Web服务器能够被访问，必须在目标服务器的防火墙上开放端口（如80和443）。不同的Linux发行版使用不同的防火墙管理工具（例如Ubuntu使用`UFW`，而CentOS/RHEL使用`firewalld`）。我们需要在Playbook中根据目标系统类型，添加相应的防火墙配置任务。

![](img/0873a40d6119122282f38df508c0b385_23.png)

## 配置防火墙规则

为了确保NGINX服务可访问，我们必须配置防火墙规则。这需要根据目标服务器的操作系统使用不同的Ansible模块。

![](img/0873a40d6119122282f38df508c0b385_25.png)

对于使用`firewalld`的系统（如CentOS），我们可以使用`firewalld`模块。对于使用`UFW`的系统（如Ubuntu），则需使用`ufw`模块。在Playbook中，我们可以通过条件判断（`when`语句）来为不同的系统应用正确的防火墙任务。

以下是一个配置防火墙的Playbook示例 `firewall.yml`：
```yaml
---
- hosts: web_servers
  become: yes
  tasks:
    - name: Open ports for CentOS/RHEL (firewalld)
      firewalld:
        port: "{{ item }}/tcp"
        zone: public
        permanent: yes
        state: enabled
      loop:
        - 80
        - 443
      when: ansible_os_family == "RedHat"

    - name: Open ports for Ubuntu (UFW)
      ufw:
        rule: allow
        port: "{{ item }}"
        proto: tcp
      loop:
        - 80
        - 443
      when: ansible_os_family == "Debian"
```
运行此Playbook来配置防火墙：
```bash
ansible-playbook firewall.yml
```

## 验证NGINX安装

![](img/0873a40d6119122282f38df508c0b385_27.png)

所有配置完成后，最后一步是验证NGINX是否已成功安装并运行。

![](img/0873a40d6119122282f38df508c0b385_29.png)

我们可以使用`curl`命令从命令行测试Web服务器是否响应：
```bash
curl http://<your_server_ip>
```
如果安装成功，该命令将返回NGINX的默认欢迎页面HTML代码。

![](img/0873a40d6119122282f38df508c0b385_31.png)

此外，也可以直接在Web浏览器中输入服务器的IP地址。如果看到NGINX的欢迎页面，则证明安装和配置全部成功。

![](img/0873a40d6119122282f38df508c0b385_33.png)

![](img/0873a40d6119122282f38df508c0b385_35.png)

---

![](img/0873a40d6119122282f38df508c0b385_37.png)

![](img/0873a40d6119122282f38df508c0b385_39.png)

本节课中我们一起学习了如何使用Ansible Galaxy自动化安装和配置NGINX Web服务器。关键步骤包括：在Galaxy中查找并安装官方角色、编写Playbook执行部署、处理跨操作系统的防火墙配置，以及最终验证服务状态。掌握这个方法，可以高效地管理多台服务器的Web服务部署。