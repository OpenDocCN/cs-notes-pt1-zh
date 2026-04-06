# 117：在Prometheus中使用Grafana第二部分 🖥️📊

在本节课中，我们将学习如何在Grafana中创建和配置仪表盘，以可视化从Prometheus收集的监控数据。我们将从创建第一个图表开始，逐步添加CPU、内存、磁盘和网络等核心指标的监控视图。

---

## 概述

上一节我们完成了Grafana数据源的配置。本节我们将动手创建仪表盘，将Prometheus中的指标数据转化为直观的图表。我们将重点关注几个核心系统指标，并学习Grafana图表的基本创建与定制流程。

---

## 使用预置仪表盘

安装并配置Prometheus后，Grafana会自动提供一些基于模板的预置仪表盘。

以下是可用的预置仪表盘：
*   **Stat 2.0** 和 **Stat 1.0**：提供Prometheus中一些标准数据的概览视图。
*   您可以启用并查看这些仪表盘。
*   可以点击星标将其收藏，便于后续快速查找。

这些预置仪表盘展示了一些基础的Prometheus指标，例如：
*   抓取器（Scraper）活动状态
*   内存使用情况
*   数据损坏情况（如果存在）
*   其他各类指标

---

## 创建新仪表盘

仪表盘列表（Dashboard）展示了所有已创建的视图。现在，我们开始创建一个全新的仪表盘。

要创建新仪表盘，请点击“New dashboard”。您可以选择多种创建方式：全新创建、导入已有配置或添加行/面板。

![](img/f630b9c2362ce22a9e125a7a8b88175b_1.png)

对于有图表工具使用经验的人来说，这个过程很直观。对于新手也无需担心，操作非常简单。我们选择“Create from scratch”从头开始创建。

我们将为一个被监控的Windows系统和一个作为Prometheus服务器的Linux机器添加监控指标。

![](img/f630b9c2362ce22a9e125a7a8b88175b_3.png)

---

![](img/f630b9c2362ce22a9e125a7a8b88175b_5.png)

![](img/f630b9c2362ce22a9e125a7a8b88175b_7.png)

## 选择图表类型

点击“Add new panel”后，首先需要选择图表类型。

![](img/f630b9c2362ce22a9e125a7a8b88175b_9.png)

Grafana提供了多种图表类型：
*   **Time series（时间序列图）**：最常用的类型，用于展示数据随时间的变化趋势。
*   **Bar chart（柱状图）**：以柱形展示数据。
*   **Gauge（仪表盘）**：常用于展示当前值或百分比。
*   **Pie chart（饼图）**：展示各部分占总体的比例。

![](img/f630b9c2362ce22a9e125a7a8b88175b_11.png)

本课程中，我们将主要使用**Time series**图表。其他图表设置繁多，为避免混淆，我们暂不深入。

---

## 添加监控指标

创建图表的第一步是确定要监控的指标。我们在之前的课程中已经学习过指标和数据的收集。

本节课，我们将关注几个核心指标：**CPU**、**内存**、**磁盘**和**网络带宽**。

### 1. 添加CPU使用率指标

我们首先添加一个CPU使用率指标，即过去一分钟的平均CPU使用率。

在Prometheus表达式浏览器中，找到对应的指标，例如：
```
avg(rate(node_cpu_seconds_total[1m])) * 100
```

复制整个指标表达式。

![](img/f630b9c2362ce22a9e125a7a8b88175b_13.png)

回到Grafana的图表编辑界面。确保数据源（Data source）选择为您的Prometheus服务器。在查询（Query）输入框中，粘贴复制的指标表达式。

![](img/f630b9c2362ce22a9e125a7a8b88175b_15.png)

点击“Run query”来搜索并验证该指标数据。查询成功后，图表区域将显示对应的数据曲线。

您可以为该图表设置一个标题，例如“CPU Usage - Node Server”。

点击右上角的“Apply”或“Save”按钮。保存仪表盘时，为其命名，例如“Main Panel”，并选择保存的文件夹。

![](img/f630b9c2362ce22a9e125a7a8b88175b_17.png)

![](img/f630b9c2362ce22a9e125a7a8b88175b_19.png)

### 2. 为Windows系统添加指标

用同样的方法为Windows服务器添加CPU监控。在Prometheus中找到Windows相关的CPU指标，例如：
```
avg(rate(windows_cpu_time_total[1m])) * 100
```

将其粘贴到Grafana的新建面板查询框中，运行查询，设置标题为“CPU - Windows Server”，然后保存。

![](img/f630b9c2362ce22a9e125a7a8b88175b_21.png)

### 3. 添加磁盘使用指标

![](img/f630b9c2362ce22a9e125a7a8b88175b_23.png)

接下来，我们添加磁盘使用情况的监控。

对于Linux节点，可以查找类似 `node_filesystem_free_bytes` 的指标。复制表达式并粘贴到Grafana的新面板中，运行查询后设置标题，例如“Node Disk Free Space”。

对于Windows服务器，查找类似 `windows_logical_disk_free_bytes` 的指标来监控C盘剩余空间。创建新面板，添加查询，标题设为“Windows C Drive Free Space”。

您还可以在同一个图表面板中添加多个查询。例如，在Windows磁盘空间面板中，点击“+ Query”可以添加第二个指标，如磁盘写入操作速率 `rate(windows_disk_write_bytes_total[5m])`，从而在一个视图中同时监控空间和IO。

### 4. 添加网络带宽指标

最后，我们添加网络流量监控。

对于Linux节点，通常使用 `node_network_receive_bytes_total` 和 `node_network_transmit_bytes_total` 分别表示接收和发送的字节数。可以在一个图表面板中用查询A和查询B同时添加这两个指标，标题设为“Node Network Bandwidth”。

![](img/f630b9c2362ce22a9e125a7a8b88175b_25.png)

![](img/f630b9c2362ce22a9e125a7a8b88175b_27.png)

![](img/f630b9c2362ce22a9e125a7a8b88175b_29.png)

对于Windows，查找类似的网络指标，如 `windows_net_bytes_received_total` 和 `windows_net_bytes_sent_total`。用同样的方法创建图表，标题设为“Windows Server Bandwidth”。

![](img/f630b9c2362ce22a9e125a7a8b88175b_31.png)

---

## 调整与保存

创建完所有图表后，您可以在仪表盘编辑界面自由拖拽调整每个图表的位置和大小，使其布局更美观、更符合您的观看习惯。

布局取决于您的使用场景，例如屏幕大小、是否使用多显示器等，这完全由您根据专业需求和个人偏好来决定。

![](img/f630b9c2362ce22a9e125a7a8b88175b_33.png)

调整满意后，务必点击保存。您还可以将重要的仪表盘标记为“收藏”，方便快速访问。

![](img/f630b9c2362ce22a9e125a7a8b88175b_35.png)

Grafana会自动处理时区转换，将Prometheus的UTC时间调整为您的本地时间，这使得时间序列数据的阅读非常方便。

---

![](img/f630b9c2362ce22a9e125a7a8b88175b_37.png)

## 总结

![](img/f630b9c2362ce22a9e125a7a8b88175b_38.png)

本节课我们一起学习了在Grafana中创建监控仪表盘的核心步骤。我们掌握了如何从Prometheus复制指标表达式，在Grafana中创建时间序列图表，并逐步添加了CPU、磁盘、网络等关键指标的监控视图。整个过程的核心是：在Prometheus验证指标有效后，将其表达式复制到Grafana中即可生成可视化图表。

![](img/f630b9c2362ce22a9e125a7a8b88175b_40.png)

Grafana与Prometheus的结合是目前主流的监控数据可视化方案。虽然Grafana还有大量高级配置功能，但掌握这些基础知识已足以让您开始构建实用的监控视图。