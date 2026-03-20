# 027：分治函数的主定理（Master Theorem）🧮

![](img/dcb4c03698c840e2157af2f5f82de1e9_1.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_3.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_4.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_6.png)

在本节课中，我们将学习用于分析分治算法时间复杂度的核心工具——主定理。我们将通过一系列简单的例子，理解其三种主要情况及其应用。

![](img/dcb4c03698c840e2157af2f5f82de1e9_8.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_10.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_12.png)

## 概述

![](img/dcb4c03698c840e2157af2f5f82de1e9_14.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_16.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_18.png)

主定理提供了一种快速求解特定形式递归关系渐近解的方法。这种递归关系通常出现在分治算法中，其一般形式为：

![](img/dcb4c03698c840e2157af2f5f82de1e9_20.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_22.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_24.png)

**T(n) = a T(n/b) + f(n)**

![](img/dcb4c03698c840e2157af2f5f82de1e9_26.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_28.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_30.png)

其中：
*   **a ≥ 1**，表示子问题的数量。
*   **b > 1**，表示问题规模缩小的因子。
*   **f(n)** 是将问题分解和合并子问题解所花费的时间。

![](img/dcb4c03698c840e2157af2f5f82de1e9_32.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_33.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_34.png)

我们假设 **f(n)** 具有以下形式：

![](img/dcb4c03698c840e2157af2f5f82de1e9_36.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_38.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_40.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_42.png)

**f(n) = Θ( n^k * (log n)^p )**

![](img/dcb4c03698c840e2157af2f5f82de1e9_44.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_45.png)

接下来，我们需要计算两个关键值：
1.  **log_b(a)**：以 b 为底 a 的对数。
2.  **k**：函数 f(n) 中 n 的幂次。

![](img/dcb4c03698c840e2157af2f5f82de1e9_47.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_49.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_51.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_52.png)

通过比较这两个值，我们可以将递归式的解归入以下三种情况之一。

![](img/dcb4c03698c840e2157af2f5f82de1e9_54.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_56.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_58.png)

## 主定理的三种情况

![](img/dcb4c03698c840e2157af2f5f82de1e9_60.png)

以下是主定理的三种核心情况及其判定条件。

![](img/dcb4c03698c840e2157af2f5f82de1e9_62.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_64.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_66.png)

### 情况一：递归工作量占主导

![](img/dcb4c03698c840e2157af2f5f82de1e9_68.png)

如果 **log_b(a) > k**，那么递归树中叶子层的代价占主导。此时，递归式的解为：

![](img/dcb4c03698c840e2157af2f5f82de1e9_70.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_71.png)

**T(n) = Θ( n^{log_b(a)} )**

![](img/dcb4c03698c840e2157af2f5f82de1e9_73.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_74.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_76.png)

### 情况二：各层工作量平衡

![](img/dcb4c03698c840e2157af2f5f82de1e9_78.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_80.png)

如果 **log_b(a) = k**，那么递归树每一层的工作量大致相同。此时，解的形式取决于对数因子 p 的值。以下是三个子情况：

*   **子情况 2.1**：如果 **p > -1**，则解为 **T(n) = Θ( n^k * (log n)^{p+1} )**。
*   **子情况 2.2**：如果 **p = -1**，则解为 **T(n) = Θ( n^k * log(log n) )**。
*   **子情况 2.3**：如果 **p < -1**，则解为 **T(n) = Θ( n^k )**。

![](img/dcb4c03698c840e2157af2f5f82de1e9_82.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_84.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_85.png)

### 情况三：合并工作量占主导

![](img/dcb4c03698c840e2157af2f5f82de1e9_87.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_89.png)

如果 **log_b(a) < k**，那么递归树根节点合并子问题的工作量占主导。此时，解的形式也取决于 p 的值：

*   如果 **p ≥ 0**，则解为 **T(n) = Θ( f(n) ) = Θ( n^k * (log n)^p )**。
*   如果 **p < 0**，则解为 **T(n) = Θ( n^k )**，忽略对数项。

![](img/dcb4c03698c840e2157af2f5f82de1e9_91.png)

## 应用示例

![](img/dcb4c03698c840e2157af2f5f82de1e9_93.png)

上一节我们介绍了主定理的三种情况，本节中我们通过具体例子来看看如何应用。

![](img/dcb4c03698c840e2157af2f5f82de1e9_95.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_96.png)

### 情况一示例

![](img/dcb4c03698c840e2157af2f5f82de1e9_98.png)

以下是满足情况一条件的几个递归式。

![](img/dcb4c03698c840e2157af2f5f82de1e9_100.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_101.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_102.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_103.png)

*   **例1**：T(n) = 2T(n/2) + 1
    *   a=2, b=2, f(n)=1 => k=0, p=0。
    *   log_2(2) = 1 > 0 (k)。
    *   解：**T(n) = Θ(n^1) = Θ(n)**。

![](img/dcb4c03698c840e2157af2f5f82de1e9_105.png)

*   **例2**：T(n) = 4T(n/2) + n
    *   a=4, b=2, f(n)=n => k=1, p=0。
    *   log_2(4) = 2 > 1 (k)。
    *   解：**T(n) = Θ(n^2)**。

![](img/dcb4c03698c840e2157af2f5f82de1e9_107.png)

*   **例3**：T(n) = 8T(n/2) + n^2
    *   a=8, b=2, f(n)=n^2 => k=2, p=0。
    *   log_2(8) = 3 > 2 (k)。
    *   解：**T(n) = Θ(n^3)**。

![](img/dcb4c03698c840e2157af2f5f82de1e9_109.png)

### 情况二示例

![](img/dcb4c03698c840e2157af2f5f82de1e9_111.png)

现在，我们来看满足情况二条件的例子，即 log_b(a) 等于 k。

![](img/dcb4c03698c840e2157af2f5f82de1e9_113.png)

*   **例1**：T(n) = 2T(n/2) + n
    *   a=2, b=2, f(n)=n => k=1, p=0。
    *   log_2(2) = 1 = 1 (k)，且 p=0 > -1。
    *   解：**T(n) = Θ(n^1 * (log n)^{0+1}) = Θ(n log n)**。

![](img/dcb4c03698c840e2157af2f5f82de1e9_115.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_117.png)

*   **例2**：T(n) = 4T(n/2) + n^2
    *   a=4, b=2, f(n)=n^2 => k=2, p=0。
    *   log_2(4) = 2 = 2 (k)，且 p=0 > -1。
    *   解：**T(n) = Θ(n^2 * (log n)^{0+1}) = Θ(n^2 log n)**。

*   **例3**：T(n) = 2T(n/2) + n / log n
    *   a=2, b=2, f(n)=n / log n => 可视为 k=1, p=-1。
    *   log_2(2) = 1 = 1 (k)，且 p = -1。
    *   解：**T(n) = Θ(n^1 * log(log n)) = Θ(n log(log n))**。

![](img/dcb4c03698c840e2157af2f5f82de1e9_119.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_120.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_121.png)

### 情况三示例

![](img/dcb4c03698c840e2157af2f5f82de1e9_123.png)

最后，我们看看情况三的例子，即函数 f(n) 的增长速度超过了递归部分。

![](img/dcb4c03698c840e2157af2f5f82de1e9_125.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_126.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_127.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_129.png)

*   **例1**：T(n) = T(n/2) + n^2
    *   a=1, b=2, f(n)=n^2 => k=2, p=0。
    *   log_2(1) = 0 < 2 (k)，且 p=0 ≥ 0。
    *   解：**T(n) = Θ(f(n)) = Θ(n^2)**。

![](img/dcb4c03698c840e2157af2f5f82de1e9_131.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_133.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_135.png)

*   **例2**：T(n) = 2T(n/2) + n^3
    *   a=2, b=2, f(n)=n^3 => k=3, p=0。
    *   log_2(2) = 1 < 3 (k)，且 p=0 ≥ 0。
    *   解：**T(n) = Θ(n^3)**。

![](img/dcb4c03698c840e2157af2f5f82de1e9_137.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_139.png)

*   **例3**：T(n) = 4T(n/2) + n^3 / log n
    *   a=4, b=2, f(n)=n^3 / log n => 可视为 k=3, p=-1。
    *   log_2(4) = 2 < 3 (k)，且 p=-1 < 0。
    *   解：**T(n) = Θ(n^3)**（忽略分母的 log n 项）。

![](img/dcb4c03698c840e2157af2f5f82de1e9_141.png)

![](img/dcb4c03698c840e2157af2f5f82de1e9_143.png)

## 总结

本节课中，我们一起学习了用于求解分治算法递归式的主定理。我们首先介绍了其一般形式 **T(n) = a T(n/b) + f(n)**，并定义了关键参数 **log_b(a)** 和 **k**。然后，我们详细讲解了三种核心情况：
1.  当 **log_b(a) > k** 时，解由递归部分主导。
2.  当 **log_b(a) = k** 时，解需要额外考虑对数因子 p。
3.  当 **log_b(a) < k** 时，解由合并函数 f(n) 主导。

![](img/dcb4c03698c840e2157af2f5f82de1e9_145.png)

通过多个简单的示例，我们实践了如何根据递归式的系数快速判断其所属情况并得出时间复杂度。掌握主定理能极大地简化对许多经典分治算法（如归并排序、快速排序、二分查找）的时间复杂度分析。