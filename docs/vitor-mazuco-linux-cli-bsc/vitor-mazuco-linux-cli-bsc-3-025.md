# 025：删除与重置提交 🔄

![](img/59b8e17ac730f4dbd4b37d2abfee7735_1.png)

在本节课中，我们将学习如何在Git中删除提交、重置到特定提交，以及在不同分支间切换。这些操作能帮助我们管理项目历史，修正错误或调整开发方向。

## 切换分支的高效方法

上一节我们介绍了分支的基本概念，本节中我们来看看如何更高效地在分支间切换。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_3.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_5.png)

我们可以使用 `git checkout` 命令，后跟分支名称，来切换到指定分支。一个实用技巧是使用 `git checkout -` 命令，它允许你在最近切换的两个分支间快速来回切换。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_7.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_9.png)

以下是切换分支的命令示例：
```bash
git checkout master
git checkout berries
git checkout -
```

![](img/59b8e17ac730f4dbd4b37d2abfee7735_11.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_13.png)

## 重置提交以回退历史

![](img/59b8e17ac730f4dbd4b37d2abfee7735_15.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_17.png)

有时我们需要撤销某些提交，或者将分支状态回退到之前的某个时间点。这可以通过 `git reset` 命令实现。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_19.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_21.png)

`git reset` 命令有三种主要模式：
*   **`--soft`**： 仅重置提交历史，工作区和暂存区的内容保持不变。
*   **`--mixed`** (默认)： 重置提交历史和暂存区，但工作区的修改会保留。
*   **`--hard`**： **危险操作**。重置提交历史、暂存区和工作区，所有未提交的更改都将丢失。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_23.png)

为了查看可用的提交历史，我们首先使用 `git log` 命令获取提交的哈希值（ID）。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_25.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_27.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_29.png)

## 实践：回退并创建新提交

![](img/59b8e17ac730f4dbd4b37d2abfee7735_31.png)

让我们在 `berries` 分支上进行操作。首先，我们使用 `git log` 查看该分支的提交历史，并找到我们想要回退到的那个提交的哈希值（例如，添加黑莓的那个提交）。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_33.png)

接着，我们执行 `git reset --hard <commit-hash>` 命令，将 `berries` 分支的HEAD指针、暂存区和工作区都重置到该指定提交的状态。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_35.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_37.png)

重置后，我们可以基于这个历史点继续工作。例如，我们修改购物清单，添加一个新项目（如西瓜），然后创建一个新的提交。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_39.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_41.png)

## 理解分支与提交历史的关系

![](img/59b8e17ac730f4dbd4b37d2abfee7735_43.png)

创建新提交后，我们再次使用 `git log --oneline --graph --all` 命令来可视化整个仓库的提交历史。这个视图清晰地展示了不同分支是如何从共同的历史点分叉，以及它们各自的演进路径。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_45.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_46.png)

我们可以看到，`master` 分支保持原样，而 `berries` 分支则从某个历史点重新开始并添加了新的提交。我们还可以创建更多分支（例如 `melon`），它们可以指向不同的提交。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_48.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_50.png)

## 验证文件状态与自动补全技巧

![](img/59b8e17ac730f4dbd4b37d2abfee7735_52.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_53.png)

为了验证不同分支下文件的实际内容，我们可以切换到各个分支（如 `master`, `berries`, `melon`），并使用 `cat` 命令查看购物清单文件。这将证实每个分支都拥有其独立的文件状态。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_55.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_57.png)

最后，介绍一个提高效率的小技巧：在输入分支名时，可以按 `Tab` 键进行自动补全。如果存在多个可能选项，按两次 `Tab` 键会列出所有匹配的分支名称。

![](img/59b8e17ac730f4dbd4b37d2abfee7735_59.png)

![](img/59b8e17ac730f4dbd4b37d2abfee7735_61.png)

本节课中我们一起学习了如何使用 `git reset` 回退提交历史，如何在不同分支间高效切换，以及如何查看和理解分支图谱。我们还实践了基于旧历史创建新提交，并学会了使用 `Tab` 键进行命令自动补全。掌握这些操作能让你更自如地管理项目的版本历史。