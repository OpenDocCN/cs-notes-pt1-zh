# 007：部署管理 🚀

![](img/a63c5f6ee89f3ca5ed76870d09703228_1.png)

在本节课中，我们将深入学习 Kubernetes 中的 **Deployment** 对象。Deployment 为 Pod 和 ReplicaSet 提供了声明式更新功能，允许我们以简单、可靠的方式管理应用的新版本。

## 什么是 Deployment？🤔

Deployment 是 Kubernetes 中用于管理应用部署的核心对象。它允许我们声明应用的期望状态，并自动管理 Pod 和 ReplicaSet 的更新过程。

上一节我们介绍了 ReplicaSet，本节中我们来看看如何使用 Deployment 进行更高级的部署管理。

![](img/a63c5f6ee89f3ca5ed76870d09703228_3.png)

## 创建第一个 Deployment 📝

让我们开始创建第一个 Deployment 清单文件。

以下是创建一个名为 `app-deployment.yaml` 的 Deployment 清单的步骤：

![](img/a63c5f6ee89f3ca5ed76870d09703228_5.png)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

使用 `kubectl apply` 命令创建 Deployment：

![](img/a63c5f6ee89f3ca5ed76870d09703228_7.png)

```bash
kubectl apply -f app-deployment.yaml
```

![](img/a63c5f6ee89f3ca5ed76870d09703228_9.png)

![](img/a63c5f6ee89f3ca5ed76870d09703228_11.png)

## 查看和管理 Deployment 🔍

![](img/a63c5f6ee89f3ca5ed76870d09703228_13.png)

创建 Deployment 后，我们可以使用多种命令来查看和管理它。

以下是查看 Deployment 状态的命令：

```bash
kubectl get deployments
kubectl describe deployment app-deployment
```

## 扩缩容 Deployment 📈

我们可以轻松地调整 Deployment 的副本数量。

以下是扩缩容 Deployment 的两种方法：

![](img/a63c5f6ee89f3ca5ed76870d09703228_15.png)

1.  通过修改清单文件并重新应用：
    ```bash
    # 修改 app-deployment.yaml 中的 replicas 字段，然后执行
    kubectl apply -f app-deployment.yaml
    ```

![](img/a63c5f6ee89f3ca5ed76870d09703228_17.png)

2.  使用 `kubectl scale` 命令直接调整：
    ```bash
    kubectl scale deployment app-deployment --replicas=3
    ```

![](img/a63c5f6ee89f3ca5ed76870d09703228_19.png)

![](img/a63c5f6ee89f3ca5ed76870d09703228_21.png)

## 更新 Deployment 🔄

Deployment 支持滚动更新，这是其核心功能之一。我们可以通过更新 Pod 模板（例如容器镜像）来触发更新。

![](img/a63c5f6ee89f3ca5ed76870d09703228_23.png)

![](img/a63c5f6ee89f3ca5ed76870d09703228_25.png)

以下是更新 Deployment 中容器镜像的步骤：

![](img/a63c5f6ee89f3ca5ed76870d09703228_27.png)

1.  修改清单文件中的 `image` 字段。
2.  执行 `kubectl apply -f app-deployment.yaml`。

Kubernetes 将启动滚动更新过程，逐步用新 Pod 替换旧 Pod。

## 使用 Rollout 命令 🎯

![](img/a63c5f6ee89f3ca5ed76870d09703228_29.png)

![](img/a63c5f6ee89f3ca5ed76870d09703228_31.png)

Kubernetes 提供了 `kubectl rollout` 命令来管理部署过程。

![](img/a63c5f6ee89f3ca5ed76870d09703228_33.png)

以下是常用的 rollout 子命令：

*   `kubectl rollout status deployment/app-deployment`：查看更新状态。
*   `kubectl rollout history deployment/app-deployment`：查看更新历史。
*   `kubectl rollout pause deployment/app-deployment`：暂停更新。
*   `kubectl rollout resume deployment/app-deployment`：恢复更新。

## 回滚 Deployment ↩️

![](img/a63c5f6ee89f3ca5ed76870d09703228_35.png)

如果新版本出现问题，我们可以轻松回滚到之前的版本。

![](img/a63c5f6ee89f3ca5ed76870d09703228_37.png)

以下是回滚 Deployment 的命令：

```bash
# 回滚到上一个版本
kubectl rollout undo deployment/app-deployment

![](img/a63c5f6ee89f3ca5ed76870d09703228_39.png)

# 回滚到特定版本（通过 revision 编号指定）
kubectl rollout undo deployment/app-deployment --to-revision=2
```

![](img/a63c5f6ee89f3ca5ed76870d09703228_41.png)

## 添加注解 📌

我们可以在 Deployment 的元数据中添加注解，以记录更新原因等信息。

以下是在 Deployment 清单中添加注解的示例：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deployment
  annotations:
    example.com/change-cause: "更新镜像到 nginx:1.16.1"
...
```

## 删除 Deployment 🗑️

最后，当我们不再需要某个 Deployment 时，可以将其删除。

以下是删除 Deployment 的两种方法：

1.  通过名称删除：
    ```bash
    kubectl delete deployment app-deployment
    ```

2.  通过清单文件删除：
    ```bash
    kubectl delete -f app-deployment.yaml
    ```

## 总结 📚

![](img/a63c5f6ee89f3ca5ed76870d09703228_43.png)

本节课中我们一起学习了 Kubernetes Deployment 的核心概念和操作。我们了解了如何创建、查看、扩缩容、更新、回滚和删除 Deployment。Deployment 通过其声明式特性和滚动更新机制，为应用部署提供了强大且可靠的管理能力。记住，始终通过修改清单文件来管理 Deployment 是推荐的最佳实践，这有助于维护配置的一致性和可追溯性。