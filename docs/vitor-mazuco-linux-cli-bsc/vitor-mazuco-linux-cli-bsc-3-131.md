# 131：设置负载均衡器 🚦

在本节课中，我们将学习如何使用 Terraform 在 AWS 上配置一个负载均衡器。负载均衡器是管理多个服务器实例流量的关键组件，它能将用户请求智能地分发到健康的服务器上，并在实例出现故障时自动重定向流量，从而确保应用的高可用性。

上一节我们创建了一个包含多个实例的集群，但每个实例都有独立的公网 IP 地址。直接使用 IP 地址访问不仅不便管理，而且在实例故障时难以处理。本节中，我们来看看如何通过负载均衡器提供一个统一的访问入口。

## 负载均衡器核心概念

AWS 提供几种类型的负载均衡器，适用于不同场景：

以下是主要的负载均衡器类型：
*   **应用负载均衡器**：最适合平衡 **HTTP** 或 **HTTPS** 这类 Web 流量。它在 OSI 模型的**第7层**工作。
*   **网络负载均衡器**：主要用于处理 **TCP**、**UDP** 和 **TLS** 流量。例如，适用于 VoIP 服务器。它的响应速度通常比 ALB 更快。
*   **经典负载均衡器**：这是一个旧版系统，目前不推荐使用。虽然它能处理 Web 和 TCP/SSL 流量，但功能不如前两者。

对于 Web 服务器，我们通常使用**应用负载均衡器**；对于使用特定 TCP/UDP 端口的服务器，则使用**网络负载均衡器**。

## 负载均衡器工作原理

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_1.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_3.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_5.png)

负载均衡器的工作原理可以概括为以下几个核心组件：

以下是负载均衡器的工作流程简述：
1.  **监听器**：在特定端口（如 `80` 或 `443`）上监听传入的请求。
2.  **规则**：根据请求的路径或域名（如 `/branch1` 或 `branch2.example.com`）定义路由逻辑。
3.  **目标组**：包含一个或多个接收流量的实际服务器实例。负载均衡器会持续对目标组中的实例进行健康检查。
4.  **路由**：监听器根据规则，将匹配的请求转发到对应的目标组。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_7.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_9.png)

简单来说，流程是：**用户 -> 监听器 -> 规则 -> 目标组 -> 后端服务器实例**。

## 使用 Terraform 配置负载均衡器

接下来，我们将通过扩展之前的 Terraform 配置文件来创建负载均衡器。请注意，我们将大量使用变量，以便于维护和修改代码。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_11.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_13.png)

首先，我们需要创建负载均衡器资源本身。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_15.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_17.png)

以下是创建应用负载均衡器的核心代码块：
```hcl
resource "aws_lb" "test" {
  name               = "test-cluster"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.lb.id]
  subnets            = [for subnet in aws_subnet.public : subnet.id]

  tags = {
    Environment = "test"
  }
}
```
这段代码定义了负载均衡器的名称、类型（应用型）、关联的安全组以及它所在的子网。

创建负载均衡器后，我们需要为其添加一个监听器，以接收 HTTP 流量。

以下是创建监听器的代码：
```hcl
resource "aws_lb_listener" "http" {
  load_balancer_arn = aws_lb.test.arn
  port              = var.server_port
  protocol          = "HTTP"

  default_action {
    type = "fixed-response"

    fixed_response {
      content_type = "text/plain"
      message_body = "404: page not found"
      status_code  = 404
    }
  }
}
```
监听器绑定到我们创建的负载均衡器，在 `var.server_port`（例如80端口）上监听 HTTP 请求。`default_action` 定义了当所有后端实例都不可用时的默认响应（这里返回404）。

现在，我们需要调整之前创建的安全组，使其适用于负载均衡器集群，而不仅仅是单个实例。主要是更新安全组的名称和引用。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_19.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_21.png)

同时，我们还需要创建**目标组**。目标组定义了接收流量的后端实例集合以及健康检查策略。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_23.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_24.png)

以下是创建目标组的代码：
```hcl
resource "aws_lb_target_group" "asg" {
  name     = "terraform-asg-example"
  port     = var.server_port
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id

  health_check {
    path                = "/"
    protocol            = "HTTP"
    matcher             = "200"
    interval            = 15
    timeout             = 3
    healthy_threshold   = 2
    unhealthy_threshold = 2
  }
}
```
健康检查配置非常重要，它每隔 `15` 秒检查一次实例的健康状况（通过 HTTP GET 请求根路径“/”），超时时间为 `3` 秒。`healthy_threshold` 和 `unhealthy_threshold` 都设置为 `2`，意味着需要连续两次成功或失败才能改变实例的健康状态。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_26.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_28.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_30.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_32.png)

接下来，我们需要修改**自动伸缩组**的配置，将其与刚创建的目标组关联起来，这样新启动的实例会自动注册到目标组中。

最后，我们需要为监听器创建具体的**转发规则**，并将目标组关联起来。同时，添加一个输出项来显示负载均衡器的 DNS 名称，这是我们访问集群的入口。

以下是创建监听器规则和输出 DNS 的代码：
```hcl
resource "aws_lb_listener_rule" "asg" {
  listener_arn = aws_lb_listener.http.arn
  priority     = 100

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.asg.arn
  }

  condition {
    path_pattern {
      values = ["*"]
    }
  }
}

output "alb_dns_name" {
  value       = aws_lb.test.dns_name
  description = "The domain name of the load balancer"
}
```
规则将所有路径（`“*”`）的请求都转发到我们的目标组。`alb_dns_name` 输出将为我们提供负载均衡器的域名。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_34.png)

## 部署与测试

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_36.png)

配置文件编写完成后，就可以使用 Terraform 进行部署了。

运行以下命令来创建所有资源：
```bash
terraform apply
```
Terraform 会列出所有将要创建或变更的资源，确认无误后输入 `yes` 开始部署。这个过程可能需要几分钟。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_38.png)

部署成功后，可以在输出中找到负载均衡器的 DNS 名称。使用 `curl` 命令或浏览器访问该 DNS 名称，应该能看到后端实例返回的网页内容（例如 “Hello World”）。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_40.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_42.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_43.png)

你可以尝试终止一个后端实例，观察负载均衡器是否能够自动将流量路由到剩余的健康实例，从而体验其高可用性。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_45.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_46.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_48.png)

## 资源清理

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_50.png)

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_52.png)

完成测试后，务必使用以下命令销毁所有创建的资源，以避免产生不必要的费用：
```bash
terraform destroy
```
Terraform 将自动、有序地删除负载均衡器、目标组、实例、安全组等所有相关资源，这比在 AWS 控制台手动逐一删除要高效得多。

![](img/f47f6eb34cd1affa9c385d6cdf6c6e89_54.png)

本节课中我们一起学习了 AWS 负载均衡器的重要性及其类型，并亲手使用 Terraform 代码创建了一个完整的、具备自动伸缩和健康检查功能的负载均衡集群。你掌握了如何配置监听器、目标组以及将自动伸缩组与负载均衡器集成，最后还学会了如何通过统一的 DNS 名称访问集群并进行测试。