# 130：在 AWS 上创建集群 🚀

## 概述
在本节课中，我们将学习如何使用 Terraform 在 AWS 上创建一个集群。之前我们只创建了单个实例，但实际应用中，单个主机存在单点故障和负载过高等风险。本节我们将配置自动伸缩组（Auto Scaling Group, ASG），实现多个实例的自动创建与管理，为后续配置负载均衡器打下基础。

![](img/06b390541107287ecb29933e30a7f32c_1.png)

![](img/06b390541107287ecb29933e30a7f32c_3.png)

![](img/06b390541107287ecb29933e30a7f32c_4.png)

![](img/06b390541107287ecb29933e30a7f32c_6.png)

## 配置文件的核心修改
为了创建集群，我们需要对原有的 Terraform 配置文件进行几处关键修改。主要变化是从创建单个 `aws_instance` 资源，转变为配置 `aws_launch_configuration` 和 `aws_autoscaling_group`。

![](img/06b390541107287ecb29933e30a7f32c_8.png)

![](img/06b390541107287ecb29933e30a7f32c_10.png)

以下是需要修改的核心部分：

![](img/06b390541107287ecb29933e30a7f32c_12.png)

1.  **资源类型变更**：将资源从 `aws_instance` 改为 `aws_launch_configuration`。
2.  **参数名更新**：
    *   `ami` 参数名变更为 `image_id`。
    *   `vpc_security_group_ids` 参数名变更为 `security_groups`。
3.  **移除旧参数**：移除 `tags` 参数，因为标签将在 ASG 中统一管理。
4.  **新增生命周期配置**：在启动配置中添加 `lifecycle` 块，设置 `create_before_destroy = true`，确保替换资源时先创建后销毁。
5.  **新增数据源**：添加 `aws_vpc` 和 `aws_subnet_ids` 数据源，以自动获取默认 VPC 和子网信息，供 ASG 使用。
6.  **定义自动伸缩组**：新增 `aws_autoscaling_group` 资源块，用于定义集群的实例数量、子网等配置。

![](img/06b390541107287ecb29933e30a7f32c_14.png)

## 配置步骤详解
接下来，我们详细看看每个部分的配置代码。

### 1. 定义变量与提供商
这部分与创建单实例时基本相同，定义了区域和实例类型等变量。

```hcl
variable "aws_region" {
  default = "us-east-1"
}

![](img/06b390541107287ecb29933e30a7f32c_16.png)

![](img/06b390541107287ecb29933e30a7f32c_18.png)

![](img/06b390541107287ecb29933e30a7f32c_20.png)

variable "instance_type" {
  default = "t2.micro"
}

![](img/06b390541107287ecb29933e30a7f32c_22.png)

provider "aws" {
  region = var.aws_region
}
```

### 2. 配置启动模板
这里我们创建 `aws_launch_configuration`，它定义了每个实例的“蓝图”，包括使用的镜像、实例类型、安全组和启动脚本。

```hcl
resource "aws_launch_configuration" "test_server" {
  name          = "test-server-launch-config"
  image_id      = "ami-0c55b159cbfafe1f0" # 示例 AMI ID，请根据您的区域更换
  instance_type = var.instance_type
  security_groups = [aws_security_group.instance.id]

  user_data = <<-EOF
              #!/bin/bash
              echo "Hello, World" > index.html
              nohup busybox httpd -f -p 8080 &
              EOF

  lifecycle {
    create_before_destroy = true
  }
}
```

### 3. 获取网络信息
我们使用数据源自动查询默认的 VPC 及其子网 ID，这样配置更具通用性，无需硬编码网络信息。

```hcl
data "aws_vpc" "default" {
  default = true
}

data "aws_subnet_ids" "default" {
  vpc_id = data.aws_vpc.default.id
}
```

![](img/06b390541107287ecb29933e30a7f32c_24.png)

![](img/06b390541107287ecb29933e30a7f32c_26.png)

### 4. 创建自动伸缩组
这是集群的核心配置。它基于上面定义的启动模板，在指定的子网中创建和管理指定数量的实例。

![](img/06b390541107287ecb29933e30a7f32c_28.png)

```hcl
resource "aws_autoscaling_group" "test_server" {
  name                 = "test-server-asg"
  launch_configuration = aws_launch_configuration.test_server.name
  vpc_zone_identifier  = data.aws_subnet_ids.default.ids

  min_size = 2
  max_size = 4
  desired_capacity = 2

  tag {
    key                 = "Name"
    value               = "terraform-asg-example"
    propagate_at_launch = true
  }
}
```

### 5. 配置安全组
安全组规则保持不变，用于控制实例的网络访问。

![](img/06b390541107287ecb29933e30a7f32c_30.png)

![](img/06b390541107287ecb29933e30a7f32c_32.png)

```hcl
resource "aws_security_group" "instance" {
  name = "terraform-example-instance"

  ingress {
    from_port   = 8080
    to_port     = 8080
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

![](img/06b390541107287ecb29933e30a7f32c_34.png)

![](img/06b390541107287ecb29933e30a7f32c_36.png)

## 应用配置与注意事项
完成上述配置后，使用 `terraform apply` 命令创建资源。

![](img/06b390541107287ecb29933e30a7f32c_38.png)

在应用过程中，您可能会遇到区域配额限制的问题。例如，在某些区域（如圣保罗 `sa-east-1`）的免费账户可能无法创建多个实例。如果遇到此类问题，可以尝试切换到配额更宽松的区域，例如北弗吉尼亚（`us-east-1`）。

成功执行后，Terraform 将创建一个包含 2 到 4 个实例的集群，并配置好自动伸缩组和安全组。

![](img/06b390541107287ecb29933e30a7f32c_40.png)

![](img/06b390541107287ecb29933e30a7f32c_41.png)

![](img/06b390541107287ecb29933e30a7f32c_43.png)

![](img/06b390541107287ecb29933e30a7f32c_44.png)

![](img/06b390541107287ecb29933e30a7f32c_46.png)

## 总结
本节课中，我们一起学习了如何使用 Terraform 在 AWS 上创建集群。关键点在于将配置从单一的 `aws_instance` 升级为 `aws_launch_configuration` 和 `aws_autoscaling_group` 的组合，并利用数据源动态获取网络信息。这样我们就能够轻松部署和管理一个可伸缩的实例集群。目前，每个集群实例拥有独立的公网 IP，在下一节课中，我们将为这个集群添加一个负载均衡器，提供统一的访问入口。