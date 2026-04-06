# 011：工作队列任务 🚀

在本节课中，我们将学习如何在Kubernetes中创建和管理工作队列任务。工作队列是一种任务队列，我们将创建任务，然后使用Job资源来并行处理这些任务，直到队列清空。整个过程将涉及创建ReplicaSet、Service、生成队列任务以及运行消费这些任务的Job。

---

## 概述

![](img/8b9ec0121608c4425f05497c9c9fa239_1.png)

我们将通过一个完整的示例来演示工作队列的概念。首先，我们会创建一个ReplicaSet来运行一个Web服务器，该服务器提供了一个任务队列的Web界面。接着，我们会创建一个Service来暴露这个Web界面。然后，我们将编写一个Shell脚本，向队列中批量添加任务。最后，我们将创建一个Kubernetes Job，它会启动多个Pod并行地消费（处理）队列中的任务，直到所有任务完成。

![](img/8b9ec0121608c4425f05497c9c9fa239_3.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_5.png)

---

## 创建基础ReplicaSet

首先，我们需要创建一个标准的ReplicaSet来托管我们的应用。这个应用是一个简单的Web服务器，它提供了一个任务队列的管理界面。

![](img/8b9ec0121608c4425f05497c9c9fa239_7.png)

以下是创建ReplicaSet的YAML配置文件内容，我们将其保存为 `rs.yaml`：

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: queue-server
  labels:
    chapter: jobs
spec:
  replicas: 1
  selector:
    matchLabels:
      app: queue-app
  template:
    metadata:
      labels:
        app: queue-app
        chapter: jobs
    spec:
      containers:
      - name: queue-container
        image: your-queue-server-image:tag # 请替换为实际的镜像
        ports:
        - containerPort: 8080
```

使用 `kubectl apply` 命令来创建这个ReplicaSet：

```bash
kubectl apply -f rs.yaml
```

创建成功后，ReplicaSet会确保一个Pod始终在运行。这个Pod运行着我们的队列服务器。

![](img/8b9ec0121608c4425f05497c9c9fa239_9.png)

---

## 暴露服务并访问Web界面

![](img/8b9ec0121608c4425f05497c9c9fa239_11.png)

为了让外部能够访问队列服务器的Web界面，我们需要创建一个Service。

![](img/8b9ec0121608c4425f05497c9c9fa239_13.png)

以下是Service的YAML配置文件内容，我们将其保存为 `service.yaml`：

![](img/8b9ec0121608c4425f05497c9c9fa239_15.png)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: queue-service
  labels:
    chapter: jobs
spec:
  selector:
    app: queue-app
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
  type: ClusterIP
```

应用这个Service配置：

![](img/8b9ec0121608c4425f05497c9c9fa239_17.png)

```bash
kubectl apply -f service.yaml
```

![](img/8b9ec0121608c4425f05497c9c9fa239_19.png)

现在，我们需要将服务端口转发到本地，以便在浏览器中访问。我们可以使用一个Shell变量来简化命令：

```bash
export POD_NAME=$(kubectl get pods -l app=queue-app -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080
```

执行上述命令后，你可以在浏览器中打开 `http://localhost:8080` 来访问队列的Web管理界面。

![](img/8b9ec0121608c4425f05497c9c9fa239_21.png)

---

## 向队列中添加任务

在Web界面中，我们可以看到当前的队列状态。为了演示，我们需要向队列中添加一批任务。我们将编写一个简单的Shell脚本，使用 `curl` 命令向服务器发送请求来创建任务。

![](img/8b9ec0121608c4425f05497c9c9fa239_23.png)

创建一个名为 `test.sh` 的脚本文件，内容如下：

![](img/8b9ec0121608c4425f05497c9c9fa239_24.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_26.png)

```bash
#!/bin/bash
for i in {1..100}
do
   curl -X POST http://localhost:8080/tasks -d "task=$i"
done
```

![](img/8b9ec0121608c4425f05497c9c9fa239_28.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_30.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_32.png)

这个脚本会向 `http://localhost:8080/tasks` 端点发送100个POST请求，从而创建100个队列任务。你可以根据机器性能调整循环次数（例如，改为300次）。

![](img/8b9ec0121608c4425f05497c9c9fa239_34.png)

给脚本添加执行权限并运行它：

![](img/8b9ec0121608c4425f05497c9c9fa239_36.png)

```bash
chmod +x test.sh
./test.sh
```

![](img/8b9ec0121608c4425f05497c9c9fa239_38.png)

执行后，你可以在Web界面或通过命令行看到队列中的任务数量在增加。每个任务都会有一个唯一的ID。

---

![](img/8b9ec0121608c4425f05497c9c9fa239_40.png)

## 创建Job来消费队列任务

![](img/8b9ec0121608c4425f05497c9c9fa239_41.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_42.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_44.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_46.png)

现在队列中已经有了任务，接下来我们创建一个Kubernetes Job来消费这些任务。这个Job会启动多个Pod（我们设置为5个）并行地从队列中获取并处理任务，直到队列为空。

以下是Job的YAML配置文件内容，我们将其保存为 `job-consumer.yaml`：

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: queue-consumer
  labels:
    chapter: jobs
spec:
  parallelism: 5
  completions: 100 # 总共需要完成的任务数，应与创建的任务数匹配
  template:
    metadata:
      labels:
        app: queue-consumer
        chapter: jobs
    spec:
      containers:
      - name: worker
        image: your-worker-image:tag # 请替换为实际的工作程序镜像
        args: ["--server", "queue-service:8080"]
        resources:
          limits:
            cpu: "500m"
      restartPolicy: OnFailure
```

应用这个Job配置：

```bash
kubectl apply -f job-consumer.yaml
```

![](img/8b9ec0121608c4425f05497c9c9fa239_48.png)

创建Job后，我们可以使用以下命令来观察Pod的状态：

```bash
kubectl get pods
```

![](img/8b9ec0121608c4425f05497c9c9fa239_50.png)

你会看到有5个Pod被创建并开始运行（状态为 `Running`）。这些Pod就是我们的“工人”，它们会不断地从队列中拉取任务并执行。随着任务被处理，队列中的任务数量会逐渐减少。

![](img/8b9ec0121608c4425f05497c9c9fa239_52.png)

你可以持续观察队列Web界面或使用命令 `kubectl get jobs` 和 `kubectl get pods` 来查看处理进度。当所有任务都被消费完后，Job会完成，相关的Pod状态会变为 `Completed`。

![](img/8b9ec0121608c4425f05497c9c9fa239_54.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_56.png)

---

![](img/8b9ec0121608c4425f05497c9c9fa239_58.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_59.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_61.png)

## 清理资源

![](img/8b9ec0121608c4425f05497c9c9fa239_62.png)

实验完成后，一个高效的清理方法是利用我们为所有资源（ReplicaSet、Service、Job）设置的共同标签 `chapter: jobs`。我们可以使用一条命令删除所有带有该标签的资源。

![](img/8b9ec0121608c4425f05497c9c9fa239_64.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_65.png)

执行以下命令：

```bash
kubectl delete all -l chapter=jobs
```

![](img/8b9ec0121608c4425f05497c9c9fa239_67.png)

这条命令会删除所有标签为 `chapter=jobs` 的Pod、ReplicaSet、Service和Job。这展示了使用标签进行资源分组和管理的重要性。

![](img/8b9ec0121608c4425f05497c9c9fa239_69.png)

如果你想重新开始整个实验，只需重新应用之前创建的YAML文件即可。

---

![](img/8b9ec0121608c4425f05497c9c9fa239_71.png)

![](img/8b9ec0121608c4425f05497c9c9fa239_72.png)

## 总结

![](img/8b9ec0121608c4425f05497c9c9fa239_74.png)

本节课我们一起学习了Kubernetes中工作队列任务的处理流程。我们首先创建了一个提供队列服务的ReplicaSet，并通过Service将其暴露。然后，我们使用Shell脚本批量创建了队列任务。最后，我们创建了一个Job，它能够启动多个Pod并行地消费队列中的任务，直到任务全部完成。通过使用统一的标签，我们还学会了如何高效地清理所有相关资源。这个流程展示了Kubernetes在管理批处理和并行任务方面的强大能力。