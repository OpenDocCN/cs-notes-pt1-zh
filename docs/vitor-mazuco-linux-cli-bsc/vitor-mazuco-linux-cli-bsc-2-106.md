# 106：使用 Bulk 操作 🚀

![](img/6d275899cf65bf45323e8a0e8b943243_1.png)

在本节课中，我们将学习 MongoDB 中的 Bulk 操作。这是一种用于同时处理大量文档的系统，可以显著提升数据库操作的性能。

## 概述

大多数数据库系统，包括 MongoDB，都提供了一种 API 调用方式，允许在**单次操作**中插入或检索多行文档。通过使用 Bulk 操作，我们可以显著提高性能，并大幅减少客户端与数据库之间的往返事务次数。

## 准备测试数据

![](img/6d275899cf65bf45323e8a0e8b943243_3.png)

![](img/6d275899cf65bf45323e8a0e8b943243_5.png)

为了演示 Bulk 操作的工作原理，我们首先需要创建一个测试数据库并插入一些数据。

![](img/6d275899cf65bf45323e8a0e8b943243_7.png)

以下是初始化数据库和集合的示例代码：

![](img/6d275899cf65bf45323e8a0e8b943243_9.png)

![](img/6d275899cf65bf45323e8a0e8b943243_11.png)

![](img/6d275899cf65bf45323e8a0e8b943243_12.png)

```javascript
use testDB
db.persons.insertMany([
  { name: "Java", country: "UKE" },
  { name: "Python", country: "UKE" },
  { name: "Swift", country: "UKE" }
])
```

![](img/6d275899cf65bf45323e8a0e8b943243_14.png)

![](img/6d275899cf65bf45323e8a0e8b943243_16.png)

执行上述代码后，我们使用 `db.persons.find()` 命令可以查看到三条已插入的记录。

![](img/6d275899cf65bf45323e8a0e8b943243_18.png)

## 执行 Bulk 更新操作

![](img/6d275899cf65bf45323e8a0e8b943243_20.png)

![](img/6d275899cf65bf45323e8a0e8b943243_22.png)

上一节我们插入了测试数据，本节中我们来看看如何使用 Bulk 操作来批量更新这些文档。

![](img/6d275899cf65bf45323e8a0e8b943243_24.png)

Bulk 操作的核心是初始化一个操作列表，然后一次性执行。以下是操作步骤：

1.  **初始化 Bulk 操作**：首先，我们需要初始化一个针对特定集合的 Bulk 操作对象。
2.  **定义操作**：接着，我们可以向这个 Bulk 对象中添加多个查找、更新或删除操作。
3.  **执行操作**：最后，一次性执行所有排队的操作。

以下是更新所有 `country` 字段为 “India” 的 Bulk 操作示例：

![](img/6d275899cf65bf45323e8a0e8b943243_26.png)

![](img/6d275899cf65bf45323e8a0e8b943243_28.png)

```javascript
var bulk = db.persons.initializeOrderedBulkOp();
bulk.find({}).update({$set: {country: "India"}});
bulk.execute();
```

![](img/6d275899cf65bf45323e8a0e8b943243_30.png)

执行后，系统会返回找到并修改的文档数量。我们可以再次使用 `db.persons.find()` 来验证所有文档的 `country` 字段都已更新为 “India”。

![](img/6d275899cf65bf45323e8a0e8b943243_32.png)

## 执行有条件的 Bulk 更新

![](img/6d275899cf65bf45323e8a0e8b943243_34.png)

我们也可以为 Bulk 操作添加条件，使其只更新部分文档。

![](img/6d275899cf65bf45323e8a0e8b943243_36.png)

![](img/6d275899cf65bf45323e8a0e8b943243_38.png)

例如，以下操作只更新第一条匹配的文档：

![](img/6d275899cf65bf45323e8a0e8b943243_40.png)

```javascript
var bulk = db.persons.initializeOrderedBulkOp();
bulk.find({}).updateOne({$set: {country: "BankOne"}});
bulk.execute();
```

执行后，使用 `db.persons.find()` 命令查看，会发现只有第一条文档的 `country` 被更新为了 “BankOne”。

![](img/6d275899cf65bf45323e8a0e8b943243_42.png)

![](img/6d275899cf65bf45323e8a0e8b943243_43.png)

## 处理大规模数据

Bulk 操作在处理大量数据时优势尤为明显。为了展示这一点，我们可以先插入大量数据。

以下是插入5000条文档的示例代码：

![](img/6d275899cf65bf45323e8a0e8b943243_45.png)

```javascript
for (var i = 0; i < 5000; i++) {
    db.persons.insertOne({name: "Geek", country: "Initial"})
}
```

![](img/6d275899cf65bf45323e8a0e8b943243_47.png)

数据准备完成后，我们可以使用 Bulk 操作一次性更新这5000条文档。

![](img/6d275899cf65bf45323e8a0e8b943243_49.png)

以下是批量更新所有名为 “Geek” 的文档的示例：

![](img/6d275899cf65bf45323e8a0e8b943243_51.png)

![](img/6d275899cf65bf45323e8a0e8b943243_53.png)

```javascript
var bulk = db.persons.initializeOrderedBulkOp();
bulk.find({name: "Geek"}).update({$set: {name: "Python"}});
bulk.execute();
```

![](img/6d275899cf65bf45323e8a0e8b943243_55.png)

执行后，命令会返回匹配并更新的文档数量（应为5000）。通过 `db.persons.find({name: "Python"}).count()` 可以验证所有目标文档都已被快速更新。

![](img/6d275899cf65bf45323e8a0e8b943243_56.png)

## 总结

![](img/6d275899cf65bf45323e8a0e8b943243_58.png)

本节课中我们一起学习了 MongoDB 的 Bulk 操作。我们了解到，Bulk 操作允许我们将多个插入、更新或删除操作组合成一个请求发送给数据库，这能极大地提升处理大量数据时的效率。关键步骤包括初始化 Bulk 操作对象、定义操作队列以及最终执行。掌握这一功能对于需要高性能批量数据处理的场景至关重要。