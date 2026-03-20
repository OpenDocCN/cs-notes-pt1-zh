# 021：递推关系 (T(n) = T(n-1) + log n) 求解 🧮

![](img/c414042cbdca40d4ff36015e72dd901a_1.png)

![](img/c414042cbdca40d4ff36015e72dd901a_3.png)

在本节课中，我们将学习如何分析一个特定算法的运行时间，该算法的运行时间由递推关系 **T(n) = T(n-1) + log n** 描述。我们将通过两种方法（递归树法和代入法）来求解这个递推式，并最终得出其时间复杂度。此外，我们还将总结一类递推关系的快速求解规律。

![](img/c414042cbdca40d4ff36015e72dd901a_5.png)

![](img/c414042cbdca40d4ff36015e72dd901a_6.png)

## 算法分析与递推式建立

我们首先分析一个给定的算法或函数，目标是找出它的运行时间。

![](img/c414042cbdca40d4ff36015e72dd901a_8.png)

![](img/c414042cbdca40d4ff36015e72dd901a_9.png)

![](img/c414042cbdca40d4ff36015e72dd901a_10.png)

![](img/c414042cbdca40d4ff36015e72dd901a_11.png)

该算法包含一个循环。观察循环，变量 `i` 从1开始，每次迭代乘以2。根据时间复杂度的示例知识，我们可以直接判断这个循环语句的执行次数。

![](img/c414042cbdca40d4ff36015e72dd901a_13.png)

这个语句将执行 **log n** 次。

为了简化分析，我们专注于算法中主要的打印操作。打印操作本身消耗的时间我们考虑为 **log n**。

![](img/c414042cbdca40d4ff36015e72dd901a_15.png)

![](img/c414042cbdca40d4ff36015e72dd901a_16.png)

算法剩余部分是一个递归调用 `T(n-1)`。

![](img/c414042cbdca40d4ff36015e72dd901a_18.png)

![](img/c414042cbdca40d4ff36015e72dd901a_19.png)

因此，这个算法的总运行时间 **T(n)** 是递归调用时间加上本次操作的时间，即：

![](img/c414042cbdca40d4ff36015e72dd901a_21.png)

![](img/c414042cbdca40d4ff36015e72dd901a_23.png)

![](img/c414042cbdca40d4ff36015e72dd901a_25.png)

**T(n) = T(n-1) + log n**

![](img/c414042cbdca40d4ff36015e72dd901a_27.png)

同时，我们知道基准情况：当 **n = 0** 时，**T(0) = 1**。

所以，完整的递推关系是：
*   **T(n) = 1**，当 n = 0。
*   **T(n) = T(n-1) + log n**，当 n > 0。

![](img/c414042cbdca40d4ff36015e72dd901a_29.png)

接下来，我们将使用两种方法求解这个递推式。

![](img/c414042cbdca40d4ff36015e72dd901a_30.png)

![](img/c414042cbdca40d4ff36015e72dd901a_32.png)

## 方法一：递归树法 🌳

我们使用递归树法来展开递推关系。

![](img/c414042cbdca40d4ff36015e72dd901a_34.png)

![](img/c414042cbdca40d4ff36015e72dd901a_36.png)

递推式 **T(n) = T(n-1) + log n** 表示，解决规模为 `n` 的问题需要 `log n` 的时间，再加上解决规模为 `n-1` 的子问题的时间。

我们可以将其逐层展开：
*   第一层：代价为 **log n**，并产生一个规模为 `n-1` 的子问题。
*   第二层：代价为 **log(n-1)**，并产生一个规模为 `n-2` 的子问题。
*   以此类推，直到规模为 `2` 时，代价为 **log 2**，产生规模为 `1` 的子问题。
*   规模为 `1` 时，代价为 **log 1**，产生规模为 `0` 的子问题。
*   规模为 `0` 时，根据基准情况，代价为 **1**。

![](img/c414042cbdca40d4ff36015e72dd901a_38.png)

![](img/c414042cbdca40d4ff36015e72dd901a_40.png)

现在，将每一层的代价相加，得到总时间：

![](img/c414042cbdca40d4ff36015e72dd901a_42.png)

![](img/c414042cbdca40d4ff36015e72dd901a_43.png)

**T(n) = log n + log(n-1) + log(n-2) + ... + log 2 + log 1 + 1**

我们可以忽略最后的常数1，因为它不影响渐近复杂度。于是得到：

![](img/c414042cbdca40d4ff36015e72dd901a_45.png)

**T(n) = log n + log(n-1) + log(n-2) + ... + log 2 + log 1**

![](img/c414042cbdca40d4ff36015e72dd901a_46.png)

根据对数运算性质，对数相加等于真数相乘后取对数：

![](img/c414042cbdca40d4ff36015e72dd901a_48.png)

![](img/c414042cbdca40d4ff36015e72dd901a_50.png)

**T(n) = log( n * (n-1) * (n-2) * ... * 2 * 1 ) = log(n!)**

![](img/c414042cbdca40d4ff36015e72dd901a_52.png)

![](img/c414042cbdca40d4ff36015e72dd901a_54.png)

因此，运行时间是 **log(n!)**。

![](img/c414042cbdca40d4ff36015e72dd901a_56.png)

![](img/c414042cbdca40d4ff36015e72dd901a_58.png)

![](img/c414042cbdca40d4ff36015e72dd901a_60.png)

我们知道阶乘 `n!` 的一个上界是 **n^n**。所以，`log(n!)` 的上界是 `log(n^n) = n log n`。

![](img/c414042cbdca40d4ff36015e72dd901a_62.png)

![](img/c414042cbdca40d4ff36015e72dd901a_64.png)

![](img/c414042cbdca40d4ff36015e72dd901a_66.png)

由此，我们得出该算法的时间复杂度为 **O(n log n)**。

![](img/c414042cbdca40d4ff36015e72dd901a_68.png)

![](img/c414042cbdca40d4ff36015e72dd901a_70.png)

## 方法二：代入法（归纳法） 🔄

![](img/c414042cbdca40d4ff36015e72dd901a_71.png)

![](img/c414042cbdca40d4ff36015e72dd901a_73.png)

上一节我们用递归树法得到了结果，本节我们使用代入法来验证。

![](img/c414042cbdca40d4ff36015e72dd901a_75.png)

![](img/c414042cbdca40d4ff36015e72dd901a_77.png)

我们从递推式开始：**T(n) = T(n-1) + log n**

我们反复将等式右边的 `T(n-1)` 用其自身的递推式展开：

![](img/c414042cbdca40d4ff36015e72dd901a_79.png)

![](img/c414042cbdca40d4ff36015e72dd901a_81.png)

![](img/c414042cbdca40d4ff36015e72dd901a_83.png)

1.  第一次展开：
    **T(n) = [T(n-2) + log(n-1)] + log n = T(n-2) + log(n-1) + log n**

![](img/c414042cbdca40d4ff36015e72dd901a_85.png)

2.  第二次展开：
    **T(n) = [T(n-3) + log(n-2)] + log(n-1) + log n = T(n-3) + log(n-2) + log(n-1) + log n**

3.  继续展开 `k` 次后，我们得到：
    **T(n) = T(n-k) + log(n-(k-1)) + ... + log(n-1) + log n**

![](img/c414042cbdca40d4ff36015e72dd901a_87.png)

![](img/c414042cbdca40d4ff36015e72dd901a_88.png)

当 `k = n` 时，`T(n-k) = T(0) = 1`。此时等式变为：

![](img/c414042cbdca40d4ff36015e72dd901a_90.png)

![](img/c414042cbdca40d4ff36015e72dd901a_92.png)

**T(n) = 1 + log 1 + log 2 + ... + log(n-1) + log n**

![](img/c414042cbdca40d4ff36015e72dd901a_93.png)

![](img/c414042cbdca40d4ff36015e72dd901a_95.png)

![](img/c414042cbdca40d4ff36015e72dd901a_96.png)

同样，我们得到：

![](img/c414042cbdca40d4ff36015e72dd901a_98.png)

![](img/c414042cbdca40d4ff36015e72dd901a_100.png)

**T(n) = 1 + log(n!)**

因此，时间复杂度仍然是 **O(log(n!))**，即 **O(n log n)**。

![](img/c414042cbdca40d4ff36015e72dd901a_102.png)

![](img/c414042cbdca40d4ff36015e72dd901a_103.png)

![](img/c414042cbdca40d4ff36015e72dd901a_104.png)

代入法得到了与递归树法一致的结论。

![](img/c414042cbdca40d4ff36015e72dd901a_105.png)

![](img/c414042cbdca40d4ff36015e72dd901a_106.png)

## 规律总结与快速求解 🚀

![](img/c414042cbdca40d4ff36015e72dd901a_108.png)

![](img/c414042cbdca40d4ff36015e72dd901a_110.png)

前面我们通过两种方法详细求解了递推式。现在，让我们回顾一类特定递推关系的求解规律，以便未来能快速判断。

![](img/c414042cbdca40d4ff36015e72dd901a_112.png)

以下是几个递推关系及其解的例子：
*   **T(n) = T(n-1) + 1** 的解是 **O(n)**。
*   **T(n) = T(n-1) + n** 的解是 **O(n²)**。
*   **T(n) = T(n-1) + log n** 的解是 **O(n log n)**。

![](img/c414042cbdca40d4ff36015e72dd901a_114.png)

观察这些例子，可以发现一个模式：当递推式形如 **T(n) = T(n-1) + f(n)**，且没有前项系数时，其解大致是 **f(n) * n**。

![](img/c414042cbdca40d4ff36015e72dd901a_116.png)

这是因为 `f(n)` 这个操作会随着递归重复执行大约 `n` 次。

![](img/c414042cbdca40d4ff36015e72dd901a_118.png)

![](img/c414042cbdca40d4ff36015e72dd901a_119.png)

基于这个观察，我们可以快速推断：
*   如果 **T(n) = T(n-1) + n²**，那么解大约是 **n² * n = O(n³)**。
*   即使递推式是 **T(n) = T(n-100) + n**，对于很大的 `n` 值，它仍然会执行大约 `n/100` 次 `n` 的操作，结果仍然是 **O(n²)**。常数因子在渐近分析中被忽略。

![](img/c414042cbdca40d4ff36015e72dd901a_120.png)

![](img/c414042cbdca40d4ff36015e72dd901a_122.png)

以下是快速判断的核心要点：
对于形式为 **T(n) = T(n - c) + f(n)** （其中 c 是常数）的递推式，其时间复杂度通常为 **O(n * f(n))**。

![](img/c414042cbdca40d4ff36015e72dd901a_124.png)

![](img/c414042cbdca40d4ff36015e72dd901a_126.png)

**但是**，这个规律有一个重要的前提：递推式中 `T(n-1)` 前面没有系数。如果递推式形如 **T(n) = 2T(n-1) + 1**，情况就不同了，这将在后续课程中讨论。

## 课程总结 📝

![](img/c414042cbdca40d4ff36015e72dd901a_128.png)

![](img/c414042cbdca40d4ff36015e72dd901a_129.png)

![](img/c414042cbdca40d4ff36015e72dd901a_130.png)

本节课中，我们一起学习了如何分析由 **T(n) = T(n-1) + log n** 描述的算法。
1.  我们首先建立了算法的递推关系式。
2.  接着，使用**递归树法**展开递推式，求和后得到时间复杂度为 **O(log(n!))**，进而简化为 **O(n log n)**。
3.  然后，使用**代入法**进行了验证，得到了相同的结果。
4.  最后，我们总结了一类递推关系 **T(n) = T(n-c) + f(n)** 的快速求解规律：其时间复杂度通常为 **O(n * f(n))**，但需注意该规律仅在递归前项系数为1时适用。

![](img/c414042cbdca40d4ff36015e72dd901a_132.png)

![](img/c414042cbdca40d4ff36015e72dd901a_133.png)

掌握这些方法，能帮助你更高效地分析递归算法的时间复杂度。