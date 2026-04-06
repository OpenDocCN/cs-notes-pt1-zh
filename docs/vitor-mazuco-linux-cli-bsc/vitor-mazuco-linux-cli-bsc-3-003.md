# 003：标签与注解

在本节课中，我们将学习Kubernetes中的标签和注解。它们是附加在对象上的元数据，用于组织和选择对象子集，使管理更加高效。

## 标签基础

上一节我们介绍了Kubernetes的基本对象，本节中我们来看看如何为这些对象添加标识信息。

标签是键值对，其格式类似于JSON。它们被附加到Pod等对象上，用于指定对象的标识，或为用户添加有意义的属性。标签通常定义在对象的`metadata`字段下。

![](img/8625a55999d5bad38b15a7de15ef9d16_1.png)

以下是标签的核心概念：
*   **键值对结构**：`key: value`
*   **唯一性**：每个键在同一个对象中必须是唯一的。
*   **语法规则**：有效的标签键可以包含前缀和名称两个部分，由斜杠`/`分隔。名称部分允许的字符包括：字母（A-Z, a-z）、数字（0-9）、连字符（-）、下划线（_）和点（.）。如果省略前缀，则该标签被视为用户私有。

![](img/8625a55999d5bad38b15a7de15ef9d16_3.png)

标签可以在创建对象模板时附加，也可以在之后随时添加、修改或删除。

![](img/8625a55999d5bad38b15a7de15ef9d16_5.png)

## 创建带标签的部署

![](img/8625a55999d5bad38b15a7de15ef9d16_7.png)

为了实践标签的使用，我们将创建四个部署文件。每个文件的核心区别在于其标签配置。

![](img/8625a55999d5bad38b15a7de15ef9d16_9.png)

以下是第一个部署文件的示例，重点关注`labels`部分：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-prod
  labels:
    app: test
    version: "1"
    environment: production
    tier: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: test
  template:
    metadata:
      labels:
        app: test
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
```

![](img/8625a55999d5bad38b15a7de15ef9d16_11.png)

我们使用`kubectl apply -f <文件名>`命令来创建部署。按照相同逻辑，我们创建了另外三个部署文件，分别具有不同的标签，例如`environment: testing`、`app: test2`等。

![](img/8625a55999d5bad38b15a7de15ef9d16_13.png)

## 查看与筛选标签

![](img/8625a55999d5bad38b15a7de15ef9d16_15.png)

![](img/8625a55999d5bad38b15a7de15ef9d16_17.png)

创建对象后，我们可以查看和筛选标签。

![](img/8625a55999d5bad38b15a7de15ef9d16_19.png)

使用以下命令查看所有部署及其标签：
```bash
kubectl get deployments --show-labels
```

标签的主要作用是进行筛选。我们可以使用`-l`选项配合选择器来过滤对象。

![](img/8625a55999d5bad38b15a7de15ef9d16_21.png)

![](img/8625a55999d5bad38b15a7de15ef9d16_23.png)

以下是常用的筛选命令示例：
*   筛选具有特定标签的部署：`kubectl get deployments -l app=test`
*   筛选具有多个特定值的标签：`kubectl get pods -l 'app in (test, test2)'`
*   使用否定条件筛选：`kubectl get deployments -l 'app=test,canary!=true'`

## 更新与删除标签

标签可以动态管理。我们可以为现有对象添加新标签。

使用以下命令为部署添加一个标签：
```bash
kubectl label deployment test-prod canary=true
```

![](img/8625a55999d5bad38b15a7de15ef9d16_25.png)

![](img/8625a55999d5bad38b15a7de15ef9d16_27.png)

![](img/8625a55999d5bad38b15a7de15ef9d16_29.png)

要删除一个标签，在`label`命令的键名后加上减号`-`：
```bash
kubectl label deployment test-prod canary-
```

![](img/8625a55999d5bad38b15a7de15ef9d16_31.png)

## 注解简介

除了标签，Kubernetes还有另一种元数据叫注解。注解也是键值对，但它用于存储非识别性的信息，通常是工具或库需要读取的数据，例如构建版本、Git分支、图标链接或负责人信息。

注解定义在`metadata.annotations`字段下。与标签不同，注解没有严格的语法限制，可以包含任何数据。

以下是注解的示例：
```yaml
metadata:
  annotations:
    imageregistry: "https://hub.docker.com/"
    buildVersion: "v2.1.5"
```

## 清理资源

![](img/8625a55999d5bad38b15a7de15ef9d16_33.png)

学习完成后，我们需要清理创建的资源。可以使用选择器进行批量删除。

![](img/8625a55999d5bad38b15a7de15ef9d16_35.png)

删除所有带有特定标签的部署：
```bash
kubectl delete deployment -l app=test
```

![](img/8625a55999d5bad38b15a7de15ef9d16_37.png)

删除命名空间中的所有部署（请谨慎使用）：
```bash
kubectl delete deployment --all
```

## 总结

![](img/8625a55999d5bad38b15a7de15ef9d16_39.png)

![](img/8625a55999d5bad38b15a7de15ef9d16_41.png)

本节课中我们一起学习了Kubernetes的标签和注解。标签是用于标识和选择对象的关键键值对，而注解用于存储辅助性元数据。我们掌握了如何创建、查看、筛选、更新和删除标签，并了解了注解的基本用途。合理使用标签和注解能极大提升管理Kubernetes资源的效率。