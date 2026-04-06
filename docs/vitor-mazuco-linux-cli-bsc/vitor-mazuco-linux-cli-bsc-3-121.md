# 121：使用Prometheus与Grafana监控（第二部分）

## 概述
在本节课中，我们将学习如何使用Grafana创建仪表盘，以进一步提升基于Prometheus和PostgreSQL的监控能力。我们将完成Grafana的安装、配置，并导入一个现成的PostgreSQL监控仪表盘。

![](img/ff8ad40561cf9525bf96e668f41e2ded_1.png)

---

## 安装Grafana
上一节我们介绍了Prometheus和Postgres Exporter的配置。本节中，我们来看看如何安装和配置Grafana来可视化监控数据。

首先，我们需要在系统上安装Grafana。以下是在基于RPM的Linux系统（如CentOS/RHEL/Fedora）上的安装命令。如果你使用的是Ubuntu，命令会有所不同，但过程类似。

![](img/ff8ad40561cf9525bf96e668f41e2ded_3.png)

```bash
sudo dnf install grafana sqlite
```

**说明**：
*   `grafana`：Grafana监控仪表盘软件。
*   `sqlite`：Grafana用于存储自身配置和数据的轻量级数据库。确保安装它，这是Grafana正常运行所必需的。

![](img/ff8ad40561cf9525bf96e668f41e2ded_5.png)

![](img/ff8ad40561cf9525bf96e668f41e2ded_7.png)

安装过程通常很快。

---

![](img/ff8ad40561cf9525bf96e668f41e2ded_9.png)

## 启动与访问Grafana
安装完成后，我们需要启动Grafana服务并设置开机自启。

![](img/ff8ad40561cf9525bf96e668f41e2ded_11.png)

![](img/ff8ad40561cf9525bf96e668f41e2ded_13.png)

```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

你可以使用以下命令检查服务状态：
```bash
sudo systemctl status grafana-server
```

Grafana默认运行在**3000**端口。请确保防火墙规则允许访问此端口。

在浏览器中，通过 `http://<你的服务器IP地址>:3000` 来访问Grafana的Web界面。

初始的默认登录凭据是：
*   **用户名**：`admin`
*   **密码**：`admin`

首次登录后，系统会要求你更改密码。这是一个重要的安全步骤。

---

![](img/ff8ad40561cf9525bf96e668f41e2ded_15.png)

![](img/ff8ad40561cf9525bf96e668f41e2ded_16.png)

## 配置数据源
成功登录后，我们需要将Grafana连接到我们的数据源——也就是之前设置的Prometheus服务器。

以下是配置步骤：
1.  在Grafana主页，点击左侧导航栏的 **“连接”** (Connections) 图标。
2.  选择 **“数据源”** (Data sources)。
3.  点击 **“添加数据源”** (Add data source)。
4.  从列表中选择 **“Prometheus”**。Prometheus是Grafana原生支持且最常用的数据源之一。
5.  在配置页面，主要需要设置 **HTTP URL**。如果你将Grafana安装在和Prometheus同一台机器上，URL通常是 `http://localhost:9090`。如果Prometheus安装在另一台服务器，请填写其正确的IP地址和端口。
6.  其他设置（如认证）通常保持默认即可，除非你的Prometheus配置了安全访问。
7.  点击页面底部的 **“保存并测试”** (Save & test)。如果连接成功，你会看到一个绿色的成功提示。

至此，Grafana已经可以查询Prometheus中的监控数据了。

---

![](img/ff8ad40561cf9525bf96e668f41e2ded_18.png)

![](img/ff8ad40561cf9525bf96e668f41e2ded_20.png)

![](img/ff8ad40561cf9525bf96e668f41e2ded_22.png)

## 导入仪表盘
现在，我们将导入一个专门为PostgreSQL监控设计的现成仪表盘，这比从头创建要高效得多。

![](img/ff8ad40561cf9525bf96e668f41e2ded_24.png)

![](img/ff8ad40561cf9525bf96e668f41e2ded_26.png)

Grafana社区提供了大量优秀的仪表盘模板。我们将使用一个与Postgres Exporter兼容性很好的模板。

![](img/ff8ad40561cf9525bf96e668f41e2ded_28.png)

![](img/ff8ad40561cf9525bf96e668f41e2ded_30.png)

以下是导入仪表盘的两种主要方法：

![](img/ff8ad40561cf9525bf96e668f41e2ded_32.png)

**方法一：使用仪表盘ID（推荐）**
1.  在Grafana左侧导航栏，将鼠标悬停在 **“仪表盘”** (Dashboards) 图标上，然后选择 **“导入”** (Import)。
2.  在 **“通过Grafana.com导入”** (Import via grafana.com) 的输入框中，粘贴仪表盘ID：`9628`。
3.  点击 **“加载”** (Load)。
4.  在下一个页面，为仪表盘选择一个文件夹（或保持默认），并从 **“Prometheus”** 下拉菜单中选择我们刚才配置的数据源。
5.  点击 **“导入”** (Import)。

**方法二：上传JSON文件**
1.  从Grafana官方网站或社区找到对应仪表盘的JSON文件并下载。
2.  在“导入”页面，点击 **“上传JSON文件”** (Upload JSON file)，选择下载的文件。
3.  后续步骤与方法一相同。

![](img/ff8ad40561cf9525bf96e668f41e2ded_34.png)

导入成功后，一个功能完整的PostgreSQL监控仪表盘就会立即呈现在你面前。

---

![](img/ff8ad40561cf9525bf96e668f41e2ded_36.png)

## 探索仪表盘
导入的仪表盘已经预置了监控PostgreSQL最关键的各项指标。它通过丰富的图表和表格进行展示，使得监控数据一目了然。

仪表盘通常包含以下核心监控区域：
*   **系统资源**：如内存使用率、CPU负载、磁盘I/O。
*   **数据库连接**：当前连接数、最大允许连接数。
*   **查询性能**：每秒事务数（TPS）、查询延迟、活跃查询。
*   **数据操作**：`INSERT`、`UPDATE`、`DELETE`、`SELECT` 等操作的统计。
*   **缓冲区与缓存**：缓存命中率、缓冲区使用情况。
*   **复制与高可用**（如果配置了）：复制延迟、状态。

你可以自由地探索这个仪表盘，点击图表标题可以查看查询的详细PromQL表达式，这有助于你理解其监控原理。你也可以根据需要，对现有面板进行编辑，或添加新的监控指标。

---

## 总结
本节课中，我们一起学习了监控栈的最后一个关键组件——Grafana的部署和使用。我们完成了从安装Grafana、配置Prometheus数据源，到导入社区优秀仪表盘的全过程。现在，你拥有了一个功能强大、可视化效果出色的PostgreSQL监控平台。

![](img/ff8ad40561cf9525bf96e668f41e2ded_38.png)

这个组合（Prometheus + Postgres Exporter + Grafana）提供了一种灵活、现代且高效的监控解决方案，其能力不亚于传统的监控系统（如Zabbix）。通过本课程的学习，你应该能够为自己的数据库系统搭建起完善的监控体系，从而更好地保障其稳定运行。