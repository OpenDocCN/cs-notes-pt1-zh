# 051：矩阵链乘法问题 🧮

![](img/82f0a604397bb1d4fed8f2ace87dd093_1.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_3.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_4.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_6.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_8.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_9.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_10.png)

在本节课中，我们将学习如何使用动态规划解决**矩阵链乘法**问题。我们将从矩阵乘法的基本规则开始，逐步推导出动态规划公式，并通过一个完整的例子演示如何应用该公式来找到最优的括号化方案，以最小化乘法运算的总次数。

![](img/82f0a604397bb1d4fed8f2ace87dd093_12.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_13.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_15.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_17.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_19.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_20.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_22.png)

---

![](img/82f0a604397bb1d4fed8f2ace87dd093_24.png)

## 矩阵乘法基础 📐

![](img/82f0a604397bb1d4fed8f2ace87dd093_26.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_27.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_28.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_30.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_31.png)

首先，我们需要理解两个矩阵相乘的基本规则。

![](img/82f0a604397bb1d4fed8f2ace87dd093_33.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_34.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_35.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_37.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_38.png)

两个矩阵 **A** 和 **B** 可以相乘的条件是：**A** 的列数必须等于 **B** 的行数。

![](img/82f0a604397bb1d4fed8f2ace87dd093_40.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_42.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_43.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_45.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_47.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_48.png)

假设矩阵 **A** 的维度是 `p × q`，矩阵 **B** 的维度是 `q × r`。那么：
*   它们可以相乘。
*   结果矩阵 **C** 的维度将是 `p × r`。
*   计算 **C** 所需的标量乘法总数为 `p × q × r`。

![](img/82f0a604397bb1d4fed8f2ace87dd093_50.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_51.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_52.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_54.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_55.png)

例如，一个 `2×3` 的矩阵与一个 `3×2` 的矩阵相乘：
*   条件满足（3 = 3）。
*   结果矩阵维度为 `2×2`。
*   总乘法次数为 `2 × 3 × 2 = 12`。

![](img/82f0a604397bb1d4fed8f2ace87dd093_57.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_58.png)

对于两个矩阵，我们通常用三个不同的维度来描述：`p`、`q`、`r`。其中 `q` 是共用的维度。

![](img/82f0a604397bb1d4fed8f2ace87dd093_60.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_61.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_62.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_63.png)

---

![](img/82f0a604397bb1d4fed8f2ace87dd093_65.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_66.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_67.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_68.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_69.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_70.png)

## 什么是矩阵链乘法问题？⛓️

![](img/82f0a604397bb1d4fed8f2ace87dd093_72.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_74.png)

上一节我们介绍了两个矩阵的乘法。本节中我们来看看当有多个矩阵（形成一个链）需要相乘时，会出现什么问题。

![](img/82f0a604397bb1d4fed8f2ace87dd093_76.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_77.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_79.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_80.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_81.png)

矩阵乘法满足结合律，这意味着对于链 `A1 × A2 × A3 × ... × An`，我们可以通过不同的方式添加括号来改变乘法的顺序。虽然最终的计算结果相同，但不同的计算顺序（括号化方案）会导致所需的标量乘法总次数截然不同。

![](img/82f0a604397bb1d4fed8f2ace87dd093_83.png)

**矩阵链乘法问题**的目标是：给定一个矩阵链（每个矩阵的维度已知），确定一个最优的括号化方案，使得计算该链乘积所需的总标量乘法次数最少。

![](img/82f0a604397bb1d4fed8f2ace87dd093_85.png)

考虑三个矩阵 `A1(2×3)`, `A2(3×4)`, `A3(4×2)`：
*   方案一：`(A1 × A2) × A3`
    *   计算 `A1 × A2`：`2×3×4 = 24` 次乘法，得到 `(2×4)` 的中间矩阵。
    *   计算中间矩阵 `× A3`：`2×4×2 = 16` 次乘法。
    *   **总次数：24 + 16 = 40**
*   方案二：`A1 × (A2 × A3)`
    *   计算 `A2 × A3`：`3×4×2 = 24` 次乘法，得到 `(3×2)` 的中间矩阵。
    *   计算 `A1 ×` 中间矩阵：`2×3×2 = 12` 次乘法。
    *   **总次数：24 + 12 = 36**

![](img/82f0a604397bb1d4fed8f2ace87dd093_87.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_89.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_91.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_93.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_95.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_97.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_99.png)

可以看到，第二种方案更优。当矩阵数量很大时，不同方案间的效率差异会非常显著。暴力枚举所有括号化方案（其数量是卡特兰数）是不可行的，因此我们需要更聪明的方法——动态规划。

![](img/82f0a604397bb1d4fed8f2ace87dd093_101.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_102.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_104.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_105.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_107.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_109.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_110.png)

---

![](img/82f0a604397bb1d4fed8f2ace87dd093_112.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_113.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_114.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_115.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_116.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_118.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_119.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_121.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_122.png)

## 动态规划公式推导 🧠

![](img/82f0a604397bb1d4fed8f2ace87dd093_124.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_126.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_128.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_130.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_132.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_133.png)

动态规划的核心思想是将大问题分解为重叠的子问题，并存储子问题的解以避免重复计算。对于矩阵链 `A_i ... A_j`，我们尝试在所有可能的位置 `k` (`i ≤ k < j`) 将其分割为两部分 `(A_i ... A_k)` 和 `(A_{k+1} ... A_j)`。

![](img/82f0a604397bb1d4fed8f2ace87dd093_135.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_136.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_138.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_140.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_141.png)

设：
*   `d[i]` 表示矩阵 `A_i` 的行数（`i` 从 0 开始）。因此矩阵 `A_i` 的维度为 `d[i-1] × d[i]`。
*   `m[i][j]` 表示计算子链 `A_i ... A_j` 所需的最少标量乘法次数。

![](img/82f0a604397bb1d4fed8f2ace87dd093_143.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_144.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_146.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_147.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_148.png)

对于链 `A_i ... A_j`，如果我们选择在 `A_k` 和 `A_{k+1}` 之间分割，那么：
1.  计算左边子链 `A_i ... A_k` 的最小代价为 `m[i][k]`。
2.  计算右边子链 `A_{k+1} ... A_j` 的最小代价为 `m[k+1][j]`。
3.  将这两个结果矩阵相乘的代价为 `d[i-1] × d[k] × d[j]`（左边结果矩阵维度为 `d[i-1] × d[k]`，右边为 `d[k] × d[j]`）。

![](img/82f0a604397bb1d4fed8f2ace87dd093_150.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_152.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_153.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_154.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_155.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_156.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_157.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_158.png)

因此，对于每个 `k`，总代价为：`m[i][k] + m[k+1][j] + d[i-1] * d[k] * d[j]`。
我们需要遍历所有可能的 `k`，并取其中的最小值作为 `m[i][j]` 的最优解。

![](img/82f0a604397bb1d4fed8f2ace87dd093_160.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_161.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_162.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_163.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_164.png)

由此得到动态规划递推公式：

![](img/82f0a604397bb1d4fed8f2ace87dd093_166.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_167.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_168.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_169.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_170.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_172.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_173.png)

**当 `i < j` 时：**
`m[i][j] = min_{i ≤ k < j} ( m[i][k] + m[k+1][j] + d[i-1] * d[k] * d[j] )`

![](img/82f0a604397bb1d4fed8f2ace87dd093_174.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_176.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_177.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_179.png)

**边界条件：**
`m[i][i] = 0` （单个矩阵不需要乘法）

![](img/82f0a604397bb1d4fed8f2ace87dd093_181.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_182.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_184.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_185.png)

同时，我们需要另一个表 `s[i][j]` 来记录得到 `m[i][j]` 最优值时的分割点 `k`，以便最后构造出最优的括号化方案。

![](img/82f0a604397bb1d4fed8f2ace87dd093_187.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_189.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_190.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_192.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_193.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_195.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_197.png)

---

![](img/82f0a604397bb1d4fed8f2ace87dd093_199.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_201.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_203.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_205.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_207.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_209.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_210.png)

## 动态规划求解步骤 📊

![](img/82f0a604397bb1d4fed8f2ace87dd093_212.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_213.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_215.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_216.png)

上一节我们推导出了核心公式。本节中我们来看看如何系统地填表求解。

![](img/82f0a604397bb1d4fed8f2ace87dd093_218.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_219.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_220.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_221.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_222.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_224.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_225.png)

我们以包含4个矩阵的链为例，其维度数组为：`d = [3, 2, 4, 2, 5]`。这意味着：
*   `A1: 3×2`
*   `A2: 2×4`
*   `A3: 4×2`
*   `A4: 2×5`

![](img/82f0a604397bb1d4fed8f2ace87dd093_227.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_228.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_229.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_231.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_233.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_235.png)

我们需要填充两个 `n×n` 的表格（`n=4`）：`m`（代价表）和 `s`（分割点表）。

![](img/82f0a604397bb1d4fed8f2ace87dd093_236.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_237.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_239.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_241.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_243.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_245.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_247.png)

以下是具体的填表过程：

![](img/82f0a604397bb1d4fed8f2ace87dd093_249.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_250.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_251.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_253.png)

1.  **初始化**：将对角线 `m[i][i]` 设为 0，因为单个矩阵的乘法代价为0。
    ```
    m[1][1] = 0
    m[2][2] = 0
    m[3][3] = 0
    m[4][4] = 0
    ```

![](img/82f0a604397bb1d4fed8f2ace87dd093_255.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_256.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_257.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_258.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_259.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_261.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_263.png)

2.  **计算链长为2的子问题**（即 `j-i = 1`）：
    *   `m[1][2]`：只有一种分割方式（k=1）。
        `= m[1][1] + m[2][2] + d0*d1*d2 = 0 + 0 + 3*2*4 = 24`，`s[1][2]=1`
    *   `m[2][3]`：k=2。
        `= 0 + 0 + d1*d2*d3 = 2*4*2 = 16`，`s[2][3]=2`
    *   `m[3][4]`：k=3。
        `= 0 + 0 + d2*d3*d4 = 4*2*5 = 40`，`s[3][4]=3`

![](img/82f0a604397bb1d4fed8f2ace87dd093_265.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_266.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_267.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_268.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_270.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_272.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_273.png)

3.  **计算链长为3的子问题**（即 `j-i = 2`）：
    *   `m[1][3]`：k可以是1或2。
        *   k=1: `m[1][1]+m[2][3] + d0*d1*d3 = 0+16 + 3*2*2 = 16+12=28`
        *   k=2: `m[1][2]+m[3][3] + d0*d2*d3 = 24+0 + 3*4*2=24+24=48`
        *   最小值是28，所以 `m[1][3]=28`，`s[1][3]=1`
    *   `m[2][4]`：k可以是2或3。
        *   k=2: `m[2][2]+m[3][4] + d1*d2*d4 = 0+40 + 2*4*5=40+40=80`
        *   k=3: `m[2][3]+m[4][4] + d1*d3*d4 = 16+0 + 2*2*5=16+20=36`
        *   最小值是36，所以 `m[2][4]=36`，`s[2][4]=3`

![](img/82f0a604397bb1d4fed8f2ace87dd093_275.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_276.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_278.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_279.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_280.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_282.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_283.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_284.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_285.png)

4.  **计算链长为4的子问题**（即整个链，`j-i = 3`）：
    *   `m[1][4]`：k可以是1, 2, 3。
        *   k=1: `m[1][1]+m[2][4] + d0*d1*d4 = 0+36 + 3*2*5=36+30=66`
        *   k=2: `m[1][2]+m[3][4] + d0*d2*d4 = 24+40 + 3*4*5=64+60=124`
        *   k=3: `m[1][3]+m[4][4] + d0*d3*d4 = 28+0 + 3*2*5=28+30=58`
        *   最小值是58，所以 `m[1][4]=58`，`s[1][4]=3`

![](img/82f0a604397bb1d4fed8f2ace87dd093_287.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_289.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_290.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_292.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_294.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_296.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_298.png)

最终，我们得到最小乘法总次数为 **58**。`s` 表记录了最优分割点。

![](img/82f0a604397bb1d4fed8f2ace87dd093_300.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_302.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_303.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_305.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_306.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_307.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_308.png)

---

![](img/82f0a604397bb1d4fed8f2ace87dd093_310.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_312.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_314.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_316.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_318.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_320.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_322.png)

## 构造最优括号化方案 🏗️

![](img/82f0a604397bb1d4fed8f2ace87dd093_324.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_326.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_328.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_329.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_331.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_332.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_333.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_335.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_337.png)

现在我们已经得到了最小代价和分割点表 `s`。本节中我们利用 `s` 表来重建最优的括号化方案。

![](img/82f0a604397bb1d4fed8f2ace87dd093_339.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_341.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_343.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_344.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_346.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_347.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_349.png)

递归地从 `s[1][4]` 开始：
1.  `s[1][4] = 3`，表示最优分割在矩阵 `A3` 之后。因此整体结构为：`(A1 A2 A3) (A4)`。
2.  对于左子链 `(1, 3)`，查 `s[1][3] = 1`，表示在 `A1` 之后分割。结构变为：`((A1) (A2 A3)) (A4)`。
3.  对于子链 `(2, 3)`，查 `s[2][3] = 2`，表示在 `A2` 之后分割（即 `A2` 和 `A3` 直接相乘）。最终结构为：`((A1) ((A2)(A3))) (A4)`。

![](img/82f0a604397bb1d4fed8f2ace87dd093_351.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_352.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_353.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_354.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_355.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_356.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_358.png)

去掉不必要的单矩阵括号，最优的乘法顺序是：`(A1 × (A2 × A3)) × A4`。

![](img/82f0a604397bb1d4fed8f2ace87dd093_360.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_361.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_362.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_364.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_366.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_368.png)

这意味着计算步骤是：
1.  先计算 `A2 × A3`（代价16）。
2.  再将结果与 `A1` 相乘（代价 `3*2*2=12`？注意维度变化：`A1(3×2)` 乘 `(A2A3)(2×2)` 代价为 `3*2*2=12`，总代价此时为28）。
3.  最后将结果 `(3×2)` 与 `A4(2×5)` 相乘（代价 `3*2*5=30`）。
总验证代价：16 + 12 + 30 = 58，与动态规划结果一致。

![](img/82f0a604397bb1d4fed8f2ace87dd093_370.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_372.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_373.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_375.png)

---

![](img/82f0a604397bb1d4fed8f2ace87dd093_377.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_378.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_380.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_382.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_384.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_385.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_386.png)

## 算法复杂度与总结 🎯

![](img/82f0a604397bb1d4fed8f2ace87dd093_388.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_390.png)

本节课中我们一起学习了如何使用动态规划解决矩阵链乘法问题。

![](img/82f0a604397bb1d4fed8f2ace87dd093_391.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_392.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_393.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_394.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_395.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_396.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_397.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_398.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_399.png)

**算法复杂度分析**：
*   我们需要填充一个 `n×n` 的 `m` 表，这大约有 `O(n²)` 个子问题。
*   对于每个子问题 `m[i][j]`，我们需要检查最多 `j-i` 种分割可能，在最坏情况下是 `O(n)`。
*   因此，动态规划算法的总时间复杂度为 **O(n³)**。这比暴力枚举所有卡特兰数数量的括号化方案要高效得多。
*   空间复杂度为 **O(n²)**，用于存储 `m` 表和 `s` 表。

![](img/82f0a604397bb1d4fed8f2ace87dd093_401.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_402.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_404.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_405.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_407.png)

**核心要点总结**：
1.  **问题定义**：寻找矩阵链相乘的最优括号化顺序，以最小化标量乘法次数。
2.  **动态规划思路**：将大链分解为两个子链，合并子问题的最优解。
3.  **关键公式**：`m[i][j] = min_{i≤k<j} ( m[i][k] + m[k+1][j] + d[i-1]*d[k]*d[j] )`
4.  **求解步骤**：按链长自底向上填表，记录分割点。
5.  **方案重建**：利用分割点表递归地构造出最优括号化方案。

![](img/82f0a604397bb1d4fed8f2ace87dd093_409.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_410.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_412.png)

![](img/82f0a604397bb1d4fed8f2ace87dd093_414.png)

通过掌握这个经典的动态规划问题，你不仅学会了如何优化矩阵乘法顺序，更重要的是理解了如何将复杂问题分解为重叠子问题并高效求解的通用范式。