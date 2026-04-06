# 127：创建并配置 Web 服务器实例 🚀

在本节课中，我们将学习如何使用 Terraform 在 AWS 上创建一个运行 Apache Web 服务器的实例。我们将配置用户数据来自动化安装和部署，并设置安全组规则以允许外部访问。最后，我们会将代码变更提交到 GitHub 进行版本管理。

---

![](img/32db23635bab57f41f37d47f8e7fa6aa_1.png)

## 概述

![](img/32db23635bab57f41f37d47f8e7fa6aa_3.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_5.png)

我们将创建一个简单的“Hello World”网页，并通过 `curl` 命令远程测试服务器。核心步骤包括：编写用户数据脚本以初始化实例、配置安全组开放端口 80，以及将 Terraform 代码的变更推送到 GitHub。

![](img/32db23635bab57f41f37d47f8e7fa6aa_7.png)

---

## 配置用户数据

![](img/32db23635bab57f41f37d47f8e7fa6aa_9.png)

上一节我们介绍了 Terraform 的基本结构，本节中我们来看看如何为实例配置启动脚本。

![](img/32db23635bab57f41f37d47f8e7fa6aa_11.png)

用户数据（User Data）是在实例首次启动时执行的脚本。我们将使用它来安装 Apache 并创建一个简单的 HTML 页面。

以下是用户数据脚本的内容：

```bash
#!/bin/bash
apt update
apt install -y apache2
systemctl start apache2
echo "Hello World" > /var/www/html/index.html
```

![](img/32db23635bab57f41f37d47f8e7fa6aa_13.png)

在 Terraform 代码中，我们使用 `user_data` 参数和 `<<-EOF ... EOF` 语法来嵌入这个多行脚本。同时，我们设置 `user_data_replace_on_change = true`。这意味着，如果用户数据内容发生更改，Terraform 将销毁旧实例并创建一个新实例。因为用户数据仅在首次启动时运行，所以这个设置通常是合理的。

![](img/32db23635bab57f41f37d47f8e7fa6aa_15.png)

---

![](img/32db23635bab57f41f37d47f8e7fa6aa_17.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_19.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_21.png)

## 配置安全组规则

![](img/32db23635bab57f41f37d47f8e7fa6aa_23.png)

现在，我们需要配置网络访问规则。AWS 安全组默认阻止所有入站和出站流量。

![](img/32db23635bab57f41f37d47f8e7fa6aa_25.png)

安全组负责管理实例的入口（Ingress）和出口（Egress）规则。我们将创建一个新的安全组资源。

![](img/32db23635bab57f41f37d47f8e7fa6aa_27.png)

以下是安全组资源的配置示例：

```hcl
resource "aws_security_group" "instance" {
  name = "instance-sg-test"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # 允许所有IP访问，生产环境应限制
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"] # 允许所有出站流量
  }
}
```

![](img/32db23635bab57f41f37d47f8e7fa6aa_29.png)

**说明**：
*   `ingress` 规则开放了 TCP 协议的 80 端口（HTTP），`cidr_blocks` 设置为 `0.0.0.0/0` 表示允许任何 IP 地址访问。在实际生产环境中，应将其限制为特定的 IP 范围。
*   `egress` 规则允许所有出站流量，这是常见配置，以便实例可以访问外部网络（如下载软件包）。

![](img/32db23635bab57f41f37d47f8e7fa6aa_31.png)

---

![](img/32db23635bab57f41f37d47f8e7fa6aa_33.png)

## 关联安全组与实例

![](img/32db23635bab57f41f37d47f8e7fa6aa_35.png)

配置好安全组后，我们需要将其关联到 EC2 实例上。

在 EC2 实例的资源定义中，通过 `vpc_security_group_ids` 参数引用我们创建的安全组 ID。Terraform 使用特定的语法进行资源间引用。

关联语法如下：
`vpc_security_group_ids = [aws_security_group.instance.id]`

![](img/32db23635bab57f41f37d47f8e7fa6aa_37.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_38.png)

这行代码放置在 `aws_instance` 资源块内。它告诉 AWS，这个实例应该应用我们定义的名为 `instance` 的安全组的规则。

![](img/32db23635bab57f41f37d47f8e7fa6aa_40.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_42.png)

---

![](img/32db23635bab57f41f37d47f8e7fa6aa_44.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_46.png)

## 生成资源关系图

![](img/32db23635bab57f41f37d47f8e7fa6aa_48.png)

在应用配置之前，我们可以生成一个资源关系图来可视化 Terraform 管理的资源及其依赖关系。

![](img/32db23635bab57f41f37d47f8e7fa6aa_50.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_51.png)

使用命令 `terraform graph` 可以生成图定义。我们可以将输出粘贴到 [Graphviz Online](https://dreampuf.github.io/GraphvizOnline) 等工具中查看图表。图表会显示 `aws_instance` 和 `aws_security_group` 资源都依赖于 `aws` 提供商，帮助我们理解部署结构。

---

![](img/32db23635bab57f41f37d47f8e7fa6aa_53.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_55.png)

## 部署与测试

![](img/32db23635bab57f41f37d47f8e7fa6aa_57.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_59.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_61.png)

所有配置完成后，就可以部署资源并进行测试了。

![](img/32db23635bab57f41f37d47f8e7fa6aa_63.png)

首先，运行 `terraform apply` 命令来创建资源。Terraform 会先创建安全组，然后创建 EC2 实例。创建完成后，在 AWS 控制台找到实例的公有 IPv4 地址。

![](img/32db23635bab57f41f37d47f8e7fa6aa_65.png)

然后，打开浏览器或使用 `curl` 命令访问该 IP 地址。如果看到 “Hello World” 页面，说明 Apache 服务器已成功运行，并且安全组规则生效。

![](img/32db23635bab57f41f37d47f8e7fa6aa_67.png)

---

![](img/32db23635bab57f41f37d47f8e7fa6aa_69.png)

## 提交变更到 GitHub

![](img/32db23635bab57f41f37d47f8e7fa6aa_71.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_73.png)

最后，我们将代码变更提交到 GitHub，以记录基础设施的更改。

使用以下 Git 命令来提交和推送更改：
```bash
git add main.tf
git commit -m “feat: add web server instance with apache and security group”
git pull origin main
git push origin main
```
提交后，可以在 GitHub 仓库的提交历史中查看具体的代码变更（增、删、改的行数），这对于团队协作和审计至关重要。

![](img/32db23635bab57f41f37d47f8e7fa6aa_75.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_77.png)

---

![](img/32db23635bab57f41f37d47f8e7fa6aa_79.png)

## 总结

![](img/32db23635bab57f41f37d47f8e7fa6aa_81.png)

本节课中我们一起学习了如何使用 Terraform 在 AWS 上部署一个基础的 Web 服务器。我们掌握了三个核心操作：
1.  使用 **用户数据** 自动化实例的初始化和服务部署。
2.  配置 **安全组** 规则来控制实例的网络访问。
3.  通过 **资源引用**（如 `aws_security_group.instance.id`）将安全组关联到实例。

![](img/32db23635bab57f41f37d47f8e7fa6aa_83.png)

![](img/32db23635bab57f41f37d47f8e7fa6aa_84.png)

我们还了解了如何生成资源关系图、部署测试服务，以及将基础设施代码的变更同步到 GitHub 进行版本管理。这些是使用 Terraform 进行基础设施即代码（IaC）管理的基础技能。