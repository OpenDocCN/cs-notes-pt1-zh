# 072：MySQL数据加密教程 🔐

在本节课中，我们将要学习如何在MySQL数据库中加密敏感数据。我们将探讨两种主要的加密类型：哈希加密和二进制加密，并通过实际操作演示如何使用它们来保护密码等信息。

---

## 概述

在数据库中，以明文形式存储敏感数据是不安全的。如果数据库被入侵或用户拥有未授权访问权限，这些数据将面临风险。为了最小化损失和损害，MySQL提供了多种加密功能。从MySQL 8版本开始，加密功能得到了显著增强。本节课我们将学习这些功能。

---

![](img/b6ba335daefa2c301f291a1377c067b4_1.png)

![](img/b6ba335daefa2c301f291a1377c067b4_2.png)

## 哈希加密与二进制加密的区别

![](img/b6ba335daefa2c301f291a1377c067b4_4.png)

上一节我们介绍了加密的重要性，本节中我们来看看MySQL中两种主要的加密逻辑。

*   **哈希加密**：这是一个单向过程。数据被加密后，生成的哈希值无法被逆向恢复为原始数据。它常用于存储密码。常见的哈希函数包括`MD5`和`SHA`系列。
*   **二进制加密**：这是一个双向过程。数据可以被加密，也可以使用密钥被解密，恢复为原始数据。它适用于需要后续读取的敏感信息，如信用卡号。`AES_ENCRYPT`就是一个例子。

---

![](img/b6ba335daefa2c301f291a1377c067b4_6.png)

## 使用哈希函数加密数据

![](img/b6ba335daefa2c301f291a1377c067b4_8.png)

以下是MySQL中常用的哈希函数及其应用。

![](img/b6ba335daefa2c301f291a1377c067b4_10.png)

### MD5 哈希函数

![](img/b6ba335daefa2c301f291a1377c067b4_12.png)

![](img/b6ba335daefa2c301f291a1377c067b4_14.png)

`MD5`是一种常见的哈希函数，但安全性较低，其生成的哈希值在许多网站上已被破解。

创建用于测试的用户表：
```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE,
    password VARCHAR(255)
);
```

![](img/b6ba335daefa2c301f291a1377c067b4_16.png)

使用`MD5`函数插入加密密码：
```sql
INSERT INTO users (email, password) VALUES ('user@com.br', MD5('password123'));
```

![](img/b6ba335daefa2c301f291a1377c067b4_18.png)

查询结果：
```sql
SELECT * FROM users;
```
你将看到一个由`MD5`生成的固定长度的哈希字符串。

![](img/b6ba335daefa2c301f291a1377c067b4_20.png)

### SHA 系列哈希函数

`SHA`系列函数（如`SHA1`, `SHA2`）比`MD5`更安全。`SHA2`函数允许指定哈希长度（224, 256, 384, 512位），其中512位是目前推荐的最小安全长度。

![](img/b6ba335daefa2c301f291a1377c067b4_22.png)

使用`SHA1`插入数据：
```sql
INSERT INTO users (email, password) VALUES ('user2@com.br', SHA1('password123'));
```

![](img/b6ba335daefa2c301f291a1377c067b4_24.png)

使用更安全的`SHA2`（512位）插入数据：
```sql
INSERT INTO users (email, password) VALUES ('user3@com.br', SHA2('password123', 512));
```

![](img/b6ba335daefa2c301f291a1377c067b4_26.png)

查询并比较不同哈希函数的结果：
```sql
SELECT
    MD5('password123') as md5_hash,
    SHA1('password123') as sha1_hash,
    SHA2('password123', 512) as sha2_hash;
```
可以看到，`SHA2(512)`生成的哈希值最长，也最安全。

---

![](img/b6ba335daefa2c301f291a1377c067b4_28.png)

## 使用二进制加密函数加密数据

上一节我们介绍了不可逆的哈希加密，本节中我们来看看可逆的二进制加密，它适用于需要解密的数据。

使用二进制加密时，存储加密数据的列需要设置为`BLOB`（Binary Large Object）类型，因为它用于存储二进制格式的数据。

![](img/b6ba335daefa2c301f291a1377c067b4_30.png)

创建用于存储二进制加密数据的表：
```sql
CREATE TABLE secure_data (
    id INT PRIMARY KEY AUTO_INCREMENT,
    encrypted_data BLOB
);
```

![](img/b6ba335daefa2c301f291a1377c067b4_32.png)

### ENCODE / DECODE 函数

`ENCODE`和`DECODE`函数较为简单，但在新版本中已被标记为弃用，不过仍可使用。它们需要一个‘秘钥’字符串来进行加解密。

使用`ENCODE`加密并插入数据：
```sql
INSERT INTO secure_data (encrypted_data) VALUES (ENCODE('password123', 'my_secret_key'));
```

使用`DECODE`解密数据：
```sql
SELECT DECODE(encrypted_data, 'my_secret_key') AS decrypted_data FROM secure_data;
```

![](img/b6ba335daefa2c301f291a1377c067b4_34.png)

### AES_ENCRYPT / AES_DECRYPT 函数

`AES_ENCRYPT`和`AES_DECRYPT`是MySQL官方推荐使用的、更强大的加密函数。

![](img/b6ba335daefa2c301f291a1377c067b4_36.png)

使用`AES_ENCRYPT`加密并插入数据：
```sql
INSERT INTO secure_data (encrypted_data) VALUES (AES_ENCRYPT('password123', 'my_strong_secret_key'));
```

使用`AES_DECRYPT`解密数据：
```sql
SELECT AES_DECRYPT(encrypted_data, 'my_strong_secret_key') AS decrypted_data FROM secure_data;
```

---

![](img/b6ba335daefa2c301f291a1377c067b4_38.png)

## 总结

![](img/b6ba335daefa2c301f291a1377c067b4_40.png)

本节课中我们一起学习了MySQL中的数据加密技术。

我们首先区分了**哈希加密**（单向，不可逆，如`MD5`, `SHA2`）和**二进制加密**（双向，可逆，如`AES_ENCRYPT`）的核心概念。对于密码存储，应优先使用强大的哈希函数如`SHA2(‘password‘, 512)`。对于需要还原的数据（如支付信息），则应使用`AES_ENCRYPT`函数，并配合`BLOB`类型的列进行存储。

![](img/b6ba335daefa2c301f291a1377c067b4_42.png)

![](img/b6ba335daefa2c301f291a1377c067b4_43.png)

记住，无论采用何种加密方式，使用强密码并妥善保管加解密密钥都是至关重要的安全实践。