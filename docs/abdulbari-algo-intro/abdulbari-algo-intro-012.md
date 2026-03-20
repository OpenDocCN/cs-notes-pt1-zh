算法导论：1.8.2：渐进符号（大O、Ω、Θ）示例详解 🧮

![](img/53c100aa58edbbde1b35530bf2a4c7db_1.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_3.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_4.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_6.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_8.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_10.png)

在本节课中，我们将通过具体的函数示例，深入学习如何应用大O、Ω和Θ这三种渐进符号来分析算法的时间复杂度。我们将看到如何为不同的函数找到其上界、下界和紧确界。

![](img/53c100aa58edbbde1b35530bf2a4c7db_12.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_14.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_16.png)

上一节我们介绍了渐进符号的基本定义，本节中我们来看看如何将这些定义应用到具体的函数上。

![](img/53c100aa58edbbde1b35530bf2a4c7db_18.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_20.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_21.png)

---

![](img/53c100aa58edbbde1b35530bf2a4c7db_22.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_24.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_26.png)

### 示例一：多项式函数

![](img/53c100aa58edbbde1b35530bf2a4c7db_28.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_30.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_31.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_33.png)

考虑函数 **f(n) = 2n² + 3n + 4**。

![](img/53c100aa58edbbde1b35530bf2a4c7db_35.png)

为了找到其上界（大O表示法），我们可以将其与一个更大的函数进行比较。例如：
*   2n² + 3n + 4 ≤ 2n² + 3n² + 4n² （当 n ≥ 1 时）
*   即 2n² + 3n + 4 ≤ 9n²

![](img/53c100aa58edbbde1b35530bf2a4c7db_37.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_38.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_39.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_41.png)

因此，存在常数 **c = 9** 和 **g(n) = n²**，使得对于所有足够大的 n，都有 f(n) ≤ c * g(n)。所以，我们可以说：
**f(n) = O(n²)**

![](img/53c100aa58edbbde1b35530bf2a4c7db_43.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_45.png)

对于同一个函数，我们也可以找到其下界（Ω表示法）：
*   2n² + 3n + 4 ≥ 1 * n² （当 n ≥ 1 时）

![](img/53c100aa58edbbde1b35530bf2a4c7db_47.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_49.png)

因此，存在常数 **c = 1** 和 **g(n) = n²**，使得对于所有足够大的 n，都有 f(n) ≥ c * g(n)。所以，我们可以说：
**f(n) = Ω(n²)**

![](img/53c100aa58edbbde1b35530bf2a4c7db_50.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_51.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_53.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_54.png)

由于我们同时找到了相同的上界和下界函数（n²），这意味着该函数的增长率被 n² 紧密地限定。因此，我们可以得到其紧确界（Θ表示法）：
**f(n) = Θ(n²)**

![](img/53c100aa58edbbde1b35530bf2a4c7db_56.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_58.png)

---

![](img/53c100aa58edbbde1b35530bf2a4c7db_60.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_61.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_62.png)

### 示例二：对数与多项式组合函数

![](img/53c100aa58edbbde1b35530bf2a4c7db_64.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_66.png)

考虑函数 **f(n) = n² log n + n**。

![](img/53c100aa58edbbde1b35530bf2a4c7db_67.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_68.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_70.png)

以下是分析步骤：
*   首先，n² log n + n ≤ 10 * n² log n （当 n 足够大时）。所以，**f(n) = O(n² log n)**。
*   其次，n² log n + n ≥ 1 * n² log n （当 n 足够大时）。所以，**f(n) = Ω(n² log n)**。
*   由于上下界函数相同，我们可以得出：**f(n) = Θ(n² log n)**。

![](img/53c100aa58edbbde1b35530bf2a4c7db_71.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_73.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_75.png)

值得注意的是，n² log n 的增长率介于 n² 和 n³ 之间。对于此类函数，我们可以在渐进阶的图表中为其指定一个明确的位置。

![](img/53c100aa58edbbde1b35530bf2a4c7db_77.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_78.png)

---

![](img/53c100aa58edbbde1b35530bf2a4c7db_79.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_80.png)

### 示例三：阶乘函数

![](img/53c100aa58edbbde1b35530bf2a4c7db_82.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_83.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_84.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_86.png)

考虑函数 **f(n) = n!**。

![](img/53c100aa58edbbde1b35530bf2a4c7db_88.png)

阶乘函数可以展开为：n! = n * (n-1) * (n-2) * ... * 3 * 2 * 1。

![](img/53c100aa58edbbde1b35530bf2a4c7db_89.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_90.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_91.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_93.png)

为了分析其渐进性，我们尝试寻找边界：
*   一个非常宽松的下界是：n! ≥ 1 * 1 * 1 * ... * 1 = 1。所以，**f(n) = Ω(1)**。
*   一个非常宽松的上界是：n! ≤ n * n * n * ... * n = nⁿ。所以，**f(n) = O(nⁿ)**。

![](img/53c100aa58edbbde1b35530bf2a4c7db_94.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_95.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_97.png)

这里的关键问题是，我们无法找到一个像 n² 或 n³ 这样的“简单”函数 g(n)，使得 n! 既能被 c₁ * g(n) 从下方限定，又能被 c₂ * g(n) 从上方限定（即找到 Θ 界）。n! 的增长速度极快，对于较小的 n 值，它可能接近下界，但对于较大的 n，它会迅速趋近于 nⁿ 这样的上界。

![](img/53c100aa58edbbde1b35530bf2a4c7db_99.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_100.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_101.png)

因此，对于 n!，我们无法定义一个紧确的 Θ 界。我们只能给出其非常宽泛的上界和下界。

![](img/53c100aa58edbbde1b35530bf2a4c7db_103.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_105.png)

---

![](img/53c100aa58edbbde1b35530bf2a4c7db_106.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_107.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_108.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_110.png)

### 示例四：阶乘的对数

考虑函数 **f(n) = log(n!)**。

![](img/53c100aa58edbbde1b35530bf2a4c7db_112.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_113.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_114.png)

利用对数的性质，我们可以进行分析：
*   log(n!) = log(1 * 2 * 3 * ... * n) = log 1 + log 2 + ... + log n。
*   一个宽松的上界是：log(n!) ≤ log(n * n * n * ... * n) = log(nⁿ) = n log n。所以，**f(n) = O(n log n)**。
*   一个宽松的下界是：log(n!) ≥ log(1 * 1 * 1 * ... * 1) = 0，但通常我们写作 ≥ 1。所以，**f(n) = Ω(1)**。

与 n! 类似，log(n!) 也无法找到一个紧确的 Θ 界。

---

### 如何选择使用哪种符号？

![](img/53c100aa58edbbde1b35530bf2a4c7db_116.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_117.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_118.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_119.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_120.png)

以下是关于何时使用大O、Ω和Θ符号的指导原则：

![](img/53c100aa58edbbde1b35530bf2a4c7db_122.png)

*   **优先使用 Θ**：如果你能为一个函数找到 Θ 界，这通常是最好的选择。因为它精确地描述了函数的增长率，是一个“紧确界”。这就像有人问手机的价格，你回答“大约2万元”，给出了最接近的估计。
*   **使用大O（上界）时**：应尽量给出一个“紧”的上界。不要随意给出一个过大、不具参考价值的上界（例如，对于 n² 的函数，说它是 O(n¹⁰) 虽然正确但无意义）。这就像回答手机最高价格时说“最高20万”，虽然正确，但对提问者没有帮助。
*   **使用 Ω（下界）时**：同样，应尽量给出一个“紧”的下界。避免给出过小、不具代表性的下界。

![](img/53c100aa58edbbde1b35530bf2a4c7db_124.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_125.png)

大O 和 Ω 表示法在无法确定紧确界时非常有用（如阶乘函数）。它们表示“最多不会超过”或“至少需要”的量级。而 Θ 则保证了“就是这么多”的精确性。

![](img/53c100aa58edbbde1b35530bf2a4c7db_127.png)

![](img/53c100aa58edbbde1b35530bf2a4c7db_128.png)

---

![](img/53c100aa58edbbde1b35530bf2a4c7db_130.png)

本节课中我们一起学习了如何为不同类型的函数（多项式、对数组合、阶乘）应用大O、Ω和Θ符号进行分析。我们了解到，Θ 是描述算法时间复杂度的最精确方式，应优先寻找。当无法找到 Θ 时，大O 和 Ω 分别提供了有用的上界和下界信息，但应尽可能使其“紧确”以提供有意义的参考。理解这些符号的应用是分析算法效率的基础。在接下来的视频中，我们将学习渐进符号的一些重要性质。