# 133：使用 AWS S3 进行远程状态存储 🗂️

在本节课中，我们将学习如何使用 Amazon S3 服务来远程存储和管理 Terraform 的状态文件。与之前学习的 Git 方式相比，S3 提供了更好的团队协作支持、状态锁定、加密和版本控制功能，尤其适合多人团队。

## 概述

![](img/22c3bcb3fcb393117e439e679c04522a_1.png)

我们将创建一个新的 Terraform 配置，用于在 AWS 上构建一个 S3 存储桶，并配置相关功能以安全地存储状态文件。整个过程包括创建存储桶、启用版本控制、配置服务器端加密、阻止公共访问，以及设置 DynamoDB 表来实现状态锁定。

## 创建基础配置

首先，我们需要创建一个新的工作目录和 Terraform 配置文件。

以下是创建目录和文件的步骤：
1.  在本地创建一个名为 `terraform` 的新目录。
2.  在该目录下创建一个名为 `main.tf` 的文件。

![](img/22c3bcb3fcb393117e439e679c04522a_3.png)

![](img/22c3bcb3fcb393117e439e679c04522a_5.png)

## 配置提供商与区域

![](img/22c3bcb3fcb393117e439e679c04522a_7.png)

上一节我们创建了基础文件，本节中我们来看看如何配置 AWS 提供商。

![](img/22c3bcb3fcb393117e439e679c04522a_9.png)

所有 Terraform 配置代码都可以在其官方文档中找到。首先，我们需要指定使用的云提供商（AWS）和区域。

```hcl
provider "aws" {
  region = "sa-east-1" # 选择圣保罗区域
}
```

![](img/22c3bcb3fcb393117e439e679c04522a_11.png)

![](img/22c3bcb3fcb393117e439e679c04522a_13.png)

## 创建 S3 存储桶

现在，我们来创建核心的 S3 存储桶资源。S3 存储桶的名称有严格限制，只能包含小写字母和数字。

以下是创建 S3 存储桶的配置：
```hcl
resource "aws_s3_bucket" "terraform_state" {
  bucket = "terraform-1993s3" # 存储桶名称，需全局唯一

  # 防止存储桶被意外销毁
  lifecycle {
    prevent_destroy = true
  }
}
```

## 启用版本控制

为了能够追踪状态文件的更改历史并在必要时回滚，我们需要为 S3 存储桶启用版本控制功能。

以下是启用版本控制的配置：
```hcl
resource "aws_s3_bucket_versioning" "versioning_example" {
  bucket = aws_s3_bucket.terraform_state.id
  versioning_configuration {
    status = "Enabled"
  }
}
```

## 配置服务器端加密

![](img/22c3bcb3fcb393117e439e679c04522a_15.png)

![](img/22c3bcb3fcb393117e439e679c04522a_17.png)

安全性至关重要。我们需要启用服务器端加密，以确保状态文件在传输和静态存储时都是加密的。

以下是启用 AES-256 服务器端加密的配置：
```hcl
resource "aws_s3_bucket_server_side_encryption_configuration" "example" {
  bucket = aws_s3_bucket.terraform_state.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}
```

![](img/22c3bcb3fcb393117e439e679c04522a_19.png)

## 阻止公共访问

默认情况下，S3 存储桶是私有的，但显式地阻止所有公共访问是一项重要的安全最佳实践。

以下是配置阻止公共访问的代码：
```hcl
resource "aws_s3_bucket_public_access_block" "example" {
  bucket = aws_s3_bucket.terraform_state.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

## 创建 DynamoDB 表以实现状态锁定

![](img/22c3bcb3fcb393117e439e679c04522a_21.png)

当多人协作时，需要防止同时对状态文件进行修改。我们将使用 AWS DynamoDB 表来实现状态锁定机制。

以下是创建 DynamoDB 锁定表的配置：
```hcl
resource "aws_dynamodb_table" "terraform_locks" {
  name         = "terraform-state-locking"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }
}
```

![](img/22c3bcb3fcb393117e439e679c04522a_23.png)

![](img/22c3bcb3fcb393117e439e679c04522a_25.png)

![](img/22c3bcb3fcb393117e439e679c04522a_27.png)

## 初始化与部署资源

![](img/22c3bcb3fcb393117e439e679c04522a_29.png)

配置编写完成后，我们需要初始化 Terraform 并应用配置以在 AWS 上创建这些资源。

![](img/22c3bcb3fcb393117e439e679c04522a_31.png)

![](img/22c3bcb3fcb393117e439e679c04522a_33.png)

以下是部署步骤：
1.  在终端中，进入 `terraform` 目录。
2.  运行 `terraform init` 命令来初始化工作目录并下载必要的提供商插件。
3.  运行 `terraform apply` 命令，审查执行计划后输入 `yes` 来创建资源。

![](img/22c3bcb3fcb393117e439e679c04522a_35.png)

![](img/22c3bcb3fcb393117e439e679c04522a_37.png)

## 配置后端以迁移状态

资源创建成功后，本地的 `.tfstate` 文件仍未迁移到云端。我们需要配置后端（Backend），将状态存储指向刚创建的 S3 存储桶和 DynamoDB 表。

![](img/22c3bcb3fcb393117e439e679c04522a_39.png)

以下是后端配置代码，需要添加到 `main.tf` 文件中：
```hcl
terraform {
  backend "s3" {
    bucket         = "terraform-1993s3"       # 你的 S3 存储桶名称
    key            = "global/s3/terraform.tfstate" # 状态文件在 S3 中的路径
    region         = "sa-east-1"             # 区域
    dynamodb_table = "terraform-state-locking" # DynamoDB 表名
    encrypt        = true
  }
}
```

添加配置后，需要重新初始化 Terraform 以迁移状态。由于当前状态已被本地文件锁定，我们需要使用 `-lock=false` 参数来跳过锁定检查。

![](img/22c3bcb3fcb393117e439e679c04522a_41.png)

运行以下命令进行迁移：
```
terraform init -lock=false
```
Terraform 会提示将本地状态迁移到 S3 后端，确认后即可完成迁移。

## 验证与输出

![](img/22c3bcb3fcb393117e439e679c04522a_43.png)

![](img/22c3bcb3fcb393117e439e679c04522a_45.png)

最后，我们可以添加输出变量来方便地查看已创建资源的名称，并验证状态文件是否已成功存储在 S3 中。

![](img/22c3bcb3fcb393117e439e679c04522a_47.png)

以下是在 `main.tf` 中添加的输出配置：
```hcl
output "s3_bucket_arn" {
  value       = aws_s3_bucket.terraform_state.arn
  description = "The ARN of the S3 bucket"
}

output "dynamodb_table_name" {
  value       = aws_dynamodb_table.terraform_locks.name
  description = "The name of the DynamoDB table"
}
```
运行 `terraform apply` 即可在控制台看到输出信息。同时，你可以在 AWS 管理控制台的 S3 服务中，看到存储桶内已存在 `global/s3/terraform.tfstate` 文件，并且版本控制已生效。

## 总结

![](img/22c3bcb3fcb393117e439e679c04522a_49.png)

![](img/22c3bcb3fcb393117e439e679c04522a_51.png)

![](img/22c3bcb3fcb393117e439e679c04522a_52.png)

本节课中我们一起学习了如何使用 AWS S3 和 DynamoDB 为 Terraform 设置远程后端。我们逐步配置了一个安全的、支持版本控制和状态锁定的远程状态存储方案。这种方法比使用 Git 管理状态文件更安全、更可靠，特别适合团队协作环境。配置完成后，Terraform 会在每次操作前自动拉取最新状态，并在操作后自动推送更新，确保了状态的一致性和安全性。