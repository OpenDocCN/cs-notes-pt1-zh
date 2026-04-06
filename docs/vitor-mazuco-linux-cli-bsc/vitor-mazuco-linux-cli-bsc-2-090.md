# 090：使用Shell运行脚本 🚀

在本节课中，我们将学习如何在MongoDB环境中，使用Shell来运行JavaScript脚本文件。这是一种非常实用的方法，可以让你通过单个命令执行多个JavaScript文件，从而高效地操作数据库。

## 概述

上一节我们介绍了Shell的基本操作。本节中，我们来看看如何利用MongoDB Shell来执行外部的JavaScript脚本。这对于执行批量数据库操作（如更新、创建或删除数据）非常有用，因为MongoDB Shell能够完美地解析和执行JavaScript文件。

## 创建JavaScript脚本文件

首先，我们需要创建一个可以被MongoDB Shell读取的JavaScript文件。这个文件将包含我们想要执行的数据库操作或任何JavaScript代码。

以下是创建和编写一个简单示例脚本的步骤：

1.  **创建文件**：使用文本编辑器创建一个名为 `mongo_script.js` 的文件。
2.  **编写脚本内容**：在该文件中，我们可以写入任何有效的JavaScript代码。例如，我们可以定义变量、执行逻辑，甚至调用MongoDB的数据库命令。

下面是一个简单的脚本示例，它定义了一个数组，并随机打印其中的一个元素：

```javascript
// mongo_script.js 文件内容
var compliments = ['smart', 'intelligent', 'pleasant'];
var index = Math.floor(Math.random() * 3); // 生成0到2的随机数
print('You are ' + compliments[index] + '!');
```

这段代码创建了一个包含三个单词的数组，然后随机选择一个并打印出来。这只是一个演示，用于说明MongoDB Shell可以执行JavaScript逻辑。

## 运行脚本的两种方法

保存并退出编辑器后，我们就有了一个可执行的 `.js` 文件。接下来，我们将学习两种在MongoDB Shell中运行此脚本的方法。

### 方法一：启动Shell时直接运行

第一种方法是在启动MongoDB Shell的同时指定要运行的脚本文件。

操作步骤如下：
*   打开终端。
*   确保终端当前目录与你的脚本文件（`mongo_script.js`）所在目录相同。
*   执行以下命令：
    ```bash
    mongo mongo_script.js
    ```

执行此命令后，Shell会连接到本地的MongoDB服务器，然后自动读取并执行 `mongo_script.js` 文件中的代码。代码执行完毕后，Shell会自动退出。这是一种快速运行一次性脚本的方式。

### 方法二：在已启动的Shell中运行

第二种方法是先启动MongoDB Shell，然后在Shell内部加载并运行脚本。

操作步骤如下：
1.  首先，在终端输入 `mongo` 命令启动Shell。
2.  进入Shell环境后，使用 `load()` 函数来加载脚本。同样，你需要确保Shell的当前工作目录与脚本文件所在目录一致。
3.  输入以下命令：
    ```javascript
    load('mongo_script.js')
    ```

输入命令并按下回车后，Shell会立即执行指定文件中的JavaScript代码。这种方法适合在需要与Shell进行交互式操作的同时运行脚本。

## 扩展应用与展望

通过运行JavaScript脚本，我们不仅可以执行简单的打印操作，还能处理复杂的数据库任务，例如批量插入数据、更新文档或执行聚合查询。

此外，MongoDB Shell的功能不仅限于此。在更高级的配置中，你甚至可以在Shell内部执行一些Linux系统命令。这对于处理数据库备份、文件管理等任务非常有用。我们将在后续更深入的课程中探讨这些高级技巧。

## 总结

本节课中，我们一起学习了在MongoDB Shell中运行JavaScript脚本的两种基本方法：在启动时直接运行和在Shell内部使用 `load()` 函数。这为我们自动化、批量处理数据库操作提供了一个强大而灵活的工具。掌握这个技巧，能显著提升管理MongoDB数据库的效率和便捷性。下一节课，我们将继续探索Shell的其他实用功能。