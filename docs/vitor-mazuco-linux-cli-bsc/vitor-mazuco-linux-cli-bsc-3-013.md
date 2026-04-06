# 013：Secrets 🔐

在本节课中，我们将学习Kubernetes中的Secrets（密钥）对象。Secrets用于存储少量敏感数据，例如密码、令牌或安全密钥。我们将通过一个使用TLS证书和密钥的实际例子，来演示如何创建和使用Secrets。

---

## 概述

Secrets是Kubernetes中用于管理敏感信息的对象。它可以安全地存储密码、API令牌或TLS证书等数据，并允许Pod以卷（Volume）的形式挂载和使用这些数据。本节我们将重点学习通用的`Opaque`类型Secret。

上一节我们介绍了ConfigMaps，本节中我们来看看如何管理更敏感的数据——Secrets。

---

![](img/60ab21d1ac32ae608f3636a56c9df4fe_1.png)

## 什么是Secret？

Secret是一个包含少量敏感数据的对象。根据其名称“秘密”可知，它专门用于存储密码、令牌、安全密钥等信息。这些信息可以以特定方式配置在Pod或容器镜像中。

Kubernetes支持多种类型的Secret，以下是最常用的几种：
*   **`docker-registry`**: 用于存储Docker镜像仓库的认证信息。
*   **`tls`**: 用于存储TLS证书和密钥。
*   **`basic-auth`**: 用于存储基本的HTTP认证凭据。
*   **`ssh-auth`**: 用于存储SSH密钥。
*   **`bootstrap-token`**: 用于节点引导的令牌数据。

本节我们将专注于通用的`Opaque`类型，它是最基础的使用方式。

![](img/60ab21d1ac32ae608f3636a56c9df4fe_3.png)

![](img/60ab21d1ac32ae608f3636a56c9df4fe_5.png)

---

## 准备TLS证书和密钥

为了演示，我们需要TLS证书和密钥。我们将从Google APIs下载一个免费的证书和密钥用于测试。

![](img/60ab21d1ac32ae608f3636a56c9df4fe_7.png)

**请注意**：此证书仅用于学习和测试，**切勿**在生产环境中使用。生产环境必须使用自己生成的、经过加密的证书。

![](img/60ab21d1ac32ae608f3636a56c9df4fe_9.png)

以下是下载命令：
```bash
# 下载证书
wget https://pki.goog/roots.pem -O cert.crt

# 下载密钥（示例，实际密钥需自行生成或从安全来源获取）
# 此处仅为演示流程，假设我们有一个 `key.pem` 文件
```
下载后，你可以使用`cat`命令查看文件内容，确认证书和密钥已就绪。

![](img/60ab21d1ac32ae608f3636a56c9df4fe_11.png)

---

![](img/60ab21d1ac32ae608f3636a56c9df4fe_13.png)

## 创建Secret

![](img/60ab21d1ac32ae608f3636a56c9df4fe_15.png)

现在，我们使用下载的证书和密钥文件来创建一个Secret。我们将使用`kubectl create secret generic`命令，并以`Opaque`类型（默认类型）创建。

命令格式如下：
```bash
kubectl create secret generic <secret名称> --from-file=<密钥文件> --from-file=<证书文件>
```

![](img/60ab21d1ac32ae608f3636a56c9df4fe_17.png)

假设我们的证书文件是`cert.crt`，密钥文件是`key.pem`，并想创建一个名为`tls-secret`的Secret，命令如下：
```bash
kubectl create secret generic tls-secret --from-file=tls.key=key.pem --from-file=tls.crt=cert.crt
```

![](img/60ab21d1ac32ae608f3636a56c9df4fe_19.png)

创建成功后，可以使用以下命令查看Secret的详细信息：
```bash
kubectl describe secret tls-secret
```
输出会显示Secret的类型（`Opaque`）、名称、命名空间以及包含的数据项（`tls.key`和`tls.crt`）。

![](img/60ab21d1ac32ae608f3636a56c9df4fe_21.png)

---

## 在Pod中使用Secret（通过Volume挂载）

创建好Secret后，我们可以在Pod中通过Volume挂载的方式使用它。这类似于上一节ConfigMap的用法。

以下是创建一个Pod的YAML清单文件示例（例如`pod-with-secret.yaml`）：
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-secret-pod
spec:
  containers:
  - name: app
    image: nginx:alpine
    volumeMounts:
    - name: tls-volume
      mountPath: /etc/tls
      readOnly: true  # 重要：以只读方式挂载，符合安全最佳实践
  volumes:
  - name: tls-volume
    secret:
      secretName: tls-secret  # 这里引用我们之前创建的Secret名称
```

![](img/60ab21d1ac32ae608f3636a56c9df4fe_23.png)

关键部分解释：
1.  **`volumeMounts`**: 在容器内部，将名为`tls-volume`的卷挂载到路径`/etc/tls`。
2.  **`readOnly: true`**: 设置为只读，增强安全性。
3.  **`volumes`**: 定义卷的来源，这里指向名为`tls-secret`的Secret。

![](img/60ab21d1ac32ae608f3636a56c9df4fe_25.png)

使用以下命令创建Pod：
```bash
kubectl apply -f pod-with-secret.yaml
```

---

![](img/60ab21d1ac32ae608f3636a56c9df4fe_27.png)

## 验证Secret在Pod中的使用

![](img/60ab21d1ac32ae608f3636a56c9df4fe_29.png)

Pod运行后，我们可以验证Secret文件是否已成功挂载。

![](img/60ab21d1ac32ae608f3636a56c9df4fe_31.png)

![](img/60ab21d1ac32ae608f3636a56c9df4fe_33.png)

首先，将Pod的端口（例如容器内的443端口）转发到本地：
```bash
kubectl port-forward pod/myapp-secret-pod 8443:443
```

然后，在浏览器中访问`https://localhost:8443`。
*   由于我们使用的是自签名/测试证书，浏览器会发出安全警告。在高级设置中接受风险并继续即可。
*   如果Pod运行的是Nginx等Web服务器，现在你应该可以通过HTTPS加密连接访问它了。
*   你还可以使用`kubectl exec`命令进入Pod，查看`/etc/tls`目录下是否存在`tls.key`和`tls.crt`文件。

```bash
kubectl exec myapp-secret-pod -- ls /etc/tls
```

---

## 总结

本节课中我们一起学习了Kubernetes Secrets的基础知识。我们了解到：
1.  Secret用于安全存储敏感信息。
2.  我们使用`kubectl create secret generic`命令创建了一个`Opaque`类型的Secret来存储TLS证书和密钥。
3.  通过在Pod定义中配置`volumes`和`volumeMounts`，可以将Secret作为文件挂载到容器内部使用，并且应设置为`readOnly`模式。
4.  我们完成了从创建Secret到在Pod中挂载使用的完整流程验证。

![](img/60ab21d1ac32ae608f3636a56c9df4fe_35.png)

这只是Secret最基础的用法。在实际中，还有`docker-registry`、`tls`等特定类型的Secret，它们能更好地适配特定场景。在后续课程中，我们会继续探索这些更高级的用法。

![](img/60ab21d1ac32ae608f3636a56c9df4fe_36.png)

![](img/60ab21d1ac32ae608f3636a56c9df4fe_38.png)

如果有任何疑问，欢迎在课程论坛中提出。我们下节课再见！