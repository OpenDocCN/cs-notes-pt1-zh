# 084：归并排序算法详解 🧩

![](img/4f3a6a1da6c9f2efa498be88753e58c2_1.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_3.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_5.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_7.png)

在本节课中，我们将要学习归并排序算法。归并排序是一种基于“分治法”的高效排序算法，其核心思想是将一个大问题分解为若干个小问题，分别解决后再将结果合并。本节课将详细讲解归并排序的两个核心过程：**合并**与**递归分解**，并通过示例和伪代码帮助你彻底理解其工作原理。

![](img/4f3a6a1da6c9f2efa498be88753e58c2_9.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_10.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_12.png)

## 概述

![](img/4f3a6a1da6c9f2efa498be88753e58c2_14.png)

归并排序的过程可以概括为两个主要步骤：首先，将待排序的列表递归地分成两半，直到每个子列表只包含一个元素（一个元素的列表自然是有序的）。然后，将这些有序的子列表两两合并，最终得到一个完整的有序列表。理解“合并”过程是掌握归并排序的关键。

![](img/4f3a6a1da6c9f2efa498be88753e58c2_16.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_18.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_20.png)

## 合并两个有序列表 🤝

![](img/4f3a6a1da6c9f2efa498be88753e58c2_22.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_24.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_26.png)

上一节我们概述了归并排序的流程，本节中我们来看看其最核心的操作：**合并**。合并是指将两个已经分别排好序的列表，组合成一个新的有序列表。

![](img/4f3a6a1da6c9f2efa498be88753e58c2_28.png)

假设我们有两个有序列表 A 和 B。合并过程需要三个指针：
*   **i**：指向列表 A 的当前待比较元素。
*   **j**：指向列表 B 的当前待比较元素。
*   **k**：指向合并结果列表 C 的当前位置。

![](img/4f3a6a1da6c9f2efa498be88753e58c2_30.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_31.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_32.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_34.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_36.png)

以下是合并过程的步骤：
1.  比较 `A[i]` 和 `B[j]`。
2.  将较小的那个元素复制到 `C[k]`。
3.  移动被复制元素所在列表的指针（i 或 j）以及结果列表的指针 k。
4.  重复步骤 1-3，直到其中一个列表的所有元素都被复制完毕。
5.  将另一个列表中剩余的所有元素直接复制到结果列表 C 的末尾。

![](img/4f3a6a1da6c9f2efa498be88753e58c2_37.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_39.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_41.png)

这个过程可以用以下伪代码来描述：

![](img/4f3a6a1da6c9f2efa498be88753e58c2_43.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_45.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_47.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_49.png)

```pseudocode
// 假设列表 A 的范围是 A[low...mid]，列表 B 的范围是 A[mid+1...high]
// 结果将存回原数组 A[low...high] 或另一个数组 C
i = low
j = mid + 1
k = low // 如果使用新数组 C，则 k=0

![](img/4f3a6a1da6c9f2efa498be88753e58c2_51.png)

while (i <= mid && j <= high)
    if (A[i] < A[j])
        C[k] = A[i]
        i++
    else
        C[k] = A[j]
        j++
    k++

![](img/4f3a6a1da6c9f2efa498be88753e58c2_53.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_55.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_56.png)

// 复制剩余元素
while (i <= mid)
    C[k] = A[i]
    i++
    k++

![](img/4f3a6a1da6c9f2efa498be88753e58c2_58.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_60.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_62.png)

while (j <= high)
    C[k] = A[j]
    j++
    k++
```

![](img/4f3a6a1da6c9f2efa498be88753e58c2_64.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_66.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_67.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_69.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_70.png)

## 递归分解与合并流程 🔄

![](img/4f3a6a1da6c9f2efa498be88753e58c2_72.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_74.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_76.png)

理解了如何合并两个有序列表后，我们来看看归并排序如何利用递归来分解问题。对于一个包含 n 个元素的数组，归并排序的递归过程如下：

![](img/4f3a6a1da6c9f2efa498be88753e58c2_78.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_80.png)

1.  **分解**：找到数组的中间点 `mid`，将数组逻辑上分成左右两个子数组。
    *   公式：`mid = (low + high) / 2`
2.  **递归排序**：对左半部分 `A[low...mid]` 递归调用归并排序，使其有序。
3.  **递归排序**：对右半部分 `A[mid+1...high]` 递归调用归并排序，使其有序。
4.  **合并**：调用上面介绍的合并过程，将两个已排序的子数组合并为一个完整的有序数组。

![](img/4f3a6a1da6c9f2efa498be88753e58c2_82.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_84.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_86.png)

这个递归过程会一直进行，直到子数组的长度为 1（此时 `low == high`），因为单个元素的数组被视为已排序。然后递归开始“返回”，逐层合并这些小数组。

![](img/4f3a6a1da6c9f2efa498be88753e58c2_88.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_90.png)

## 完整示例演示 📊

![](img/4f3a6a1da6c9f2efa498be88753e58c2_92.png)

让我们通过一个具体例子 `[2, 8, 5, 3, 9, 4, 1, 7]` 来可视化整个归并排序过程。

![](img/4f3a6a1da6c9f2efa498be88753e58c2_94.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_95.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_97.png)

**分解阶段（递归向下）：**
*   初始数组: `[2, 8, 5, 3, 9, 4, 1, 7]`
*   第一次分解: 左 `[2, 8, 5, 3]`， 右 `[9, 4, 1, 7]`
*   继续分解左半部分: `[2, 8]` 和 `[5, 3]`
*   继续分解，直到每个子数组只剩一个元素：`[2]`, `[8]`, `[5]`, `[3]`, `[9]`, `[4]`, `[1]`, `[7]`

![](img/4f3a6a1da6c9f2efa498be88753e58c2_99.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_101.png)

**合并阶段（递归返回）：**
*   合并 `[2]` 和 `[8]` 得到 `[2, 8]`
*   合并 `[5]` 和 `[3]` 得到 `[3, 5]`
*   合并 `[2, 8]` 和 `[3, 5]` 得到 `[2, 3, 5, 8]`
*   同理，右半部分合并后得到 `[1, 4, 7, 9]`
*   最后合并左右两个大数组 `[2, 3, 5, 8]` 和 `[1, 4, 7, 9]`，得到最终结果 `[1, 2, 3, 4, 5, 7, 8, 9]`

![](img/4f3a6a1da6c9f2efa498be88753e58c2_103.png)

整个过程的模式是：**先不断二分分解，再逐层两两合并**。

![](img/4f3a6a1da6c9f2efa498be88753e58c2_105.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_107.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_108.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_109.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_110.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_111.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_113.png)

## 总结

![](img/4f3a6a1da6c9f2efa498be88753e58c2_115.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_116.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_117.png)

![](img/4f3a6a1da6c9f2efa498be88753e58c2_119.png)

本节课中我们一起学习了归并排序算法。我们首先明确了其“分而治之”的核心思想，然后深入剖析了将两个有序列表合并为一个有序列表的**关键步骤**。接着，我们理解了算法如何通过**递归调用**将原始问题分解到最小单元（单个元素），再通过合并操作自底向上地构建出最终的有序序列。归并排序的时间复杂度为 **O(n log n)**，是一种稳定且高效的通用排序算法。掌握合并过程与递归分解的配合，是理解归并排序的精髓。