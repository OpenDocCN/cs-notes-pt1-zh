# 102：修改文档 📝

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_1.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_2.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_3.png)

在本节课中，我们将学习如何在 MongoDB 中修改文档。我们将重点介绍 `update` 和 `save` 这两个方法，了解它们的区别以及各自适用的场景。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_5.png)

## 概述

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_7.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_8.png)

在 MongoDB 中，修改现有文档是常见的操作。为了实现这一目标，MongoDB 提供了 `update` 和 `save` 两种主要方法。理解它们之间的差异对于高效、安全地操作数据至关重要。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_10.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_12.png)

## `update` 与 `save` 方法简介

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_14.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_16.png)

上一节我们介绍了文档的基本操作，本节中我们来看看如何修改它们。`update` 和 `save` 是用于修改文档的两个核心命令。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_17.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_19.png)

以下是它们的基本语法：

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_21.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_22.png)

```javascript
db.collection.update( <query>, <update>, <options> )
db.collection.save( <document> )
```

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_24.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_26.png)

*   **`db.collection.update()`**: 用于更新符合查询条件的文档。
*   **`db.collection.save()`**: 用于替换整个文档。如果文档包含 `_id` 字段，则替换具有该 `_id` 的文档；否则，会插入一个新文档。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_28.png)

## 使用 `update` 命令

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_30.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_31.png)

`update` 命令的语法非常基础。你需要指定集合名称、选择文档的条件以及要更新的数据。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_33.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_35.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_36.png)

例如，假设我们有一个文档，其标题（title）为 “MongoDB Course”。现在我们想将其修改为 “New MongoDB”。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_38.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_39.png)

操作步骤如下：
1.  使用 `find` 命令定位到目标文档。
2.  执行 `update` 命令。由于我们设定了字符数量限制（即字段长度固定），`update` 命令要求新值的字符数必须与原值相同，不能增加或减少。
3.  执行命令后，会返回修改结果（如 `modifiedCount: 1`）。
4.  再次使用 `find` 命令查看，可以看到标题已成功更改为 “New MongoDB”。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_41.png)

```javascript
// 假设原文档：{ title: “MongoDB Course” }
db.yourCollection.update(
    { title: “MongoDB Course” },
    { $set: { title: “New MongoDB” } }
)
```

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_43.png)

一个有趣的技巧是使用 `multi: true` 选项。如果你想更新多个符合条件的不同文档或字段，可以使用这个命令，它能更高效地批量修改数据。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_45.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_46.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_47.png)

```javascript
db.yourCollection.update(
    { status: “active” },
    { $set: { priority: 1 } },
    { multi: true }
)
```

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_49.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_51.png)

## 使用 `save` 命令

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_53.png)

如果你想改变字符数量，或者在创建集合时设定了限制导致 `update` 命令报错，那么 `save` 命令可以绕过这种限制。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_54.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_56.png)

操作步骤如下：
1.  定位到包含字符字段的文档。
2.  使用 `db.collection.save()` 并传入包含 `_id` 和新字段值的完整文档对象。
3.  执行后，该文档将被新文档完全替换。
4.  使用 `find` 命令验证，标题已成功更改。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_58.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_60.png)

```javascript
// 替换整个文档
db.yourCollection.save({
    _id: ObjectId(“...”), // 必须包含原文档的 _id
    title: “New MongoDB Course with More Characters”,
    description: “Updated description.”
})
```

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_62.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_63.png)

## 总结

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_65.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_66.png)

本节课中我们一起学习了 MongoDB 中修改文档的两种方法：`update` 和 `save`。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_68.png)

它们的核心区别在于：
*   **`update` 命令**：用于局部更新文档的特定字段，在字段有严格约束（如固定长度）时，必须遵守原规则。它提供了更精细的控制。
*   **`save` 命令**：用于替换整个文档。当需要修改的字段长度或结构发生变化，超出原有约束时，可以使用此命令。

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_69.png)

![](img/ed0b8e0c81d33c1878f7b39de0482c2d_71.png)

因此，如果你希望操作更安全，且不想改变字段的原有约束（如字符数），应优先使用 `update` 命令。反之，如果需要突破原有结构限制，则可以使用 `save` 命令。你可以根据实际需求选择最合适的方法。