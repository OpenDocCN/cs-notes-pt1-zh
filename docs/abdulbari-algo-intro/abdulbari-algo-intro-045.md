# 045：Dijkstra算法 - 单源最短路径 - 贪心法 🚀

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_1.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_2.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_3.png)

在本节课中，我们将要学习Dijkstra算法。这是一种用于解决**单源最短路径问题**的贪心算法。我们将了解其工作原理、步骤、时间复杂性，并探讨其局限性。

## 概述 📋

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_5.png)

单源最短路径问题是指，给定一个带权图，我们需要找出从一个指定的**源顶点**到图中所有其他顶点的最短路径。这是一个**最小化问题**，属于优化问题范畴，因此可以使用贪心法来解决。贪心法的核心思想是分阶段解决问题，每一步只考虑一个输入，以期望获得全局最优解。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_7.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_8.png)

## Dijkstra算法的基本思想 💡

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_10.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_11.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_13.png)

上一节我们介绍了问题的定义，本节中我们来看看Dijkstra算法的核心思想。该算法通过逐步选择当前距离源点最近的顶点，并“松弛”其相邻顶点的距离来工作。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_15.png)

### 核心概念：松弛操作

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_17.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_18.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_19.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_20.png)

松弛是Dijkstra算法的关键操作。假设我们有一个顶点 **u**（其当前最短距离已知）和一个相邻顶点 **v**。松弛操作检查是否通过 **u** 到达 **v** 的路径比 **v** 当前已知的最短路径更短。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_22.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_23.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_25.png)

其判断公式如下：
```
如果 distance[u] + cost(u, v) < distance[v]
则 distance[v] = distance[u] + cost(u, v)
```

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_27.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_28.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_30.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_31.png)

这个操作会不断更新顶点的最短距离估计值。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_33.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_35.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_36.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_37.png)

## 算法步骤详解 🛠️

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_39.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_41.png)

让我们通过一个具体例子，一步步理解Dijkstra算法是如何执行的。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_43.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_44.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_45.png)

### 初始化

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_47.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_49.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_51.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_52.png)

首先，我们需要进行初始化设置。对于源顶点，其距离设为0。对于其他所有顶点，距离初始化为无穷大（∞），表示暂时没有已知路径。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_54.png)

以下是初始化步骤的伪代码表示：
```
初始化距离数组 dist[]
dist[source] = 0
对于所有其他顶点 v：
    dist[v] = ∞
创建一个集合 S，用于存放已找到最短路径的顶点（初始为空）
```

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_56.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_57.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_59.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_61.png)

### 迭代过程

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_63.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_64.png)

接下来，我们进入算法的核心迭代循环。这个过程会重复执行，直到所有顶点都被处理完毕。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_65.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_67.png)

以下是每一步需要执行的操作列表：
1.  **选择顶点**：从尚未处理的顶点集合中，选出当前 `dist` 值最小的顶点 **u**。这保证了我们总是先处理离源点“最近”的顶点。
2.  **标记处理**：将顶点 **u** 加入已处理集合 **S**。这意味着从源点到 **u** 的最短路径已经最终确定。
3.  **松弛相邻顶点**：检查 **u** 的所有未被最终确定的邻居顶点 **v**。对每个邻居 **v**，尝试进行松弛操作：如果 `dist[u] + weight(u, v) < dist[v]`，则更新 `dist[v] = dist[u] + weight(u, v)`。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_69.png)

这个过程会一直重复，直到所有顶点都加入了集合 **S**。最终，`dist` 数组中存储的就是从源点到每个顶点的最短路径长度。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_71.png)

## 算法实例演示 📝

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_72.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_73.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_74.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_75.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_76.png)

让我们通过一个具体的图例来演示上述步骤。假设我们有如下带权图，并选择顶点1作为源点。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_78.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_79.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_80.png)

（此处原视频有图示，教程中可描述：图中有6个顶点，边及其权重为：1-2(2), 1-3(4), 2-3(1), 2-4(7), 3-5(3), 4-5(2), 4-6(5), 5-6(1)）

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_82.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_83.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_85.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_87.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_88.png)

我们将通过表格来追踪算法的执行过程。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_90.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_91.png)

| 步骤 | 已选顶点 (S) | 距离 (dist) | 说明 |
| :--- | :--- | :--- | :--- |
| 初始化 | { } | [0, 2, 4, ∞, ∞, ∞] | 源点1距离为0，直接邻居2和3的距离更新为2和4，其余为∞。 |
| 1 | {1, 2} | [0, 2, **3**, **9**, ∞, ∞] | 选择最小dist顶点2。松弛其邻居：到3：2+1=3 < 4，更新；到4：2+7=9 < ∞，更新。 |
| 2 | {1, 2, 3} | [0, 2, 3, 9, **6**, ∞] | 选择最小dist顶点3。松弛其邻居5：3+3=6 < ∞，更新。 |
| 3 | {1, 2, 3, 5} | [0, 2, 3, **8**, 6, **11**] | 选择最小dist顶点5。松弛其邻居：到4：6+2=8 < 9，更新；到6：6+5=11 < ∞，更新。 |
| 4 | {1, 2, 3, 4, 5} | [0, 2, 3, 8, 6, **9**] | 选择最小dist顶点4。松弛其邻居6：8+1=9 < 11，更新。 |
| 5 | {1, 2, 3, 4, 5, 6} | [0, 2, 3, 8, 6, 9] | 选择最后一个顶点6。无未处理邻居，算法结束。 |

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_93.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_95.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_97.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_98.png)

最终，我们得到了从顶点1到所有顶点的最短距离：1->2:2, 1->3:3, 1->4:8, 1->5:6, 1->6:9。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_100.png)

## 时间复杂性分析 ⏱️

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_102.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_103.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_105.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_106.png)

上一节我们走完了算法的流程，本节我们来分析一下它的效率。Dijkstra算法的时间复杂度取决于如何实现“选择最小距离顶点”和“更新距离”这两个操作。

*   最简单的实现方式是使用数组来存储距离。每次选择最小顶点需要 **O(V)** 时间（V为顶点数），总共需要选择V次。每次松弛操作需要 **O(1)** 时间，但总共可能需要对所有边进行松弛（E为边数）。
*   因此，使用数组的简单实现总时间复杂度为 **O(V² + E)**。在稠密图（E ≈ V²）中，这近似于 **O(V²)**。
*   更高效的实现是使用**最小堆**（优先队列）来存储顶点。每次提取最小顶点和更新键值的操作可以优化到 **O(log V)**。这样，总时间复杂度可以降低到 **O((V+E) log V)**，对于稀疏图更为高效。

我们可以总结其时间复杂度为：
```
简单数组实现：O(V²)
二叉堆优先队列实现：O((V + E) log V)
斐波那契堆实现：O(E + V log V) （理论最优）
```

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_108.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_110.png)

## 算法的适用范围与局限性 ⚠️

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_112.png)

Dijkstra算法功能强大，但并非适用于所有场景。了解其局限性至关重要。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_114.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_115.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_117.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_119.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_120.png)

### 适用范围
*   **图类型**：适用于**有向图**和**无向图**。对于无向图，每条边可以视为两条方向相反的有向边。
*   **边权**：要求图中所有边的**权重为非负值**。这是保证算法正确性的关键前提。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_122.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_124.png)

### 局限性：负权边问题

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_126.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_127.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_129.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_130.png)

Dijkstra算法最大的局限性在于**无法处理包含负权边的图**。原因在于其贪心选择策略：一旦一个顶点被标记为已处理（加入集合S），算法就认为已经找到了到达该顶点的最终最短路径，之后不会再对其进行更新。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_132.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_133.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_135.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_136.png)

考虑一个包含负权边的简单图：顶点A(0) -> B(5), A->C(3), C->B(-10)。从A到B：
1.  初始化：dist[B]=5, dist[C]=3。
2.  选择C（dist=3），标记为已处理。通过C松弛B：3 + (-10) = -7，这比当前的5更短。
3.  然而，由于C已被标记为“已处理”，算法不会再考虑通过C去更新其他顶点（如B）的可能性。
4.  最终，算法错误地输出A到B的最短路径为5，而实际正确的最短路径是-7。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_138.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_140.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_142.png)

**结论**：当图中存在负权边时，Dijkstra算法可能无法得出正确结果。对于包含负权边的单源最短路径问题，需要使用其他算法，例如**Bellman-Ford算法**（将在动态规划部分介绍）。

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_144.png)

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_145.png)

## 总结 🎯

![](img/69c7d679d3cb7b7d1ca06ec7d2dcd678_147.png)

本节课中我们一起学习了Dijkstra算法。我们首先明确了它要解决的**单源最短路径问题**。接着，我们深入探讨了其基于**贪心策略**的核心思想，即通过不断选择当前最短路径顶点并**松弛**其邻居来逐步逼近最优解。我们通过详细的步骤演示和实例，清晰地展示了算法的工作流程。随后，我们分析了算法的时间复杂性，并指出了其最重要的**局限性**：无法处理带有**负权边**的图。理解Dijkstra算法的原理、实现及其适用边界，是学习图论算法的重要基础。对于存在负权边的场景，我们将在后续课程中学习Bellman-Ford算法。