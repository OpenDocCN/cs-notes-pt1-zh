# 017：使用角色绑定

在本节课中，我们将学习Kubernetes中的权限绑定。我们将了解如何将之前定义的“角色”或“集群角色”通过“角色绑定”或“集群角色绑定”应用到用户或用户组上，从而授予他们相应的操作权限。

## 概述

上一节我们介绍了如何定义角色和集群角色。本节中我们来看看如何将这些权限定义应用到具体的用户或用户组上，这个过程称为“绑定”。我们将创建两种类型的绑定：作用于特定命名空间的**角色绑定**和作用于整个集群的**集群角色绑定**。

![](img/b58a5b15ecaa0d16818932d93d1e67cc_1.png)

## 角色绑定与集群角色绑定的区别

以下是两者的核心区别：
*   **角色绑定**：将**角色**的权限授予特定命名空间内的用户或用户组。
*   **集群角色绑定**：将**集群角色**的权限授予整个集群范围内的用户或用户组。

## 创建角色绑定

我们将创建几个角色绑定，将之前定义的权限应用到不同的用户组和用户上。

### 为开发组创建绑定

首先，我们创建一个名为 `developer-rolebind.yaml` 的清单文件，将 `developer` 角色绑定到 `dev` 用户组。

![](img/b58a5b15ecaa0d16818932d93d1e67cc_3.png)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer
  namespace: project1
subjects:
- kind: Group
  name: dev
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

![](img/b58a5b15ecaa0d16818932d93d1e67cc_5.png)

![](img/b58a5b15ecaa0d16818932d93d1e67cc_6.png)

使用以下命令应用这个绑定：
```bash
kubectl apply -f developer-rolebind.yaml
```

创建成功后，可以使用 `kubectl describe rolebinding developer -n project1` 命令查看绑定的详细信息。

### 为只读组创建绑定

![](img/b58a5b15ecaa0d16818932d93d1e67cc_8.png)

接下来，我们为 `bi` 用户组创建一个只读权限的绑定。创建文件 `readonly-rolebind.yaml`。

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: readonly
  namespace: project1
subjects:
- kind: Group
  name: bi
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: readonly
  apiGroup: rbac.authorization.k8s.io
```

![](img/b58a5b15ecaa0d16818932d93d1e67cc_10.png)

应用此绑定：
```bash
kubectl apply -f readonly-rolebind.yaml
```

![](img/b58a5b15ecaa0d16818932d93d1e67cc_12.png)

![](img/b58a5b15ecaa0d16818932d93d1e67cc_14.png)

### 为特定用户创建绑定

![](img/b58a5b15ecaa0d16818932d93d1e67cc_16.png)

我们也可以将权限直接绑定到特定用户。例如，将 `developer-admin` 角色绑定到用户 `vitor`。创建文件 `developer-admin-bind.yaml`。

![](img/b58a5b15ecaa0d16818932d93d1e67cc_18.png)

![](img/b58a5b15ecaa0d16818932d93d1e67cc_20.png)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-admin
  namespace: project1
subjects:
- kind: User
  name: vitor
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer-admin
  apiGroup: rbac.authorization.k8s.io
```

应用此绑定：
```bash
kubectl apply -f developer-admin-bind.yaml
```

![](img/b58a5b15ecaa0d16818932d93d1e67cc_22.png)

### 跨命名空间绑定

![](img/b58a5b15ecaa0d16818932d93d1e67cc_24.png)

你可以在不同的命名空间为同一用户绑定不同的角色。例如，在 `project2` 命名空间为用户 `vitor` 绑定 `developer-admin` 角色。创建文件 `developer-admin-project2.yaml`。

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-admin
  namespace: project2
subjects:
- kind: User
  name: vitor
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer-admin
  apiGroup: rbac.authorization.k8s.io
```

应用此绑定：
```bash
kubectl apply -f developer-admin-project2.yaml
```

## 创建集群角色绑定

![](img/b58a5b15ecaa0d16818932d93d1e67cc_26.png)

![](img/b58a5b15ecaa0d16818932d93d1e67cc_28.png)

![](img/b58a5b15ecaa0d16818932d93d1e67cc_30.png)

现在，我们来看看如何创建集群级别的绑定。

![](img/b58a5b15ecaa0d16818932d93d1e67cc_32.png)

![](img/b58a5b15ecaa0d16818932d93d1e67cc_34.png)

### 绑定集群只读角色

![](img/b58a5b15ecaa0d16818932d93d1e67cc_36.png)

![](img/b58a5b15ecaa0d16818932d93d1e67cc_38.png)

创建一个集群角色绑定，将 `readonly` 集群角色授予用户 `vitor`。创建文件 `readonly-clusterbind.yaml`。

![](img/b58a5b15ecaa0d16818932d93d1e67cc_40.png)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: readonly
subjects:
- kind: User
  name: vitor
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: readonly
  apiGroup: rbac.authorization.k8s.io
```

应用此集群绑定：
```bash
kubectl apply -f readonly-clusterbind.yaml
```

![](img/b58a5b15ecaa0d16818932d93d1e67cc_42.png)

### 绑定集群操作员角色

![](img/b58a5b15ecaa0d16818932d93d1e67cc_44.png)

同理，我们可以创建另一个集群角色绑定，例如将 `cluster-operator` 角色授予另一个用户。创建文件 `cluster-operator-bind.yaml`。

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-operator
subjects:
- kind: User
  name: john
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-operator
  apiGroup: rbac.authorization.k8s.io
```

![](img/b58a5b15ecaa0d16818932d93d1e67cc_46.png)

![](img/b58a5b15ecaa0d16818932d93d1e67cc_48.png)

应用此绑定：
```bash
kubectl apply -f cluster-operator-bind.yaml
```

## 验证与查看绑定

![](img/b58a5b15ecaa0d16818932d93d1e67cc_50.png)

![](img/b58a5b15ecaa0d16818932d93d1e67cc_52.png)

绑定创建完成后，可以使用以下命令查看所有已创建的绑定：

```bash
kubectl get rolebindings,clusterrolebindings --all-namespaces -o custom-columns="KIND:kind,NAMESPACE:metadata.namespace,NAME:metadata.name,SERVICE_ACCOUNTS:subjects[?(@.kind=='ServiceAccount')].name"
```

![](img/b58a5b15ecaa0d16818932d93d1e67cc_54.png)

这个命令会列出所有角色绑定和集群角色绑定，并按类型、命名空间、名称进行组织。

![](img/b58a5b15ecaa0d16818932d93d1e67cc_56.png)

## 测试权限

你可以使用 `kubectl auth can-i` 命令来测试某个用户是否拥有特定权限，或者直接以对应用户的上下文执行命令来验证。

## 总结

![](img/b58a5b15ecaa0d16818932d93d1e67cc_58.png)

本节课中我们一起学习了Kubernetes权限绑定的核心操作。我们掌握了如何通过**角色绑定**在命名空间级别为用户或用户组授权，以及如何通过**集群角色绑定**在集群级别进行授权。绑定是将预定义的权限规则与具体实体关联起来的关键步骤，它和角色定义共同构成了Kubernetes RBAC权限管理体系的基础。请记住，Kubernetes的API可能会更新，如果遇到问题，请参考官方最新文档调整清单文件格式。