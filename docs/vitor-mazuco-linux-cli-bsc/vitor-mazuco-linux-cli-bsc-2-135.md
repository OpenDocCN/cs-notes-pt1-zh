# 135：更改MongoDB oplog大小 🔧

![](img/77012f4519131a7be6b5f8f70a4a82d1_1.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_2.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_3.png)

在本节课中，我们将学习如何在MongoDB中查看和修改oplog的大小。oplog是MongoDB复制集的核心组件，用于记录所有修改数据库状态的操作，以便在副本成员之间同步数据。了解如何管理其大小对于数据库维护至关重要。

![](img/77012f4519131a7be6b5f8f70a4a82d1_4.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_5.png)

上一节我们介绍了MongoDB复制集的基本概念，本节中我们来看看如何实际操作oplog。

![](img/77012f4519131a7be6b5f8f70a4a82d1_6.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_7.png)

## 查看当前oplog状态

![](img/77012f4519131a7be6b5f8f70a4a82d1_8.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_9.png)

首先，我们需要连接到MongoDB实例并查看当前的oplog状态。以下是操作步骤：

![](img/77012f4519131a7be6b5f8f70a4a82d1_10.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_12.png)

1.  使用 `mongo` 命令连接到MongoDB shell。
2.  切换到 `local` 数据库，因为oplog存储在此。
3.  查询 `oplog.rs` 集合以获取信息。

![](img/77012f4519131a7be6b5f8f70a4a82d1_14.png)

具体命令如下：

![](img/77012f4519131a7be6b5f8f70a4a82d1_15.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_17.png)

```javascript
use local
db.oplog.rs.stats()
```

![](img/77012f4519131a7be6b5f8f70a4a82d1_19.png)

执行 `db.oplog.rs.stats()` 命令后，会返回一个包含oplog信息的文档。我们需要关注其中的 `maxSize` 字段，它显示了oplog当前的最大容量（以字节为单位）。

![](img/77012f4519131a7be6b5f8f70a4a82d1_21.png)

## 修改oplog大小

![](img/77012f4519131a7be6b5f8f70a4a82d1_22.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_24.png)

如果发现oplog大小不满足需求，可以在副本集成员上动态修改它。修改操作需要在每个副本集成员上单独执行。

![](img/77012f4519131a7be6b5f8f70a4a82d1_26.png)

以下是修改oplog大小的步骤：

![](img/77012f4519131a7be6b5f8f70a4a82d1_28.png)

1.  以复制集模式重启MongoDB实例，并附带 `--replSet` 和 `--oplogSize` 参数。
2.  `--oplogSize` 参数用于指定新的oplog大小，单位是MB。

![](img/77012f4519131a7be6b5f8f70a4a82d1_30.png)

例如，要将oplog大小设置为8192MB（8GB），可以使用以下命令启动mongod进程：

![](img/77012f4519131a7be6b5f8f70a4a82d1_32.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_34.png)

```bash
mongod --replSet myReplicaSet --oplogSize 8192
```

![](img/77012f4519131a7be6b5f8f70a4a82d1_35.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_36.png)

**核心概念**：oplog大小的计算公式为 `新的oplog大小 = 指定的兆字节数`。例如，`--oplogSize 8192` 即表示将oplog最大容量设置为8192兆字节。

![](img/77012f4519131a7be6b5f8f70a4a82d1_38.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_39.png)

## 验证更改

![](img/77012f4519131a7be6b5f8f70a4a82d1_41.png)

修改完成后，需要验证oplog的新大小是否已生效。

![](img/77012f4519131a7be6b5f8f70a4a82d1_43.png)

重新连接到MongoDB shell，再次运行查看状态的命令：

![](img/77012f4519131a7be6b5f8f70a4a82d1_45.png)

```javascript
use local
db.oplog.rs.stats()
```

![](img/77012f4519131a7be6b5f8f70a4a82d1_47.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_49.png)

检查返回结果中的 `maxSize` 字段，确认其值是否已更新为你所设定的大小。

![](img/77012f4519131a7be6b5f8f70a4a82d1_50.png)

![](img/77012f4519131a7be6b5f8f70a4a82d1_51.png)

本节课中我们一起学习了如何查看MongoDB oplog的当前状态，以及如何通过重启服务并指定 `--oplogSize` 参数来动态调整其大小。正确管理oplog大小是确保MongoDB复制集数据同步稳定高效的重要环节。