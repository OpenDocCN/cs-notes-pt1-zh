# 024：了解HEAD 🔄

![](img/9ab8eaa03825a2563b7faaa11d7686dc_1.png)

在本节课中，我们将要学习Git中一个核心概念——HEAD。我们将了解HEAD是什么，它如何指向当前的工作分支，以及当我们切换分支时，HEAD和文件状态会发生什么变化。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_3.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_4.png)

---

上一节我们简单提到了HEAD。它是一个特殊的引用，本质上是一个指向当前所在位置的指针。

在实践中，HEAD只是一个纯文本文件。我们可以通过命令查看它。例如，在`.git`目录下执行 `cat HEAD` 命令，会看到一个文件路径，例如 `ref: refs/heads/master`。这个文件包含了当前分支的引用信息。

**核心概念**：HEAD文件的内容是一个引用路径，指向当前检出的分支。其格式通常为：
```
ref: refs/heads/<branch-name>
```

![](img/9ab8eaa03825a2563b7faaa11d7686dc_6.png)

---

![](img/9ab8eaa03825a2563b7faaa11d7686dc_8.png)

为了更直观地理解HEAD如何工作，我们将通过创建一个新分支来演示。请注意，分支的详细内容将在后续课程讨论，此处仅为说明HEAD的机制。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_10.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_12.png)

以下是创建并切换分支的步骤：

![](img/9ab8eaa03825a2563b7faaa11d7686dc_13.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_15.png)

1.  **创建新分支**：使用 `git branch <分支名>` 命令。例如，我们创建一个名为 `berries` 的分支。
    ```bash
    git branch berries
    ```

![](img/9ab8eaa03825a2563b7faaa11d7686dc_17.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_19.png)

2.  **查看分支状态**：使用 `git log --oneline --graph --all` 命令，可以看到现在存在 `master` 和 `berries` 两个分支。星号 (`*`) 标记表示当前所在分支（此时应为 `master`）。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_21.png)

3.  **切换分支**：使用 `git checkout <分支名>` 命令切换到新分支。
    ```bash
    git checkout berries
    ```
    再次运行 `git log --oneline --graph --all`，会发现星号标记现在位于 `berries` 分支旁。这意味着**HEAD现在指向了 `berries` 分支**。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_23.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_24.png)

---

![](img/9ab8eaa03825a2563b7faaa11d7686dc_26.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_27.png)

现在，我们的HEAD指向 `berries` 分支。让我们在此分支上进行一些修改，观察HEAD如何跟随最新的提交移动。

1.  **修改文件**：例如，我们有一个 `list.txt` 文件，初始内容为“banana, orange”。我们为其添加“blackberry”。
    ```bash
    echo “blackberry” >> list.txt
    cat list.txt
    # 输出：banana, orange, blackberry
    ```

![](img/9ab8eaa03825a2563b7faaa11d7686dc_29.png)

2.  **提交更改**：执行添加和提交操作。
    ```bash
    git add list.txt
    git commit -m “Added blackberry to the list”
    ```

![](img/9ab8eaa03825a2563b7faaa11d7686dc_31.png)

3.  **观察HEAD移动**：再次查看日志 `git log --oneline --graph --all`。你会发现 `berries` 分支的指针（以及HEAD，因为它指向 `berries`）已经移动到了这个最新的提交上。而 `master` 分支的指针仍然停留在原来的提交位置。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_33.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_35.png)

**核心概念**：HEAD会跟随当前分支的最新提交而移动。每个分支的指针独立移动，记录该分支的进展。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_37.png)

---

![](img/9ab8eaa03825a2563b7faaa11d7686dc_39.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_41.png)

上一节我们看到在 `berries` 分支上文件已被更新。现在，让我们切换回 `master` 分支，观察工作目录和HEAD的变化。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_43.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_44.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_46.png)

1.  **切换回主分支**：
    ```bash
    git checkout master
    ```

![](img/9ab8eaa03825a2563b7faaa11d7686dc_48.png)

2.  **检查文件状态**：查看 `list.txt` 文件的内容。
    ```bash
    cat list.txt
    # 输出：banana, orange
    ```
    你会发现“blackberry”消失了。这是因为每个分支拥有独立的工作目录状态。在 `master` 分支上，文件状态停留在切换出去之前的那一刻。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_50.png)

3.  **验证HEAD指向**：再次查看HEAD文件。
    ```bash
    cat .git/HEAD
    # 输出：ref: refs/heads/master
    ```
    同时，查看日志 `git log --oneline --graph`（不加 `--all` 参数），只会显示 `master` 分支的历史，`berries` 分支上的提交暂时不可见，因为我们当前不在那个分支上。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_52.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_54.png)

---

![](img/9ab8eaa03825a2563b7faaa11d7686dc_56.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_58.png)

如果想查看所有分支的完整提交历史，可以使用 `git log --oneline --graph --all` 命令。这会显示所有分支及其提交的图谱，帮助你理解不同分支的演进路线和HEAD的当前位置。

![](img/9ab8eaa03825a2563b7faaa11d7686dc_60.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_62.png)

---

![](img/9ab8eaa03825a2563b7faaa11d7686dc_64.png)

![](img/9ab8eaa03825a2563b7faaa11d7686dc_66.png)

本节课中我们一起学习了Git中HEAD的概念。我们了解到HEAD是一个指向当前活动分支的指针，它存储在一个简单的文本文件中。当我们进行提交时，HEAD会随着当前分支的指针一起向前移动。切换分支会改变HEAD的指向，同时工作目录的文件也会恢复到目标分支所记录的状态。理解HEAD是掌握Git分支和工作流的基础。在接下来的课程中，我们将继续深入学习分支的创建与管理。