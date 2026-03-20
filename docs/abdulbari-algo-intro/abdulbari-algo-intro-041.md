# 041：带截止时间的作业调度问题 🕒

![](img/e2bf43acdaab76056b4ad2c9cf102856_1.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_3.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_5.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_7.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_8.png)

在本节课中，我们将学习如何使用贪心算法解决“带截止时间的作业调度问题”。这是一个典型的优化问题，目标是在满足每个作业截止时间的前提下，选择一组作业以获得最大总利润。

![](img/e2bf43acdaab76056b4ad2c9cf102856_10.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_12.png)

## 问题定义

![](img/e2bf43acdaab76056b4ad2c9cf102856_14.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_16.png)

假设有 `n` 个作业，每个作业 `i` 都有：
*   一个利润 `p_i`
*   一个截止时间 `d_i`

![](img/e2bf43acdaab76056b4ad2c9cf102856_18.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_20.png)

所有作业都需要在一台机器上处理，每个作业的处理时间恰好为 **1 个单位时间**。机器从时间 `0` 开始工作。目标是选择一个作业子集，使得子集中的每个作业都能在其截止时间前完成，并且所有被选作业的总利润最大。

![](img/e2bf43acdaab76056b4ad2c9cf102856_22.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_23.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_24.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_25.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_26.png)

## 问题示例与理解

![](img/e2bf43acdaab76056b4ad2c9cf102856_28.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_29.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_31.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_32.png)

我们通过一个例子来理解问题。假设有 5 个作业，其利润和截止时间如下：

![](img/e2bf43acdaab76056b4ad2c9cf102856_34.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_35.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_36.png)

| 作业编号 | 利润 | 截止时间 |
| :--- | :--- | :--- |
| J1 | 20 | 2 |
| J2 | 15 | 2 |
| J3 | 10 | 1 |
| J4 | 5 | 3 |
| J5 | 1 | 3 |

![](img/e2bf43acdaab76056b4ad2c9cf102856_38.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_40.png)

**情景比喻**：想象一个打字员（机器）在早上 9 点开门，有 5 位顾客（作业）等待服务。每位顾客都愿意支付一定费用（利润），并且只愿意等待到某个特定时间（截止时间）。打字员每小时（1 个单位时间）只能完成一份文件。目标是选择服务哪些顾客，以使总收入最高。

![](img/e2bf43acdaab76056b4ad2c9cf102856_42.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_43.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_44.png)

观察发现，最大截止时间是 3，因此我们最多有 3 个时间槽位：`(0-1)`, `(1-2)`, `(2-3)`。这意味着最多只能完成 3 个作业。

![](img/e2bf43acdaab76056b4ad2c9cf102856_46.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_48.png)

## 贪心算法策略

![](img/e2bf43acdaab76056b4ad2c9cf102856_50.png)

这是一个最大化问题，适合使用贪心算法。我们的策略是：
**按利润降序考虑作业，并为每个作业尽可能晚地安排一个空闲的时间槽位。**

![](img/e2bf43acdaab76056b4ad2c9cf102856_52.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_53.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_55.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_57.png)

上一节我们定义了问题，本节中我们来看看具体的解决步骤。

![](img/e2bf43acdaab76056b4ad2c9cf102856_59.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_61.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_63.png)

以下是应用贪心算法解决问题的详细步骤：

![](img/e2bf43acdaab76056b4ad2c9cf102856_64.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_66.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_67.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_69.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_70.png)

1.  **排序**：将所有作业按利润从高到低排序。
2.  **初始化时间槽**：找出所有作业中的最大截止时间 `max_deadline`。创建 `max_deadline` 个时间槽（通常编号为 1 到 `max_deadline`），初始均为空。
3.  **迭代安排**：按顺序考虑每个作业（已排序）：
    a. 对于当前作业，查看其截止时间 `d` 对应的槽位。
    b. 如果该槽位空闲，则将作业安排在此槽位。
    c. 如果该槽位已被占用，则向前（向更早的时间）查找，直到找到一个空闲槽位。如果找不到任何空闲槽位（截止时间之前的所有槽位都满了），则放弃此作业。
4.  **计算总利润**：将所有被安排作业的利润相加，得到最大总利润。

![](img/e2bf43acdaab76056b4ad2c9cf102856_72.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_74.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_76.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_77.png)

## 算法演示：例1

![](img/e2bf43acdaab76056b4ad2c9cf102856_79.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_80.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_81.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_82.png)

我们使用上述例子进行演示。作业已按利润降序排列。

![](img/e2bf43acdaab76056b4ad2c9cf102856_84.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_85.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_87.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_88.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_90.png)

*   **考虑 J1 (利润20， 截止2)**：查看槽位2。空闲，安排 J1 到槽位2。利润 = 20。
*   **考虑 J2 (利润15， 截止2)**：查看槽位2，已被 J1 占用。向前查找，槽位1空闲。安排 J2 到槽位1。利润 = 20 + 15 = 35。
*   **考虑 J3 (利润10， 截止1)**：查看槽位1，已被 J2 占用。向前查找，无更早槽位。放弃 J3。
*   **考虑 J4 (利润5， 截止3)**：查看槽位3。空闲，安排 J4 到槽位3。利润 = 35 + 5 = 40。
*   **考虑 J5 (利润1， 截止3)**：查看槽位3，已被 J4 占用。向前查找，槽位2和1均被占用。放弃 J5。

![](img/e2bf43acdaab76056b4ad2c9cf102856_92.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_94.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_95.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_96.png)

**最终安排**：槽位1: J2， 槽位2: J1， 槽位3: J4。总利润 = **40**。

![](img/e2bf43acdaab76056b4ad2c9cf102856_97.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_99.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_100.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_101.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_102.png)

## 算法演示：例2

![](img/e2bf43acdaab76056b4ad2c9cf102856_104.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_105.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_107.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_109.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_110.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_112.png)

再看一个例子，巩固理解。作业如下：

![](img/e2bf43acdaab76056b4ad2c9cf102856_113.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_115.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_117.png)

| 作业 | 利润 | 截止时间 |
| :--- | :--- | :--- |
| J1 | 35 | 3 |
| J2 | 30 | 4 |
| J3 | 25 | 4 |
| J4 | 20 | 2 |
| J5 | 15 | 3 |
| J6 | 12 | 1 |
| J7 | 5 | 2 |

![](img/e2bf43acdaab76056b4ad2c9cf102856_119.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_120.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_122.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_124.png)

最大截止时间为4，因此有4个槽位：1, 2, 3, 4。

![](img/e2bf43acdaab76056b4ad2c9cf102856_126.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_128.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_129.png)

*   **J1 (35, d=3)**：槽位3空闲，安排。利润=35。
*   **J2 (30, d=4)**：槽位4空闲，安排。利润=65。
*   **J3 (25, d=4)**：槽位4已被占，向前查找。槽位2空闲，安排。利润=90。
*   **J4 (20, d=2)**：槽位2已被占，向前查找。槽位1空闲，安排。利润=110。
*   **J5 (15, d=3)**：槽位3已被占，向前查找。槽位1和2均被占，放弃。
*   **J6, J7**：所有槽位已满，放弃。

![](img/e2bf43acdaab76056b4ad2c9cf102856_131.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_133.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_134.png)

**最终安排**：槽位1: J4， 槽位2: J3， 槽位3: J1， 槽位4: J2。总利润 = **110**。

![](img/e2bf43acdaab76056b4ad2c9cf102856_136.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_137.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_139.png)

## 总结

![](img/e2bf43acdaab76056b4ad2c9cf102856_141.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_142.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_144.png)

![](img/e2bf43acdaab76056b4ad2c9cf102856_145.png)

本节课中我们一起学习了如何使用贪心算法解决“带截止时间的作业调度问题”。核心步骤是：**按利润降序选择作业，并为每个作业在其截止时间之前尽可能晚地安排一个空闲时间槽**。这个方法简单直观，能有效地在满足约束的条件下找到总利润较高的解。理解每个作业处理时间为1单位以及“尽可能晚安排”以保留早期槽位给其他作业这两个关键点，是掌握本算法的重点。