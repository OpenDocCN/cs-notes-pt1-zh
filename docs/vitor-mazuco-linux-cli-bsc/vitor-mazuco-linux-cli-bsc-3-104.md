Linux命令行基础：P104：聚合操作符

![](img/38c83f66da23e9039b636a948b3a7abe_1.png)

![](img/38c83f66da23e9039b636a948b3a7abe_3.png)

在本节课中，我们将要学习Prometheus查询语言（PromQL）中的聚合操作符。我们将重点介绍`by`和`without`子句的用法，以及如何使用`sum`和`count`等聚合函数对时间序列数据进行分组和计算。

![](img/38c83f66da23e9039b636a948b3a7abe_5.png)

![](img/38c83f66da23e9039b636a948b3a7abe_7.png)

---

![](img/38c83f66da23e9039b636a948b3a7abe_9.png)

![](img/38c83f66da23e9039b636a948b3a7abe_10.png)

### 概述

![](img/38c83f66da23e9039b636a948b3a7abe_12.png)

![](img/38c83f66da23e9039b636a948b3a7abe_14.png)

聚合操作允许我们对具有相同标签的时间序列数据进行分组和计算。这有助于从大量数据中提取汇总信息。我们将从`by`操作符开始，它与之前学过的`without`操作符功能互补。

![](img/38c83f66da23e9039b636a948b3a7abe_16.png)

---

![](img/38c83f66da23e9039b636a948b3a7abe_18.png)

![](img/38c83f66da23e9039b636a948b3a7abe_20.png)

![](img/38c83f66da23e9039b636a948b3a7abe_21.png)

### `by` 操作符

![](img/38c83f66da23e9039b636a948b3a7abe_23.png)

上一节我们介绍了`without`操作符，它用于指定要从结果中移除的标签。本节中我们来看看`by`操作符，它用于指定要在结果中保留的标签。

![](img/38c83f66da23e9039b636a948b3a7abe_25.png)

![](img/38c83f66da23e9039b636a948b3a7abe_26.png)

使用`by`时需要特别注意，以确保不会意外移除你希望在告警或仪表盘中传播的标签。例如，在图表中你可能需要保留某些标签用于区分不同的数据线。

![](img/38c83f66da23e9039b636a948b3a7abe_28.png)

![](img/38c83f66da23e9039b636a948b3a7abe_29.png)

**重要规则**：不能在同一个查询中同时使用`by`和`without`操作符。

![](img/38c83f66da23e9039b636a948b3a7abe_31.png)

![](img/38c83f66da23e9039b636a948b3a7abe_33.png)

![](img/38c83f66da23e9039b636a948b3a7abe_35.png)

以下是使用`sum by`进行聚合的示例：
```promql
sum by (job, instance) (node_filesystem_size_bytes)
```
这个查询会按照`job`和`instance`标签对`node_filesystem_size_bytes`指标进行分组求和，计算每个作业和实例的总文件系统大小。

![](img/38c83f66da23e9039b636a948b3a7abe_37.png)

![](img/38c83f66da23e9039b636a948b3a7abe_38.png)

---

![](img/38c83f66da23e9039b636a948b3a7abe_40.png)

![](img/38c83f66da23e9039b636a948b3a7abe_42.png)

### `without` 操作符的补充说明

![](img/38c83f66da23e9039b636a948b3a7abe_44.png)

有些情况下，你只是想移除某些不需要的标签，而不是指定要保留的。这时`without`就很有用。例如，使用`info`类指标时，可能会随时间添加更多标签，你可以用`without`来清理输出。

![](img/38c83f66da23e9039b636a948b3a7abe_46.png)

![](img/38c83f66da23e9039b636a948b3a7abe_48.png)

---

![](img/38c83f66da23e9039b636a948b3a7abe_50.png)

![](img/38c83f66da23e9039b636a948b3a7abe_52.png)

### `count` 聚合函数

`count`函数用于计算一个组内时间序列的数量。它返回该组的计数值。

![](img/38c83f66da23e9039b636a948b3a7abe_54.png)

![](img/38c83f66da23e9039b636a948b3a7abe_55.png)

![](img/38c83f66da23e9039b636a948b3a7abe_57.png)

例如，以下查询用于计算每台机器拥有的磁盘设备数量：
```promql
count without (device) (node_disk_read_bytes_total)
```
这个查询会移除`device`标签，然后对剩余标签组合进行计数，从而得到每台机器的磁盘数量。

![](img/38c83f66da23e9039b636a948b3a7abe_59.png)

---

![](img/38c83f66da23e9039b636a948b3a7abe_61.png)

### 嵌套使用 `count`

![](img/38c83f66da23e9039b636a948b3a7abe_63.png)

你还可以嵌套使用`count`来计算某个标签的唯一值数量。这涉及到两次聚合。

![](img/38c83f66da23e9039b636a948b3a7abe_65.png)

首先，一个内部的`count`移除除目标标签外的其他标签，为每个实例生成一个时间序列。然后，一个外部的`count`计算这些时间序列的数量，即每个实例的CPU核心数。

![](img/38c83f66da23e9039b636a948b3a7abe_67.png)

![](img/38c83f66da23e9039b636a948b3a7abe_69.png)

示例查询如下：
```promql
count without (mode) (
  count without (cpu) (node_cpu_seconds_total)
)
```
这个查询的逻辑是：
1.  内部的 `count without (cpu)` 为每个实例和每种CPU模式生成一个计数。
2.  外部的 `count without (mode)` 再对上一步的结果按实例计数，得到每个实例的CPU核心总数。

![](img/38c83f66da23e9039b636a948b3a7abe_71.png)

---

![](img/38c83f66da23e9039b636a948b3a7abe_73.png)

![](img/38c83f66da23e9039b636a948b3a7abe_75.png)

### 使用 `count by`

![](img/38c83f66da23e9039b636a948b3a7abe_76.png)

![](img/38c83f66da23e9039b636a948b3a7abe_78.png)

你也可以使用`count by`来实现类似的分组计数。其逻辑与使用`without`相反，是指定要保留的标签。

![](img/38c83f66da23e9039b636a948b3a7abe_79.png)

![](img/38c83f66da23e9039b636a948b3a7abe_81.png)

例如，计算每个实例的CPU数量：
```promql
count by (instance) (node_cpu_seconds_total)
```
这个查询会按照`instance`标签分组，计算每个实例下`node_cpu_seconds_total`指标的时间序列数量，结果与上面嵌套查询的最终效果类似。选择`by`还是`without`取决于你更倾向于指定保留项还是移除项。

![](img/38c83f66da23e9039b636a948b3a7abe_83.png)

---

![](img/38c83f66da23e9039b636a948b3a7abe_84.png)

![](img/38c83f66da23e9039b636a948b3a7abe_85.png)

![](img/38c83f66da23e9039b636a948b3a7abe_87.png)

### 总结

![](img/38c83f66da23e9039b636a948b3a7abe_89.png)

本节课中我们一起学习了PromQL中几个核心的聚合操作符和函数：
*   `by`：指定在聚合结果中**保留**的标签。
*   `without`：指定在聚合结果中**移除**的标签（两者不可在同一查询中使用）。
*   `sum`：对分组内的值进行求和。
*   `count`：计算分组内时间序列的数量，并可嵌套使用以计算标签的唯一值数量。

![](img/38c83f66da23e9039b636a948b3a7abe_91.png)

![](img/38c83f66da23e9039b636a948b3a7abe_92.png)

这些是基础但强大的操作符，能帮助我们对监控数据进行有效的汇总和分析。在后续课程中，我们将继续学习其他类型的操作符。