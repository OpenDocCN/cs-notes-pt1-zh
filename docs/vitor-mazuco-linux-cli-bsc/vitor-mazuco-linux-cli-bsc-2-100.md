MongoDB基础：第二部分：文档插入操作 🗂️

![](img/84dc55adcebf4e35504457d2790b06fb_1.png)

在本节课中，我们将学习如何在MongoDB中操作文档。数据库中最核心的部分就是文档，掌握如何插入文档是使用MongoDB的第一步。

上一节我们介绍了MongoDB的基本概念，本节中我们来看看如何向集合中插入文档。

![](img/84dc55adcebf4e35504457d2790b06fb_3.png)

**插入**操作是指将内容放入数据库的过程。其语法非常基础和简单，你只需要使用 `db.<集合名>.insert()` 命令。

例如，我们有一个名为 `text` 的数据库。首先，我们查看其中已有的集合。

![](img/84dc55adcebf4e35504457d2790b06fb_5.png)

```javascript
db.text.showCollections()
```

假设我们有一个名为 `mycollection` 的集合。现在，我们向这个集合插入一个文档。

```javascript
db.mycollection.insert({
    title: “MongoDB Co.”,
    description: “A simple description by Victor Mason.”,
    url: “https://example.com”,
    tags: [“database”, “MongoDB”, “NoSQL”],
    likes: 100
})
```

插入后，我们可以使用 `find()` 命令来查看集合中的所有文档。

```javascript
db.mycollection.find()
```

需要注意的是，如果不加任何条件，`find()` 命令会获取集合中的**所有**文档。在处理大量数据时需要谨慎使用。

除了插入单个文档，MongoDB也支持批量插入操作。这意味着你可以通过一个命令插入多个不同类型的文档。

以下是批量插入的示例：

```javascript
db.mycollection.insert([
    {
        title: “Post One”,
        content: “Content for first post.”
    },
    {
        title: “Post Two”,
        tags: [“bulk”, “insert”],
        active: true
    }
])
```

执行批量插入后，再次使用 `find()` 命令，可以看到所有新插入的内容，包括我们之前插入的单个文档。

本节课中我们一起学习了MongoDB文档插入的基础操作，包括单个插入和批量插入，并使用 `find()` 命令进行验证。在下一节课中，我们将更深入地探讨如何使用 `find()` 命令进行高效和精确的文档查询。