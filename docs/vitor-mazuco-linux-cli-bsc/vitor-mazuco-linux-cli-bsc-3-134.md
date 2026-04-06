# 134：使用工作区实现环境隔离 🛡️

在本节课中，我们将学习如何使用 Terraform 的工作区功能来隔离不同的基础设施环境。这是一种重要的安全实践，可以确保测试环境的变更不会影响到生产环境。

## 概述

上一节我们介绍了使用远程后端存储状态文件。本节中，我们来看看如何通过工作区为不同的环境（如开发、测试、生产）创建完全隔离的状态管理空间。这就像一艘大船的水密舱壁，一个舱室进水不会影响其他舱室。

## 为什么需要环境隔离？

Terraform 可以管理大量基础设施，包括创建和销毁服务器。如果所有环境都共享同一个状态文件，一旦测试环境的代码或配置出现问题，可能会意外地影响甚至破坏生产环境。工作区通过为每个环境创建独立的状态存储空间来解决这个问题。

## 工作区核心概念

默认情况下，每个 Terraform 项目都会自动创建一个名为 `default` 的工作区。所有操作都在这个工作区内进行。我们可以创建新的工作区，并在它们之间切换，每个工作区都拥有自己独立的状态文件。

![](img/69ad5b39f0177e26f50d1b94eabb635f_1.png)

以下是管理工作区的核心命令：

*   `terraform workspace show`：显示当前使用的工作区。
*   `terraform workspace new <名称>`：创建一个新的工作区并切换到它。
*   `terraform workspace list`：列出所有可用的工作区。
*   `terraform workspace select <名称>`：切换到指定的工作区。

## 实践：使用工作区管理不同环境

![](img/69ad5b39f0177e26f50d1b94eabb635f_3.png)

让我们通过一个简单的例子来演示工作区的使用。我们将创建一个基础架构，并在不同的工作区中部署它，以观察状态的隔离效果。

### 1. 准备基础代码

![](img/69ad5b39f0177e26f50d1b94eabb635f_5.png)

首先，我们创建一个新的目录和 Terraform 配置文件 `main.tf`。此代码仅用于演示，它将部署一个简单的 AWS EC2 实例。

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0" # 示例 Ubuntu AMI ID，请根据区域替换
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleServer"
  }
}
```

### 2. 初始化与默认工作区部署

![](img/69ad5b39f0177e26f50d1b94eabb635f_7.png)

![](img/69ad5b39f0177e26f50d1b94eabb635f_9.png)

在终端中进入项目目录，执行初始化并应用配置。此时，所有操作都在默认的 `default` 工作区中进行。

```bash
terraform init
terraform apply
```

执行后，一个 EC2 实例会被创建，其状态保存在 `default` 工作区下。

### 3. 创建并使用新工作区

![](img/69ad5b39f0177e26f50d1b94eabb635f_11.png)

现在，我们创建一个用于测试的新工作区 `test1`。

![](img/69ad5b39f0177e26f50d1b94eabb635f_13.png)

![](img/69ad5b39f0177e26f50d1b94eabb635f_15.png)

```bash
terraform workspace new test1
```

![](img/69ad5b39f0177e26f50d1b94eabb635f_17.png)

创建后，Terraform 会自动切换到 `test1` 工作区。再次运行 `terraform apply` 命令。

```bash
terraform apply
```

![](img/69ad5b39f0177e26f50d1b94eabb635f_19.png)

![](img/69ad5b39f0177e26f50d1b94eabb635f_21.png)

**关键点**：虽然我们应用的是完全相同的代码，但 Terraform 会在 `test1` 工作区下创建另一个全新的 EC2 实例。`default` 工作区中的实例不受任何影响。

### 4. 管理工作区与状态隔离

![](img/69ad5b39f0177e26f50d1b94eabb635f_23.png)

我们可以列出所有工作区来查看当前状态。星号 (`*`) 标记了当前活跃的工作区。

![](img/69ad5b39f0177e26f50d1b94eabb635f_25.png)

![](img/69ad5b39f0177e26f50d1b94eabb635f_27.png)

```bash
terraform workspace list
```

![](img/69ad5b39f0177e26f50d1b94eabb635f_29.png)

输出可能类似于：
```
  default
* test1
```

![](img/69ad5b39f0177e26f50d1b94eabb635f_31.png)

要切换回 `default` 工作区，使用 `select` 命令：

```bash
terraform workspace select default
```

现在，如果我们运行 `terraform destroy`，它将只销毁 `default` 工作区中管理的实例，而 `test1` 工作区中的实例会完好无损。这完美演示了环境隔离。

![](img/69ad5b39f0177e26f50d1b94eabb635f_33.png)

### 5. 结合远程后端

![](img/69ad5b39f0177e26f50d1b94eabb635f_35.png)

![](img/69ad5b39f0177e26f50d1b94eabb635f_37.png)

工作区的强大之处在于能与远程后端（如 AWS S3）协同工作。当配置了 S3 后端后，每个工作区的状态文件会被存储在 S3 桶内以工作区命名的不同文件夹中。

例如，S3 桶内可能会生成如下结构：
```
s3://your-terraform-bucket/
├── env:/
│   ├── default/terraform.tfstate
│   ├── test1/terraform.tfstate
│   └── test2/terraform.tfstate
```

![](img/69ad5b39f0177e26f50d1b94eabb635f_39.png)

这种结构使得每个环境的状态文件物理上也完全分离，进一步提升了安全性和可管理性。

![](img/69ad5b39f0177e26f50d1b94eabb635f_41.png)

## 总结

![](img/69ad5b39f0177e26f50d1b94eabb635f_43.png)

本节课中我们一起学习了 Terraform 工作区的核心概念与操作方法。我们了解到：

![](img/69ad5b39f0177e26f50d1b94eabb635f_45.png)

1.  **工作区**是隔离不同基础设施环境（如开发、测试、生产）的有效工具。
2.  通过 `terraform workspace` 系列命令可以轻松创建、列出和切换工作区。
3.  每个工作区维护自己独立的状态，对一个工作区执行的操作（如 `apply` 或 `destroy`）不会影响其他工作区。
4.  当与远程后端（如 S3）结合使用时，状态文件会按工作区自动分离存储，实现了物理层面的隔离。

![](img/69ad5b39f0177e26f50d1b94eabb635f_47.png)

![](img/69ad5b39f0177e26f50d1b94eabb635f_48.png)

![](img/69ad5b39f0177e26f50d1b94eabb635f_50.png)

使用工作区是一种推荐的最佳实践，它能为你管理复杂的基础设施提供至关重要的安全保障。