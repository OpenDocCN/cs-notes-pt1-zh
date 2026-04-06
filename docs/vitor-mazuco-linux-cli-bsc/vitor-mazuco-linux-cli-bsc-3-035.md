# 035：在GitHub上创建新仓库 🚀

在本节课中，我们将学习如何将本地Git仓库与GitHub平台进行同步。主要内容包括创建GitHub账户、配置SSH密钥认证、在GitHub上创建新仓库，以及将本地代码推送到远程仓库。

![](img/5402d5f97b2f2fbe2374d5c6edb33308_1.png)

![](img/5402d5f97b2f2fbe2374d5c6edb33308_3.png)

---

![](img/5402d5f97b2f2fbe2374d5c6edb33308_5.png)

![](img/5402d5f97b2f2fbe2374d5c6edb33308_7.png)

## 概述

上一节我们介绍了Git的基本操作。本节中，我们来看看如何将本地的Git仓库与GitHub这个流行的代码托管平台连接起来。通过配置SSH密钥，我们可以实现安全、便捷的代码同步，而无需每次操作都输入密码。

![](img/5402d5f97b2f2fbe2374d5c6edb33308_9.png)

---

## 创建并配置SSH密钥

首先，需要在本地机器上生成一对SSH密钥（公钥和私钥），并将公钥添加到你的GitHub账户中。这是实现免密码认证的关键步骤。

以下是生成SSH密钥的命令：

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

执行命令后，系统会提示你选择密钥的保存路径（直接按回车使用默认路径即可），并询问是否设置密码（同样可以直接按回车跳过）。命令执行成功后，会在 `~/.ssh/` 目录下生成两个文件：`id_rsa`（私钥）和 `id_rsa.pub`（公钥）。

![](img/5402d5f97b2f2fbe2374d5c6edb33308_11.png)

**注意**：私钥必须严格保密，绝不能泄露。我们只需要将公钥复制到GitHub。

接下来，需要将公钥内容添加到GitHub账户。

![](img/5402d5f97b2f2fbe2374d5c6edb33308_13.png)

![](img/5402d5f97b2f2fbe2374d5c6edb33308_15.png)

1.  登录GitHub，点击右上角头像，进入 **Settings**（设置）。
2.  在左侧边栏找到 **SSH and GPG keys**（SSH和GPG密钥）。
3.  点击 **New SSH key**（新建SSH密钥）。
4.  在“Title”字段为这个密钥起一个名字（例如“My Linux Laptop”）。
5.  将公钥文件（`~/.ssh/id_rsa.pub`）的全部内容复制并粘贴到“Key”字段中。
6.  点击 **Add SSH key**（添加SSH密钥）完成添加。

至此，你的本地机器已经获得了访问GitHub仓库的权限。

---

## 在GitHub上创建新仓库

完成密钥配置后，我们可以在GitHub上创建一个新的远程仓库，用于存放我们的代码。

1.  在GitHub主页，点击右上角的 **+** 号，选择 **New repository**（新建仓库）。
2.  填写仓库信息：
    *   **Repository name**（仓库名称）：例如 `github-test`。
    *   **Description**（描述）：可选，简要描述你的项目。
    *   **Public/Private**（公开/私有）：选择 **Public**（公开），这样所有人都可以查看（私有仓库是付费功能）。
    *   其他选项如初始化README文件、添加.gitignore模板或选择许可证，可以暂时跳过，我们将在本地创建。
3.  点击 **Create repository**（创建仓库）。

![](img/5402d5f97b2f2fbe2374d5c6edb33308_17.png)

仓库创建成功后，页面会显示一个URL。请复制以 `git@github.com:` 开头的 **SSH地址**，例如 `git@github.com:yourusername/github-test.git`。这个地址稍后会用到。

---

## 初始化本地仓库并关联远程仓库

现在，我们回到本地Linux终端，创建一个本地Git仓库，并将其与我们刚刚在GitHub上创建的远程仓库关联起来。

![](img/5402d5f97b2f2fbe2374d5c6edb33308_19.png)

以下是操作步骤：

![](img/5402d5f97b2f2fbe2374d5c6edb33308_21.png)

![](img/5402d5f97b2f2fbe2374d5c6edb33308_22.png)

1.  创建一个新目录并进入：
    ```bash
    mkdir github-test
    cd github-test
    ```

![](img/5402d5f97b2f2fbe2374d5c6edb33308_24.png)

2.  初始化本地Git仓库：
    ```bash
    git init
    ```

3.  创建一个示例文件（例如README.md）并提交：
    ```bash
    echo "# GitHub Test Project" > README.md
    git add README.md
    git commit -m "Initial commit: Add README file"
    ```

![](img/5402d5f97b2f2fbe2374d5c6edb33308_26.png)

4.  将本地仓库与远程GitHub仓库关联。使用上一步复制的SSH地址：
    ```bash
    git remote add origin git@github.com:yourusername/github-test.git
    ```
    这里的 `origin` 是远程仓库的默认别名。

![](img/5402d5f97b2f2fbe2374d5c6edb33308_28.png)

5.  将本地代码推送到远程仓库（GitHub）：
    ```bash
    git push -u origin master
    ```
    如果是第一次连接，可能会提示你确认主机密钥，输入 `yes` 即可。

命令执行成功后，刷新你的GitHub仓库页面，就能看到刚刚推送的 `README.md` 文件和提交记录了。

---

![](img/5402d5f97b2f2fbe2374d5c6edb33308_30.png)

![](img/5402d5f97b2f2fbe2374d5c6edb33308_32.png)

## 验证与进一步操作

![](img/5402d5f97b2f2fbe2374d5c6edb33308_34.png)

推送完成后，可以进行一些验证和后续操作。

![](img/5402d5f97b2f2fbe2374d5c6edb33308_36.png)

*   **查看提交日志**：使用 `git log` 命令可以查看详细的提交历史。
*   **查看特定提交**：使用 `git show <commit-hash>` 可以查看某次提交的详细信息。
*   **创建与推送新分支**：
    ```bash
    git checkout -b new-feature  # 创建并切换到新分支
    # ... 进行一些修改 ...
    git add .
    git commit -m "Add a new feature"
    git push -u origin new-feature # 将新分支推送到远程
    ```
    之后，你可以在GitHub仓库页面的分支下拉列表中看到这个新分支。

![](img/5402d5f97b2f2fbe2374d5c6edb33308_38.png)

![](img/5402d5f97b2f2fbe2374d5c6edb33308_40.png)

---

![](img/5402d5f97b2f2fbe2374d5c6edb33308_42.png)

## 总结

![](img/5402d5f97b2f2fbe2374d5c6edb33308_44.png)

![](img/5402d5f97b2f2fbe2374d5c6edb33308_46.png)

本节课中我们一起学习了如何搭建本地Git环境与GitHub之间的桥梁。我们完成了从生成SSH密钥、在GitHub添加公钥，到创建远程仓库、初始化本地项目并最终实现代码推送的完整流程。掌握这些步骤，你就能够轻松地将本地代码备份到云端，并开始与他人协作。