# 023：Git引用详解 📚

在本节课中，我们将要学习Git中一个核心概念——**引用**。引用是Git版本控制系统中用于标记提交（commit）的移动标签，它帮助我们更高效地管理项目历史。

## 什么是Git引用？ 🏷️

上一节我们介绍了Git的基本操作，本节中我们来看看什么是引用。引用不仅仅是一个标签，它是一个可以移动的标签，被放置在某个提交上。引用不应被随意命名，以防止在搜索特定配置时造成混淆。通过引用，我们可以方便地移动、回退、合并或在必要时丢弃某些提交。

为了更深入地理解这个概念，接下来我们将通过实践来探索。

![](img/896653e1d2a9d418d640f4221dc682ec_1.png)

## 实践：创建一个Git仓库 🛠️

![](img/896653e1d2a9d418d640f4221dc682ec_3.png)

![](img/896653e1d2a9d418d640f4221dc682ec_5.png)

![](img/896653e1d2a9d418d640f4221dc682ec_6.png)

为了理解引用的工作原理，让我们从零开始创建一个Git仓库。

![](img/896653e1d2a9d418d640f4221dc682ec_8.png)

![](img/896653e1d2a9d418d640f4221dc682ec_10.png)

首先，我们回到Linux系统的根目录。我们将在这里创建一个新目录。

![](img/896653e1d2a9d418d640f4221dc682ec_12.png)

![](img/896653e1d2a9d418d640f4221dc682ec_14.png)

以下是创建和初始化仓库的步骤：

1.  使用 `mkdir` 命令创建一个名为 `grocery_store` 的目录。
    ```bash
    mkdir grocery_store
    ```
2.  进入新创建的目录。
    ```bash
    cd grocery_store
    ```
3.  使用 `ls -la` 命令检查目录内容，确认没有任何文件。
    ```bash
    ls -la
    ```
4.  初始化Git仓库并创建一个 `README.md` 文件。
    ```bash
    git init
    echo “# My Shopping Repository” > README.md
    ```
5.  向 `README.md` 文件中添加一个购物清单。
    ```bash
    echo “banana” >> README.md
    ```
6.  将文件添加到暂存区并提交更改。
    ```bash
    git add README.md
    git commit -m “Add banana to the shopping list”
    ```

完成上述步骤后，我们就有了第一个提交。

## 探索提交历史与引用 🔍

![](img/896653e1d2a9d418d640f4221dc682ec_16.png)

现在，让我们开始探索引用的概念。首先，检查我们仓库的当前状态和提交历史。

![](img/896653e1d2a9d418d640f4221dc682ec_18.png)

![](img/896653e1d2a9d418d640f4221dc682ec_20.png)

1.  使用 `git log` 命令查看提交历史。
    ```bash
    git log
    ```
    这个命令会显示提交的哈希值、作者、日期和提交信息。
2.  使用 `git log --oneline` 命令以简洁的单行格式查看提交历史。
    ```bash
    git log --oneline
    ```
3.  使用 `git log --oneline --decorate` 命令查看附加在提交上的标签（即引用）。
    ```bash
    git log --oneline --decorate
    ```
    输出会显示 `HEAD` 和 `master` 等引用指向了哪个提交。

![](img/896653e1d2a9d418d640f4221dc682ec_22.png)

![](img/896653e1d2a9d418d640f4221dc682ec_24.png)

接下来，我们创建第二个提交，观察引用的变化。

![](img/896653e1d2a9d418d640f4221dc682ec_26.png)

![](img/896653e1d2a9d418d640f4221dc682ec_28.png)

![](img/896653e1d2a9d418d640f4221dc682ec_29.png)

1.  向购物清单中添加一个新项目。
    ```bash
    echo “orange” >> README.md
    ```
2.  提交这次新的更改。
    ```bash
    git add README.md
    git commit -m “Add orange to the shopping list”
    ```
3.  再次运行 `git log --oneline --decorate` 命令。
    ```bash
    git log --oneline --decorate
    ```
    现在可以看到两个提交，并且 `HEAD` 和 `master` 引用都移动到了最新的（第二个）提交上。

![](img/896653e1d2a9d418d640f4221dc682ec_31.png)

![](img/896653e1d2a9d418d640f4221dc682ec_32.png)

## 引用是如何工作的？ ⚙️

![](img/896653e1d2a9d418d640f4221dc682ec_34.png)

![](img/896653e1d2a9d418d640f4221dc682ec_35.png)

那么，引用是如何工作的呢？`HEAD` 是一个特殊的引用，它通常指向当前所在的分支（例如 `master`）。而分支引用（如 `master`）则指向该分支上的最新提交，这个提交被称为“尖端提交”。

![](img/896653e1d2a9d418d640f4221dc682ec_37.png)

![](img/896653e1d2a9d418d640f4221dc682ec_39.png)

每次你进行新的提交时，当前分支的引用就会自动向前移动，始终与该分支上的最新提交保持关联。

![](img/896653e1d2a9d418d640f4221dc682ec_41.png)

![](img/896653e1d2a9d418d640f4221dc682ec_43.png)

Git在内部是如何管理这些引用的呢？让我们查看Git仓库的隐藏目录结构。

![](img/896653e1d2a9d418d640f4221dc682ec_45.png)

![](img/896653e1d2a9d418d640f4221dc682ec_47.png)

1.  进入仓库的 `.git` 目录。
    ```bash
    cd .git
    ls -la
    ```
2.  查看 `refs` 目录。
    ```bash
    ls -la refs/
    ```
    你会看到 `heads` 和 `tags` 等子目录。
3.  查看 `heads` 目录的内容。
    ```bash
    cat refs/heads/master
    ```
    这个文件里存储的是一个哈希值，它正是 `master` 分支所指向的那个最新提交的ID。Git通过这种简单的文本文件来管理所有引用。

![](img/896653e1d2a9d418d640f4221dc682ec_49.png)

![](img/896653e1d2a9d418d640f4221dc682ec_51.png)

## 总结 📝

本节课中我们一起学习了Git引用的核心概念。我们了解到：

*   **引用**是Git中指向特定提交的移动标签。
*   `HEAD` 引用通常指向当前检出的分支。
*   **分支引用**（如 `master`）指向该分支历史中的最新提交。
*   每次提交后，相应的分支引用会自动更新。
*   Git在 `.git/refs/` 目录下使用纯文本文件来存储这些引用信息。

![](img/896653e1d2a9d418d640f4221dc682ec_53.png)

理解引用是掌握Git分支、合并和回退等高级操作的基础。在下一节课中，我们将更详细地探讨分支的概念。