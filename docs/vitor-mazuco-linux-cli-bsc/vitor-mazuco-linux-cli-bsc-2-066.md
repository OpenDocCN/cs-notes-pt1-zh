# 066：创建MySQL主从复制 🗄️➡️🗄️

在本节课中，我们将学习如何在两台Linux机器之间建立一个简单的MySQL主从复制。我们将配置一台主服务器和一台从服务器，实现数据的实时同步。

![](img/ff7e83a3665a25e4335260fa3e476ab9_1.png)

## 概述

![](img/ff7e83a3665a25e4335260fa3e476ab9_3.png)

![](img/ff7e83a3665a25e4335260fa3e476ab9_4.png)

MySQL主从复制是一种常见的高可用性配置。它允许你将主服务器上的所有数据库、表、内容以及删除操作，自动复制到从服务器上。这为数据提供了安全备份，并能在主服务器出现故障时提供服务。

## 配置主服务器

上一节我们介绍了复制的基本概念，本节中我们来看看如何配置主服务器。

首先，我们需要编辑主服务器的MySQL配置文件 `my.cnf`，添加以下设置：

```ini
server-id = 1
log_bin = /var/log/mysql/mysql-bin.log
binlog_expire_logs_seconds = 864000
max_binlog_size = 100M
binlog_format = ROW
auto_increment_increment = 2
auto_increment_offset = 1
bind-address = 0.0.0.0
```

以下是各配置项的解释：
*   **`server-id = 1`**：为主服务器设置唯一ID。
*   **`log_bin`**：启用二进制日志，记录所有数据更改。
*   **`binlog_expire_logs_seconds`** 和 **`max_binlog_size`**：设置日志的保留时间和最大大小。
*   **`auto_increment_increment`** 和 **`auto_increment_offset`**：用于在多主复制中避免主键冲突，在主从配置中按示例设置即可。
*   **`bind-address = 0.0.0.0`**：允许远程连接。为增强安全性，可以将其改为从服务器的具体IP地址。

![](img/ff7e83a3665a25e4335260fa3e476ab9_6.png)

配置完成后，重启MySQL服务使设置生效：
```bash
sudo systemctl restart mysql
```

![](img/ff7e83a3665a25e4335260fa3e476ab9_8.png)

![](img/ff7e83a3665a25e4335260fa3e476ab9_10.png)

## 创建复制用户

现在，我们需要在主服务器上创建一个专门用于复制的用户。

![](img/ff7e83a3665a25e4335260fa3e476ab9_12.png)

登录到主服务器的MySQL命令行：
```bash
mysql -u root -p
```

![](img/ff7e83a3665a25e4335260fa3e476ab9_14.png)

![](img/ff7e83a3665a25e4335260fa3e476ab9_16.png)

执行以下SQL命令创建用户并授权：
```sql
CREATE USER 'replication_user'@'%' IDENTIFIED BY 'YourStrongPassword123!';
GRANT REPLICATION SLAVE ON *.* TO 'replication_user'@'%';
FLUSH PRIVILEGES;
```

请将 `'YourStrongPassword123!'` 替换为一个强密码。

## 配置从服务器

![](img/ff7e83a3665a25e4335260fa3e476ab9_18.png)

接下来，我们配置从服务器。其步骤与主服务器类似，但有一些关键区别。

![](img/ff7e83a3665a25e4335260fa3e476ab9_20.png)

编辑从服务器的 `my.cnf` 文件，添加以下配置：

```ini
server-id = 2
log_bin = /var/log/mysql/mysql-bin.log
binlog_expire_logs_seconds = 864000
max_binlog_size = 100M
binlog_format = ROW
auto_increment_increment = 2
auto_increment_offset = 2
bind-address = 0.0.0.0
```

![](img/ff7e83a3665a25e4335260fa3e476ab9_22.png)

主要变化是：
*   **`server-id = 2`**：为从服务器设置一个与主服务器不同的唯一ID。
*   **`auto_increment_offset = 2`**：偏移量设置为2，与主服务器区分。

同样，重启从服务器的MySQL服务：
```bash
sudo systemctl restart mysql
```

![](img/ff7e83a3665a25e4335260fa3e476ab9_24.png)

## 获取主服务器状态并建立连接

在从服务器连接到主服务器之前，我们需要从主服务器获取一些关键信息。

![](img/ff7e83a3665a25e4335260fa3e476ab9_26.png)

在主服务器上，登录MySQL并执行：
```sql
SHOW MASTER STATUS;
```

![](img/ff7e83a3665a25e4335260fa3e476ab9_28.png)

命令会返回类似以下的结果，请记录 `File` 和 `Position` 的值：
```
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000006 |      873 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
```

现在，切换到从服务器。登录MySQL后，执行以下命令来配置复制链路。请替换其中的IP地址、文件名和位置为你自己的值：

```sql
STOP SLAVE;

CHANGE MASTER TO
MASTER_HOST='192.168.1.100',
MASTER_PORT=3306,
MASTER_USER='replication_user',
MASTER_PASSWORD='YourStrongPassword123!',
MASTER_LOG_FILE='mysql-bin.000006',
MASTER_LOG_POS=873;

START SLAVE;
```

## 验证复制状态

配置完成后，我们需要检查复制是否正常运行。

![](img/ff7e83a3665a25e4335260fa3e476ab9_30.png)

![](img/ff7e83a3665a25e4335260fa3e476ab9_32.png)

在从服务器上执行以下命令查看复制状态：
```sql
SHOW SLAVE STATUS\G
```

![](img/ff7e83a3665a25e4335260fa3e476ab9_34.png)

在输出结果中，重点关注以下两个字段：
*   **`Slave_IO_Running`**：应为 `Yes`，表示IO线程正常运行。
*   **`Slave_SQL_Running`**：应为 `Yes`，表示SQL线程正常运行。

![](img/ff7e83a3665a25e4335260fa3e476ab9_35.png)

![](img/ff7e83a3665a25e4335260fa3e476ab9_37.png)

如果这两个值都是 `Yes`，说明复制链路已成功建立。

![](img/ff7e83a3665a25e4335260fa3e476ab9_39.png)

![](img/ff7e83a3665a25e4335260fa3e476ab9_40.png)

## 测试复制功能

![](img/ff7e83a3665a25e4335260fa3e476ab9_42.png)

最后，让我们通过一个简单的测试来验证复制是否真正生效。

1.  在主服务器上创建一个新的数据库和表：
    ```sql
    CREATE DATABASE test_replication;
    USE test_replication;
    CREATE TABLE example (id INT, name VARCHAR(50));
    INSERT INTO example VALUES (1, 'Test Data');
    ```

2.  立即切换到从服务器，检查是否同步了数据：
    ```sql
    SHOW DATABASES; -- 应能看到 `test_replication` 数据库
    USE test_replication;
    SELECT * FROM example; -- 应能看到插入的数据 `(1, 'Test Data')`
    ```

如果从服务器上能看到相同的数据库和数据，恭喜你！MySQL主从复制已经配置成功并开始工作。

## 总结

![](img/ff7e83a3665a25e4335260fa3e476ab9_44.png)

本节课中我们一起学习了如何搭建MySQL主从复制。我们逐步完成了配置主服务器和从服务器的 `my.cnf` 文件、创建复制用户、获取主服务器日志状态、在从服务器上建立复制链路，并最终验证和测试了复制功能。这个过程是构建高可用数据库系统的基础，你可以根据需要添加更多从服务器来扩展读取能力或提高数据安全性。