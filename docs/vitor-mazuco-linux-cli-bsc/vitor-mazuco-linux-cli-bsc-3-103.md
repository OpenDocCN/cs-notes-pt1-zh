# 103：标签选择器与匹配器 🔍

![](img/bfabde4e94ed7de774c655f4227bdddd_1.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_3.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_5.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_7.png)

在本节课中，我们将要学习PromQL中如何使用标签选择器和匹配器来精确筛选和查询特定的监控指标。这是构建有效查询、获取所需数据的关键步骤。

![](img/bfabde4e94ed7de774c655f4227bdddd_9.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_11.png)

上一节我们介绍了PromQL的基本查询结构，本节中我们来看看如何通过标签来限定查询范围。

![](img/bfabde4e94ed7de774c655f4227bdddd_13.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_15.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_17.png)

## 标签选择器

![](img/bfabde4e94ed7de774c655f4227bdddd_19.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_21.png)

标签选择器用于根据指标的标签（如`job`、`instance`）来过滤时间序列。它允许我们只获取带有特定标签值的指标数据。

![](img/bfabde4e94ed7de774c655f4227bdddd_23.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_25.png)

例如，我们有一个内存使用指标 `process_resident_memory_bytes`。执行该查询可能会返回来自不同任务（如`node`和`prometheus`）的数据。

![](img/bfabde4e94ed7de774c655f4227bdddd_27.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_29.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_31.png)

为了只获取`job`标签为`node`的指标数据，我们可以使用以下选择器：

![](img/bfabde4e94ed7de774c655f4227bdddd_33.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_34.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_35.png)

```promql
process_resident_memory_bytes{job="node"}
```

![](img/bfabde4e94ed7de774c655f4227bdddd_37.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_38.png)

这个查询将返回所有`job`标签值**等于**`"node"`的时间序列，从而排除了`job="prometheus"`的数据。

![](img/bfabde4e94ed7de774c655f4227bdddd_40.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_42.png)

## 匹配器类型

![](img/bfabde4e94ed7de774c655f4227bdddd_44.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_46.png)

除了精确匹配，PromQL提供了多种匹配器（Matchers）来满足不同的筛选需求。以下是四种主要的匹配器：

![](img/bfabde4e94ed7de774c655f4227bdddd_47.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_49.png)

*   **`=`**：相等匹配器。标签值必须完全等于指定的字符串。
    *   示例：`job="prometheus"` 只匹配`job`标签为`prometheus`的序列。

![](img/bfabde4e94ed7de774c655f4227bdddd_51.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_53.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_54.png)

*   **`!=`**：不相等匹配器。标签值必须不等于指定的字符串。
    *   示例：`job!="node"` 会返回所有`job`标签值**不是**`"node"`的序列，例如`prometheus`。

![](img/bfabde4e94ed7de774c655f4227bdddd_56.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_58.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_60.png)

*   **`=~`**：正则表达式匹配器。标签值必须匹配提供的正则表达式。
    *   示例：`job=~"n.*"` 会匹配所有`job`标签值以字母`n`开头的序列，例如`node`。`job=~"p.*"` 则会匹配以`p`开头的序列，如`prometheus`。

![](img/bfabde4e94ed7de774c655f4227bdddd_62.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_63.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_65.png)

*   **`!~`**：正则表达式不匹配器。标签值必须**不**匹配提供的正则表达式。
    *   示例：`job!~"n.*"` 会返回所有`job`标签值**不以**字母`n`开头的序列。

![](img/bfabde4e94ed7de774c655f4227bdddd_67.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_69.png)

这些匹配器可以组合使用，帮助我们构建非常灵活和精确的查询，从而在海量指标数据中快速定位到我们关心的部分。

![](img/bfabde4e94ed7de774c655f4227bdddd_71.png)

## 总结

![](img/bfabde4e94ed7de774c655f4227bdddd_73.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_75.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_76.png)

本节课中我们一起学习了PromQL中标签选择器与匹配器的核心用法。我们了解到：
1.  标签选择器 `{label="value"}` 是筛选指标的基础。
2.  通过四种匹配器（`=`, `!=`, `=~`, `!~`）可以实现精确匹配、排除、正则匹配及正则排除，极大地增强了查询的灵活性。

![](img/bfabde4e94ed7de774c655f4227bdddd_78.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_79.png)

![](img/bfabde4e94ed7de774c655f4227bdddd_81.png)

掌握这些知识是编写高效PromQL查询语句的基石。本课程示例中使用的所有指标，你都可以在提供的配套文件中进行访问和练习。