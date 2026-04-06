# 108：使用函数

![](img/6a8e22959cb7580575dad05da66209ca_1.png)

![](img/6a8e22959cb7580575dad05da66209ca_2.png)

![](img/6a8e22959cb7580575dad05da66209ca_3.png)

![](img/6a8e22959cb7580575dad05da66209ca_4.png)

![](img/6a8e22959cb7580575dad05da66209ca_5.png)

在本节课中，我们将要学习如何在Prometheus查询语言（PromQL）中使用函数。Prometheus提供了大量功能各异的函数，涵盖数学运算、处理计算机指标和直方图等多个方面。

![](img/6a8e22959cb7580575dad05da66209ca_7.png)

![](img/6a8e22959cb7580575dad05da66209ca_9.png)

## 概述

![](img/6a8e22959cb7580575dad05da66209ca_11.png)

Prometheus的函数库非常丰富，提供了数十种函数以满足不同的监控和计算需求。这些函数主要操作瞬时向量或单个时间序列的样本数据。我们将从几个核心函数开始学习，理解它们的作用和用法。

![](img/6a8e22959cb7580575dad05da66209ca_13.png)

![](img/6a8e22959cb7580575dad05da66209ca_14.png)

![](img/6a8e22959cb7580575dad05da66209ca_15.png)

![](img/6a8e22959cb7580575dad05da66209ca_17.png)

## 函数返回值类型

![](img/6a8e22959cb7580575dad05da66209ca_18.png)

![](img/6a8e22959cb7580575dad05da66209ca_20.png)

上一节我们介绍了Prometheus的基本概念，本节中我们来看看函数的返回值类型。Prometheus函数主要返回两种类型的值：标量（Scalar）和瞬时向量（Instant Vector）。

![](img/6a8e22959cb7580575dad05da66209ca_22.png)

![](img/6a8e22959cb7580575dad05da66209ca_23.png)

![](img/6a8e22959cb7580575dad05da66209ca_25.png)

以下是主要的返回值类型：

![](img/6a8e22959cb7580575dad05da66209ca_27.png)

![](img/6a8e22959cb7580575dad05da66209ca_28.png)

*   **标量值函数**：少数函数返回标量值。例如：
    *   `time()` 函数：返回当前时间戳。
    *   `scalar()` 函数：将瞬时向量转换为标量。
*   **瞬时向量函数**：绝大多数函数返回的是瞬时向量。这些函数通常对单个时间序列的样本或瞬时向量的样本进行操作。

![](img/6a8e22959cb7580575dad05da66209ca_30.png)

![](img/6a8e22959cb7580575dad05da66209ca_32.png)

![](img/6a8e22959cb7580575dad05da66209ca_33.png)

## `vector()` 函数

![](img/6a8e22959cb7580575dad05da66209ca_35.png)

![](img/6a8e22959cb7580575dad05da66209ca_37.png)

![](img/6a8e22959cb7580575dad05da66209ca_39.png)

现在，让我们深入了解第一个具体函数：`vector()` 函数。

![](img/6a8e22959cb7580575dad05da66209ca_41.png)

![](img/6a8e22959cb7580575dad05da66209ca_43.png)

![](img/6a8e22959cb7580575dad05da66209ca_44.png)

`vector()` 函数的作用是接收一个标量值，并将其转换为一个不带标签的瞬时向量。其基本语法如下：
```promql
vector(<scalar>)
```
例如，执行 `vector(1)` 会返回一个值为1的瞬时向量。这个函数非常有用，它可以确保我们的表达式始终返回一个结果，而不依赖于任何特定时间序列的存在。

![](img/6a8e22959cb7580575dad05da66209ca_46.png)

![](img/6a8e22959cb7580575dad05da66209ca_48.png)

![](img/6a8e22959cb7580575dad05da66209ca_50.png)

## `scalar()` 函数

![](img/6a8e22959cb7580575dad05da66209ca_52.png)

![](img/6a8e22959cb7580575dad05da66209ca_54.png)

![](img/6a8e22959cb7580575dad05da66209ca_56.png)

接下来，我们看看与 `vector()` 功能相反的 `scalar()` 函数。

![](img/6a8e22959cb7580575dad05da66209ca_58.png)

![](img/6a8e22959cb7580575dad05da66209ca_59.png)

![](img/6a8e22959cb7580575dad05da66209ca_61.png)

`scalar()` 函数接收一个瞬时向量（该向量应只包含一个样本），并将其转换为标量值。如果输入的瞬时向量没有返回任何值，或者结果无法表示，它将返回一个特殊的“未定义数值”（NaN）。这个函数对于处理标量常量非常方便。

![](img/6a8e22959cb7580575dad05da66209ca_63.png)

![](img/6a8e22959cb7580575dad05da66209ca_65.png)

![](img/6a8e22959cb7580575dad05da66209ca_66.png)

以下是 `scalar()` 函数的一些应用示例：

![](img/6a8e22959cb7580575dad05da66209ca_68.png)

![](img/6a8e22959cb7580575dad05da66209ca_69.png)

*   **计算常数**：例如，计算10的自然对数，可以使用 `scalar(ln(vector(10)))`，结果约为2.3026。
*   **提取时间信息**：如果想查看服务器在当前年份是否启动过，可以使用 `year(scalar(process_start_time_seconds))` 这样的查询，它会返回年份（例如2023），这比使用其他分组函数更简洁。

![](img/6a8e22959cb7580575dad05da66209ca_71.png)

![](img/6a8e22959cb7580575dad05da66209ca_72.png)

![](img/6a8e22959cb7580575dad05da66209ca_74.png)

**需要注意的是**，`scalar()` 函数的使用应当谨慎，因为它会丢弃输入向量的所有标签。丢失标签意味着失去了基于标签进行向量匹配的能力，这是一个重要的限制。

![](img/6a8e22959cb7580575dad05da66209ca_75.png)

![](img/6a8e22959cb7580575dad05da66209ca_77.png)

![](img/6a8e22959cb7580575dad05da66209ca_79.png)

## 实践案例：计算非空闲CPU比例

![](img/6a8e22959cb7580575dad05da66209ca_81.png)

![](img/6a8e22959cb7580575dad05da66209ca_83.png)

为了加深理解，我们来看一个结合了 `sum()`、`scalar()` 和 `count()` 函数的实际案例：计算机器CPU的非空闲时间比例。

![](img/6a8e22959cb7580575dad05da66209ca_85.png)

![](img/6a8e22959cb7580575dad05da66209ca_86.png)

![](img/6a8e22959cb7580575dad05da66209ca_88.png)

![](img/6a8e22959cb7580575dad05da66209ca_90.png)

假设我们想计算过去5分钟内，所有机器CPU非空闲（即除`idle`状态外）的时间占比。我们可以使用以下查询：
```promql
sum(rate(node_cpu_seconds_total{mode!="idle"}[5m])) / scalar(count(node_cpu_seconds_total))
```
这个查询的含义是：
1.  `sum(rate(...))`：计算所有非空闲CPU模式在5分钟内的总使用率。
2.  `scalar(count(...))`：计算 `node_cpu_seconds_total` 指标的时间序列总数，并将其转换为标量，作为除数。
3.  两者相除，得到的结果（例如0.10）就表示CPU非空闲时间的比例。

![](img/6a8e22959cb7580575dad05da66209ca_91.png)

![](img/6a8e22959cb7580575dad05da66209ca_93.png)

![](img/6a8e22959cb7580575dad05da66209ca_95.png)

你也可以选择不使用 `scalar()`，通过 `by` 子句来分组计算，但使用 `scalar()` 通常使表达式更简洁直观。这个查询能够一次性计算出所有机器的整体CPU使用率。

![](img/6a8e22959cb7580575dad05da66209ca_96.png)

![](img/6a8e22959cb7580575dad05da66209ca_98.png)

![](img/6a8e22959cb7580575dad05da66209ca_99.png)

## 总结

![](img/6a8e22959cb7580575dad05da66209ca_101.png)

![](img/6a8e22959cb7580575dad05da66209ca_102.png)

本节课中我们一起学习了Prometheus函数的基础知识。我们了解了函数的主要返回值类型（标量和瞬时向量），并重点研究了两个核心函数：
1.  **`vector()`函数**：将标量转换为瞬时向量，用于确保查询返回结果。
2.  **`scalar()`函数**：将单样本瞬时向量转换为标量，便于计算但会丢失标签信息，需谨慎使用。

![](img/6a8e22959cb7580575dad05da66209ca_104.png)

![](img/6a8e22959cb7580575dad05da66209ca_106.png)

![](img/6a8e22959cb7580575dad05da66209ca_108.png)

最后，我们通过一个计算CPU使用率的实际案例，综合运用了所学函数。Prometheus拥有数十个函数，我们在此仅介绍了最常用的一部分，掌握这些核心函数是有效使用PromQL进行监控分析的关键。在后续课程中，我们将继续探索更多有用的函数。