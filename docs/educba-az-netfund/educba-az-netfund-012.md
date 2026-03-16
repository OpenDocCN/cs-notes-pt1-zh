# 012：Azure防火墙 🔥

![](img/bf063ae74290c1af7fc0da778efd7599_0.png)

![](img/bf063ae74290c1af7fc0da778efd7599_2.png)

![](img/bf063ae74290c1af7fc0da778efd7599_4.png)

![](img/bf063ae74290c1af7fc0da778efd7599_6.png)

![](img/bf063ae74290c1af7fc0da778efd7599_7.png)

在本节课中，我们将学习Azure防火墙。Azure防火墙是一种云原生的、智能的网络安全服务，用于保护您的Azure虚拟网络资源。我们将了解它的核心概念、规则类型，并通过一个实际配置示例来演示其工作原理。

## 什么是防火墙？

首先，我们来理解什么是防火墙。在计算机世界中，防火墙的作用是保护您的系统免受不需要的软件或网络流量的侵入。当您浏览互联网时，防火墙会在您的系统周围建立一道“墙”，只允许合法的文件或来源进入您的系统。

## 什么是Azure防火墙？

![](img/bf063ae74290c1af7fc0da778efd7599_9.png)

![](img/bf063ae74290c1af7fc0da778efd7599_10.png)

Azure防火墙是一种**状态防火墙即服务**。它提供智能的网络防火墙安全，是一个云原生的解决方案。其主要特点包括：
*   **内置高可用性**：您无需手动配置多个防火墙来实现高可用，服务本身已提供。
*   **支持第3层到第7层过滤**：这意味着它可以基于网络层（IP地址、端口）和应用层（URL、域名）进行流量控制。
*   **基于威胁情报的过滤**：可以基于已知的恶意IP地址、域名或URL来告警或拒绝流量。

![](img/bf063ae74290c1af7fc0da778efd7599_12.png)

## Azure防火墙的规则类型

Azure防火墙主要通过三种类型的规则来控制流量。以下是这些规则的详细介绍：

![](img/bf063ae74290c1af7fc0da778efd7599_14.png)

**1. NAT规则**
NAT规则用于允许入站互联网连接，通过配置目标网络地址转换来实现。例如，将公网IP的某个端口流量转发到内部虚拟机的特定端口。

![](img/bf063ae74290c1af7fc0da778efd7599_15.png)

![](img/bf063ae74290c1af7fc0da778efd7599_16.png)

**2. 网络规则**
网络规则用于配置基于源地址、协议、目标端口和目标地址的访问控制。例如，允许特定子网访问另一个子网上的SQL服务器端口。

![](img/bf063ae74290c1af7fc0da778efd7599_18.png)

![](img/bf063ae74290c1af7fc0da778efd7599_20.png)

**3. 应用规则**
应用规则用于配置基于完全限定域名（FQDN）的访问控制。例如，允许特定子网访问 `*.google.com` 或 `www.microsoft.com`。

![](img/bf063ae74290c1af7fc0da778efd7599_22.png)

![](img/bf063ae74290c1af7fc0da778efd7599_24.png)

## Azure防火墙与网络安全组（NSG）的协同工作

![](img/bf063ae74290c1af7fc0da778efd7599_26.png)

![](img/bf063ae74290c1af7fc0da778efd7599_28.png)

![](img/bf063ae74290c1af7fc0da778efd7599_30.png)

上一节我们介绍了Azure防火墙的规则，本节我们来看看它如何与网络安全组协同工作，为您的资源提供多层防护。

![](img/bf063ae74290c1af7fc0da778efd7599_32.png)

![](img/bf063ae74290c1af7fc0da778efd7599_33.png)

Azure防火墙提供了额外的、完全托管的网络安全层。它通常与“中心-辐射”网络架构结合使用。

![](img/bf063ae74290c1af7fc0da778efd7599_35.png)

![](img/bf063ae74290c1af7fc0da778efd7599_36.png)

让我们通过一个示例来理解：
假设您有多个虚拟网络（辐射网络）。在中心位置部署一个Azure防火墙。所有进出辐射网络的流量首先会经过此防火墙。防火墙会根据其配置的规则（网络规则或应用规则）决定是否允许流量通过。

![](img/bf063ae74290c1af7fc0da778efd7599_38.png)

![](img/bf063ae74290c1af7fc0da778efd7599_39.png)

![](img/bf063ae74290c1af7fc0da778efd7599_41.png)

即使流量通过了防火墙，在到达目标虚拟机之前，还需要通过虚拟机或子网级别配置的**网络安全组**的检查。NSG会再次根据其规则进行过滤。

![](img/bf063ae74290c1af7fc0da778efd7599_43.png)

![](img/bf063ae74290c1af7fc0da778efd7599_44.png)

![](img/bf063ae74290c1af7fc0da778efd7599_46.png)

![](img/bf063ae74290c1af7fc0da778efd7599_48.png)

这样，Azure防火墙和NSG共同构成了两层安全防护，极大地增强了网络安全性。

![](img/bf063ae74290c1af7fc0da778efd7599_50.png)

![](img/bf063ae74290c1af7fc0da778efd7599_52.png)

---

![](img/bf063ae74290c1af7fc0da778efd7599_54.png)

![](img/bf063ae74290c1af7fc0da778efd7599_55.png)

![](img/bf063ae74290c1af7fc0da778efd7599_57.png)

## Azure防火墙实战演示 🛠️

![](img/bf063ae74290c1af7fc0da778efd7599_59.png)

![](img/bf063ae74290c1af7fc0da778efd7599_61.png)

现在，让我们通过一个实际配置示例来看看Azure防火墙如何工作。我们已经预先创建好了一些资源以便演示。

![](img/bf063ae74290c1af7fc0da778efd7599_62.png)

![](img/bf063ae74290c1af7fc0da778efd7599_63.png)

![](img/bf063ae74290c1af7fc0da778efd7599_65.png)

![](img/bf063ae74290c1af7fc0da778efd7599_67.png)

### 环境准备

![](img/bf063ae74290c1af7fc0da778efd7599_69.png)

![](img/bf063ae74290c1af7fc0da778efd7599_71.png)

我们创建了以下资源：
1.  **一个虚拟网络**：地址空间为 `10.0.0.0/16`。
2.  **两个子网**：
    *   **AzureFirewallSubnet**：地址范围 `10.0.1.0/26`，用于部署Azure防火墙。
    *   **WorkloadSubnet**：地址范围 `10.0.2.0/24`，用于部署业务虚拟机。
3.  **一台虚拟机**：部署在 `WorkloadSubnet` 中。其网络安全组已禁用所有入站端口（包括RDP），仅依靠防火墙进行访问控制。
4.  **一个Azure防火墙**：部署在 `AzureFirewallSubnet` 中，并关联了一个公共IP地址。
5.  **一个路由表**：关联到 `WorkloadSubnet`，确保所有发往互联网的流量都经过Azure防火墙。路由配置如下：
    *   **地址前缀**：`0.0.0.0/0` (代表所有互联网流量)
    *   **下一跃点类型**：虚拟设备
    *   **下一跃点地址**：`10.0.1.4` (Azure防火墙的私有IP地址)

![](img/bf063ae74290c1af7fc0da778efd7599_73.png)

![](img/bf063ae74290c1af7fc0da778efd7599_75.png)

![](img/bf063ae74290c1af7fc0da778efd7599_77.png)

### 配置防火墙规则

![](img/bf063ae74290c1af7fc0da778efd7599_78.png)

![](img/bf063ae74290c1af7fc0da778efd7599_80.png)

![](img/bf063ae74290c1af7fc0da778efd7599_82.png)

接下来，我们为Azure防火墙配置具体的规则。

![](img/bf063ae74290c1af7fc0da778efd7599_84.png)

**1. 应用规则**
我们配置了一条规则，允许来自工作负载子网 (`10.0.2.0/24`) 的流量访问 `www.google.com`。
```plaintext
名称: Allow-Google
源: 10.0.2.0/24
协议: HTTP, HTTPS
目标: www.google.com
操作: 允许
```

![](img/bf063ae74290c1af7fc0da778efd7599_86.png)

![](img/bf063ae74290c1af7fc0da778efd7599_87.png)

**2. 网络规则**
我们配置了一条规则，允许工作负载子网通过UDP协议访问外部DNS服务器 (`209.244.0.3` 和 `209.244.0.4`) 的53端口，以进行域名解析。
```plaintext
名称: Allow-DNS
源: 10.0.2.0/24
协议: UDP
目标地址: 209.244.0.3, 209.244.0.4
目标端口: 53
操作: 允许
```
同时，我们在虚拟机的网络接口卡上配置了这些外部DNS服务器地址。

![](img/bf063ae74290c1af7fc0da778efd7599_89.png)

**3. NAT规则**
我们配置了一条规则，将发送到防火墙公共IP地址3389端口的RDP流量，转发到内部虚拟机 (`10.0.2.4`) 的3389端口。
```plaintext
名称: Allow-RDP
源: *
目标地址: <防火墙公共IP>
目标端口: 3389
转换地址: 10.0.2.4
转换端口: 3389
协议: TCP
操作: 允许
```

![](img/bf063ae74290c1af7fc0da778efd7599_91.png)

![](img/bf063ae74290c1af7fc0da778efd7599_93.png)

![](img/bf063ae74290c1af7fc0da778efd7599_94.png)

### 测试验证

![](img/bf063ae74290c1af7fc0da778efd7599_96.png)

现在，我们来测试配置是否生效。

1.  **测试RDP连接**：使用防火墙的公共IP地址进行RDP连接。连接成功，这证明了NAT规则工作正常。
2.  **测试应用规则**：在虚拟机内部打开浏览器，访问 `www.google.com`。访问成功，因为应用规则允许此域名。
3.  **测试规则拒绝**：在同一个浏览器中尝试访问 `www.microsoft.com`。访问被阻止，因为我们没有配置允许此域名的规则。

![](img/bf063ae74290c1af7fc0da778efd7599_98.png)

![](img/bf063ae74290c1af7fc0da778efd7599_100.png)

![](img/bf063ae74290c1af7fc0da778efd7599_102.png)

这个测试清晰地展示了Azure防火墙如何根据我们定义的规则，精确地控制网络流量的允许与拒绝。

![](img/bf063ae74290c1af7fc0da778efd7599_104.png)

![](img/bf063ae74290c1af7fc0da778efd7599_105.png)

---

![](img/bf063ae74290c1af7fc0da778efd7599_107.png)

## 总结

![](img/bf063ae74290c1af7fc0da778efd7599_109.png)

![](img/bf063ae74290c1af7fc0da778efd7599_111.png)

![](img/bf063ae74290c1af7fc0da778efd7599_113.png)

在本节课中，我们一起学习了Azure防火墙的核心知识。我们首先了解了防火墙的基本概念以及Azure防火墙作为一种云原生服务的特点。然后，我们详细探讨了其三种核心规则类型：**NAT规则**、**网络规则**和**应用规则**，并解释了它们如何与网络安全组协同构建多层防御。

![](img/bf063ae74290c1af7fc0da778efd7599_115.png)

![](img/bf063ae74290c1af7fc0da778efd7599_117.png)

最后，通过一个完整的实战演示，我们一步步配置了虚拟网络、子网、路由以及防火墙规则，并验证了防火墙能够有效地基于域名、端口和IP地址来控制网络访问。Azure防火墙是保护Azure虚拟网络环境中工作负载安全的关键服务。