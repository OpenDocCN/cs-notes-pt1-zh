# 005：二分查找 🔍

![](img/641da2c2a30cde79e2af58f13d637ad4_1.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_2.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_4.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_5.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_7.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_9.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_10.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_12.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_14.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_16.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_18.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_20.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_22.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_24.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_26.png)

在本节课中，我们将要学习一种基础且强大的算法——二分查找。我们将从最简单的在有序数组中查找元素开始，逐步深入到更复杂和通用的应用场景，例如查找边界值、解决“好/坏”判定问题，以及处理浮点数搜索。最后，我们还会简要介绍三分查找及其优化技巧。

![](img/641da2c2a30cde79e2af58f13d637ad4_28.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_30.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_32.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_34.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_36.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_37.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_39.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_41.png)

## 什么是二分查找？🤔

![](img/641da2c2a30cde79e2af58f13d637ad4_43.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_45.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_47.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_49.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_51.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_53.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_55.png)

二分查找是一种用于在**有序**列表中查找目标元素的基础技术。它的核心思想是通过不断将搜索范围对半分割，从而快速缩小查找空间。

![](img/641da2c2a30cde79e2af58f13d637ad4_57.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_58.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_60.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_62.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_64.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_66.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_68.png)

## 在有序数组中查找元素 📊

![](img/641da2c2a30cde79e2af58f13d637ad4_70.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_72.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_74.png)

我们首先解决一个最常见的问题：在一个已排序的整数数组中，查找一个特定的值 `x`。

![](img/641da2c2a30cde79e2af58f13d637ad4_76.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_78.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_80.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_82.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_83.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_85.png)

假设我们有一个排序数组 `a`，例如 `[1, 3, 5, 7, 9, 11, 13, 15, 17, 19, 21, 23, 25]`。我们的任务是找到数组中等于 `x` 的元素。

![](img/641da2c2a30cde79e2af58f13d637ad4_87.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_89.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_91.png)

### 基本思路

基本思路是维护一个可能包含目标元素 `x` 的数组段。我们使用两个指针 `L`（左）和 `R`（右）来标记这个段的边界，并始终保持一个性质：元素 `x` 一定在 `L` 和 `R` 之间的段内。

![](img/641da2c2a30cde79e2af58f13d637ad4_93.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_95.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_97.png)

初始时，我们可以设置 `L = 0`，`R = n - 1`（`n` 是数组长度）。然后，我们通过以下步骤不断缩小这个段的范围：

![](img/641da2c2a30cde79e2af58f13d637ad4_99.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_101.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_103.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_104.png)

1.  计算中间位置 `m = (L + R) / 2`。
2.  比较中间元素 `a[m]` 与目标值 `x`：
    *   如果 `a[m] < x`，由于数组有序，`x` 不可能在 `m` 及其左侧。因此，我们将 `L` 更新为 `m + 1`。
    *   如果 `a[m] > x`，由于数组有序，`x` 不可能在 `m` 及其右侧。因此，我们将 `R` 更新为 `m - 1`。
    *   如果 `a[m] == x`，那么我们找到了目标，直接返回 `m`。

![](img/641da2c2a30cde79e2af58f13d637ad4_106.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_108.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_110.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_112.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_114.png)

我们重复这个过程，直到 `L > R`（段为空）或找到目标。

![](img/641da2c2a30cde79e2af58f13d637ad4_116.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_118.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_120.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_122.png)

以下是该算法的伪代码描述：
```
L = 0
R = n - 1
while L <= R:
    m = (L + R) // 2
    if a[m] < x:
        L = m + 1
    elif a[m] > x:
        R = m - 1
    else:
        return m
return -1  // 未找到
```

![](img/641da2c2a30cde79e2af58f13d637ad4_124.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_126.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_128.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_130.png)

### 时间复杂度分析

![](img/641da2c2a30cde79e2af58f13d637ad4_132.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_134.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_136.png)

每次迭代，搜索范围的大小至少减半。因此，对于一个长度为 `n` 的数组，最多需要 **O(log n)** 次比较。这使得二分查找比线性查找（O(n)）高效得多。

![](img/641da2c2a30cde79e2af58f13d637ad4_138.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_140.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_142.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_144.png)

然而，上述实现方式在处理一些边界情况（如查找第一个/最后一个等于 `x` 的元素）时可能不够清晰。接下来，我们将学习一种更通用、更清晰的实现方法。

![](img/641da2c2a30cde79e2af58f13d637ad4_146.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_148.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_150.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_151.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_153.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_154.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_156.png)

## 更通用的二分查找实现 🛠️

![](img/641da2c2a30cde79e2af58f13d637ad4_158.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_160.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_162.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_163.png)

上一节我们介绍了基础的二分查找。本节中，我们来看看如何用一种更清晰、更通用的方式来实现它，以解决更复杂的问题，例如“查找第一个大于等于 `x` 的元素”。

![](img/641da2c2a30cde79e2af58f13d637ad4_165.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_166.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_168.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_169.png)

### 问题定义

![](img/641da2c2a30cde79e2af58f13d637ad4_171.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_173.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_175.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_177.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_179.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_181.png)

给定一个有序数组 `a` 和一个值 `x`，我们希望找到数组中**第一个**（最左边的）**大于或等于** `x` 的元素。如果所有元素都小于 `x`，则返回一个特殊值（例如数组长度 `n`）。

![](img/641da2c2a30cde79e2af58f13d637ad4_183.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_184.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_186.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_188.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_190.png)

### 新的思路：维护不变性

![](img/641da2c2a30cde79e2af58f13d637ad4_192.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_194.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_196.png)

我们依然使用两个指针 `L` 和 `R`，但这次我们维护一个不同的不变性（Invariant）：
*   `a[L] < x` （`L` 指向的元素**严格小于** `x`）
*   `a[R] >= x` （`R` 指向的元素**大于或等于** `x`）

![](img/641da2c2a30cde79e2af58f13d637ad4_198.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_200.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_202.png)

我们的目标是让 `L` 和 `R` 最终成为相邻的位置（`R = L + 1`）。此时，根据不变性，`a[L] < x` 且 `a[R] >= x`，那么 `R` 就是我们要找的第一个大于等于 `x` 的元素的位置。

![](img/641da2c2a30cde79e2af58f13d637ad4_204.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_206.png)

### 初始化与虚拟元素

![](img/641da2c2a30cde79e2af58f13d637ad4_208.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_210.png)

为了确保初始时不变性成立，我们需要小心地选择 `L` 和 `R` 的初始值。一个巧妙的技巧是引入两个**虚拟元素**：
*   在数组“前面”添加一个值为 `-∞` 的元素，其索引为 `-1`。
*   在数组“后面”添加一个值为 `+∞` 的元素，其索引为 `n`。

![](img/641da2c2a30cde79e2af58f13d637ad4_212.png)

这样，我们可以安全地初始化：
*   `L = -1` （指向虚拟的 `-∞`，保证 `a[L] < x`）
*   `R = n` （指向虚拟的 `+∞`，保证 `a[R] >= x`）

![](img/641da2c2a30cde79e2af58f13d637ad4_214.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_215.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_217.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_219.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_220.png)

在实际代码中，我们并不真的创建这些元素，只是想象它们存在。

![](img/641da2c2a30cde79e2af58f13d637ad4_222.png)

### 算法步骤

![](img/641da2c2a30cde79e2af58f13d637ad4_224.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_226.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_228.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_230.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_232.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_234.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_236.png)

算法循环的条件是 `R` 和 `L` 不相邻（即 `R > L + 1`）。在每次循环中：
1.  计算中间位置 `m = (L + R) // 2`。注意，由于 `L` 和 `R` 是虚拟索引，`m` 始终是有效的数组索引（`0 <= m < n`）。
2.  检查 `a[m]`：
    *   如果 `a[m] >= x`，那么 `m` 位置满足 `R` 指针的性质。我们将 `R` 移动到 `m`。
    *   如果 `a[m] < x`，那么 `m` 位置满足 `L` 指针的性质。我们将 `L` 移动到 `m`。

![](img/641da2c2a30cde79e2af58f13d637ad4_237.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_239.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_240.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_242.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_243.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_245.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_247.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_249.png)

循环结束后，`R` 就是答案。如果 `R == n`，说明所有元素都小于 `x`。

![](img/641da2c2a30cde79e2af58f13d637ad4_251.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_253.png)

以下是该算法的伪代码描述：
```
L = -1
R = n
while R > L + 1:
    m = (L + R) // 2
    if a[m] >= x:
        R = m
    else:
        L = m
return R  // R 是第一个 >= x 的元素索引，若 R==n 则表示未找到
```

![](img/641da2c2a30cde79e2af58f13d637ad4_255.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_257.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_258.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_260.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_261.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_263.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_265.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_267.png)

### 变体：查找最后一个小于等于 x 的元素

![](img/641da2c2a30cde79e2af58f13d637ad4_269.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_271.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_272.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_273.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_275.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_276.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_278.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_279.png)

只需稍作修改，我们就可以解决对称的问题：查找**最后一个**（最右边的）**小于或等于** `x` 的元素。

![](img/641da2c2a30cde79e2af58f13d637ad4_281.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_283.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_285.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_287.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_289.png)

此时，我们维护的不变性变为：
*   `a[L] <= x` （`L` 指向的元素**小于或等于** `x`）
*   `a[R] > x` （`R` 指向的元素**严格大于** `x`）

![](img/641da2c2a30cde79e2af58f13d637ad4_291.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_293.png)

初始化同样为 `L = -1`, `R = n`。循环中的判断逻辑改为：
*   如果 `a[m] > x`，则 `R = m`
*   否则（`a[m] <= x`），则 `L = m`

循环结束后，`L` 就是答案。如果 `L == -1`，说明所有元素都大于 `x`。

这种“维护不变性”的二分查找框架非常清晰，只需根据具体问题定义好 `L` 和 `R` 指针所代表的性质，以及中间点 `m` 的判断逻辑即可。

## 二分查找的典型应用：判定问题 ✅❌

上一节我们学习了通用的二分查找框架。本节中，我们来看看二分查找最经典的一类应用场景：解决具有**单调性**的判定问题。

### 问题模型

我们面对的问题通常具有以下形式：
存在一个定义在整数（或实数）上的判定函数 `good(x)`，它对于输入值 `x` 返回 `true`（好）或 `false`（坏）。
并且，这个函数具有**单调性**：如果 `x` 是好的，那么所有比 `x` 大的值也是好的（或者反过来，如果 `x` 是坏的，那么所有比 `x` 小的值也是坏的）。

我们的任务是找到满足 `good(x)` 为 `true` 的**最小**（或最大）的 `x`。

### 图形化理解

我们可以将数轴上的点分为“好”和“坏”两部分。由于单调性，数轴上存在一个分界点：该点左侧全是坏点，右侧全是好点（或者相反）。我们的目标就是找到这个分界点。

```
坏点 | 坏点 | ... | 坏点 | 分界点 | 好点 | 好点 | ... | 好点
```

![](img/641da2c2a30cde79e2af58f13d637ad4_295.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_297.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_299.png)

这正好与我们之前“查找第一个好点”的二分查找框架吻合！

### 实例：最小正方形覆盖

![](img/641da2c2a30cde79e2af58f13d637ad4_301.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_303.png)

**问题**：我们有 `n` 个尺寸为 `w_i × h_i` 的矩形。我们需要找到一个最小的正方形（边长为整数 `x`），使得所有矩形都能不重叠地放入这个正方形中（假设可以任意旋转或排列，但这里简化为例题）。

**分析**：
1.  **定义判定函数**：`good(x)` = 边长为 `x` 的正方形能否放下所有 `n` 个矩形。
2.  **验证单调性**：如果一个边长为 `x` 的正方形能放下所有矩形（`good(x) = true`），那么边长为 `x+1, x+2, ...` 的更大正方形也一定能放下。因此，函数 `good(x)` 是单调的（从某个点开始由 `false` 变为 `true`）。
3.  **应用二分查找**：我们需要找到最小的 `x` 使得 `good(x)` 为 `true`。这正是“查找第一个好点”的问题。
    *   初始化：`L` 指向一个已知的坏点（例如 `L=0`，边长为0的正方形肯定放不下任何东西）。`R` 指向一个已知的好点。如何找到一个初始的好点？可以采用“指数搜索”：从 `R=1` 开始，只要 `good(R)` 为 `false`，就将 `R` 乘以2（例如 `R=1,2,4,8,...`），直到找到一个好点。这样可以避免初始 `R` 过大导致计算溢出。
    *   然后使用标准的二分查找框架缩小 `[L, R]` 范围，直到找到分界点 `R`。

**实现 `good(x)` 函数**：这是问题的核心。一个简单的策略是计算在 `x×x` 的正方形中，按网格排列最多能放下多少个给定矩形。如果数量 `>= n`，则 `good(x)=true`。更精确的判定可能需要复杂的几何包装算法，但思路不变。

### 实例：人员集合的最短时间

**问题**：`n` 个人站在一条直线上，第 `i` 个人在位置 `p_i`，最大移动速度为 `v_i`。他们想移动到同一点集合。求所有人集合所需的最短时间 `T`。

**分析**：
1.  **定义判定函数**：`good(t)` = 是否能在 `t` 秒内让所有人移动到同一点。
2.  **验证单调性**：如果时间 `t` 足够（`good(t)=true`），那么给更多时间 `t’ > t`，他们肯定也能集合（可以先花 `t` 秒移动到目标点，然后原地等待）。函数单调。
3.  **应用二分查找**：找到最小的 `t` 使得 `good(t)=true`。
4.  **实现 `good(t)` 函数**：对于给定的时间 `t`，第 `i` 个人可以移动到的位置范围是 `[p_i - v_i*t, p_i + v_i*t]`。所有人要集合到同一点，意味着存在一个点同时位于所有人的可达区间内。这等价于所有区间的交集非空。计算所有区间左端点的最大值 `L_max` 和右端点的最小值 `R_min`。如果 `L_max <= R_min`，则交集非空，`good(t)=true`；否则为 `false`。

![](img/641da2c2a30cde79e2af58f13d637ad4_305.png)

通过这两个例子可以看出，二分查找将**优化问题**（求最小/最大值）转化为了**判定问题**（判断某个值是否可行）。我们只需要专注于实现高效的 `good(x)` 函数，剩下的搜索工作就交给二分查找的通用框架。

## 浮点数二分与三分查找 📐

上一节我们讨论了基于判定问题的整数二分查找。本节中，我们来看看当搜索空间是连续值（浮点数）时该如何处理，并介绍一种相关的算法——三分查找。

### 浮点数二分查找

![](img/641da2c2a30cde79e2af58f13d637ad4_307.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_309.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_311.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_313.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_314.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_316.png)

当答案 `x` 是实数时，二分查找的基本逻辑不变。我们仍然维护一个包含答案的区间 `[L, R]`，并通过判断中间点 `m` 的性质来缩小区间。

![](img/641da2c2a30cde79e2af58f13d637ad4_318.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_320.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_322.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_324.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_326.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_327.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_329.png)

**关键变化与挑战**：
1.  **循环终止条件**：我们不能再用 `L <= R` 或 `R > L + 1` 作为整数比较。因为浮点数有精度限制，可能永远无法完全相等。
2.  **常用方法**：
    *   **固定迭代次数**：进行固定次数的二分迭代（例如100次）。每次迭代将区间长度减半，100次后区间长度变为初始的 `1/2^100`，这通常能达到极高的精度，且能避免无限循环和精度判断的麻烦。这是最安全、最常用的方法。
    *   **设定精度阈值**：当区间长度 `R - L` 小于某个预设的精度 `epsilon`（例如 `1e-9`）时退出循环。但需要注意，当答案本身很大时，相对精度可能仍然不足。

![](img/641da2c2a30cde79e2af58f13d637ad4_331.png)

**浮点数二分查找框架（固定迭代法）**：
```
L = 初始左边界
R = 初始右边界
for i in range(100):  # 例如迭代100次
    m = (L + R) / 2.0
    if good(m):  # 根据问题定义判断 m 是否“偏大”或“偏小”
        R = m    # 如果 m 是“好”的（或满足某种条件），答案在左半部分
    else:
        L = m    # 否则答案在右半部分
# 循环结束后，答案在 [L, R] 内，通常取 (L+R)/2 或 R 作为近似解
```

![](img/641da2c2a30cde79e2af58f13d637ad4_333.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_335.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_337.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_339.png)

### 三分查找

![](img/641da2c2a30cde79e2af58f13d637ad4_341.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_343.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_345.png)

三分查找用于在**单峰函数**（Unimodal Function）上寻找极值（最大值或最小值）。单峰函数指的是先严格单调递增（或递减），到达一个峰值（或谷值）后，再严格单调递减（或递增）的函数。

![](img/641da2c2a30cde79e2af58f13d637ad4_347.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_349.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_351.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_353.png)

**问题模型**：有一个函数 `f(x)`，在定义域 `[L, R]` 上是单峰的。我们想找到其极大值点（对于先增后减的函数）或极小值点（对于先减后增的函数）。我们只能查询函数在某点的值 `f(x)`，而无法直接求导。

![](img/641da2c2a30cde79e2af58f13d637ad4_355.png)

**算法思路（求极大值）**：
1.  在区间 `[L, R]` 内取两个点 `m1` 和 `m2`，且 `L < m1 < m2 < R`。
2.  比较 `f(m1)` 和 `f(m2)`：
    *   如果 `f(m1) < f(m2)`，那么极大值点不可能在 `m1` 左侧。因为如果极大值点在 `m1` 左边，由于函数先增后减，`m1` 到 `m2` 应该是递减的，与 `f(m1) < f(m2)` 矛盾。因此，我们可以将搜索范围缩小到 `[m1, R]`。
    *   如果 `f(m1) > f(m2)`，同理，极大值点不可能在 `m2` 右侧。搜索范围缩小到 `[L, m2]`。
    *   如果 `f(m1) == f(m2)`，那么极大值点一定在 `[m1, m2]` 之间。搜索范围缩小到 `[m1, m2]`。
3.  重复上述步骤，直到区间足够小。

![](img/641da2c2a30cde79e2af58f13d637ad4_357.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_359.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_361.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_363.png)

**点的选取**：为了每次迭代能均等地缩小区间，通常取 `m1` 和 `m2` 为区间的三等分点：`m1 = L + (R-L)/3`, `m2 = R - (R-L)/3`。

![](img/641da2c2a30cde79e2af58f13d637ad4_365.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_367.png)

**时间复杂度**：每次迭代去掉原区间长度的 1/3，复杂度也是 **O(log n)**，但常数比二分查找大。

![](img/641da2c2a30cde79e2af58f13d637ad4_369.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_371.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_373.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_375.png)

**优化技巧（黄金分割法）**：在三分查找中，每次需要计算两个点的函数值。通过精心选择 `m1` 和 `m2` 的比例（例如使用黄金分割比 ≈ 0.618），可以使得在缩小区间后，其中一个点恰好是新区间的三等分点之一，从而在下次迭代中**复用**这个点的函数值，节省一次计算。这可以将效率提升近一倍。

![](img/641da2c2a30cde79e2af58f13d637ad4_377.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_379.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_381.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_382.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_384.png)

## 总结 📝

![](img/641da2c2a30cde79e2af58f13d637ad4_386.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_388.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_389.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_391.png)

本节课中我们一起学习了二分查找算法及其广泛应用。

![](img/641da2c2a30cde79e2af58f13d637ad4_393.png)

![](img/641da2c2a30cde79e2af58f13d637ad4_395.png)

1.  **核心思想**：通过不断将有序搜索空间对半分割，以对数时间复杂度快速定位目标。
2.  **基础应用**：在有序数组中查找特定元素。
3.  **通用框架**：通过维护指针 `L` 和 `R` 的某种“不变性”，可以清晰、统一地实现查找边界值（如第一个大于等于 `x` 的元素）的算法。
4.  **核心应用模式**：将求最优解（最小化/最大化）问题转化为对候选解 `x` 的**单调判定问题** `good(x)`。二分查找用于快速找到满足 `good(x)` 为真的边界点。
5.  **浮点数处理**：采用固定迭代次数的二分法，安全且高效。
6.  **扩展算法**：三分查找用于求解单峰函数的极值问题。

掌握二分查找的关键在于准确识别问题的单调性，并正确实现判定函数。这种“转化”思维是解决许多算法问题的有力工具。