# 026：初识Reflogs文件 🔍

在本节课中，我们将首次学习Git中的`reflogs`（引用日志）。`reflogs`是一个记录工具，它会追踪并保存仓库中所有引用（如分支、HEAD）的更新历史。这对于查看操作记录、恢复误操作或理解仓库状态变化至关重要。

## 什么是Reflogs？ 📝

![](img/8e09e2dc46bece7986cbb8b910c5cbea_1.png)

简单来说，`reflogs`可以记录在仓库中执行提交、重置、检出等操作时，引用（例如`HEAD`、分支标签）的每一次更新。它相当于Git操作的“安全网”或“历史记录器”。

![](img/8e09e2dc46bece7986cbb8b910c5cbea_3.png)

## 查看全局Reflogs日志 🌐

![](img/8e09e2dc46bece7986cbb8b910c5cbea_5.png)

要查看仓库中所有引用的完整更新历史，可以使用以下命令：

![](img/8e09e2dc46bece7986cbb8b910c5cbea_7.png)

```bash
git reflog show
```

![](img/8e09e2dc46bece7986cbb8b910c5cbea_9.png)

![](img/8e09e2dc46bece7986cbb8b910c5cbea_10.png)

执行该命令后，终端会显示一个按时间倒序排列的日志列表。列表中包含了`HEAD`引用在仓库中的每一次移动记录，从最近的操作回溯到初始状态。

![](img/8e09e2dc46bece7986cbb8b910c5cbea_12.png)

## 查看特定分支的Reflogs日志 🌿

上一节我们介绍了如何查看全局的引用日志。本节中，我们来看看如何查看特定分支的日志记录。

![](img/8e09e2dc46bece7986cbb8b910c5cbea_14.png)

以下是查看特定分支（例如`master`或`berries`分支）`reflogs`的方法：

![](img/8e09e2dc46bece7986cbb8b910c5cbea_16.png)

![](img/8e09e2dc46bece7986cbb8b910c5cbea_18.png)

```bash
git reflog show <branch-name>
```

![](img/8e09e2dc46bece7986cbb8b910c5cbea_20.png)

例如，要查看`master`分支的日志，命令为：
```bash
git reflog show master
```

![](img/8e09e2dc46bece7986cbb8b910c5cbea_22.png)

![](img/8e09e2dc46bece7986cbb8b910c5cbea_24.png)

要查看`berries`分支的日志，命令为：
```bash
git reflog show berries
```

![](img/8e09e2dc46bece7986cbb8b910c5cbea_26.png)

每个分支都有自己独立的引用日志，记录着该分支特有的提交、合并、重置等操作历史。通过对比不同分支的日志，可以清晰地了解它们各自的配置和演变路径。

![](img/8e09e2dc46bece7986cbb8b910c5cbea_28.png)

![](img/8e09e2dc46bece7986cbb8b910c5cbea_29.png)

## 总结 🎯

![](img/8e09e2dc46bece7986cbb8b910c5cbea_31.png)

本节课中我们一起学习了Git的`reflogs`功能。我们了解到`reflogs`是记录引用更新历史的强大工具，可以通过`git reflog show`命令查看全局日志，也可以通过指定分支名来查看特定分支的日志。掌握`reflogs`有助于我们追踪仓库中的所有重要操作，是进行版本控制和问题排查的关键技能。