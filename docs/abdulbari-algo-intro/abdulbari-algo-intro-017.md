# 017：不相交集合数据结构 - 加权合并与折叠查找 🔗

![](img/c04b099741deaa52439c003d29cb9afa_1.png)

![](img/c04b099741deaa52439c003d29cb9afa_3.png)

![](img/c04b099741deaa52439c003d29cb9afa_5.png)

![](img/c04b099741deaa52439c003d29cb9afa_6.png)

![](img/c04b099741deaa52439c003d29cb9afa_7.png)

在本节课中，我们将要学习一种重要的数据结构——不相交集合。我们将了解它的基本概念、核心操作，以及如何利用它来检测无向图中的环。我们还将学习两种高效的优化方法：加权合并与折叠查找。

![](img/c04b099741deaa52439c003d29cb9afa_9.png)

![](img/c04b099741deaa52439c003d29cb9afa_10.png)

---

![](img/c04b099741deaa52439c003d29cb9afa_12.png)

## 什么是“不相交集合”？🤔

![](img/c04b099741deaa52439c003d29cb9afa_14.png)

![](img/c04b099741deaa52439c003d29cb9afa_15.png)

![](img/c04b099741deaa52439c003d29cb9afa_16.png)

![](img/c04b099741deaa52439c003d29cb9afa_17.png)

![](img/c04b099741deaa52439c003d29cb9afa_19.png)

不相交集合与数学中的集合概念相似，但为了在算法中应用而有所调整。它最著名的应用是Kruskal算法，用于检测图中的环。

![](img/c04b099741deaa52439c003d29cb9afa_21.png)

不相交集合是指多个集合，其中任意两个集合之间没有公共元素。例如，在一个非连通的无向图中，每个连通分量可以看作一个独立的集合。如果两个集合的交集为空，则它们是“不相交”的。

![](img/c04b099741deaa52439c003d29cb9afa_23.png)

![](img/c04b099741deaa52439c003d29cb9afa_24.png)

---

## 核心操作：查找与合并 🔍

![](img/c04b099741deaa52439c003d29cb9afa_26.png)

![](img/c04b099741deaa52439c003d29cb9afa_27.png)

不相交集合主要支持两种操作：**查找** 和 **合并**。

![](img/c04b099741deaa52439c003d29cb9afa_29.png)

![](img/c04b099741deaa52439c003d29cb9afa_31.png)

![](img/c04b099741deaa52439c003d29cb9afa_33.png)

### 查找操作
查找操作的目的是确定一个元素属于哪个集合。例如，给定元素 `x`，`find(x)` 返回 `x` 所属集合的代表元素（或根节点）。

![](img/c04b099741deaa52439c003d29cb9afa_35.png)

![](img/c04b099741deaa52439c003d29cb9afa_37.png)

![](img/c04b099741deaa52439c003d29cb9afa_38.png)

![](img/c04b099741deaa52439c003d29cb9afa_40.png)

![](img/c04b099741deaa52439c003d29cb9afa_41.png)

![](img/c04b099741deaa52439c003d29cb9afa_42.png)

### 合并操作
合并操作的目的是将两个不同的集合合并为一个。当我们向图中添加一条连接两个不同集合中顶点的边时，就需要执行合并操作。如果两个顶点已经属于同一个集合，那么添加这条边就会形成一个环。

![](img/c04b099741deaa52439c003d29cb9afa_44.png)

![](img/c04b099741deaa52439c003d29cb9afa_46.png)

![](img/c04b099741deaa52439c003d29cb9afa_47.png)

---

![](img/c04b099741deaa52439c003d29cb9afa_48.png)

![](img/c04b099741deaa52439c003d29cb9afa_50.png)

![](img/c04b099741deaa52439c003d29cb9afa_52.png)

![](img/c04b099741deaa52439c003d29cb9afa_54.png)

## 应用：检测无向图中的环 🚴

![](img/c04b099741deaa52439c003d29cb9afa_56.png)

![](img/c04b099741deaa52439c003d29cb9afa_57.png)

不相交集合是检测无向图中环的有效工具。其核心思想如下：

1.  初始时，将图中的每个顶点视为一个独立的集合。
2.  按顺序考虑图中的每条边 `(u, v)`。
3.  对每条边，执行 `find(u)` 和 `find(v)`。
    *   如果 `find(u) != find(v)`，说明 `u` 和 `v` 属于不同集合，添加这条边不会形成环。此时执行 `union(u, v)` 合并这两个集合。
    *   如果 `find(u) == find(v)`，说明 `u` 和 `v` 已经属于同一个集合，添加这条边会形成环。

![](img/c04b099741deaa52439c003d29cb9afa_59.png)

![](img/c04b099741deaa52439c003d29cb9afa_60.png)

通过这种方式，我们可以在构建最小生成树（如Kruskal算法）时，有效地避免环的产生。

![](img/c04b099741deaa52439c003d29cb9afa_62.png)

![](img/c04b099741deaa52439c003d29cb9afa_64.png)

![](img/c04b099741deaa52439c003d29cb9afa_65.png)

---

![](img/c04b099741deaa52439c003d29cb9afa_67.png)

![](img/c04b099741deaa52439c003d29cb9afa_68.png)

![](img/c04b099741deaa52439c003d29cb9afa_70.png)

![](img/c04b099741deaa52439c003d29cb9afa_72.png)

![](img/c04b099741deaa52439c003d29cb9afa_74.png)

## 图形化表示与数组实现 📊

![](img/c04b099741deaa52439c003d29cb9afa_75.png)

![](img/c04b099741deaa52439c003d29cb9afa_77.png)

为了在计算机中实现不相交集合，我们通常使用数组来表示。

![](img/c04b099741deaa52439c003d29cb9afa_79.png)

### 数组表示法
我们使用一个名为 `parent` 的数组。数组的索引代表顶点编号，数组的值代表该顶点的父节点。
*   初始时，每个顶点都是独立的集合，即自己是自己的根。我们通常用 `-1` 或自身的负值（表示集合大小）来表示根节点。
*   例如，`parent[i] = -1` 表示顶点 `i` 是一个集合的根，且该集合大小为1。

![](img/c04b099741deaa52439c003d29cb9afa_81.png)

![](img/c04b099741deaa52439c003d29cb9afa_83.png)

### 操作示例
假设有顶点 `1` 和 `2`：
*   `find(1)`：查看 `parent[1]`。若为负值，则 `1` 是根。
*   `find(2)`：查看 `parent[2]`。若为负值，则 `2` 是根。
*   由于 `find(1) != find(2)`，执行 `union(1, 2)`。我们可以选择让 `1` 作为新集合的根，并令 `parent[2] = 1`。同时，更新根 `1` 的值为 `-2`，表示集合大小变为2。

![](img/c04b099741deaa52439c003d29cb9afa_85.png)

![](img/c04b099741deaa52439c003d29cb9afa_86.png)

通过这种方式，我们可以高效地追踪所有顶点所属的集合关系。

![](img/c04b099741deaa52439c003d29cb9afa_88.png)

![](img/c04b099741deaa52439c003d29cb9afa_90.png)

![](img/c04b099741deaa52439c003d29cb9afa_91.png)

![](img/c04b099741deaa52439c003d29cb9afa_93.png)

---

![](img/c04b099741deaa52439c003d29cb9afa_95.png)

## 高效优化：加权合并与折叠查找 ⚡

![](img/c04b099741deaa52439c003d29cb9afa_96.png)

![](img/c04b099741deaa52439c003d29cb9afa_98.png)

![](img/c04b099741deaa52439c003d29cb9afa_99.png)

![](img/c04b099741deaa52439c003d29cb9afa_100.png)

基础的查找与合并操作在最坏情况下可能效率不高。以下是两种关键的优化策略。

![](img/c04b099741deaa52439c003d29cb9afa_102.png)

![](img/c04b099741deaa52439c003d29cb9afa_104.png)

![](img/c04b099741deaa52439c003d29cb9afa_106.png)

### 加权合并
在执行合并操作时，我们总是将**元素较少的集合**合并到**元素较多的集合**的根下。这可以防止树变得过高，从而保证查找操作的效率。
在数组实现中，我们通过根节点存储的**负值大小**来判断集合的权重。

![](img/c04b099741deaa52439c003d29cb9afa_108.png)

### 折叠查找
在查找某个元素的根节点时，我们遍历从该元素到根节点的路径。**折叠查找**优化在于，在找到根之后，我们将路径上所有经过的节点**直接指向根节点**。
这样，下次查找这些节点时，就只需要常数时间。

![](img/c04b099741deaa52439c003d29cb9afa_110.png)

以下是这两种优化的核心思想：

![](img/c04b099741deaa52439c003d29cb9afa_112.png)

![](img/c04b099741deaa52439c003d29cb9afa_113.png)

![](img/c04b099741deaa52439c003d29cb9afa_114.png)

![](img/c04b099741deaa52439c003d29cb9afa_115.png)

![](img/c04b099741deaa52439c003d29cb9afa_116.png)

**加权合并伪代码**：
```
union(x, y):
    rootX = find(x)
    rootY = find(y)
    if rootX == rootY: return
    // 比较权重（集合大小）
    if size[rootX] < size[rootY]:
        parent[rootX] = rootY
        size[rootY] += size[rootX]
    else:
        parent[rootY] = rootX
        size[rootX] += size[rootY]
```

![](img/c04b099741deaa52439c003d29cb9afa_118.png)

![](img/c04b099741deaa52439c003d29cb9afa_119.png)

**折叠查找伪代码**：
```
find(x):
    if parent[x] < 0: // x是根节点
        return x
    else:
        // 递归查找根，并折叠路径
        parent[x] = find(parent[x])
        return parent[x]
```

![](img/c04b099741deaa52439c003d29cb9afa_121.png)

![](img/c04b099741deaa52439c003d29cb9afa_122.png)

![](img/c04b099741deaa52439c003d29cb9afa_123.png)

![](img/c04b099741deaa52439c003d29cb9afa_124.png)

结合加权合并与折叠查找，不相交集合的每个操作平均时间接近常数复杂度，效率非常高。

![](img/c04b099741deaa52439c003d29cb9afa_126.png)

![](img/c04b099741deaa52439c003d29cb9afa_127.png)

![](img/c04b099741deaa52439c003d29cb9afa_128.png)

![](img/c04b099741deaa52439c003d29cb9afa_130.png)

---

## 总结 📝

![](img/c04b099741deaa52439c003d29cb9afa_132.png)

![](img/c04b099741deaa52439c003d29cb9afa_134.png)

本节课我们一起学习了不相交集合数据结构。
*   我们首先了解了不相交集合的概念及其两个核心操作：**查找**与**合并**。
*   接着，我们探讨了如何利用不相交集合来**检测无向图中的环**，这是Kruskal等算法的基础。
*   然后，我们学习了如何使用**数组**来高效表示和操作不相交集合。
*   最后，我们介绍了两种重要的优化技术：**加权合并**与**折叠查找**。加权合并通过总是将小树合并到大树下来保持树的平衡；折叠查找则在查找过程中压缩路径，将节点直接链接到根，从而大幅提升后续查找的效率。

![](img/c04b099741deaa52439c003d29cb9afa_136.png)

![](img/c04b099741deaa52439c003d29cb9afa_137.png)

![](img/c04b099741deaa52439c003d29cb9afa_138.png)

![](img/c04b099741deaa52439c003d29cb9afa_139.png)

掌握不相交集合及其优化，对于理解许多图论算法至关重要。