# 199：检查Pod健康状态 🩺

在本节课中，我们将学习如何在Kubernetes中为Pod配置健康状态检查。这能确保我们的应用程序、网站或数据库等服务正常运行。我们将重点介绍“存活探针”的概念及其配置方法。

![](img/9680cb650ade966da88bf2dbaf5fdca8_1.png)

## 概述

![](img/9680cb650ade966da88bf2dbaf5fdca8_3.png)

![](img/9680cb650ade966da88bf2dbaf5fdca8_5.png)

上一节我们介绍了Pod的基本概念和创建方法。本节中，我们来看看如何确保Pod内部应用的健康状态。Kubernetes通过“探针”来检查容器内部应用的健康状况，并在发现问题时自动重启容器，从而保证服务的高可用性。

![](img/9680cb650ade966da88bf2dbaf5fdca8_7.png)

## 什么是健康检查？

![](img/9680cb650ade966da88bf2dbaf5fdca8_9.png)

![](img/9680cb650ade966da88bf2dbaf5fdca8_11.png)

健康检查用于为应用程序提供运行保障。其核心逻辑是测试应用程序是否真正在工作。例如：
*   测试一个网页是否能正常访问。
*   测试数据库端口是否可连接。
*   测试登录功能是否能返回预期响应。

![](img/9680cb650ade966da88bf2dbaf5fdca8_13.png)

简而言之，健康检查确保一切都在完美运行。在更具体的场景下，例如处理**死锁**（即一个或多个进程被阻止继续执行）时，就需要使用健康检查来探测问题。

![](img/9680cb650ade966da88bf2dbaf5fdca8_15.png)

## 存活探针概念

Kubernetes使用一种称为**存活探针**的概念来执行健康检查。存活探针是一组用于检查系统关键部分属性的机制，它能测试系统是否存在问题。这是一个相对具体的概念，需要你明确想要检查什么以及如何检查。

## 如何配置存活探针

![](img/9680cb650ade966da88bf2dbaf5fdca8_17.png)

我们需要在Pod的定义文件中创建自己的健康检查配置。

![](img/9680cb650ade966da88bf2dbaf5fdca8_19.png)

以下是创建一个用于检查HTTP请求的存活探针的步骤：

1.  **创建Pod定义文件**：首先，创建一个YAML文件，例如 `health-check-pod.yaml`。
2.  **定义Pod基础信息**：包括API版本、类型、名称和容器镜像。我们使用一个包含多种测试概念的公共Docker镜像。
3.  **配置存活探针**：这是关键部分。我们将在容器规范中添加 `livenessProbe` 字段。

### 存活探针配置详解

以下是一个存活探针的YAML配置示例：

![](img/9680cb650ade966da88bf2dbaf5fdca8_21.png)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: health-check-pod
spec:
  containers:
  - name: app-container
    image: k8s.gcr.io/liveness # 用于测试的公共镜像
    ports:
    - containerPort: 8080
    livenessProbe:
      httpGet:
        path: /healthz # 要检查的路径
        port: 8080    # 要检查的端口
      initialDelaySeconds: 5 # 容器启动后等待5秒开始探测
      periodSeconds: 5       # 每5秒执行一次探测
      timeoutSeconds: 1      # 探测请求超时时间为1秒
      successThreshold: 1    # 连续1次成功视为健康
      failureThreshold: 3    # 连续3次失败视为不健康
```

**配置参数说明：**
*   **`httpGet`**: 指定使用HTTP GET请求进行探测。你需要定义检查的`path`和`port`。
*   **`initialDelaySeconds`**: 容器启动后，等待多少秒才开始第一次健康检查。
*   **`periodSeconds`**: 执行健康检查的频率（每多少秒一次）。
*   **`timeoutSeconds`**: 每次探测请求的超时时间。应用必须在此时长内响应。
*   **`successThreshold`**: 探测失败后，被视为成功所需的最小连续成功次数。
*   **`failureThreshold`**: 当探测失败达到此次数后，Kubernetes将重启容器。这是我们容错尝试的上限。

![](img/9680cb650ade966da88bf2dbaf5fdca8_23.png)

![](img/9680cb650ade966da88bf2dbaf5fdca8_25.png)

![](img/9680cb650ade966da88bf2dbaf5fdca8_26.png)

**状态码判断**：探针期望HTTP响应状态码大于等于200且小于400（例如200 OK）才被视为成功。

![](img/9680cb650ade966da88bf2dbaf5fdca8_28.png)

![](img/9680cb650ade966da88bf2dbaf5fdca8_30.png)

![](img/9680cb650ade966da88bf2dbaf5fdca8_32.png)

## 实践测试

![](img/9680cb650ade966da88bf2dbaf5fdca8_34.png)

现在，让我们应用这个配置并观察其工作原理。

![](img/9680cb650ade966da88bf2dbaf5fdca8_36.png)

1.  **部署Pod**：使用 `kubectl apply -f health-check-pod.yaml` 命令创建Pod。
2.  **端口转发**：使用 `kubectl port-forward pod/health-check-pod 8080:8080` 将Pod端口映射到本地，以便在浏览器中访问 `http://localhost:8080`。
3.  **观察日志**：打开另一个终端，使用 `kubectl describe pod health-check-pod` 命令查看Pod事件，或使用 `kubectl logs health-check-pod` 查看容器日志。
4.  **模拟故障**：我们使用的测试镜像提供了一个可以手动触发故障的端点（例如，访问 `/healthz?fail=true` 会返回500状态码）。触发后，观察事件日志。
5.  **观察自动恢复**：当存活探针连续3次（`failureThreshold`）检测到失败后，Kubernetes会**杀死并重启**这个容器。在事件日志中，你可以看到容器重启的记录。随后，服务应恢复正常（返回200状态码）。

![](img/9680cb650ade966da88bf2dbaf5fdca8_38.png)

## 总结

![](img/9680cb650ade966da88bf2dbaf5fdca8_40.png)

![](img/9680cb650ade966da88bf2dbaf5fdca8_42.png)

![](img/9680cb650ade966da88bf2dbaf5fdca8_43.png)

本节课中我们一起学习了Kubernetes存活探针的配置与使用。我们了解到：
*   健康检查对于保障应用持续可用至关重要。
*   **存活探针**用于监控容器内应用的健康状态。
*   通过定义 `livenessProbe`，我们可以配置HTTP检查、检查频率、超时和重试策略。
*   当探测连续失败达到指定阈值时，Kubernetes会自动重启容器，实现应用的自我修复。

![](img/9680cb650ade966da88bf2dbaf5fdca8_45.png)

![](img/9680cb650ade966da88bf2dbaf5fdca8_47.png)

除了HTTP检查，Kubernetes还支持TCP套接字检查和执行命令检查等多种探针类型，你可以根据应用特点选择合适的检查方式。