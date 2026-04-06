# 054：使用GROUP BY进行分组统计 📊

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_1.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_3.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_5.png)

在本节课中，我们将学习SQL中的`GROUP BY`子句。它与`ORDER BY`类似，但主要用于配合聚合函数（如`MAX`、`MIN`、`SUM`、`AVG`、`COUNT`）使用，以便将结果集按照一列或多列进行分组，从而生成汇总数据。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_7.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_9.png)

## 理解GROUP BY的基本用法

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_10.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_12.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_14.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_16.png)

上一节我们介绍了排序，本节中我们来看看如何对数据进行分组统计。`GROUP BY`是将数据分组的有效方式，它与`ORDER BY`非常相似。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_18.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_19.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_21.png)

例如，我们有一个名为`Means`的表，使用`COUNT(*)`可以统计总行数，结果为16。但`GROUP BY`能让我们进行更细粒度的统计。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_23.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_24.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_26.png)

以下是使用`GROUP BY`统计每个发件人消息数量的基本命令：
```sql
SELECT source_user, COUNT(*) FROM Means GROUP BY source_user;
```
执行该命令后，我们会得到每个发件人（`source_user`）发送的消息数量。例如，test1发送了3条，Renaldo发送了2条，等等。这样，我们就将计数结果按发件人进行了分组。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_27.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_28.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_30.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_31.png)

## 应用聚合函数进行分析

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_33.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_34.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_36.png)

除了计数，我们还可以使用其他聚合函数来分析分组后的数据。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_37.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_39.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_41.png)

例如，如果我们想查看每个发件人发送的总字节数，可以使用`SUM`函数：
```sql
SELECT source_user, SUM(bytes) FROM Means GROUP BY source_user;
```

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_43.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_44.png)

如果想计算每个发件人发送的每条消息的平均字节数，则使用`AVG`函数：
```sql
SELECT source_user, AVG(bytes) FROM Means GROUP BY source_user;
```
通过这个查询，我们可以得出结论，例如Rinaldo是发送消息最多的人，并且其每条消息的平均字节数也较高。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_46.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_47.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_48.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_50.png)

## 按多列进行分组

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_51.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_53.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_55.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_56.png)

`GROUP BY`的强大之处在于可以按多个列进行分组，从而获得更详细的洞察。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_58.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_60.png)

例如，如果我们想知道每个发件人从每个主机（`source_host`）发送了多少条消息，可以同时按发件人和主机分组：
```sql
SELECT source_user, source_host, COUNT(*) FROM Means GROUP BY source_user, source_host;
```
这个查询会显示组合信息，例如test1从saturn主机发送了2次，从venus主机发送了1次等。这帮助我们了解到，例如Phil是从mars主机发送消息最多的发件人。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_62.png)

## 在分组中查找极值

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_64.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_66.png)

我们还可以在分组中结合使用`MAX`、`MIN`等函数来查找极值。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_68.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_69.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_71.png)

例如，查找每个发件人发送的最大消息的大小以及发送时间：
```sql
SELECT source_user, MAX(bytes), dt FROM Means GROUP BY source_user;
```
这个查询会返回每个发件人发送的最大消息的字节数及其对应的日期时间。例如，可能显示Rinaldo在05:03发送了一条非常大的消息。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_73.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_75.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_76.png)

另一个例子是，查找每对发件人和收件人之间发送的最大消息大小：
```sql
SELECT source_user, dest_user, MAX(bytes) FROM Means GROUP BY source_user, dest_user;
```
这个查询会显示，例如，从Renaldo发送给Jean的消息中，最大的一条尺寸是多少。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_78.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_80.png)

## 结合实际场景举例

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_82.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_83.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_84.png)

让我们将这个概念应用到另一个场景。假设我们有一个司机行程表，我们想知道每位司机的最长行程距离。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_86.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_88.png)

查询可能如下所示：
```sql
SELECT driver_name, MAX(trip_distance) FROM trips GROUP BY driver_name;
```
执行后，我们可能会看到John的最长行程是152公里，Susie是502公里，Lucas是300公里。这样，我们就能轻松找出三位司机各自的最长行程。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_90.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_92.png)

## 总结

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_94.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_95.png)

本节课中我们一起学习了`GROUP BY`子句的核心用法。`GROUP BY`非常实用，因为它允许你将所有信息按照所需的列进行分组汇总。虽然它与`ORDER BY`有相似之处，但功能侧重点不同，你需要根据想要提取的统计信息或函数来选择合适的命令。

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_97.png)

![](img/864b2d5c1d7bea60f7cfe31d04ed9361_99.png)

想象一下在一个拥有数百万条记录的表中（例如电子商务数据），你可以用它来找出最贵的产品、销量最高或最低的产品、退货率最高或最低的产品等等。通过编写简单的SQL语句，你就能获取所有这些关键的汇总信息。