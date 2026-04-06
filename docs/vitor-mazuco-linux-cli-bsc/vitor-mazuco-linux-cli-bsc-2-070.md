# 070：限制用户访问权限

![](img/2391eb842c8fd0e62074df025982e3d1_1.png)

![](img/2391eb842c8fd0e62074df025982e3d1_2.png)

在本节课中，我们将学习一个重要的MySQL安全实践：如何通过限制用户的访问来源来增强数据库的安全性。我们将重点讨论为什么应避免使用通配符主机名，以及如何将其更改为更安全的访问控制规则。

![](img/2391eb842c8fd0e62074df025982e3d1_4.png)

上一节我们介绍了MySQL用户管理的基础知识，本节中我们来看看如何通过细化访问权限来提升安全性。

![](img/2391eb842c8fd0e62074df025982e3d1_6.png)

## 避免使用通配符主机名

![](img/2391eb842c8fd0e62074df025982e3d1_8.png)

在创建MySQL用户时，使用通配符 `%` 作为主机名意味着该用户可以从**任何机器**和**任何位置**进行连接。这会带来严重的安全风险，因为它极大地扩大了潜在的攻击面。

![](img/2391eb842c8fd0e62074df025982e3d1_10.png)

例如，我们在之前课程中创建的 `application` 用户就使用了 `%` 作为主机名：
```sql
CREATE USER 'application'@'%' IDENTIFIED BY 'password';
```
这种设置非常不安全。

![](img/2391eb842c8fd0e62074df025982e3d1_12.png)

## 如何检查和修改用户主机名

![](img/2391eb842c8fd0e62074df025982e3d1_14.png)

![](img/2391eb842c8fd0e62074df025982e3d1_15.png)

![](img/2391eb842c8fd0e62074df025982e3d1_17.png)

你可以使用以下命令查找所有使用了 `%`  主机名的用户：
```sql
SELECT user, host FROM mysql.user WHERE host = '%';
```

![](img/2391eb842c8fd0e62074df025982e3d1_19.png)

![](img/2391eb842c8fd0e62074df025982e3d1_21.png)

发现不安全的用户后，可以使用 `RENAME USER` 命令来修改用户的访问来源。该命令可以同时更改用户名和其允许连接的主机地址。

![](img/2391eb842c8fd0e62074df025982e3d1_23.png)

以下是修改用户主机名的具体方法：

![](img/2391eb842c8fd0e62074df025982e3d1_25.png)

![](img/2391eb842c8fd0e62074df025982e3d1_27.png)

### 方法一：限制为特定IP地址
将用户访问权限限制在特定的内部网络IP地址上，例如 `192.168.0.100`。这样，该用户只能从公司内网的这一台特定机器访问数据库。
```sql
RENAME USER 'application'@'%' TO 'application'@'192.168.0.100';
```

![](img/2391eb842c8fd0e62074df025982e3d1_29.png)

![](img/2391eb842c8fd0e62074df025982e3d1_31.png)

### 方法二：限制为特定域名
如果你的MySQL服务器处于一个域环境中（例如使用Active Directory），可以将主机名设置为域名。这样，只有属于该域的机器才能连接。
```sql
RENAME USER 'application'@'%' TO 'application'@'%.yourcompany.com';
```
或者，更严格地限制为域内的特定主机：
```sql
RENAME USER 'application'@'%' TO 'application'@'mysql-master.yourcompany.com';
```

![](img/2391eb842c8fd0e62074df025982e3d1_33.png)

![](img/2391eb842c8fd0e62074df025982e3d1_35.png)

### 方法三：限制为IP地址段
你也可以使用子网掩码的形式，允许一个IP地址段内的机器访问，这比单个IP更灵活，但比 `%` 更安全。
```sql
RENAME USER 'application'@'%' TO 'application'@'192.168.0.%';
```

![](img/2391eb842c8fd0e62074df025982e3d1_37.png)

## 核心安全建议

![](img/2391eb842c8fd0e62074df025982e3d1_39.png)

![](img/2391eb842c8fd0e62074df025982e3d1_40.png)

![](img/2391eb842c8fd0e62074df025982e3d1_41.png)

综上所述，为了数据库安全，请务必遵循以下原则：
*   **永远不要**在生产环境中使用 `'username'@'%'` 这种形式的用户。
*   始终将用户的主机名限制在**绝对必要的最小范围**内，例如特定的服务器IP、内部网络段或公司域名。
*   定期审查 `mysql.user` 表，确保没有遗留不安全的通配符访问规则。

![](img/2391eb842c8fd0e62074df025982e3d1_43.png)

![](img/2391eb842c8fd0e62074df025982e3d1_45.png)

本节课中我们一起学习了如何通过限制MySQL用户的访问来源来加固数据库。关键点是避免使用通配符 `%` 作为主机名，并将其替换为具体的IP地址、域名或网络段。这是构建安全数据库环境的基础步骤之一。