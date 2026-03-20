# 003：算法的特性 📝

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_0.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_2.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_4.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_5.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_6.png)

在本节课中，我们将要学习算法的核心特性。理解这些特性是判断一个过程能否被称为“算法”的关键，也是我们设计和分析算法的基础。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_7.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_9.png)

上一节我们介绍了算法的定义，本节中我们来看看一个合格的算法必须具备哪些具体特征。

## 输入 (Input)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_11.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_12.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_13.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_15.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_17.png)

算法必须接收输入。这里的输入可以是零个或多个。例如，一个生成随机数的算法可能不需要任何外部输入，而一个排序算法则需要一个待排序的列表作为输入。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_19.png)

**核心概念**：算法可以表示为 `Algorithm(Input) -> Output`，其中输入集合可以为空。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_21.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_23.png)

## 输出 (Output)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_24.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_26.png)

算法必须产生至少一个输出。没有输出的算法是毫无意义的。你可以将算法想象成一个函数：它被调用后，必须返回一个结果。即使在编程中函数可能声明为 `void` 类型，它也会通过修改外部状态等方式产生某种“效果”，这同样可视为输出。

**核心概念**：算法执行后，必须有 `Output ≠ ∅`（输出非空）。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_28.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_30.png)

## 明确性 (Definiteness)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_32.png)

算法的每一步都必须清晰、明确，且只有单一、确切的含义。不能存在任何模棱两可或无法执行的语句。例如，你不能在算法中使用一个未定义或虚构的值（如虚数单位 `i` 在实数运算中），因为计算机无法理解或处理它。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_34.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_35.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_37.png)

**核心概念**：算法中的每个步骤都必须是**确定性的**和**可执行的**。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_39.png)

## 有限性 (Finiteness)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_41.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_42.png)

算法必须在有限的步骤后终止。它不能无限循环下去。一个算法可能只有10行代码，也可能有上万行，但其语句集合必须是有限的，并且执行过程会结束。这与一些持续运行的服务程序（如数据库服务器、Web服务器）有本质区别，后者是“过程”而非“算法”。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_44.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_46.png)

**核心概念**：算法的执行时间必须是有限的，即 `Time(Algorithm) < ∞`。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_48.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_49.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_51.png)

## 有效性 (Effectiveness)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_53.png)

算法中的每一个操作都必须是基本的、可行的，并且对达成最终目标有贡献。不应包含任何冗余或无意义的步骤。这类似于做化学实验时，你不会去加热一个与最终溶液无关的化学品；或者做菜时，你不会准备一道根本用不上的食材。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_55.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_56.png)

**核心概念**：算法中的所有操作都应是**必要的**和**可实现的**。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_57.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_59.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_61.png)

---

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_63.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_64.png)

为了更清晰地总结，以下是算法的五大关键特性列表：

*   **输入**：算法有零个或多个输入。
*   **输出**：算法至少产生一个输出。
*   **明确性**：每条指令清晰无歧义。
*   **有限性**：算法在执行有限步骤后自动结束。
*   **有效性**：每条指令都基本、可行且必要。

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_66.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_67.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_68.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_69.png)

---

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_71.png)

![](img/da9daa6f7b8e0dcc7a8a053c05e4bed5_72.png)

本节课中我们一起学习了算法的五个基本特性：**输入、输出、明确性、有限性和有效性**。这些特性共同定义了一个合格的算法，将其与一般性的“过程”或“程序”区分开来。理解这些特性，是后续我们设计、分析和评估算法优劣的基石。在下一节中，我们将探讨如何编写和表示算法。