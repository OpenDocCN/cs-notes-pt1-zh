# 156：安装Docker 🐳

在本节课中，我们将学习如何在两种主流的Linux发行版——Ubuntu和Rocky Linux（基于Red Hat）上安装Docker。Docker是一个强大的容器化平台，能帮助您轻松地打包、分发和运行应用程序。

## 概述

![](img/4d5f384c19b8cd872606f4828c7be8bf_1.png)

![](img/4d5f384c19b8cd872606f4828c7be8bf_2.png)

我们将分步介绍在Ubuntu和Rocky Linux系统上安装Docker的完整流程。核心步骤包括更新系统、添加Docker官方仓库、安装Docker引擎，以及最后的安装验证。请确保您拥有系统的管理员权限（sudo）来执行这些命令。

![](img/4d5f384c19b8cd872606f4828c7be8bf_4.png)

![](img/4d5f384c19b8cd872606f4828c7be8bf_5.png)

---

## 在Ubuntu系统上安装Docker 🐧

![](img/4d5f384c19b8cd872606f4828c7be8bf_7.png)

![](img/4d5f384c19b8cd872606f4828c7be8bf_9.png)

上一节我们介绍了课程目标，本节中我们来看看如何在Ubuntu系统上安装Docker。Ubuntu上的安装过程相对直接，主要通过APT包管理器完成。

![](img/4d5f384c19b8cd872606f4828c7be8bf_11.png)

以下是具体的安装步骤：

1.  **更新软件包列表**：首先，更新系统的APT仓库索引，确保获取到最新的软件包信息。
    ```bash
    sudo apt update
    ```

![](img/4d5f384c19b8cd872606f4828c7be8bf_13.png)

2.  **安装证书工具**：安装`ca-certificates`、`curl`和`gnupg`等必要的工具，用于安全地添加软件源。
    ```bash
    sudo apt install ca-certificates curl gnupg
    ```

![](img/4d5f384c19b8cd872606f4828c7be8bf_15.png)

3.  **添加Docker的官方GPG密钥**：此密钥用于验证从Docker仓库下载的软件包的完整性。
    ```bash
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
    ```

![](img/4d5f384c19b8cd872606f4828c7be8bf_17.png)

4.  **添加Docker的APT仓库**：将Docker的稳定版仓库添加到系统的软件源列表中。
    ```bash
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```

5.  **再次更新软件包列表**：添加新仓库后，需要再次更新APT索引以包含Docker的软件包。
    ```bash
    sudo apt update
    ```

![](img/4d5f384c19b8cd872606f4828c7be8bf_19.png)

6.  **安装Docker引擎**：安装Docker社区版及其相关组件。
    ```bash
    sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

![](img/4d5f384c19b8cd872606f4828c7be8bf_21.png)

7.  **验证安装**：运行一个测试容器来确认Docker已正确安装并可以正常工作。
    ```bash
    sudo docker run hello-world
    ```
    如果安装成功，此命令会从Docker Hub下载一个测试镜像并运行，输出“Hello from Docker!”等信息。

![](img/4d5f384c19b8cd872606f4828c7be8bf_23.png)

---

## 在Rocky Linux系统上安装Docker 🪨

在Ubuntu上成功安装Docker后，我们来看看在基于Red Hat的Rocky Linux系统上的安装方法。Rocky Linux使用DNF包管理器，步骤同样清晰明了。

以下是具体的安装步骤：

![](img/4d5f384c19b8cd872606f4828c7be8bf_25.png)

1.  **添加Docker的YUM仓库**：首先，配置系统以使用Docker的官方仓库。
    ```bash
    sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
    ```

![](img/4d5f384c19b8cd872606f4828c7be8bf_27.png)

2.  **安装Docker引擎**：使用DNF命令安装Docker及其必要组件。
    ```bash
    sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

3.  **启动并设置Docker开机自启**：安装完成后，启动Docker服务并使其在系统启动时自动运行。
    ```bash
    sudo systemctl start docker
    sudo systemctl enable docker
    ```

4.  **（可选）将用户加入docker组**：为了避免每次使用Docker命令都需要`sudo`，可以将您的用户添加到`docker`用户组中。
    ```bash
    sudo usermod -aG docker $USER
    ```
    **注意**：执行此命令后，您需要**注销并重新登录**，或者开启一个新的终端会话，才能使组权限生效。

![](img/4d5f384c19b8cd872606f4828c7be8bf_29.png)

5.  **验证安装**：与Ubuntu上一样，运行Hello World容器来验证安装。
    ```bash
    docker run hello-world
    ```
    如果之前已将用户加入`docker`组，这里可以省略`sudo`。

![](img/4d5f384c19b8cd872606f4828c7be8bf_30.png)

---

## 总结与注意事项 📝

本节课中我们一起学习了在Ubuntu和Rocky Linux两种Linux发行版上安装Docker的完整流程。关键步骤都涉及添加官方仓库、安装软件包以及运行测试容器进行验证。

![](img/4d5f384c19b8cd872606f4828c7be8bf_32.png)

![](img/4d5f384c19b8cd872606f4828c7be8bf_33.png)

![](img/4d5f384c19b8cd872606f4828c7be8bf_34.png)

> **重要提示**：软件安装的细节（如仓库地址、包名称）可能会随Docker版本和Linux发行版更新而略有变化。如果在跟随本教程操作时遇到问题，请务必参考[Docker官方安装文档](https://docs.docker.com/engine/install/)以获取最新的指导。同时，欢迎在我们的课程论坛中提出任何疑问。

![](img/4d5f384c19b8cd872606f4828c7be8bf_36.png)

现在，您的Docker环境已经准备就绪，可以继续后续关于Docker使用和容器管理的课程了。