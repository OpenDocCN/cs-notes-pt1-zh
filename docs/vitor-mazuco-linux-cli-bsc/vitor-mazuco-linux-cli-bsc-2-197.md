# 197：创建Pod 🚀

在本节课中，我们将逐步学习如何在系统中创建第一个Pod。我们将通过两种主要方法来实现：直接使用`kubectl run`命令，以及通过编写YAML清单文件。课程将涵盖创建、查看、获取详细信息以及删除Pod的全过程。

---

## 使用 `kubectl run` 命令创建Pod

上一节我们介绍了课程概述，本节中我们来看看第一种创建Pod的方法：使用`kubectl run`命令。这是一种快速创建单个Pod的便捷方式。

以下是创建Pod的具体命令格式：
```bash
kubectl run <pod名称> --image=<镜像名称>
```

例如，我们可以运行以下命令来创建一个名为`my-pod`的Pod，并使用一个演示镜像：
```bash
kubectl run my-pod --image=ghcr.io/your-username/demo-image:latest
```
命令执行后，如果成功，终端会显示类似“pod/my-pod created”的提示。

---

![](img/7583888fb4d7b2305d00abb16af0fe4b_1.png)

## 查看已创建的Pod

![](img/7583888fb4d7b2305d00abb16af0fe4b_3.png)

Pod创建成功后，我们需要验证其状态。为此，可以使用`kubectl get pods`命令。

以下是该命令的典型输出示例：
```bash
kubectl get pods
```
输出会显示Pod的**名称**、**就绪状态**、**状态**、**重启次数**、**运行时长**等信息。

---

## 删除通过命令创建的Pod

当我们不再需要某个Pod时，可以将其删除以释放资源。删除Pod使用`kubectl delete`命令。

以下是删除Pod的命令格式：
```bash
kubectl delete pod <pod名称>
```
例如，要删除之前创建的`my-pod`，可以运行：
```bash
kubectl delete pod my-pod
```
删除成功后，系统会提示“pod \”my-pod\” deleted”。再次使用`kubectl get pods`命令查看，该Pod将不再出现在列表中。

---

## 通过YAML清单文件创建Pod

![](img/7583888fb4d7b2305d00abb16af0fe4b_5.png)

除了使用命令行，更规范和可复用的方法是编写一个YAML清单文件。上一节我们使用了命令，本节中我们来看看如何通过文件定义Pod。

![](img/7583888fb4d7b2305d00abb16af0fe4b_7.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_9.png)

以下是一个Pod清单文件（例如 `pod-manifest.yaml`）的最小化示例：
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-from-file
spec:
  containers:
  - name: my-container
    image: ghcr.io/your-username/demo-image:latest
    ports:
    - containerPort: 8080
      name: http
      protocol: TCP
```
这个文件定义了Pod的基本信息，包括API版本、类型、元数据（名称）和容器规格（容器名、镜像和端口）。

![](img/7583888fb4d7b2305d00abb16af0fe4b_10.png)

---

![](img/7583888fb4d7b2305d00abb16af0fe4b_12.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_13.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_15.png)

## 应用YAML文件以创建Pod

![](img/7583888fb4d7b2305d00abb16af0fe4b_17.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_19.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_21.png)

文件编写完成后，我们需要使用`kubectl apply`命令来根据文件创建资源。

![](img/7583888fb4d7b2305d00abb16af0fe4b_23.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_24.png)

以下是应用YAML文件的命令：
```bash
kubectl apply -f pod-manifest.yaml
```
执行后，终端会显示“pod/my-pod-from-file created”。此时，使用`kubectl get pods`命令可以看到新创建的Pod。

![](img/7583888fb4d7b2305d00abb16af0fe4b_26.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_27.png)

---

![](img/7583888fb4d7b2305d00abb16af0fe4b_29.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_31.png)

## 获取Pod的详细信息

![](img/7583888fb4d7b2305d00abb16af0fe4b_33.png)

为了获取Pod更全面的信息，我们可以使用`kubectl describe`命令或指定输出格式为JSON。

![](img/7583888fb4d7b2305d00abb16af0fe4b_35.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_37.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_39.png)

以下是两种获取详细信息的方法：
1.  **使用 `describe` 命令**：
    ```bash
    kubectl describe pod my-pod-from-file
    ```
    此命令会输出Pod的详细状态、事件、IP地址、所在节点等信息。
2.  **以JSON格式输出**：
    ```bash
    kubectl get pod my-pod-from-file -o json
    ```
    这将返回一个结构化的JSON对象，包含Pod配置和状态的**所有键值对**，信息非常详尽。

![](img/7583888fb4d7b2305d00abb16af0fe4b_41.png)

---

![](img/7583888fb4d7b2305d00abb16af0fe4b_43.png)

![](img/7583888fb4d7b2305d00abb16af0fe4b_45.png)

## 删除通过文件创建的Pod

![](img/7583888fb4d7b2305d00abb16af0fe4b_47.png)

与创建相对应，我们也可以基于YAML文件来删除Pod，确保管理操作的一致性。

![](img/7583888fb4d7b2305d00abb16af0fe4b_49.png)

以下是使用文件删除Pod的命令：
```bash
kubectl delete -f pod-manifest.yaml
```
执行后，终端会确认“pod \”my-pod-from-file\” deleted”。

---

## 总结

本节课中我们一起学习了创建和管理Kubernetes Pod的核心操作。我们掌握了两种创建方法：快速的`kubectl run`命令和可重复的YAML清单文件。我们还学习了如何查看Pod列表、获取详细状态信息（包括使用`describe`和JSON格式输出），以及如何通过对应的方法删除Pod。这些命令是日常管理Kubernetes工作负载的基础，建议在您的环境中进行练习以加深理解。

![](img/7583888fb4d7b2305d00abb16af0fe4b_51.png)

所有在本课中使用的命令都已提供，您可以在自己的机器上复现以更好地掌握。