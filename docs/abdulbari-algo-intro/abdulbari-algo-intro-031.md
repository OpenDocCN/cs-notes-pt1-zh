# 031：递归二分查找法 🔍

![](img/92f7eb167477426dac9426b70cbdab58_1.png)

![](img/92f7eb167477426dac9426b70cbdab58_2.png)

![](img/92f7eb167477426dac9426b70cbdab58_4.png)

![](img/92f7eb167477426dac9426b70cbdab58_6.png)

![](img/92f7eb167477426dac9426b70cbdab58_8.png)

![](img/92f7eb167477426dac9426b70cbdab58_9.png)

在本节课中，我们将学习如何为二分查找编写一个递归算法。二分查找遵循分治策略，我们将详细解析其递归实现、核心逻辑以及时间复杂度分析。

---

![](img/92f7eb167477426dac9426b70cbdab58_11.png)

## 算法概述与递归思想

![](img/92f7eb167477426dac9426b70cbdab58_13.png)

![](img/92f7eb167477426dac9426b70cbdab58_14.png)

![](img/92f7eb167477426dac9426b70cbdab58_16.png)

上一节我们介绍了二分查找的基本概念。本节中，我们来看看如何用递归的方式实现它。分治策略的核心是：如果问题规模较大，就将其分解为子问题来解决。因此，我们首先需要定义什么是“小问题”。

![](img/92f7eb167477426dac9426b70cbdab58_18.png)

当数组中只剩下一个元素时，我们认为这是一个小问题。对于小问题，处理方式很简单：检查这个元素是否就是我们要找的键值。

![](img/92f7eb167477426dac9426b70cbdab58_20.png)

![](img/92f7eb167477426dac9426b70cbdab58_22.png)

![](img/92f7eb167477426dac9426b70cbdab58_23.png)

---

![](img/92f7eb167477426dac9426b70cbdab58_25.png)

## 递归算法步骤

![](img/92f7eb167477426dac9426b70cbdab58_27.png)

以下是递归二分查找算法的详细步骤。

![](img/92f7eb167477426dac9426b70cbdab58_29.png)

### 1. 定义函数与参数
算法名为 `BinarySearch`，它接受三个参数：`low`（搜索区间下限索引）、`high`（搜索区间上限索引）和 `key`（要查找的目标值）。

![](img/92f7eb167477426dac9426b70cbdab58_31.png)

![](img/92f7eb167477426dac9426b70cbdab58_32.png)

![](img/92f7eb167477426dac9426b70cbdab58_33.png)

![](img/92f7eb167477426dac9426b70cbdab58_34.png)

### 2. 处理基本情况（小问题）
如果 `low == high`，说明当前搜索区间只有一个元素。这是一个小问题。
*   检查该元素 `A[low]` 是否等于 `key`。
*   如果相等，查找成功，返回索引 `low`。
*   如果不相等，查找失败，返回 `0`（或一个表示未找到的特定值）。

![](img/92f7eb167477426dac9426b70cbdab58_35.png)

![](img/92f7eb167477426dac9426b70cbdab58_37.png)

**代码描述：**
```pseudocode
if low == high
    if A[low] == key
        return low
    else
        return 0
```

![](img/92f7eb167477426dac9426b70cbdab58_39.png)

![](img/92f7eb167477426dac9426b70cbdab58_40.png)

![](img/92f7eb167477426dac9426b70cbdab58_42.png)

### 3. 处理递归情况（大问题）
如果问题规模较大（即 `low < high`），则进行以下步骤：
1.  计算中间索引：`mid = (low + high) / 2`。
2.  比较 `key` 与中间元素 `A[mid]`：
    *   如果 `key == A[mid]`，查找成功，返回 `mid`。
    *   如果 `key < A[mid]`，说明目标值只可能存在于左半部分。递归调用 `BinarySearch`，参数为 `(low, mid-1, key)`。
    *   如果 `key > A[mid]`，说明目标值只可能存在于右半部分。递归调用 `BinarySearch`，参数为 `(mid+1, high, key)`。

![](img/92f7eb167477426dac9426b70cbdab58_44.png)

![](img/92f7eb167477426dac9426b70cbdab58_45.png)

![](img/92f7eb167477426dac9426b70cbdab58_47.png)

![](img/92f7eb167477426dac9426b70cbdab58_49.png)

**代码描述：**
```pseudocode
mid = floor((low + high) / 2)
if key == A[mid]
    return mid
else if key < A[mid]
    return BinarySearch(low, mid-1, key)
else
    return BinarySearch(mid+1, high, key)
```

![](img/92f7eb167477426dac9426b70cbdab58_50.png)

---

![](img/92f7eb167477426dac9426b70cbdab58_52.png)

![](img/92f7eb167477426dac9426b70cbdab58_54.png)

## 时间复杂度分析

![](img/92f7eb167477426dac9426b70cbdab58_56.png)

![](img/92f7eb167477426dac9426b70cbdab58_57.png)

![](img/92f7eb167477426dac9426b70cbdab58_59.png)

现在我们已经编写了基于分治策略的递归算法。分治算法的时间复杂度通常使用递归关系式来求解，让我们为此算法建立一个递归式。

![](img/92f7eb167477426dac9426b70cbdab58_61.png)

![](img/92f7eb167477426dac9426b70cbdab58_62.png)

设算法处理 `n` 个元素的时间为 `T(n)`。
*   **对于小问题（n=1）**：只需进行一次比较，因此 `T(1) = 1`。
*   **对于大问题（n>1）**：算法会计算中间值并进行常数次比较（例如2-3次），我们将其计为常数时间 `1`。然后，算法会在左半部分或右半部分（二者选一）递归调用自身，问题的规模减半。因此，递归关系为：
    **`T(n) = T(n/2) + 1`** （当 n>1 时）

![](img/92f7eb167477426dac9426b70cbdab58_64.png)

![](img/92f7eb167477426dac9426b70cbdab58_66.png)

这个递归式是标准的。通过主定理（Master Theorem）或展开求解，我们可以得到其时间复杂度。

应用主定理：将 `T(n) = T(n/2) + 1` 与标准形式 `T(n) = aT(n/b) + f(n)` 对比。
*   `a = 1`
*   `b = 2`
*   `f(n) = 1 = Θ(n^0)`

![](img/92f7eb167477426dac9426b70cbdab58_68.png)

![](img/92f7eb167477426dac9426b70cbdab58_70.png)

这里，`log_b(a) = log_2(1) = 0`。由于 `f(n) = Θ(n^0)`，属于主定理的第二种情况，解为 `T(n) = Θ(n^{log_b(a)} * log n) = Θ(log n)`。

![](img/92f7eb167477426dac9426b70cbdab58_72.png)

![](img/92f7eb167477426dac9426b70cbdab58_73.png)

**结论：** 递归二分查找算法的时间复杂度为 **O(log n)**。

![](img/92f7eb167477426dac9426b70cbdab58_75.png)

![](img/92f7eb167477426dac9426b70cbdab58_77.png)

---

![](img/92f7eb167477426dac9426b70cbdab58_79.png)

## 总结

![](img/92f7eb167477426dac9426b70cbdab58_81.png)

![](img/92f7eb167477426dac9426b70cbdab58_82.png)

![](img/92f7eb167477426dac9426b70cbdab58_83.png)

![](img/92f7eb167477426dac9426b70cbdab58_85.png)

本节课中我们一起学习了二分查找的递归实现方法。我们首先定义了算法的基本情况和递归情况，然后逐步构建了完整的递归算法。最后，我们通过建立和求解递归关系式，证明了该算法具有 **O(log n)** 的优秀时间复杂度，这与迭代版本的二分查找效率一致。递归实现清晰地体现了分治策略的思想：将大问题不断分解，直至可以直接解决的小问题。