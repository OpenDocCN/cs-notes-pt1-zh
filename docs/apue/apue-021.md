# 021：Week-04, Segment-6 - 用户与组信息查询 🔍

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_1.png)

在本节课中，我们将要学习如何通过库函数查询系统中的用户和组信息。我们将重点介绍 `getpwuid`、`getpwnam` 等函数，并了解 `/etc/passwd` 和 `/etc/group` 文件的结构与访问方式。

---

## 回顾密码数据库

上一节我们讨论了 `/etc/passwd` 文件及其包含的各种账户字段。然而，我们并未深入探讨用于查询这些信息的库函数。本节将弥补这一空白。

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_3.png)

以下是用于查询用户账户信息的两个核心函数：

```c
struct passwd *getpwuid(uid_t uid);
struct passwd *getpwnam(const char *name);
```

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_5.png)

这两个函数在成功时都会返回一个 `struct passwd` 结构体指针，该结构体包含了 `/etc/passwd` 文件中的所有字段。需要注意的是，在某些系统（如BSD）上，此结构体可能包含额外字段，例如登录类别或账户过期时间。

那么，这些额外信息存储在哪里呢？

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_7.png)

---

## 主密码文件

答案在于 `/etc/master.passwd` 文件。这个文件包含了 `/etc/passwd` 中的所有字段，以及额外的字段，最重要的是，它包含了真正的加密（哈希）密码。

`/etc/passwd` 文件中通常只显示一个星号 `*` 作为占位符，而实际的密码哈希值则存储在 `/etc/master.passwd` 中。这样做是因为 `/etc/passwd` 需要对所有用户可读，以便像 `ls` 这样的命令（通过调用 `getpwuid`）能够将文件所有者的UID转换为用户名，而无需超级用户权限。

`/etc/passwd` 文件本身是通过特定工具从 `/etc/master.passwd` 文件生成的。

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_9.png)

---

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_11.png)

## 函数行为与权限

当普通用户调用 `getpwnam` 时，返回的 `struct passwd` 中的密码字段是星号 `*`。只有当调用者是超级用户（UID 0）时，`getpwnam` 才会从 `/etc/master.passwd` 中返回真实的密码哈希值。

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_13.png)

其他类Unix系统（如Linux）采用类似但不同的机制，它们使用 `/etc/shadow` 文件来存储密码哈希，并提供了 `getspnam` 等单独的函数来访问这些信息。

---

## 遍历所有用户条目

除了查询单个用户，有时我们需要遍历整个密码数据库。为此，我们使用以下函数：

```c
struct passwd *getpwent(void);
void setpwent(void);
void endpwent(void);
```

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_15.png)

`getpwent` 会顺序读取密码数据库（尽管顺序对用户不透明），`setpwent` 将流重置回开头，`endpwent` 则关闭流。

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_17.png)

以下是一个简单的程序示例，展示了如何遍历所有条目或查询特定用户：

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_19.png)

```c
// 示例代码框架：遍历或查询用户
struct passwd *pwd;
// 遍历所有条目
setpwent();
while ((pwd = getpwent()) != NULL) {
    // 打印 pwd->pw_name, pwd->pw_uid 等信息
}
endpwent();

// 查询特定用户（通过UID或用户名）
pwd = getpwnam("username");
// 或
pwd = getpwuid(1000);
```

---

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_21.png)

## 组数据库 `/etc/group`

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_23.png)

现在，让我们来看看组数据库。与用户信息类似，组信息存储在 `/etc/group` 文件中。

该文件每行的格式通常为：
`group_name:password_hash:group_id:user_list`

其中 `user_list` 是一个逗号分隔的用户名列表，标识了该组的成员。

用于查询组信息的库函数与用户函数非常相似：

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_25.png)

```c
struct group *getgrgid(gid_t gid);
struct group *getgrnam(const char *name);
struct group *getgrent(void);
```

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_27.png)

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_29.png)

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_30.png)

---

## 组密码与 `newgrp` 命令

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_32.png)

你可能注意到 `/etc/group` 文件中也有一个密码哈希字段。这源于Unix的早期设计：当时用户一次只能属于一个主要组。如果用户需要访问属于另一个组的文件，他们需要使用 `newgrp` 命令并输入该组的密码来切换主要组。

虽然现代系统通过“附加组”机制解决了这个问题，使得 `newgrp` 命令很少使用，但了解其历史背景仍然很有意义。

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_34.png)

---

## 获取用户的组列表

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_36.png)

获取用户所属的所有组比获取用户信息更复杂一些。用户有一个主要组ID（存储在 `/etc/passwd` 中）和一系列附加组ID（存储在 `/etc/group` 中）。

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_38.png)

POSIX提供了 `getgroups` 系统调用来获取附加组列表，但它不一定包含主要组ID。一个更全面的非标准函数是 `getgrouplist`，在使用前需要检查你的系统是否支持。

以下是一个实现 `groups` 命令功能的简化思路：

1.  获取用户名或UID作为输入。
2.  调用 `getgrouplist` 函数获取该用户所属的所有组ID列表。由于不知道用户属于多少组，可能需要动态分配内存。
3.  对于列表中的每个组ID，调用 `getgrgid` 来获取组名并打印。

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_40.png)

---

## 系统资源访问模式总结

本节课中我们一起学习了两个关键的系统数据库：`/etc/passwd` 和 `/etc/group`，以及访问它们的库函数。这些函数遵循一个通用模式：对于一个存储在 `/etc` 下的纯文本资源，会提供相应的 `getXbyY` 类函数来将资源检索到数据结构中。

我们了解到：
*   处理 `/etc/passwd` 数据相对直接。
*   密码哈希被保存在一个只有UID 0能访问的单独文件中（如 `/etc/master.passwd` 或 `/etc/shadow`）。
*   组可以拥有密码，但这在现代系统中已不常见。
*   遍历用户所属的组比遍历用户密码数据要复杂一些。

这种访问系统资源的一致模式，我们将在未来的课程中看到更多类似的例子。

---

![](img/4d19a7a4b5324d9e2bc25d9110ecc74c_42.png)

在下一讲中，我们将尝试理解“时间”本身在Unix系统中的表示和处理。但正如道格拉斯·亚当斯所言，“时间是一种幻觉，午餐时间更是如此。”我们下节课再见。