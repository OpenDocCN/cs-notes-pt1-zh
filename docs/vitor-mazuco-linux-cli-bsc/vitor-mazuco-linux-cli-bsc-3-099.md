# 099：目标标签操作 🏷️

在本节课中，我们将要学习Prometheus监控系统中的目标标签。目标标签用于标识和分组被监控的对象，是查询和筛选指标数据的关键。我们将了解标签的基本概念、如何配置标签，以及如何使用`relabel_configs`来动态修改标签，而无需直接改动主配置文件。

## 什么是目标标签？ 🤔

![](img/35156d3c1b7f88094e46034a8fff63fd_1.png)

上一节我们介绍了Prometheus的基本监控对象。本节中我们来看看目标标签的具体含义。

目标标签指的是用于标识和分组被Prometheus监控的对象的标签或标记。这些标签会被添加到Prometheus收集的指标数据中，可用于在查询时进行过滤、分组或以特定方式选择目标。

例如，如果你使用Prometheus系统监控多台Web服务器，可以为每台服务器添加一组标签，以唯一标识服务器名称、运行环境以及提供的服务类型。此外，添加更广泛的标签也很常见，例如系统处于开发环境还是生产环境、数据中心位置以及负责的IT团队。

以下是目标标签的一个示例配置：

```yaml
- job_name: 'web_servers'
  static_configs:
    - targets: ['webserver1:80', 'webserver2:80']
      labels:
        instance: 'webserver1'
        env: 'production'
        service: 'web'
```

在这个例子中，我们定义了一个名为`web_servers`的作业，监控两个目标。每个目标都附带了`instance`、`env`和`service`标签。定义标签时，应确保其直观、有用且能反映系统和环境的相关信息。

![](img/35156d3c1b7f88094e46034a8fff63fd_3.png)

![](img/35156d3c1b7f88094e46034a8fff63fd_5.png)

## 使用标签查询指标 🔍

我们也可以利用这些标签来查询指标。例如，如果你希望开发环境系统的警报处理方式与生产环境服务器不同，或者想了解哪个数据库负载最重、哪台服务器CPU使用率更高，标签就能派上用场。这为后续的警报配置和数据分析奠定了基础。

## 动态修改标签：Relabeling 🔄

![](img/35156d3c1b7f88094e46034a8fff63fd_7.png)

有时，你可能需要修改目标的标签，但又不想直接编辑主配置文件。这时，可以使用Prometheus的`relabel_configs`配置项。它允许你通过规则动态地重写标签。

![](img/35156d3c1b7f88094e46034a8fff63fd_9.png)

![](img/35156d3c1b7f88094e46034a8fff63fd_11.png)

以下是使用`relabel_configs`修改标签的步骤：

![](img/35156d3c1b7f88094e46034a8fff63fd_13.png)

1.  **定位源标签**：首先，需要指定你想要修改的原始标签，例如`team`。
2.  **使用正则表达式匹配**：通过正则表达式（regex）精确匹配标签值中的特定部分。例如，匹配值中的“monitoring”这个词。
3.  **执行替换操作**：将匹配到的部分替换为新的值。例如，将“monitoring”替换为“monitor”。
4.  **重启Prometheus服务**：修改配置文件后，需要重启Prometheus服务以使更改生效。

一个具体的配置示例如下：

![](img/35156d3c1b7f88094e46034a8fff63fd_15.png)

![](img/35156d3c1b7f88094e46034a8fff63fd_17.png)

```yaml
relabel_configs:
  - source_labels: [team]
    regex: 'monitoring'
    replacement: 'monitor'
    action: replace
```

![](img/35156d3c1b7f88094e46034a8fff63fd_19.png)

![](img/35156d3c1b7f88094e46034a8fff63fd_21.png)

在这个配置中，Prometheus会查找`team`标签，如果其值包含“monitoring”，就将其替换为“monitor”。

![](img/35156d3c1b7f88094e46034a8fff63fd_23.png)

![](img/35156d3c1b7f88094e46034a8fff63fd_25.png)

## 正则表达式在Relabeling中的应用 🧩

![](img/35156d3c1b7f88094e46034a8fff63fd_27.png)

![](img/35156d3c1b7f88094e46034a8fff63fd_29.png)

正则表达式在`relabeling`中非常有用，它能提供更精确的匹配模式。

*   **匹配特定结尾**：例如，使用正则表达式`(.*)ing$`可以匹配所有以“ing”结尾的单词，然后进行替换。
*   **删除标签**：如果你想完全删除某个标签，可以将`replacement`设置为空字符串，并将`action`设置为`replace`。
*   **修改其他字段**：`relabeling`不仅限于修改标签，还可以修改`__address__`这类特殊标签来改变目标的端口。例如，将所有目标的端口改为8001：

![](img/35156d3c1b7f88094e46034a8fff63fd_31.png)

```yaml
relabel_configs:
  - source_labels: [__address__]
    regex: '(.*):\d+'
    replacement: '${1}:8001'
    action: replace
```

![](img/35156d3c1b7f88094e46034a8fff63fd_33.png)

![](img/35156d3c1b7f88094e46034a8fff63fd_35.png)

![](img/35156d3c1b7f88094e46034a8fff63fd_37.png)

## 总结 📝

![](img/35156d3c1b7f88094e46034a8fff63fd_39.png)

![](img/35156d3c1b7f88094e46034a8fff63fd_41.png)

本节课中我们一起学习了Prometheus目标标签的核心概念和操作。我们了解到目标标签是组织和筛选监控数据的重要工具。通过`relabel_configs`功能，我们可以灵活地动态修改标签，包括重命名、内容替换甚至删除，而无需触及主监控配置文件，这大大提升了配置管理的效率和灵活性。掌握标签的使用是构建清晰、可维护的监控体系的关键一步。