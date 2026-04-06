# 059：使用注册变量 📝

在本节课中，我们将学习如何在 Ansible 中使用注册变量。注册变量的核心功能是捕获一个任务的输出，并将其存储在内存中，以便后续使用。这在调试、条件判断或执行特定脚本等场景中非常有用。

![](img/4d8d4e48c6d6067b099380da0237e729_1.png)

## 理解注册变量

上一节我们介绍了 Ansible 的基本任务结构，本节中我们来看看如何捕获任务的输出。注册变量的关键字是 `register`。它的作用是捕获一个任务（例如执行 Shell 命令）的结果，并将其保存为一个变量。之后，我们可以在其他任务中引用这个变量，进行条件判断、调试输出或触发其他操作。

## 实践案例：监控系统用户数量

Linux 系统通常有多个用户，包括系统创建的用户（如运行 Apache 服务的 `apache` 用户）和手动创建的用户。我们将创建一个 Ansible Playbook 来检查系统用户数量是否超过预设的阈值。

![](img/4d8d4e48c6d6067b099380da0237e729_3.png)

### 1. 获取用户数量的 Shell 命令

![](img/4d8d4e48c6d6067b099380da0237e729_4.png)

首先，我们需要一个能获取系统用户数量的命令。以下命令适用于大多数 Linux 发行版（如 CentOS、Fedora、Ubuntu、Debian）：

```bash
getent passwd | wc -l
```
这个命令会输出一个数字，代表当前系统的用户总数。

### 2. 创建 Playbook 文件

![](img/4d8d4e48c6d6067b099380da0237e729_6.png)

![](img/4d8d4e48c6d6067b099380da0237e729_8.png)

让我们创建一个名为 `count_users.yml` 的 Playbook 文件。以下是文件的基本结构和变量定义：

![](img/4d8d4e48c6d6067b099380da0237e729_10.png)

```yaml
---
- name: 检测用户数量是否超过主机限制
  hosts: all
  vars:
    max_alert: 30
  tasks:
```

![](img/4d8d4e48c6d6067b099380da0237e729_12.png)

我们在 `vars` 部分定义了一个变量 `max_alert`，其值为 `30`，作为用户数量的警报阈值。

![](img/4d8d4e48c6d6067b099380da0237e729_14.png)

![](img/4d8d4e48c6d6067b099380da0237e729_15.png)

![](img/4d8d4e48c6d6067b099380da0237e729_17.png)

### 3. 使用 `shell` 模块和 `register` 关键字

![](img/4d8d4e48c6d6067b099380da0237e729_19.png)

接下来，我们创建第一个任务，使用 `shell` 模块执行命令，并用 `register` 关键字捕获输出。

以下是任务列表：

*   **任务一：统计用户数量**
    此任务执行 Shell 命令来统计用户总数，并将结果注册到变量 `user_count` 中。
    ```yaml
    - name: 统计所有用户
      shell: "getent passwd | wc -l"
      register: user_count
    ```

*   **任务二：调试输出**
    为了查看捕获的结果，我们使用 `debug` 模块。`user_count.stdout` 包含了命令的标准输出。
    ```yaml
    - name: 显示用户数量
      debug:
        msg: "用户数量是 {{ user_count.stdout }}， 警报上限是 {{ max_alert }}"
    ```

*   **任务三：条件判断与警报**
    最后，我们创建一个条件任务。仅当用户数量超过 `max_alert` 阈值时，才会执行此任务并输出警报信息。
    ```yaml
    - name: 检测是否达到限制
      debug:
        msg: "警告：用户数量已达到限制！"
      when: user_count.stdout|int > max_alert|int
    ```
    注意：我们使用 `|int` 过滤器将字符串输出转换为整数，以便进行数值比较。

![](img/4d8d4e48c6d6067b099380da0237e729_21.png)

![](img/4d8d4e48c6d6067b099380da0237e729_23.png)

### 4. 运行与测试 Playbook

保存文件后，可以在终端中运行此 Playbook。为了节省时间，我们可以使用 `-l` 参数限制只在特定主机（如 `web1`）上运行。

![](img/4d8d4e48c6d6067b099380da0237e729_25.png)

```bash
ansible-playbook count_users.yml -l web1
```

运行后，你将看到类似以下的输出：
1.  第一个任务执行 Shell 命令并注册结果。
2.  第二个任务输出当前用户数量（例如 `38`）和设定的上限（`30`）。
3.  由于 `38 > 30`，条件成立，第三个任务会执行并打印警告信息。

### 5. 修改阈值进行测试

![](img/4d8d4e48c6d6067b099380da0237e729_27.png)

![](img/4d8d4e48c6d6067b099380da0237e729_29.png)

现在，让我们将 `vars` 部分的 `max_alert` 值修改为 `50`，然后再次运行 Playbook。

![](img/4d8d4e48c6d6067b099380da0237e729_31.png)

```yaml
  vars:
    max_alert: 50
```

![](img/4d8d4e48c6d6067b099380da0237e729_33.png)

再次运行后，你会发现：
*   用户数量（`38`）没有超过新的上限（`50`）。
*   因此，第三个任务的条件 `when` 不成立，该任务会被 **跳过**（`skipped`）。

![](img/4d8d4e48c6d6067b099380da0237e729_35.png)

![](img/4d8d4e48c6d6067b099380da0237e729_37.png)

你还可以添加一个 `else` 情形的任务，当用户数量未超限时给出提示：

![](img/4d8d4e48c6d6067b099380da0237e729_39.png)

```yaml
- name: 用户数量未超限
  debug:
    msg: "用户数量在安全范围内。"
  when: user_count.stdout|int <= max_alert|int
```

![](img/4d8d4e48c6d6067b099380da0237e729_41.png)

## 总结

![](img/4d8d4e48c6d6067b099380da0237e729_43.png)

本节课中我们一起学习了 Ansible 注册变量 `register` 的核心用法。我们通过一个监控系统用户数量的实际案例，掌握了如何：
1.  使用 `shell` 模块执行命令。
2.  使用 `register` 关键字将命令输出保存到变量。
3.  使用 `debug` 模块查看注册的变量值。
4.  使用 `when` 条件语句基于注册变量的值来控制任务执行。

![](img/4d8d4e48c6d6067b099380da0237e729_45.png)

![](img/4d8d4e48c6d6067b099380da0237e729_47.png)

注册变量是 Ansible 自动化中非常强大的功能，它允许你将任务串联起来，根据前一个任务的结果动态决定后续操作，极大地增强了 Playbook 的灵活性和实用性。