# 018：分治策略 🧩

在本节课中，我们将要学习算法设计中的一个核心策略——分治法。我们将了解它的基本思想、适用条件以及通用步骤。

## 概述

![](img/f1beacab9bd12c8f2d11b482defbaec5_1.png)

分治法是解决计算问题的一种策略。它通过将一个大问题分解为多个相同类型的小问题，分别解决这些小问题，然后将它们的解合并起来，从而得到原问题的解。这种方法本质上是递归的。

![](img/f1beacab9bd12c8f2d11b482defbaec5_3.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_5.png)

## 什么是分治策略？

![](img/f1beacab9bd12c8f2d11b482defbaec5_7.png)

策略是解决问题的一种方法或设计。对于任何计算问题，如果该策略适合，你就可以采用它来解决问题。通过实践，你可以了解哪些问题适用分治法，虽然没有绝对的公式，但存在一些指导原则。

![](img/f1beacab9bd12c8f2d11b482defbaec5_9.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_10.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_11.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_12.png)

## 分治法的核心思想

![](img/f1beacab9bd12c8f2d11b482defbaec5_14.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_15.png)

如果给定一个规模为 `n` 的问题 `P`，并且这个问题规模很大，那么你可以将其分解为 `K` 个更小的子问题：`P1`, `P2`, ..., `Pk`。

![](img/f1beacab9bd12c8f2d11b482defbaec5_17.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_18.png)

```
原问题 P (规模 n)
        ↓ (分解)
子问题 P1, P2, ..., Pk (规模更小)
```

![](img/f1beacab9bd12c8f2d11b482defbaec5_20.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_21.png)

这些子问题被分别解决，得到各自的解：`S1`, `S2`, ..., `Sk`。

```
子问题 P1, P2, ..., Pk
        ↓ (分别求解)
解 S1, S2, ..., Sk
```

![](img/f1beacab9bd12c8f2d11b482defbaec5_23.png)

最后，将这些子问题的解合并，得到原问题 `P` 的解 `S`。

![](img/f1beacab9bd12c8f2d11b482defbaec5_25.png)

```
解 S1, S2, ..., Sk
        ↓ (合并)
原问题的解 S
```

![](img/f1beacab9bd12c8f2d11b482defbaec5_27.png)

所以，分治法的核心流程是：**分解 -> 解决 -> 合并**。如果一个子问题本身也很大，可以递归地对其再次应用分治法。

![](img/f1beacab9bd12c8f2d11b482defbaec5_29.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_31.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_33.png)

## 分治法的重要特征

![](img/f1beacab9bd12c8f2d11b482defbaec5_35.png)

关于分治法，有两个至关重要的特征：

1.  **子问题与原问题同质**：分解出的子问题必须与原问题是同一类型的问题。例如，如果原问题是排序，那么子问题也必须是排序问题。它不能转换成其他类型的问题。
2.  **必须能合并解**：必须存在一种方法，能够将子问题的解有效地合并成原问题的解。如果无法合并，则不能采用此策略。

![](img/f1beacab9bd12c8f2d11b482defbaec5_37.png)

## 分治法的通用步骤

![](img/f1beacab9bd12c8f2d11b482defbaec5_38.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_39.png)

以下是分治策略的通用方法描述，可以用伪代码表示：

![](img/f1beacab9bd12c8f2d11b482defbaec5_41.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_42.png)

```pseudocode
函数 DivideAndConquer(P):
    如果 问题P的规模足够小:
        则 直接求解P并返回结果
    否则:
        1. 将问题P分解为子问题 P1, P2, ..., Pk
        2. 对于每个子问题 Pi:
            调用 DivideAndConquer(Pi) 递归求解
        3. 将子问题的解合并为原问题P的解
        返回合并后的解
```

这个过程清晰地展示了其递归属性，因此分治算法通常是递归实现的。

![](img/f1beacab9bd12c8f2d11b482defbaec5_44.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_45.png)

## 适用分治法的问题示例

![](img/f1beacab9bd12c8f2d11b482defbaec5_47.png)

上一节我们介绍了分治法的通用框架，本节中我们来看看哪些经典算法问题适用于这种策略。

![](img/f1beacab9bd12c8f2d11b482defbaec5_49.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_51.png)

以下是部分可以使用分治法解决的经典问题：
*   二分查找
*   寻找最大值与最小值
*   归并排序
*   快速排序
*   斯特拉森矩阵乘法

![](img/f1beacab9bd12c8f2d11b482defbaec5_53.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_55.png)

我们将在后续的不同视频中详细探讨这些问题。

![](img/f1beacab9bd12c8f2d11b482defbaec5_56.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_58.png)

## 递归与递推关系

![](img/f1beacab9bd12c8f2d11b482defbaec5_60.png)

由于分治策略本质上是递归的，我们需要知道如何编写递归算法、递归函数，以及如何分析它们的时间复杂度。为此，我们使用**递推关系**。

![](img/f1beacab9bd12c8f2d11b482defbaec5_62.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_63.png)

在接下来的视频中，我们将深入讲解递推关系的解法，展示各种类型的递推式及其求解方法，以便你能全面掌握分析递归式复杂度的技巧。

## 总结

![](img/f1beacab9bd12c8f2d11b482defbaec5_65.png)

![](img/f1beacab9bd12c8f2d11b482defbaec5_66.png)

本节课中我们一起学习了分治策略。我们了解到，分治法通过“分解、解决、合并”的步骤处理大规模问题，其关键在于子问题必须与原问题同质，且必须能合并子问题的解。这是一种强大的、递归的算法设计范式，为许多高效算法（如排序和查找）奠定了基础。