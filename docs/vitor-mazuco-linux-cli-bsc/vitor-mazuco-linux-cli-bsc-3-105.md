# 105：聚合操作符第二部分 🔢

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_1.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_3.png)

在本节课中，我们将继续学习PromQL中的聚合操作符。我们将重点介绍 `avg`（平均值）、`group`（分组）以及 `min` 和 `max`（最小值和最大值）这几个核心操作符。通过具体的查询示例，你将理解它们如何对时间序列数据进行汇总计算。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_4.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_6.png)

## 平均值操作符：avg 📊

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_8.png)

上一节我们介绍了 `sum` 和 `count` 操作符，本节中我们来看看 `avg` 操作符。`avg` 操作符用于计算一组时间序列值的平均值。

其基本语法是：
```
avg(表达式) by (标签)
```
或者
```
avg(表达式) without (标签)
```
它将返回指定分组内时间序列值的平均值。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_10.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_12.png)

以下是使用 `avg` 操作符的一个查询示例。我们计算过去5分钟内，每个实例（`instance`）的CPU总使用率的平均值。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_13.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_15.png)

```
avg(rate(node_cpu_seconds_total[5m])) without (cpu, mode)
```

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_17.png)

执行这个查询后，结果会显示每个实例的CPU平均使用率。例如，结果中可能包含 `instance="192.168.1.10:9100"` 和其对应的平均CPU使用率数值。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_19.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_21.png)

这个计算过程在逻辑上等同于先对每个实例的所有CPU模式（`mode`）和核心（`cpu`）的使用率求和（`sum`），再除以序列的数量（`count`）。但直接使用 `avg` 操作符更为高效和简洁。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_23.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_24.png)

## 分组操作符：group 🤝

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_26.png)

接下来，我们介绍 `group` 操作符。`group` 是一个特殊的聚合器，它不进行数值计算，而是将所有输入序列的输出值设置为 `1`。它主要用于查看在特定分组下存在哪些不同的时间序列。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_28.png)

其语法与其他聚合操作符类似：
```
group(表达式) by (标签)
```

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_30.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_31.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_33.png)

以下是使用 `group` 操作符的一个查询示例。我们想查看每个实例上存在哪些不同类型的文件系统。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_34.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_36.png)

```
group(node_filesystem_size_bytes) by (instance, fstype)
```

执行这个查询，它会返回每个实例上不同的文件系统类型（如 `ext4`， `xfs`）的数量，并且每个结果的值都是 `1`。这表示在查询的时刻，每个实例-文件系统类型组合都存在对应的时间序列。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_38.png)

实际上，在这个场景下，使用 `count`、`sum` 等其他聚合操作符也会得到类似的分组信息，但 `group` 操作符明确地将其结果值固定为 `1`，更清晰地表达了“存在性”的概念。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_40.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_42.png)

## 最小值与最大值操作符：min 和 max ⬇️⬆️

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_44.png)

最后，我们学习 `min` 和 `max` 操作符。顾名思义，它们分别用于返回一组时间序列中的最小值和最大值。

其语法为：
```
max(表达式) by/without (标签)
min(表达式) by/without (标签)
```

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_46.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_47.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_48.png)

以下是使用 `max` 操作符的查询示例。我们查找每个实例上最大的文件系统。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_50.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_52.png)

```
max(node_filesystem_size_bytes) without (device, fstype, mountpoint)
```

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_54.png)

这个查询会返回每个实例上所有挂载点中，文件系统大小的最大值。例如，结果可能显示某个实例的最大文件系统为 `50GB`。

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_56.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_58.png)

同样地，我们可以使用 `min` 操作符来查找最小值：

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_60.png)

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_61.png)

```
min(node_filesystem_size_bytes) without (device, fstype, mountpoint)
```

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_63.png)

执行后，会返回每个实例上最小的文件系统大小，可能与最大值有显著差异（例如 `76MB`）。通过对比 `min` 和 `max` 的结果，可以快速了解实例上存储资源的分布范围。

---

![](img/9da3d06c0227e4bb94c2dc67dcc738cc_65.png)

本节课中我们一起学习了三个重要的聚合操作符：用于计算平均值的 `avg`，用于标识序列存在性的 `group`，以及用于查找极值的 `min` 和 `max`。这些操作符是分析和汇总监控指标数据的基础工具。虽然PromQL还有其他操作符，但掌握这些核心操作符至关重要。我们将在后续课程中继续探索。