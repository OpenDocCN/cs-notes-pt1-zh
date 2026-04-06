Linux命令行基础：Part3：修改角色 🔧

在本节课中，我们将学习如何使用 `ALTER ROLE` 命令来修改数据库中的角色（Role）。角色是管理用户权限的重要工具，通过修改角色，我们可以调整用户的授权配置。

上一节我们介绍了角色的基本概念和创建方法，本节中我们来看看如何修改一个已存在的角色。

### 命令语法

`ALTER ROLE` 命令的基本语法结构如下：

```sql
ALTER ROLE role_name [ NOT IDENTIFIED | IDENTIFIED BY password ];
```

在这个语法中：
*   `role_name` 是你想要修改的角色的名称。
*   `IDENTIFIED BY password` 用于为角色设置密码验证。
*   `NOT IDENTIFIED` 表示该角色不需要密码验证。

![](img/45d88a6f326fde50d33881301e2f5510_1.png)

这个命令主要用于更改角色的身份验证方式，是数据库安全管理的一部分。

![](img/45d88a6f326fde50d33881301e2f5510_3.png)

![](img/45d88a6f326fde50d33881301e2f5510_5.png)

### 实践操作：修改角色

以下是修改角色的具体操作步骤，我们将通过一个例子来演示。

![](img/45d88a6f326fde50d33881301e2f5510_7.png)

![](img/45d88a6f326fde50d33881301e2f5510_9.png)

首先，我们需要创建一个新用户和一个新角色，并授予角色一些权限。

1.  **创建用户和角色**
    我们创建一个名为 `victor3` 的用户和一个名为 `test2` 的角色。
    ```sql
    CREATE USER victor3 IDENTIFIED BY abc;
    CREATE ROLE test2;
    ```

![](img/45d88a6f326fde50d33881301e2f5510_11.png)

2.  **为角色授予权限**
    接着，我们为 `test2` 角色授予 `SELECT` 和 `CREATE VIEW` 的权限。
    ```sql
    GRANT SELECT, CREATE VIEW TO test2;
    ```

3.  **将角色授予用户**
    然后，我们将 `test2` 角色授予用户 `victor3`。这样，用户 `victor3` 就自动拥有了该角色包含的所有权限。
    ```sql
    GRANT test2 TO victor3;
    ```

现在，角色已经创建并分配好了。接下来，我们尝试以 `victor3` 用户登录并使用这个角色。

![](img/45d88a6f326fde50d33881301e2f5510_13.png)

4.  **用户登录并启用角色**
    以用户 `victor3` 登录数据库后，我们尝试启用 `test2` 角色。
    ```sql
    SET ROLE test2;
    ```
    如果角色设置了密码验证，而我们在启用时没有提供密码，或者角色未被正确授予，此命令可能会失败。

![](img/45d88a6f326fde50d33881301e2f5510_15.png)

5.  **修改角色配置**
    如果上一步失败了，我们需要回到管理员账户修改角色的配置。例如，我们可以将角色设置为不需要密码验证（`NOT IDENTIFIED`）。
    ```sql
    ALTER ROLE test2 NOT IDENTIFIED;
    ```
    修改成功后，再次以 `victor3` 用户执行 `SET ROLE test2;` 命令，就可以成功启用角色了。

`ALTER ROLE` 命令的配置选项相对简单，主要是管理角色的身份验证方式，不像用户配置文件（Profile）那样有丰富的资源限制参数。

### 删除角色

![](img/45d88a6f326fde50d33881301e2f5510_17.png)

![](img/45d88a6f326fde50d33881301e2f5510_19.png)

当我们不再需要一个角色时，可以将其删除。删除角色使用 `DROP ROLE` 命令。

![](img/45d88a6f326fde50d33881301e2f5510_21.png)

以下是删除角色的步骤：

![](img/45d88a6f326fde50d33881301e2f5510_23.png)

1.  **确认角色存在**
    在删除前，可以先查询确认角色是否存在。
    ```sql
    SELECT * FROM DBA_ROLES WHERE ROLE = 'TEST2';
    ```

![](img/45d88a6f326fde50d33881301e2f5510_25.png)

![](img/45d88a6f326fde50d33881301e2f5510_26.png)

2.  **执行删除命令**
    使用 `DROP ROLE` 命令删除指定的角色。请确保没有用户正在使用此角色，否则可能需要先终止相关会话。
    ```sql
    DROP ROLE test2;
    ```

![](img/45d88a6f326fde50d33881301e2f5510_28.png)

![](img/45d88a6f326fde50d33881301e2f5510_29.png)

3.  **验证删除结果**
    删除后，再次执行查询命令，确认该角色已不存在。
    ```sql
    SELECT * FROM DBA_ROLES WHERE ROLE = 'TEST2';
    ```
    如果查询结果没有返回任何行，说明角色已被成功删除。

![](img/45d88a6f326fde50d33881301e2f5510_31.png)

修改和删除角色的命令都非常直接，没有太多复杂的秘密。

![](img/45d88a6f326fde50d33881301e2f5510_33.png)

![](img/45d88a6f326fde50d33881301e2f5510_34.png)

### 总结

![](img/45d88a6f326fde50d33881301e2f5510_36.png)

本节课中我们一起学习了如何修改和删除数据库角色。
我们掌握了 `ALTER ROLE` 命令的语法和用途，并通过实践操作演示了如何更改角色的身份验证设置。
最后，我们也学习了如何使用 `DROP ROLE` 命令来清理不再需要的角色。
这些操作是进行数据库用户权限管理的基础技能。