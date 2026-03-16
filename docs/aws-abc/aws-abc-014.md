# 014：N代表Amazon Neptune 🧠

在本节课中，我们将要学习AWS云服务中以字母“N”开头的核心服务——**Amazon Neptune**。这是一个专为处理高度互联数据而设计的图数据库服务。

过去，关系型数据库是大多数应用程序的默认选择。然而，随着现代应用需求的发展，尤其是在处理数据间关系和数据本身同等重要的场景时，关系型数据库有时会显得力不从心。使用关系型数据库处理这类用例，可能需要编写复杂的查询语句，并且在数据量增长时可能导致性能问题。

## 图数据库的优势

上一节我们提到了关系型数据库的局限性，本节中我们来看看图数据库如何提供更好的解决方案。

![](img/80e14e91e44dc828fb1c09caaaaf01ca_1.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_2.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_3.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_4.png)

图数据库（如Amazon Neptune）专为存储和查询数据之间的关系而优化。当您尝试建模的数据核心在于连接或关系时，图数据库是一个绝佳的选择。

![](img/80e14e91e44dc828fb1c09caaaaf01ca_5.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_6.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_7.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_8.png)

以下是图数据库的典型应用场景：
*   **社交媒体**：分析用户之间的好友关系、关注关系。
*   **推荐引擎**：基于用户行为、物品属性之间的复杂关系进行商品、内容推荐。
*   **欺诈检测**：识别异常的交易模式或用户关联网络。

在这些场景中，您通常需要理解“谁与谁是朋友”、“谁与谁有交易往来”或“谁与谁有协作关系”。

## 认识Amazon Neptune

Amazon Neptune是一项全托管的数据库服务，让您在AWS上更轻松地构建和运行图数据库。

Neptune的核心优势在于其**速度**与**规模**：
*   **性能**：它能够存储数十亿个关系，并在查询图时提供毫秒级延迟。
*   **扩展性**：支持每秒数十万次查询。
*   **公式化描述**：其性能可以概括为 `低延迟查询 + 高并发处理 = 适用于大规模关系数据`。

![](img/80e14e91e44dc828fb1c09caaaaf01ca_10.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_11.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_12.png)

此外，Neptune还提供内置安全功能、持续备份、无服务器计算选项以及与其他AWS服务的集成。

![](img/80e14e91e44dc828fb1c09caaaaf01ca_13.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_14.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_15.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_16.png)

## 创建与使用Neptune数据库

![](img/80e14e91e44dc828fb1c09caaaaf01ca_17.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_18.png)

创建Amazon Neptune数据库集群最简单的方法是使用AWS CloudFormation模板。该模板会自动为您创建所有必需的资源，无需手动构建。

在底层，这些数据库集群由Amazon EC2实例组成。您可以根据用例选择最佳的EC2实例类型，或者选择无服务器实例类型以实现数据库集群的自动扩展。

Neptune的一个关键架构特点是计算与存储分离：
*   **主存储**位于构成集群的数据库实例（即计算资源）外部。
*   这意味着**计算**和**存储**可以独立扩展。
*   存储容量会随着数据增长自动扩展，最高可达128 TiB。

创建数据库集群后，您可以使用以下查询语言连接并运行查询：
*   **Gremlin**
*   **openCypher**
*   **SPARQL**

![](img/80e14e91e44dc828fb1c09caaaaf01ca_20.png)

有时，将查询结果可视化会更有帮助。Neptune Workbench工具通常可以创建查询结果的可视化图表，同时也以表格形式返回结果。

![](img/80e14e91e44dc828fb1c09caaaaf01ca_21.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_22.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_23.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_24.png)

Neptune还支持**全球数据库**功能，允许您创建一个跨越多个AWS区域的数据库，从而为全球分布式应用程序提供服务。

![](img/80e14e91e44dc828fb1c09caaaaf01ca_25.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_26.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_27.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_28.png)

## 总结与后续学习

![](img/80e14e91e44dc828fb1c09caaaaf01ca_29.png)

本节课中我们一起学习了Amazon Neptune，这是一个专为处理高度互联数据而设计的全托管图数据库服务。我们了解了它相对于传统关系型数据库在特定场景下的优势、其核心特性（高性能、易扩展、计算存储分离），以及典型的应用场景和创建使用方法。

![](img/80e14e91e44dc828fb1c09caaaaf01ca_31.png)

![](img/80e14e91e44dc828fb1c09caaaaf01ca_32.png)

如果您想了解更多信息，请查阅AWS官方文档或访问AWS Skill Builder。请继续关注更多AWS云基础课程。