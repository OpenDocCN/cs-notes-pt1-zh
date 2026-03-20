# 011：渐进符号（大O、Ω、Θ）详解 🧮

在本节课中，我们将要学习算法分析中一个至关重要的数学工具——渐进符号。这些符号用于简洁地表示函数的增长级别，帮助我们理解和比较不同算法的时间复杂度，而无需每次都写出复杂的函数表达式。

## 什么是渐进符号？📈

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_1.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_3.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_5.png)

上一节我们提到了用函数表示时间复杂度。本节中我们来看看如何用更简洁的方式描述这些函数。渐进符号来源于数学，用于表示函数的“类别”或“级别”。它可以帮助我们比较函数的增长速率，并以一种简单、可交流的形式呈现算法的复杂度。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_7.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_8.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_10.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_12.png)

以下是三种核心的渐进符号：

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_14.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_16.png)

*   **大O符号 (Big O Notation)**: 这表示函数的**上界**。
*   **Ω符号 (Omega Notation)**: 这表示函数的**下界**。
*   **Θ符号 (Theta Notation)**: 这表示函数的**紧确界**（平均界）。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_18.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_20.png)

任何函数都可以用其上界、下界或紧确界来表示。其中，Θ符号最为精确和有用。当我们无法精确确定一个函数的Θ表示时，可以使用大O（上界）或Ω（下界）来描述它。

## 大O符号 (Big O) - 上界 🎯

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_22.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_23.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_24.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_25.png)

大O符号用于描述函数增长速率的上限。其定义如下：

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_26.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_27.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_29.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_31.png)

**定义**：函数 `f(n) = O(g(n))`，当且仅当存在正常数 `c` 和 `n0`，使得对于所有 `n ≥ n0`，都有：
`f(n) ≤ c * g(n)`

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_33.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_35.png)

这意味着，当 `n` 足够大时，`f(n)` 的增长不会超过 `g(n)` 乘以某个常数因子。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_37.png)

### 理解与应用

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_39.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_41.png)

让我们通过一个例子来理解。假设有一个函数 `f(n) = 2n + 3`。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_42.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_43.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_45.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_47.png)

我们需要找到一个更简单的函数 `g(n)` 和一个常数 `c`，使得 `2n + 3 ≤ c * g(n)` 对于足够大的 `n` 成立。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_48.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_49.png)

一个简单的方法是：将 `f(n)` 中的所有项都转换为 `n` 的倍数。这里，`2n + 3` 可以放大为 `2n + 3n = 5n`。因此：
`2n + 3 ≤ 5n` 对于所有 `n ≥ 1` 成立。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_51.png)

在这里，`c = 5`，`g(n) = n`。所以我们可以说：`f(n) = O(n)`。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_52.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_54.png)

值得注意的是，上界不是唯一的。对于同一个 `f(n)`，我们也可以说：
`2n + 3 ≤ 5n²` 对于所有 `n ≥ 1` 成立。
因此，`f(n) = O(n²)` 同样正确。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_56.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_57.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_59.png)

以下是关于大O符号的一些要点：

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_60.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_62.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_64.png)

*   **`f(n) = O(n)` 和 `f(n) = O(n²)` 都正确**，因为 `n` 和 `n²` 都是 `2n+3` 的上界。
*   **我们应尽量使用最“紧”的上界**。`O(n)` 比 `O(n²)` 更精确地描述了 `f(n)` 的增长。
*   **不能使用比原函数增长更慢的函数作为上界**。例如，`f(n) = O(log n)` 是错误的，因为对于大的 `n`，`2n+3` 最终会超过任何常数倍的 `log n`。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_66.png)

## Ω符号 (Omega) - 下界 ⚖️

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_68.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_69.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_70.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_72.png)

Ω符号用于描述函数增长速率的下限。其定义与大O符号对称：

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_74.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_75.png)

**定义**：函数 `f(n) = Ω(g(n))`，当且仅当存在正常数 `c` 和 `n0`，使得对于所有 `n ≥ n0`，都有：
`f(n) ≥ c * g(n)`

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_77.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_79.png)

这意味着，当 `n` 足够大时，`f(n)` 的增长至少和 `g(n)` 乘以某个常数因子一样快。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_81.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_83.png)

### 理解与应用

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_85.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_87.png)

我们继续使用函数 `f(n) = 2n + 3`。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_88.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_89.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_91.png)

我们需要找到一个函数 `g(n)` 和一个常数 `c`，使得 `2n + 3 ≥ c * g(n)`。一个简单的方法是直接取 `g(n) = n`，并令 `c = 1`。那么：
`2n + 3 ≥ 1 * n` 对于所有 `n ≥ 0` 成立。
因此，`f(n) = Ω(n)`。

同样，下界也不是唯一的。因为 `2n+3` 的增长比对数函数快，所以我们也可以说 `f(n) = Ω(log n)`，这也是正确的。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_93.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_94.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_95.png)

以下是关于Ω符号的要点：

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_97.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_99.png)

*   **`f(n) = Ω(n)` 和 `f(n) = Ω(log n)` 都正确**，因为 `n` 和 `log n` 都是 `2n+3` 的下界。
*   **我们应尽量使用最“紧”的下界**。`Ω(n)` 比 `Ω(log n)` 更精确。
*   **不能使用比原函数增长更快的函数作为下界**。例如，`f(n) = Ω(n²)` 是错误的，因为 `2n+3` 无法达到 `n²` 的增长级别。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_101.png)

## Θ符号 (Theta) - 紧确界 ⚡

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_103.png)

Θ符号结合了上界和下界，用于精确地描述函数的增长级别。当一个函数既是 `O(g(n))` 又是 `Ω(g(n))` 时，我们就可以用 `Θ(g(n))` 来表示。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_105.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_106.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_108.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_109.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_111.png)

**定义**：函数 `f(n) = Θ(g(n))`，当且仅当存在正常数 `c1`, `c2` 和 `n0`，使得对于所有 `n ≥ n0`，都有：
`c1 * g(n) ≤ f(n) ≤ c2 * g(n)`

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_113.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_115.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_116.png)

这意味着，`f(n)` 被夹在两个常数倍的 `g(n)` 之间，因此它与 `g(n)` 具有相同的增长阶数。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_118.png)

### 理解与应用

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_120.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_121.png)

再次使用 `f(n) = 2n + 3`。
我们已经找到：
*   下界：`1 * n ≤ 2n + 3` （`c1 = 1`, `g(n) = n`）
*   上界：`2n + 3 ≤ 5 * n` （`c2 = 5`, `g(n) = n`）

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_122.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_123.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_124.png)

将两者结合，得到：`1*n ≤ 2n+3 ≤ 5*n` 对于足够大的 `n` 成立。
因此，`f(n) = Θ(n)`。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_126.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_127.png)

对于Θ符号，有严格的限制：

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_129.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_131.png)

*   **`g(n)` 必须是同一个函数**。上界和下界必须使用相同的 `g(n)`（此处为 `n`）。
*   **表示是唯一的**。对于 `f(n)=2n+3`，我们只能说 `Θ(n)`。说 `Θ(n²)` 或 `Θ(log n)` 都是错误的，因为它们无法同时满足上下界的定义。
*   **这是最理想的表示**，因为它精确地刻画了函数的增长行为。在算法分析中，应尽可能使用Θ表示法。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_133.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_134.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_135.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_136.png)

## 重要注意事项 ⚠️

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_137.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_139.png)

最后，必须澄清一个常见的误解：

**渐进符号（大O、Ω、Θ）与算法的最好情况、最坏情况分析没有直接关系。**

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_141.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_142.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_143.png)

很多人错误地认为：
*   大O表示最坏情况。
*   Ω表示最好情况。
*   Θ表示平均情况。

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_145.png)

**这是不正确的。** 这些符号仅仅是用来描述**函数边界**的数学工具。你可以用大O来描述一个算法最好情况时间复杂度的上界，也可以用Ω来描述最坏情况时间复杂度的下界。案例（最好、最坏、平均）分析与边界（上界、下界、紧确界）分析是两个不同的维度，不应混淆。

---

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_147.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_148.png)

![](img/bcf5b4183fe7fb1c6831a17d70cef27e_150.png)

本节课中我们一起学习了三种核心的渐进符号：大O（上界）、Ω（下界）和Θ（紧确界）。我们理解了它们的数学定义，并通过例子 `f(n)=2n+3` 掌握了如何应用它们来简化函数的表示。记住，Θ符号是最精确的，而大O和Ω在无法确定紧确界时也很有用。正确理解这些符号是进行算法复杂度分析的基础。