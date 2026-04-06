# 097：在 Docker 中安装 MongoDB 🐳

在本节课中，我们将学习如何使用 Docker 来安装和运行 MongoDB。通过 Docker，我们无需直接在 Linux 系统上安装 MongoDB，可以轻松管理多个不同版本的实例，这对于应用开发和系统测试非常有用。

## 概述

![](img/37f5b4659517818cca378d984cc501b4_1.png)

![](img/37f5b4659517818cca378d984cc501b4_2.png)

Docker 的优势在于可以使用大量预配置的镜像，无需在物理系统上直接安装软件。它像一个轻量级的虚拟机，复用 Linux 内核，因此可以在同一系统上运行多个独立的 MongoDB 实例，每个实例可以是不同的版本。这极大地简化了开发环境的配置和管理。

## 安装 Docker

首先，你需要在你的 Linux 系统上安装 Docker 本身。Docker 的安装包可能较大，请确保你的网络连接稳定。

安装完成后，你可以使用以下命令启动 Docker 服务，并设置它在系统启动时自动运行：

![](img/37f5b4659517818cca378d984cc501b4_4.png)

```bash
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```

运行 `status` 命令可以检查 Docker 服务是否已成功启动。

## 下载 MongoDB Docker 镜像

Docker 的核心是使用镜像。我们将从 Docker Hub 拉取官方的 MongoDB 镜像。你可以自由选择所需的 MongoDB 版本。

![](img/37f5b4659517818cca378d984cc501b4_6.png)

使用 `docker pull` 命令下载特定版本的 MongoDB 镜像。例如，下载 4.2 版本：

![](img/37f5b4659517818cca378d984cc501b4_8.png)

```bash
docker pull mongo:4.2
```

![](img/37f5b4659517818cca378d984cc501b4_10.png)

![](img/37f5b4659517818cca378d984cc501b4_12.png)

你也可以选择其他版本，如 4.0、4.4 或 4.6，只需更改冒号后的版本号即可。建议始终使用官方提供的镜像以确保稳定性和安全性。

![](img/37f5b4659517818cca378d984cc501b4_14.png)

下载完成后，可以使用以下命令查看已下载的镜像列表：

![](img/37f5b4659517818cca378d984cc501b4_16.png)

![](img/37f5b4659517818cca378d984cc501b4_18.png)

```bash
docker images
```

![](img/37f5b4659517818cca378d984cc501b4_20.png)

列表中会显示镜像的名称、标签（版本）、镜像 ID 和大小等信息。

## 运行 MongoDB 容器

有了镜像之后，我们需要基于它创建一个运行的容器实例。

以下是运行一个基本 MongoDB 容器的命令，它使用默认端口 27017，并且没有设置密码：

```bash
docker run --name my-mongo -d -p 27017:27017 mongo:4.2
```

![](img/37f5b4659517818cca378d984cc501b4_22.png)

命令解析：
*   `--name my-mongo`：为容器指定一个名称，这里是 `my-mongo`。
*   `-d`：让容器在后台运行。
*   `-p 27017:27017`：将容器的 27017 端口映射到主机的 27017 端口。
*   `mongo:4.2`：指定使用的镜像和版本。

运行后，使用以下命令查看正在运行的容器：

```bash
docker ps
```

如果该端口已被占用，你需要先停止占用该端口的进程，然后再启动容器。

![](img/37f5b4659517818cca378d984cc501b4_24.png)

![](img/37f5b4659517818cca378d984cc501b4_26.png)

## 连接到 MongoDB 实例

容器运行后，你可以通过 Docker 进入容器的 MongoDB Shell 进行操作。

![](img/37f5b4659517818cca378d984cc501b4_28.png)

使用以下命令连接到名为 `my-mongo` 的容器内的 MongoDB：

```bash
docker exec -it my-mongo mongosh
```

![](img/37f5b4659517818cca378d984cc501b4_30.png)

![](img/37f5b4659517818cca378d984cc501b4_32.png)

连接成功后，你将进入 MongoDB Shell。可以尝试运行一些基本命令，例如 `show dbs` 来查看数据库列表。

## 管理容器与高级配置

![](img/37f5b4659517818cca378d984cc501b4_34.png)

![](img/37f5b4659517818cca378d984cc501b4_36.png)

上一节我们介绍了如何运行一个简单的 MongoDB 容器，本节中我们来看看如何进行更高级的管理和配置。

![](img/37f5b4659517818cca378d984cc501b4_38.png)

你可以运行多个 MongoDB 容器，只需为它们指定不同的名称和主机端口即可。例如，启动第二个实例并映射到主机的 27018 端口：

![](img/37f5b4659517818cca378d984cc501b4_40.png)

![](img/37f5b4659517818cca378d984cc501b4_42.png)

```bash
docker run --name my-mongo-2 -d -p 27018:27017 mongo:4.2
```

![](img/37f5b4659517818cca378d984cc501b4_44.png)

以下是常用的容器管理命令：

*   **停止容器**：`docker stop <容器ID或名称>`
*   **启动已停止的容器**：`docker start <容器ID或名称>`
*   **查看容器日志**：`docker logs <容器ID或名称>`
*   **删除容器**：`docker rm <容器ID或名称>`

![](img/37f5b4659517818cca378d984cc501b4_46.png)

如果需要更复杂的配置，例如绑定特定IP、使用SSL或指定数据存储目录，你可以在 `docker run` 命令中添加相应的参数，这些参数与直接安装 MongoDB 时的配置选项是类似的。

![](img/37f5b4659517818cca378d984cc501b4_48.png)

## 总结

![](img/37f5b4659517818cca378d984cc501b4_50.png)

![](img/37f5b4659517818cca378d984cc501b4_52.png)

本节课中我们一起学习了如何使用 Docker 来安装和运行 MongoDB。我们了解了 Docker 的基本概念，完成了从安装 Docker、拉取镜像、运行容器到连接和管理实例的完整流程。利用 Docker，你可以轻松地在同一台机器上创建和管理多个隔离的、不同版本的 MongoDB 环境，这为开发和测试带来了极大的便利。