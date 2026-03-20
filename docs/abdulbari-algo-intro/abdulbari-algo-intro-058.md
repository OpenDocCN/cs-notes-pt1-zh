# 058：旅行商问题 - 动态规划公式法 🧳

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_1.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_3.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_4.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_6.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_8.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_10.png)

在本节课中，我们将学习如何使用动态规划公式来解决旅行商问题。我们将通过一个具体的例子，逐步解析公式的含义和应用过程，最终计算出访问所有城市并返回起点的最短路径成本。

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_12.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_14.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_16.png)

旅行商问题描述如下：给定一个有向加权图，我们需要从某个顶点出发，访问所有其他顶点恰好一次，最后返回起点，并且要求总旅行成本最小。

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_18.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_20.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_22.png)

## 问题定义与公式引入

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_24.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_25.png)

上一节我们介绍了问题的基本描述。本节中，我们来看看解决该问题的核心动态规划公式。

对于一个有n个顶点的图，设起点为1。定义函数 **`g(i, S)`** 表示从顶点 `i` 出发，访问集合 `S` 中所有顶点恰好一次，最后回到起点1的最小成本。其中 `S` 是尚未访问的顶点集合。

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_27.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_29.png)

核心递推公式如下：

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_31.png)

**`g(i, S) = min{ C[i][j] + g(j, S - {j}) }`**，其中 `j` 属于集合 `S`。

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_33.png)

这个公式的含义是：从当前顶点 `i` 出发，要访问集合 `S` 中的所有顶点。我们可以选择先走到 `S` 中的任意一个顶点 `j`，成本是 `C[i][j]`，然后问题就转化为从 `j` 出发，访问剩余顶点 `S - {j}` 的子问题。

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_34.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_35.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_36.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_37.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_38.png)

## 公式应用与递归树展开

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_40.png)

理解了公式后，我们将其应用到一个具体的4顶点图例上。该图的成本邻接矩阵如下：

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_42.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_43.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_45.png)

|       | 1   | 2   | 3   | 4   |
| :---- | :-- | :-- | :-- | :-- |
| **1** | 0   | 10  | 15  | 20  |
| **2** | 5   | 0   | 9   | 10  |
| **3** | 6   | 13  | 0   | 12  |
| **4** | 8   | 8   | 9   | 0   |

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_47.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_49.png)

我们的目标是计算 **`g(1, {2,3,4})`**，即从顶点1出发，访问顶点2、3、4后回到1的最小成本。

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_51.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_52.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_53.png)

根据公式，我们展开第一层递归：

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_55.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_56.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_57.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_59.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_61.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_63.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_64.png)

**`g(1, {2,3,4}) = min { C[1][2] + g(2, {3,4}), C[1][3] + g(3, {2,4}), C[1][4] + g(4, {2,3}) }`**

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_66.png)

代入已知的边成本：`C[1][2]=10`, `C[1][3]=15`, `C[1][4]=20`。公式变为：

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_67.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_68.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_70.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_72.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_74.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_76.png)

**`g(1, {2,3,4}) = min { 10 + g(2, {3,4}), 15 + g(3, {2,4}), 20 + g(4, {2,3}) }`**

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_78.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_80.png)

为了求出最小值，我们需要继续计算 `g(2, {3,4})`、`g(3, {2,4})` 和 `g(4, {2,3})` 的值。这构成了一个递归树。

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_82.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_83.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_85.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_86.png)

## 自底向上的动态规划表解法

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_88.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_90.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_92.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_93.png)

理论上，我们可以通过递归树求解，但这样会产生大量重复计算，效率低下。动态规划的精髓在于自底向上填表，避免重复。

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_95.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_97.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_98.png)

我们从小规模子问题开始计算，逐步构建出大规模问题的解。以下是计算步骤：

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_100.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_101.png)

**步骤1：计算基础情况**
当集合 `S` 为空时，意味着所有顶点都已访问，只需从当前顶点 `i` 直接返回起点1。
以下是基础情况的值：
*   `g(2, {}) = C[2][1] = 5`
*   `g(3, {}) = C[3][1] = 6`
*   `g(4, {}) = C[4][1] = 8`

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_103.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_105.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_107.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_109.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_111.png)

**步骤2：计算集合 `S` 包含1个顶点的情况**
现在计算剩余一个顶点需要访问时的最小成本。
以下是计算公式和结果：
*   `g(2, {3}) = C[2][3] + g(3, {}) = 9 + 6 = 15`
*   `g(2, {4}) = C[2][4] + g(4, {}) = 10 + 8 = 18`
*   `g(3, {2}) = C[3][2] + g(2, {}) = 13 + 5 = 18`
*   `g(3, {4}) = C[3][4] + g(4, {}) = 12 + 8 = 20`
*   `g(4, {2}) = C[4][2] + g(2, {}) = 8 + 5 = 13`
*   `g(4, {3}) = C[4][3] + g(3, {}) = 9 + 6 = 15`

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_113.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_115.png)

**步骤3：计算集合 `S` 包含2个顶点的情况**
接着计算剩余两个顶点需要访问时的最小成本。
以下是计算公式和结果：
*   `g(2, {3,4}) = min { C[2][3] + g(3,{4}), C[2][4] + g(4,{3}) } = min { 9+20, 10+15 } = min {29, 25} = 25`
*   `g(3, {2,4}) = min { C[3][2] + g(2,{4}), C[3][4] + g(4,{2}) } = min { 13+18, 12+13 } = min {31, 25} = 25`
*   `g(4, {2,3}) = min { C[4][2] + g(2,{3}), C[4][3] + g(3,{2}) } = min { 8+15, 9+18 } = min {23, 27} = 23`

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_117.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_119.png)

**步骤4：计算最终答案**
最后，我们回到最初的问题，计算从顶点1出发访问所有顶点的最小成本。
`g(1, {2,3,4}) = min { 10 + g(2,{3,4}), 15 + g(3,{2,4}), 20 + g(4,{2,3}) } = min { 10+25, 15+25, 20+23 } = min {35, 40, 43} = 35`

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_121.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_122.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_124.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_126.png)

## 总结

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_128.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_129.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_131.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_132.png)

![](img/2829678f34a2ebe97a4ddfe93bfd10ba_134.png)

本节课中我们一起学习了如何使用动态规划公式解决旅行商问题。我们首先理解了定义状态 `g(i, S)` 的核心递推公式，然后通过一个4顶点的具体例子，演示了如何自底向上地填充动态规划表，从最简单的基础情况（集合S为空）开始，逐步计算出包含更多顶点的子问题解，最终得到了从起点出发访问所有顶点的最小成本为 **35**。这种方法避免了递归带来的重复计算，是解决此类组合优化问题的有效策略。