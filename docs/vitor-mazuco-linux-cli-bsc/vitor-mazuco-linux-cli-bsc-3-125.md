# 125：使用 Terraform 启动 AWS EC2 实例 🚀

在本节课中，我们将学习如何使用 Terraform 在 AWS 上创建和管理一个 EC2 实例。我们将从配置 Terraform 环境开始，编写基础设施即代码，并最终启动一个虚拟机。

## 概述

Terraform 是一种基础设施即代码工具，它允许你使用声明性配置文件来定义和部署云资源。本节我们将完成从环境配置到创建第一个 AWS EC2 实例的全过程。

---

## 配置 AWS 凭证

安装 Terraform 后，第一步是配置 AWS 访问凭证。你需要使用在 AWS 课程中获取的访问密钥 ID 和秘密访问密钥。

有两种主要方式管理这些凭证：临时设置或永久保存。

### 临时设置凭证

使用 `export` 命令临时设置环境变量。这种方式在每次登录或重启后需要重新设置。

以下是设置命令：
```bash
export AWS_ACCESS_KEY_ID="你的访问密钥ID"
export AWS_SECRET_ACCESS_KEY="你的秘密访问密钥"
```

### 永久保存凭证

为了长期使用的便利性，可以将凭证永久保存在用户配置文件中。Linux 系统在这方面比 Windows 更安全。

每个 Linux 用户都有自己的配置文件，例如 `~/.bashrc` 或 `~/.bash_profile`。使用 root 用户或其他用户时，可以编辑对应的文件。

以下是编辑配置文件并添加凭证的步骤：
1.  使用文本编辑器打开配置文件，例如 `vi ~/.bashrc`。
2.  在文件末尾添加以下两行：
    ```bash
    export AWS_ACCESS_KEY_ID="你的访问密钥ID"
    export AWS_SECRET_ACCESS_KEY="你的秘密访问密钥"
    ```
3.  保存并退出编辑器。
4.  重启机器或运行 `source ~/.bashrc` 使配置生效。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_1.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_3.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_5.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_6.png)

配置完成后，Terraform 在运行时将自动使用这些凭证。如果你有多个 AWS 用户，可以在 Linux 上创建同名用户并分别配置，这样可以精确匹配 AWS 上的权限。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_8.png)

---

![](img/19cbefdd11a3bbfe492c09b41e430aa3_10.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_12.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_14.png)

## 创建 Terraform 项目结构

配置好凭证后，接下来创建我们的 Terraform 项目目录和配置文件。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_16.png)

首先，创建一个项目文件夹并进入：
```bash
mkdir terraform
cd terraform
```

![](img/19cbefdd11a3bbfe492c09b41e430aa3_18.png)

然后，创建一个名为 `main.tf` 的文件。这是 Terraform 的主配置文件，使用 `.tf` 扩展名和 HCL 语言。

你可以使用任何喜欢的文本编辑器，如 Vim、Sublime 或 VS Code。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_20.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_22.png)

---

## 编写 Terraform 配置文件

![](img/19cbefdd11a3bbfe492c09b41e430aa3_24.png)

在 `main.tf` 文件中，我们将定义要创建的 AWS 资源。

文件开头可以添加注释。然后，我们需要指定提供商和区域。

以下是 `main.tf` 文件的基础结构：
```hcl
# 定义 AWS 提供商和区域
provider "aws" {
  region = "sa-east-1" # 例如，使用圣保罗区域
}
```

`provider` 块指定了云服务商（这里是 AWS）。`region` 参数指定了资源创建的数据中心区域，请根据你的账户和地理位置选择合适的区域代码。

接下来，我们需要定义要创建的具体资源，即一个 EC2 实例。

定义资源需要两个关键信息：
1.  **AMI ID**：这是虚拟机的操作系统镜像代码。
2.  **实例类型**：这是虚拟机的硬件配置规格。

### 获取 AMI ID

AMI ID 无法猜测，必须从 AWS 控制台获取。

以下是查找免费套餐可用 AMI 的步骤：
1.  登录 AWS 控制台，进入 EC2 服务。
2.  在左侧导航栏点击“AMIs”。
3.  在筛选条件中，选择“免费套餐资格”、“Linux”类别和“64位 (x86)”架构。
4.  从结果中选择一个系统，例如 “Ubuntu Server”。复制其 AMI ID。

**注意**：AMI ID 是唯一的，并且会随区域和版本更新而变化，请务必从控制台获取最新的 ID。

### 选择实例类型

![](img/19cbefdd11a3bbfe492c09b41e430aa3_26.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_28.png)

实例类型决定了虚拟机的 CPU、内存等硬件配置。对于免费套餐或学习用途，应选择成本最低的类型，如 `t2.micro`。

你可以在 AWS 文档中查看各实例类型的详细规格和定价。

---

## 定义 EC2 实例资源

现在，我们可以在 `main.tf` 文件中添加 `resource` 块来定义 EC2 实例。

以下是完整的 `main.tf` 文件示例：
```hcl
provider "aws" {
  region = "sa-east-1"
}

resource "aws_instance" "example_server" {
  ami           = "ami-0abcdef1234567890" # 替换为你的 Ubuntu AMI ID
  instance_type = "t2.micro"

  tags = {
    Name = "MyUbuntuServer"
  }
}
```

![](img/19cbefdd11a3bbfe492c09b41e430aa3_30.png)

代码解析：
*   `resource "aws_instance" "example_server"`：定义一个 AWS EC2 实例资源。`"example_server"` 是此资源在 Terraform 配置内部的逻辑名称，必须唯一。
*   `ami`：填入你从控制台复制的 AMI ID。
*   `instance_type`：指定实例规格，这里使用 `t2.micro`。
*   `tags`：为资源添加标签，`Name` 标签会显示在 AWS 控制台中，便于识别。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_32.png)

保存文件，配置工作就完成了。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_33.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_35.png)

---

![](img/19cbefdd11a3bbfe492c09b41e430aa3_37.png)

## 初始化与规划

![](img/19cbefdd11a3bbfe492c09b41e430aa3_39.png)

在应用配置之前，需要初始化 Terraform 工作目录。

运行以下命令：
```bash
terraform init
```
这个命令会下载必要的提供商插件（这里是 AWS），并初始化后端，创建一些隐藏的配置文件。

初始化成功后，可以使用 `plan` 命令预览 Terraform 将要执行的操作。

运行以下命令：
```bash
terraform plan
```
`terraform plan` 会生成一个执行计划，清晰地列出哪些资源将被创建（标记为 `+`）、修改（`~`）或销毁（`-`）。这是一个非常重要的安全检查步骤，确保即将发生的变更符合预期。

---

## 应用配置并创建实例

确认执行计划无误后，就可以应用配置来真正创建资源了。

运行以下命令：
```bash
terraform apply
```
Terraform 会再次显示执行计划，并提示你确认是否继续。输入 `yes` 后，它将开始创建 EC2 实例。

创建过程通常需要几十秒。完成后，你可以在 AWS EC2 控制台中看到新创建的实例，其名称正是我们在 `tags` 中定义的 `"MyUbuntuServer"`。

---

![](img/19cbefdd11a3bbfe492c09b41e430aa3_41.png)

## 修改与更新基础设施

基础设施即代码的优势在于易于修改。例如，如果想更改实例名称，只需编辑 `main.tf` 文件中的 `tags` 部分，然后再次运行 `terraform apply`。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_43.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_45.png)

Terraform 会计算出差异（这次是修改 `Name` 标签），并在你确认后更新云端的资源。你无需手动登录控制台进行操作。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_47.png)

---

![](img/19cbefdd11a3bbfe492c09b41e430aa3_49.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_51.png)

## 资源清理

![](img/19cbefdd11a3bbfe492c09b41e430aa3_53.png)

学习完成后，为避免产生不必要的费用，可以销毁创建的资源。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_55.png)

运行以下命令：
```bash
terraform destroy
```
该命令会列出所有将被销毁的资源，经确认后，Terraform 将删除在 AWS 中创建的 EC2 实例。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_57.png)

![](img/19cbefdd11a3bbfe492c09b41e430aa3_59.png)

**重要提示**：对于免费套餐用户，请务必关注 AWS 的免费额度限制，并记得在不需要时停止或终止实例，以免产生意外费用。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_61.png)

---

![](img/19cbefdd11a3bbfe492c09b41e430aa3_63.png)

## 总结

![](img/19cbefdd11a3bbfe492c09b41e430aa3_65.png)

本节课中，我们一起学习了使用 Terraform 管理 AWS 基础设施的核心流程：
1.  **配置凭证**：设置了 AWS 访问密钥。
2.  **编写代码**：使用 HCL 语言定义了提供商和 EC2 实例资源。
3.  **初始化与规划**：使用 `terraform init` 和 `plan` 命令准备和预览变更。
4.  **应用与创建**：使用 `terraform apply` 命令在 AWS 上创建了虚拟机。
5.  **变更管理**：通过修改代码并重新应用，实现了基础设施的更新。
6.  **资源清理**：使用 `terraform destroy` 安全地移除资源。

![](img/19cbefdd11a3bbfe492c09b41e430aa3_67.png)

你已成功迈出了使用代码自动化管理云资源的第一步。在后续课程中，我们将探索更复杂的配置和模块。