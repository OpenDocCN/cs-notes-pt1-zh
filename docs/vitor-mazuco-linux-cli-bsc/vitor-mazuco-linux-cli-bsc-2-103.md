# 103：删除文档 📄➡️🗑️

![](img/3a8600e930ccb393cc064657c45dfb5b_1.png)

在本节课中，我们将学习如何在 MongoDB 中删除文档。删除操作需要格外谨慎，因为一个简单的命令就可能永久清除系统中的重要数据。

## 概述与警告 ⚠️

处理任何类型的删除操作时都必须非常小心。无论是删除文档中的一个字段、删除特定文档，还是删除整个集合或数据库，一个简单的命令就可能抹去你或你客户的所有信息。如果没有备份，数据将无法恢复。因此，请务必谨慎使用删除命令。

![](img/3a8600e930ccb393cc064657c45dfb5b_3.png)

## 删除命令语法 📝

![](img/3a8600e930ccb393cc064657c45dfb5b_4.png)

删除操作的语法非常简单。其基本格式如下：

![](img/3a8600e930ccb393cc064657c45dfb5b_6.png)

```javascript
db.<collection_name>.remove(<criteria>)
```

![](img/3a8600e930ccb393cc064657c45dfb5b_8.png)

*   `db`：代表当前数据库。
*   `<collection_name>`：是你想要操作的集合名称。
*   `.remove()`：是执行删除操作的方法。
*   `<criteria>`：是删除条件，你可以使用多种类型的查询条件来指定要删除哪些文档。

![](img/3a8600e930ccb393cc064657c45dfb5b_10.png)

## 实战演示 🖥️

![](img/3a8600e930ccb393cc064657c45dfb5b_12.png)

上一节我们介绍了删除命令的基本语法，本节中我们来看看具体的操作示例。

我们将使用之前课程中用过的 `Ecommerce` 数据库。

![](img/3a8600e930ccb393cc064657c45dfb5b_14.png)

首先，切换到 `Ecommerce` 数据库并查看其中的集合：

```javascript
use Ecommerce
show collections
```

![](img/3a8600e930ccb393cc064657c45dfb5b_16.png)

![](img/3a8600e930ccb393cc064657c45dfb5b_17.png)

我们可以看到有一个 `products` 集合。使用 `find()` 命令查看其中的内容，以便进行一些删除测试。

![](img/3a8600e930ccb393cc064657c45dfb5b_19.png)

假设集合中有一个标题为 “MongoDB” 的文档，我们想要删除它。

以下是执行删除的命令：

```javascript
db.products.remove({“title”: “MongoDB”})
```

![](img/3a8600e930ccb393cc064657c45dfb5b_21.png)

按下回车后，命令会返回删除的数量（例如 `WriteResult({ “nRemoved” : 1 })`），表示一个文档已被删除。再次使用 `find()` 命令查找该标题的文档，会发现它已经不存在了。

![](img/3a8600e930ccb393cc064657c45dfb5b_22.png)

## 重要警告：无条件的删除 🚨

![](img/3a8600e930ccb393cc064657c45dfb5b_24.png)

![](img/3a8600e930ccb393cc064657c45dfb5b_25.png)

如果你在 `remove()` 方法中不指定任何删除条件，会发生什么？

![](img/3a8600e930ccb393cc064657c45dfb5b_27.png)

例如，执行以下命令：

![](img/3a8600e930ccb393cc064657c45dfb5b_29.png)

```javascript
db.products.remove({})
```

![](img/3a8600e930ccb393cc064657c45dfb5b_31.png)

**这个命令会删除 `products` 集合内的所有文档！**

MongoDB 默认不会询问或确认任何删除操作。命令执行后，数据就会被直接清除。

![](img/3a8600e930ccb393cc064657c45dfb5b_33.png)

![](img/3a8600e930ccb393cc064657c45dfb5b_34.png)

因此，请务必清楚你将要执行的删除操作类型。在进行生产环境操作前，建议在测试集合（例如 `people` 这类无关紧要的集合）上练习，这有助于你理解操作的影响，并在不小心误删时减少损失。

![](img/3a8600e930ccb393cc064657c45dfb5b_36.png)

## 总结 📚

![](img/3a8600e930ccb393cc064657c45dfb5b_38.png)

本节课中我们一起学习了 MongoDB 的文档删除操作。
我们了解了删除命令 `db.collection.remove(criteria)` 的基本语法，并通过实例演示了如何根据条件删除特定文档。
最重要的是，我们强调了**无条件删除 `db.collection.remove({})` 会清空整个集合**这一高风险操作，并反复提醒在执行删除命令时必须格外谨慎，因为 MongoDB 默认不会进行二次确认。
请务必在操作前确认数据库和集合，并确保有可靠的数据备份。