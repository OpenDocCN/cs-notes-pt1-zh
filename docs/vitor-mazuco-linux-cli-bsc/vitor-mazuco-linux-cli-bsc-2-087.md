# 087：Shell命令入门

在本节课中，我们将学习MongoDB Shell中的基本命令。这是一个非常基础的课程，旨在帮助你初步认识你的终端、Shell和MongoDB环境。我们将在MongoDB终端中进行大量操作。

## 概述：MongoDB Shell简介

MongoDB Shell是一个功能强大的JavaScript解释器。它不仅能运行JavaScript程序，还能直接执行数学运算和调用标准库函数。其命令语法在不同操作系统间是标准化的，在Linux和Windows上基本没有差异。

## 核心概念：JavaScript解释器

MongoDB Shell的核心是一个完整的JavaScript解释器。这意味着你可以直接在其中执行JavaScript代码。

例如，你可以进行变量赋值和数学计算：
```javascript
x = 200 / 5
```
执行上述代码会得到结果 `40`。

你还可以使用JavaScript的标准库，例如日期对象：
```javascript
new Date()
```
此命令会返回当前日期和时间的ISO格式字符串。

字符串操作也同样支持：
```javascript
"Hello World".replace("World", "MongoDb")
```
这段代码会将字符串中的“World”替换为“MongoDb”。

## 进阶功能：调用JavaScript函数

上一节我们介绍了基础运算，本节中我们来看看如何在Shell中定义和调用自定义函数。

例如，你可以定义一个计算阶乘的函数：
```javascript
function factorial(n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}
```
定义完成后，你可以调用它：
```javascript
factorial(5)
```
此调用会计算5的阶乘，并返回结果 `120`。

## 数据库操作：基础命令

除了纯JavaScript功能，MongoDB Shell还提供了一系列用于操作数据库的命令。虽然MongoDB本身不是关系型数据库，但其开发者设计了一些与SQL语法相似的命令，以降低学习成本。

以下是几个最基础的数据库命令：

*   **`db` 命令**：此命令用于显示当前正在使用的数据库名称。
*   **`use` 命令**：此命令用于切换到你指定的数据库。例如，`use myDatabase` 会切换到名为“myDatabase”的数据库。

## 总结

本节课中我们一起学习了MongoDB Shell的基础知识。我们了解到它是一个功能完备的JavaScript解释器，可以执行计算、处理字符串和定义函数。同时，它也提供了像 `db` 和 `use` 这样的基础命令来操作数据库。对于已经熟悉SQL（如MySQL、PostgreSQL）的用户来说，这些命令会显得非常直观。