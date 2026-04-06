# 195：了解 Kubernetes 代理与 DNS 🔄

![](img/bbede6b58e6f1770730acda720dfd60d_1.png)

![](img/bbede6b58e6f1770730acda720dfd60d_2.png)

在本节课中，我们将学习 Kubernetes 中的两个核心网络组件：**代理** 和 **DNS**。我们将了解它们的基本概念、作用以及如何查看它们的状态。这些知识是后续进行实际配置和管理的基础。

## 代理（Proxy）概述

![](img/bbede6b58e6f1770730acda720dfd60d_4.png)

![](img/bbede6b58e6f1770730acda720dfd60d_5.png)

上一节我们介绍了 Kubernetes 的基础架构，本节中我们来看看网络代理。Kubernetes 网络代理是一个运行在集群**每个节点**上的组件。它的核心功能是反映 API 定义的 Service（服务），并负责转发网络流量。

具体来说，代理负责处理以下协议的流量转发：
*   **TCP 协议**
*   **UDP 协议**
*   **SCTP 协议**

![](img/bbede6b58e6f1770730acda720dfd60d_7.png)

集群中服务的 IP 地址和端口信息，是通过 Docker 链接的环境变量发现的。代理会监听这些服务指定的端口。

![](img/bbede6b58e6f1770730acda720dfd60d_9.png)

## 查看代理状态

![](img/bbede6b58e6f1770730acda720dfd60d_11.png)

我们可以使用 `kubectl` 命令来查看代理组件的状态。以下是查看系统守护进程集（DaemonSet）的命令：

```bash
kubectl get daemonsets -n kube-system
```

![](img/bbede6b58e6f1770730acda720dfd60d_13.png)

![](img/bbede6b58e6f1770730acda720dfd60d_14.png)

执行此命令后，你将看到名为 `kube-proxy` 的守护进程集信息，其中可能包含以下关键信息：
*   **期望副本数**：例如 `DESIRED: 3`，表示应在 3 个节点上运行。
*   **当前副本数**：例如 `CURRENT: 3`，表示当前有 3 个副本在运行。
*   **就绪副本数**：例如 `READY: 3`，表示有 3 个副本已准备就绪。
*   **最新副本数**：例如 `UP-TO-DATE: 3`，表示有 3 个副本已更新到最新版本。
*   **可用副本数**：例如 `AVAILABLE: 3`，表示有 3 个副本可用。
*   **节点选择器**：定义了代理运行在哪些节点上。
*   **运行时长**：显示代理已经运行了多久。

![](img/bbede6b58e6f1770730acda720dfd60d_16.png)

这些信息对于监控集群的网络健康状况非常有用。

![](img/bbede6b58e6f1770730acda720dfd60d_18.png)

## DNS 概述

![](img/bbede6b58e6f1770730acda720dfd60d_20.png)

![](img/bbede6b58e6f1770730acda720dfd60d_22.png)

除了代理，DNS 也是 Kubernetes 集群网络的关键部分。集群 DNS 为集群内的 Pod 和服务提供名称解析服务，使得 Pod 之间可以通过服务名相互访问，而无需记住复杂的 IP 地址。

![](img/bbede6b58e6f1770730acda720dfd60d_24.png)

## 查看 DNS 状态

![](img/bbede6b58e6f1770730acda720dfd60d_26.png)

集群 DNS 通常是自动创建的。我们可以使用类似的 `kubectl` 命令来查看其部署状态。以下是查看 `kube-system` 命名空间下部署的命令：

![](img/bbede6b58e6f1770730acda720dfd60d_28.png)

```bash
kubectl get deployments -n kube-system
```

![](img/bbede6b58e6f1770730acda720dfd60d_30.png)

在输出中，你可以找到一个名为 `coredns`（或早期版本的 `kube-dns`）的部署。其状态可能显示为：
*   **就绪状态**：例如 `2/2`，表示期望 2 个副本，且 2 个都已准备就绪。

我们也可以查看对应的 DNS 服务：

![](img/bbede6b58e6f1770730acda720dfd60d_32.png)

![](img/bbede6b58e6f1770730acda720dfd60d_34.png)

```bash
kubectl get svc -n kube-system
```

![](img/bbede6b58e6f1770730acda720dfd60d_36.png)

![](img/bbede6b58e6f1770730acda720dfd60d_37.png)

你将看到一个名为 `kube-dns` 或 `coredns` 的服务。通过此命令，你可以获取：
*   **集群内部 IP 地址**：这是 DNS 服务在集群内部的访问地址。
*   **端口信息**：显示 DNS 服务正在使用哪些端口（通常是 53 端口）。

![](img/bbede6b58e6f1770730acda720dfd60d_39.png)

正如前面课程提到的，这些 IP 地址是集群内部的地址。DNS 服务正是通过这些端口为整个集群提供名称解析。

![](img/bbede6b58e6f1770730acda720dfd60d_41.png)

## 总结

![](img/bbede6b58e6f1770730acda720dfd60d_43.png)

![](img/bbede6b58e6f1770730acda720dfd60d_45.png)

本节课中我们一起学习了 Kubernetes 的两个重要网络组件。我们介绍了 **kube-proxy**，它作为网络代理运行在每个节点上，负责服务流量的转发。我们也探讨了 **集群 DNS**，它提供了内部服务发现和名称解析功能。我们学习了如何使用 `kubectl get` 命令查看这些系统组件的状态信息。

![](img/bbede6b58e6f1770730acda720dfd60d_47.png)

在接下来的课程中，我们将更详细地研究端口以及如何实际使用和配置这些组件。