# 006：ReplicaSets 详解 🚀

在本节课中，我们将深入学习 Kubernetes 中的 **ReplicaSet** 概念。ReplicaSet 是一种控制器，用于确保指定数量的 Pod 副本始终在运行。我们将探讨其目的、工作原理、如何创建和管理，以及如何实现自动扩缩容。

---

## 概述 📋

ReplicaSet 的核心目标是提供**冗余**和**高可用性**。通过运行多个相同的 Pod 实例，可以实现更好的故障容错能力。此外，它还能处理**横向扩展**，通过增加副本数量来应对更高的请求负载。对于 Kubernetes 而言，ReplicaSet 管理着集群中的 Pod，并确保所需数量的 Pod 始终处于运行状态。

![](img/6c31ef44fe94b723a1c5d1b9153613e8_1.png)

---

![](img/6c31ef44fe94b723a1c5d1b9153613e8_1.png)

## ReplicaSet 的工作原理 ⚙️

ReplicaSet 主要通过三个关键字段来工作：

1.  **Selector**：用于识别由该 ReplicaSet 管理的 Pod。
2.  **Replicas**：指定需要维持的 Pod 副本数量。
3.  **Template**：定义创建新 Pod 时所使用的 Pod 模板。

其基本逻辑是，ReplicaSet 会根据 `replicas` 字段中指定的数量，自动创建或删除 Pod，以维持期望的状态。这实现了**扩缩容**功能。

---

## 创建 ReplicaSet 清单文件 📄

接下来，我们通过创建一个清单文件来实践。我们将创建一个名为 `basic-replica.yaml` 的文件。

以下是文件的基本结构：

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: basic-replica
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
```

**核心概念解析：**
*   `kind: ReplicaSet`： 指定资源类型为 ReplicaSet。
*   `spec.replicas: 1`： **公式**，定义期望的 Pod 副本数量。此处初始值为 1。
*   `spec.selector`： 定义了 ReplicaSet 如何找到它要管理的 Pod（通过标签 `app: nginx-app`）。
*   `spec.template`： 定义了创建新 Pod 的蓝图。

**重要提示：** `metadata.name` 必须是唯一的，且 `spec.template.metadata.labels` 必须与 `spec.selector.matchLabels` 匹配，否则 ReplicaSet 将无法正常工作。建议使用 IDE 或文本编辑器来确保 YAML 格式和缩进正确。

---

## 部署与管理 ReplicaSet 🛠️

上一节我们创建了清单文件，本节中我们来看看如何将其部署到集群并进行管理。

![](img/6c31ef44fe94b723a1c5d1b9153613e8_3.png)

### 应用清单文件

![](img/6c31ef44fe94b723a1c5d1b9153613e8_5.png)

使用 `kubectl apply` 命令来创建 ReplicaSet：

```bash
kubectl apply -f basic-replica.yaml
```

![](img/6c31ef44fe94b723a1c5d1b9153613e8_7.png)

![](img/6c31ef44fe94b723a1c5d1b9153613e8_8.png)

![](img/6c31ef44fe94b723a1c5d1b9153613e8_9.png)

### 查看资源状态

创建后，可以使用以下命令查看 ReplicaSet 和 Pod 的状态：

```bash
# 查看 ReplicaSet
kubectl get rs

# 查看 Pod
kubectl get pods

# 查看 ReplicaSet 的详细信息
kubectl describe rs basic-replica
```

在 `describe` 命令的输出中，你可以看到 Pod 列表、副本状态、事件历史以及 Pod 模板的详细信息。

### 通过标签筛选 Pod

![](img/6c31ef44fe94b723a1c5d1b9153613e8_11.png)

![](img/6c31ef44fe94b723a1c5d1b9153613e8_12.png)

由于 Pod 带有标签，我们可以使用标签选择器来过滤查看：

```bash
kubectl get pods -l app=nginx-app
```

---

## 手动扩缩容 ↔️

ReplicaSet 的一个关键功能是轻松调整副本数量。我们可以使用 `kubectl scale` 命令手动完成。

**增加副本数至 5 个：**

![](img/6c31ef44fe94b723a1c5d1b9153613e8_14.png)

![](img/6c31ef44fe94b723a1c5d1b9153613e8_15.png)

```bash
kubectl scale rs basic-replica --replicas=5
```

执行后，使用 `kubectl get pods` 命令，可以看到 Pod 数量已增加到 5 个。

**减少副本数至 1 个：**

```bash
kubectl scale rs basic-replica --replicas=1
```

![](img/6c31ef44fe94b723a1c5d1b9153613e8_17.png)

**最佳实践提示：** 虽然命令行操作快捷，但更推荐的做法是修改原始的清单文件（将 `replicas` 字段改为 5），然后重新应用该文件。这确保了配置变更的可追溯性和一致性。

![](img/6c31ef44fe94b723a1c5d1b9153613e8_19.png)

```bash
# 编辑 basic-replica.yaml 文件，修改 replicas: 5
# 然后应用更新
kubectl apply -f basic-replica.yaml
```

![](img/6c31ef44fe94b723a1c5d1b9153613e8_21.png)

![](img/6c31ef44fe94b723a1c5d1b9153613e8_23.png)

---

![](img/6c31ef44fe94b723a1c5d1b9153613e8_25.png)

![](img/6c31ef44fe94b723a1c5d1b9153613e8_27.png)

## 自动扩缩容 🤖

除了手动扩缩容，Kubernetes 还支持基于资源使用率（如 CPU）的**自动扩缩容**，这通过 Horizontal Pod Autoscaler (HPA) 实现。

![](img/6c31ef44fe94b723a1c5d1b9153613e8_29.png)

以下命令创建一个 HPA，它监控 `basic-replica` 这个 ReplicaSet，将 Pod 副本数量维持在 2 到 5 个之间，目标是将 CPU 平均利用率稳定在 80%：

```bash
kubectl autoscale rs basic-replica --min=2 --max=5 --cpu-percent=80
```

![](img/6c31ef44fe94b723a1c5d1b9153613e8_31.png)

创建后，可以查看 HPA 的状态：

```bash
kubectl get hpa
```

输出将显示当前指标、最小/最大副本数等信息。HPA 是确保应用弹性与资源高效利用的强大工具。

---

![](img/6c31ef44fe94b723a1c5d1b9153613e8_33.png)

## 删除 ReplicaSet 🗑️

最后，我们来学习如何清理资源。

### 直接删除

![](img/6c31ef44fe94b723a1c5d1b9153613e8_35.png)

删除 ReplicaSet 及其管理的所有 Pod：

```bash
kubectl delete rs basic-replica
```

### 级联删除策略

有时，你可能希望只删除 ReplicaSet 控制器，而保留其创建的 Pod（例如，准备用其他控制器来接管）。可以使用 `--cascade=orphan` 选项：

```bash
kubectl delete rs basic-replica --cascade=orphan
```

执行此命令后，ReplicaSet 被删除，但 Pod 会继续运行。之后你可以手动或通过其他方式管理这些 Pod。

---

## 总结 🎯

![](img/6c31ef44fe94b723a1c5d1b9153613e8_37.png)

![](img/6c31ef44fe94b723a1c5d1b9153613e8_38.png)

本节课中，我们一起学习了 Kubernetes ReplicaSet 的核心知识。我们了解到：
1.  ReplicaSet 通过维护固定数量的 Pod 副本来提供应用冗余和高可用性。
2.  其核心配置包括 `selector`、`replicas` 和 `template` 字段。
3.  我们可以通过清单文件创建 ReplicaSet，并使用 `kubectl scale` 命令或修改清单文件来实现手动扩缩容。
4.  通过 Horizontal Pod Autoscaler (HPA) 可以基于 CPU 等指标实现自动扩缩容，使应用更具弹性。
5.  掌握了删除 ReplicaSet 的两种方式：普通删除和保留 Pod 的级联删除。

![](img/6c31ef44fe94b723a1c5d1b9153613e8_40.png)

使用 ReplicaSet 能让你的应用部署更加健壮和安全，特别是在多节点集群中，它可以帮助 Pod 分散运行，提升整体稳定性。建议结合官方文档进行更深入的学习和实践。