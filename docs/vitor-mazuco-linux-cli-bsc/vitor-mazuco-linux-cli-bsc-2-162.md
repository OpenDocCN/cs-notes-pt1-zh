# 162：创建交互式镜像 🐳

在本节课中，我们将学习如何在Docker中创建交互式镜像。我们将从基础镜像启动一个容器，在容器内进行修改，并将这些更改保存为一个新的自定义镜像。

## 概述

创建自定义镜像的第一步，是以交互模式构建一个容器。我们将选择一个基础镜像作为模板，然后在其内部运行一个容器并进行修改。

## 启动交互式容器

我们将使用之前见过的Alpine Linux镜像。我们可以指定一个特定版本，例如3.10。

以下是启动一个交互式容器的命令：

```bash
docker run -it --name sample alpine:3.10 sh
```

*   `docker run` 是运行容器的命令。
*   `-it` 参数组合表示以交互模式运行并分配一个伪终端。
*   `--name sample` 将容器命名为“sample”。
*   `alpine:3.10` 指定了要使用的基础镜像及其版本。
*   `sh` 是容器启动后要运行的命令，这里我们进入了shell。

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_1.png)

执行此命令后，系统会下载（如果本地没有）指定的Alpine 3.10镜像，并启动一个名为“sample”的交互式容器。

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_3.png)

## 在容器内进行操作

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_5.png)

现在我们已经进入了容器的shell环境。我们可以执行一些命令来修改容器。

例如，我们可以运行Alpine Linux的包管理器更新命令：

```bash
apk update
```

命令会正常执行，更新软件包列表。

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_7.png)

我们也可以测试网络连通性，例如使用ping命令：

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_8.png)

```bash
ping -c 4 8.8.8.8
```

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_10.png)

操作完成后，输入 `exit` 命令即可退出容器。

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_12.png)

## 检查容器状态与更改

退出容器后，如果我们想查看所有容器（包括已停止的），可以使用以下命令：

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_14.png)

```bash
docker ps -a
```

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_16.png)

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_17.png)

参数 `-a` 表示列出所有容器。

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_18.png)

为了查看我们的容器“sample”相对于原始基础镜像发生了哪些更改，我们可以使用 `docker diff` 命令：

```bash
docker diff sample
```

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_20.png)

命令输出会显示文件系统的变更，并用字母标识变更类型：
*   **A**：代表新增的文件。
*   **C**：代表被更改的文件。
*   **D**：代表被删除的文件。

在我们的例子中，可能会看到 **A** (新增) 和 **C** (更改) 类型的记录。

## 提交更改以创建新镜像

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_22.png)

我们可以将这些在容器内所做的更改持久化，并以此创建一个全新的镜像。这通过 `docker commit` 命令实现。

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_24.png)

```bash
docker commit sample my-custom-alpine
```

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_26.png)

*   `docker commit` 是提交更改的命令。
*   `sample` 是我们要提交的容器名称。
*   `my-custom-alpine` 是为新镜像指定的名称。

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_28.png)

执行成功后，可以使用 `docker image ls` 命令查看镜像列表，你会发现新创建的 `my-custom-alpine` 镜像。

## 查看镜像历史

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_30.png)

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_31.png)

如果你想了解新镜像的构建历史，可以使用 `docker history` 命令：

```bash
docker history my-custom-alpine
```

此命令会显示该镜像的每一层是如何创建的，包括其ID、创建命令和大小等信息。

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_33.png)

## 总结

![](img/9fb4ce90e185144b3955aaa6eb6c6fb1_35.png)

本节课我们一起学习了Docker中创建交互式镜像的完整流程。我们首先基于特定版本的基础镜像启动了一个交互式容器，然后在容器内部执行了更新和测试等操作。接着，我们学会了如何使用 `docker diff` 检查容器内的更改，并最终通过 `docker commit` 命令将这些更改保存为一个全新的自定义镜像。这是一个非常基础但强大的镜像定制方法。