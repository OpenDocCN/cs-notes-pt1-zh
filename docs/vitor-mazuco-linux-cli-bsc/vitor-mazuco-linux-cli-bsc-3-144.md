# 144：删除表空间 🗑️

![](img/03ba0ad78b6ef4ec11736f8c35809e73_1.png)

![](img/03ba0ad78b6ef4ec11736f8c35809e73_3.png)

在本节课中，我们将学习如何在Oracle数据库中使用`DROP TABLESPACE`命令来删除表空间。我们将详细讲解其语法、注意事项，并通过实例演示不同场景下的删除操作。

## 概述

`DROP TABLESPACE`命令用于从Oracle数据库中移除一个表空间。删除表空间时，需要根据其是否包含数据对象（如表）或存在约束（如外键）来使用不同的选项，否则操作会失败。

![](img/03ba0ad78b6ef4ec11736f8c35809e73_5.png)

![](img/03ba0ad78b6ef4ec11736f8c35809e73_7.png)

## 基本语法与注意事项

上一节我们介绍了创建表空间，本节中我们来看看如何删除它。`DROP TABLESPACE`命令的基本语法如下：

![](img/03ba0ad78b6ef4ec11736f8c35809e73_9.png)

```sql
DROP TABLESPACE tablespace_name
[INCLUDING CONTENTS [AND DATAFILES] [CASCADE CONSTRAINTS]];
```

![](img/03ba0ad78b6ef4ec11736f8c35809e73_11.png)

![](img/03ba0ad78b6ef4ec11736f8c35809e73_13.png)

以下是使用该命令前必须了解的关键点：
*   如果表空间**非空**（包含任何对象），必须使用`INCLUDING CONTENTS`选项。
*   如果表空间中的对象存在**约束**（例如外键），通常需要添加`CASCADE CONSTRAINTS`选项。
*   无法删除`SYSTEM`表空间。
*   删除操作是永久性的，执行前务必确认。

## 实践操作：删除表空间

我们将通过几个例子来演示不同情况下的删除操作。

### 1. 删除空表空间

首先，我们创建一个空的表空间，然后将其删除。

![](img/03ba0ad78b6ef4ec11736f8c35809e73_15.png)

```sql
-- 创建一个空的表空间
CREATE TABLESPACE space_1 DATAFILE 'space_1.dbf' SIZE 10M;

![](img/03ba0ad78b6ef4ec11736f8c35809e73_17.png)

-- 删除这个空的表空间
DROP TABLESPACE space_1;
```
对于空的表空间，可以直接使用`DROP TABLESPACE`命令。

### 2. 删除包含对象的表空间

接下来，我们创建一个包含表的表空间，并尝试删除它。

![](img/03ba0ad78b6ef4ec11736f8c35809e73_19.png)

![](img/03ba0ad78b6ef4ec11736f8c35809e73_21.png)

```sql
-- 创建第二个表空间
CREATE TABLESPACE space_2 DATAFILE 'space_2.dbf' SIZE 10M;

![](img/03ba0ad78b6ef4ec11736f8c35809e73_23.png)

-- 在该表空间中创建一张表
CREATE TABLE table2 (id NUMBER) TABLESPACE space_2;

![](img/03ba0ad78b6ef4ec11736f8c35809e73_25.png)

-- 尝试删除（不指定INCLUDING CONTENTS会失败）
DROP TABLESPACE space_2; -- 此命令将报错

![](img/03ba0ad78b6ef4ec11736f8c35809e73_27.png)

![](img/03ba0ad78b6ef4ec11736f8c35809e73_29.png)

![](img/03ba0ad78b6ef4ec11736f8c35809e73_31.png)

-- 正确删除包含对象的表空间
DROP TABLESPACE space_2 INCLUDING CONTENTS;
```
当表空间包含对象时，必须使用`INCLUDING CONTENTS`选项来同时删除其中的所有对象。

![](img/03ba0ad78b6ef4ec11736f8c35809e73_33.png)

![](img/03ba0ad78b6ef4ec11736f8c35809e73_34.png)

### 3. 删除存在约束的表空间

![](img/03ba0ad78b6ef4ec11736f8c35809e73_36.png)

最后，我们来看一个更复杂的场景：删除一个其内部表存在外键约束的表空间。

```sql
-- 创建两个表空间
CREATE TABLESPACE space_3 DATAFILE 'space_3.dbf' SIZE 10M;
CREATE TABLESPACE space_4 DATAFILE 'space_4.dbf' SIZE 10M;

![](img/03ba0ad78b6ef4ec11736f8c35809e73_38.png)

-- 在space_3中创建表table3
CREATE TABLE table3 (column1 NUMBER PRIMARY KEY) TABLESPACE space_3;

![](img/03ba0ad78b6ef4ec11736f8c35809e73_40.png)

-- 在space_4中创建表table4，并设置一个引用table3的外键约束
CREATE TABLE table4 (
    column2 NUMBER,
    CONSTRAINT fk_ref FOREIGN KEY (column2) REFERENCES table3(column1)
) TABLESPACE space_4;

![](img/03ba0ad78b6ef4ec11736f8c35809e73_42.png)

-- 尝试删除space_3（即使使用INCLUDING CONTENTS也会因外键约束而失败）
DROP TABLESPACE space_3 INCLUDING CONTENTS; -- 此命令将报错

![](img/03ba0ad78b6ef4ec11736f8c35809e73_44.png)

![](img/03ba0ad78b6ef4ec11736f8c35809e73_46.png)

-- 正确删除存在约束的表空间
DROP TABLESPACE space_3 INCLUDING CONTENTS CASCADE CONSTRAINTS;
```
当表空间中的对象被其他对象引用（存在外键约束）时，必须添加`CASCADE CONSTRAINTS`选项来级联删除这些约束。

![](img/03ba0ad78b6ef4ec11736f8c35809e73_48.png)

## 总结

![](img/03ba0ad78b6ef4ec11736f8c35809e73_50.png)

![](img/03ba0ad78b6ef4ec11736f8c35809e73_52.png)

本节课中我们一起学习了`DROP TABLESPACE`命令的使用。关键点在于：
1.  删除**空表空间**可直接操作。
2.  删除**非空表空间**必须使用`INCLUDING CONTENTS`选项。
3.  当非空表空间中的对象存在**外键约束**时，需要额外添加`CASCADE CONSTRAINTS`选项。
4.  删除操作不可逆，执行前务必确认表空间内容及关联关系。

![](img/03ba0ad78b6ef4ec11736f8c35809e73_54.png)

谨慎使用此命令，并在生产环境中操作前做好备份。