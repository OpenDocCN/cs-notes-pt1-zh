# 123：部分索引 🎯

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_1.png)

在本节课中，我们将要学习MongoDB中的部分索引。上一节我们介绍了稀疏索引，本节中我们来看看部分索引，它提供了更灵活的索引创建方式。

部分索引的优势和目的是什么？它与我们上一节课看到的稀疏索引略有相似，但有一个额外的优势。部分索引在MongoDB 3.2版本中被引入，它可以使用诸如 `$eq`（等于）、`$gt`（大于）等我们已经用过的表达式，以及 `$and`、`$or`、`$exists` 等操作符。

这带来了一个主要好处。例如，如果你有一个庞大的数据集，并且只想为其中一部分文档的某个字段创建索引，使用传统的索引会对整个集合建立索引，这可能并不理想。部分索引允许我们只针对满足特定条件的文档子集建立索引。

以下是创建一个部分索引的步骤。

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_3.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_4.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_6.png)

首先，我们使用一个模拟的 `mockData` 数据库进行测试。我们需要检查集合中文档的总数，以及没有 `language` 字段的文档数量。

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_8.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_10.png)

我们执行以下命令来查看文档总数：
```javascript
db.users.count()
```
结果显示有100,000个文档。

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_12.png)

接着，我们查找没有 `language` 字段的文档数量：
```javascript
db.users.count({ language: { $exists: false } })
```
结果显示大约有12,700个文档没有 `language` 字段。

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_14.png)

在创建新的部分索引之前，需要移除上一节课可能创建的旧索引。使用以下命令删除名为 `language_1` 的索引：
```javascript
db.users.dropIndex("language_1")
```
可以通过 `db.users.getIndexes()` 来确认索引是否被成功移除。

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_16.png)

现在，让我们比较一下使用索引前后的查询性能。首先，我们执行一个没有索引的查询，查找所有名字中包含“Sarah”的文档：
```javascript
db.users.find({ firstName: /Sarah/ }).explain("executionStats")
```
查询执行时间约为57毫秒。

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_18.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_20.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_22.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_23.png)

接下来，我们创建一个部分索引。这个索引只会在文档存在 `language` 字段时，才对 `firstName` 字段建立索引。创建索引的命令如下：
```javascript
db.users.createIndex(
  { firstName: 1 },
  { partialFilterExpression: { language: { $exists: true } } }
)
```

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_25.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_27.png)

创建索引后，我们再次运行相同的查询：
```javascript
db.users.find({ firstName: /Sarah/ }).explain("executionStats")
```
这次查询的执行时间下降到了29毫秒，并且查询计划显示它有效地使用了我们新创建的部分索引。

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_29.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_30.png)

通过对比可以看到，查询性能得到了显著优化。部分索引与稀疏索引类似，但允许你使用更丰富的查询表达式（如 `$exists`、`$and`、`$or`）来定义索引条件，从而在MongoDB中实现更精细的数据管理。

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_32.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_34.png)

![](img/a80a2fe00a91e66b8ce13aed4b3d7b71_36.png)

本节课中我们一起学习了MongoDB部分索引的创建方法和性能优势。部分索引允许你基于特定条件只为集合的一个子集建立索引，这在处理大型数据集且只需索引部分文档时非常高效。它与稀疏索引的核心区别在于能够使用更复杂的过滤表达式。