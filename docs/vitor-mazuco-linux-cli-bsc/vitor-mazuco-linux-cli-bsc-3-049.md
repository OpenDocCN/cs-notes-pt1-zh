# 049：使用用户模块 👤

在本节课中，我们将学习如何使用 Ansible 的 `user` 模块来管理远程主机上的用户账户。我们将涵盖如何检查、创建、修改用户属性、设置密码以及删除用户。

---

## 检查与创建用户

上一节我们介绍了 Ansible 的基本模块。本节中我们来看看如何使用 `user` 模块来检查并创建用户。

我们可以使用 `user` 模块来检查特定用户是否存在，如果不存在则创建它。以下是执行此操作的基本命令格式：

```bash
ansible all -m user -a "name=web state=present"
```

这个命令会在所有主机上检查名为 `web` 的用户。如果该用户不存在，Ansible 会自动创建它。命令执行后，输出会以颜色区分状态：
*   **绿色**：表示创建了新用户。
*   **黄色**：表示用户已存在，配置有变更。
*   **红色**：表示执行过程中出现了错误。

例如，为所有主机创建 `web` 用户后，输出会显示用户ID、家目录和使用的shell（如bash）等信息。

---

## 修改用户属性

创建用户后，我们可能需要修改其属性，例如用户ID（UID）。

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_1.png)

我们可以使用相同的 `user` 模块，并指定 `uid` 参数来更改用户的ID。以下命令将 `web` 用户的UID更改为2000：

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_3.png)

```bash
ansible all -m user -a "name=web uid=2000"
```

执行此命令将更新所有主机上 `web` 用户的UID。除了 `uid`，你还可以修改其他属性，例如用户所属的组（`group`）、登录shell（`shell`）或家目录路径（`home`）。所有可用的参数都可以在 Ansible 的官方文档中找到。

---

## 使用模式匹配与主机组

在管理多台主机时，我们可能只想对特定主机执行操作。Ansible 提供了灵活的方式来选择目标主机。

以下是两种常用的方法：

1.  **使用通配符**：你可以使用星号 (`*`) 作为通配符来匹配主机名。例如，`web*` 会匹配所有以 `web` 开头的主机（如 web1, web2, web10）。
    ```bash
    ansible ‘web*’ -m ping
    ```

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_5.png)

2.  **使用主机组**：在 Ansible 的清单文件（`hosts`）中，你可以将主机分组。例如，定义一个 `[web_servers]` 组，然后直接对该组执行命令。
    ```bash
    ansible web_servers -m ping
    ```

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_6.png)

良好的主机命名和分组习惯，能极大简化日常的维护和管理工作。

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_8.png)

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_10.png)

---

## 创建具有Sudo权限的用户

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_12.png)

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_14.png)

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_16.png)

有时我们需要创建具有 `sudo` 权限的用户，以便执行需要特权的命令。

以下命令创建一个名为 `webtest` 的用户，并赋予其 `sudo` 权限（使用 `-b` 和 `-K` 参数）：
```bash
ansible web_servers -m user -a “name=webtest state=present” -b -K
```
*   `-b` (`--become`): 指示 Ansible 以特权身份运行任务。
*   `-K` (`--ask-become-pass`): 提示输入特权密码（通常是远程主机的root或sudo用户密码）。

执行后，用户 `webtest` 就被创建并加入了sudoers列表。为了简化操作，你可以在 Ansible 配置文件中设置 `become: true` 等默认参数，这样就不必每次都在命令行中指定 `-b` 和 `-K`。

---

## 为用户设置登录密码

默认情况下，`user` 模块创建的用户没有登录密码。为了安全，Linux 系统要求密码必须以加密形式存储。

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_18.png)

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_20.png)

我们需要借助 Python 的 `passlib` 库来生成加密密码。首先确保安装它：
```bash
pip install passlib  # 或 pip3 install passlib
```

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_22.png)

安装后，使用以下命令为用户创建加密密码：
```bash
ansible web_servers -m user -a ‘name=webtest password={{ “123456” | password_hash(“sha512”) }} update_password=always’
```
*   `password`: 参数值使用 Jinja2 过滤器 `password_hash` 将明文 “123456” 加密为 sha512 哈希值。
*   `update_password: always`: 确保密码总是被更新为指定值。

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_24.png)

执行成功后，你就可以使用用户名 `webtest` 和密码 `123456` 通过 SSH 登录远程主机了。请注意，出于安全考虑，实际环境中应使用强密码。

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_26.png)

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_28.png)

---

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_30.png)

## 删除用户

最后，我们来看看如何删除用户。

使用 `user` 模块的 `state=absent` 参数可以删除用户。添加 `remove=yes` 和 `force=yes` 参数会同时删除用户的家目录和邮件池。
```bash
ansible web_servers -m user -a “name=webtest state=absent remove=yes force=yes”
```

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_32.png)

**重要提示**：执行删除操作前，请务必确认该用户的数据已备份或不再需要，因为此操作不可逆。

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_34.png)

---

![](img/d8e93b3329c7c025af4ae99a6c7f4c2c_36.png)

本节课中我们一起学习了 Ansible `user` 模块的核心功能。我们掌握了如何检查、创建、修改属性、设置密码以及删除远程主机上的用户账户。`user` 模块是 Ansible 上千个模块中的一个典型代表，每个模块都有其特定用途。熟练掌握查阅官方文档的方法，是高效使用 Ansible 进行自动化运维的关键。