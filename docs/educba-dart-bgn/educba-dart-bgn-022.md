# 022：列表（第二部分）📚

在本节课中，我们将要学习如何在Dart中创建和使用列表。列表是一种用于存储多个相同类型对象的集合。我们将学习如何创建固定长度和可增长长度的列表，如何向列表中添加值，以及如何访问和打印列表中的元素。此外，我们还将了解列表的一些常用属性和方法。

---

上一节我们介绍了列表的基本概念，本节中我们来看看如何具体创建和操作列表。

列表是对象的集合。如果你想将一些相似类型的值放在一起，例如整数值、双精度浮点数值或字符串值，你可以创建一个列表来存储这些同类型的值。这意味着，如果列表是整数类型，那么所有值都必须是整数；如果是双精度浮点数类型，所有值都必须是双精度浮点数；字符串列表也是如此。

列表可以创建为固定长度，这意味着你需要指定列表的大小，例如创建一个包含五个整数值的列表。列表也可以是可增长的，这意味着当向列表中添加值时，列表会自动在后台扩展。

![](img/9098158414e2c103a8db45d98d03c395_1.png)

![](img/9098158414e2c103a8db45d98d03c395_2.png)

我们将首先看一个创建列表的简单示例。我们将创建一个字符串类型的列表来存储星期几的值。

![](img/9098158414e2c103a8db45d98d03c395_3.png)

![](img/9098158414e2c103a8db45d98d03c395_4.png)

![](img/9098158414e2c103a8db45d98d03c395_5.png)

![](img/9098158414e2c103a8db45d98d03c395_6.png)

让我们快速创建另一个文件，命名为 `file4.dart`。由于我们不接受外部输入，只是在本例中创建列表，因此我们从 `void main` 开始。

以下是创建列表的步骤：
1.  使用 `List` 关键字。
2.  指定列表的类型（例如 `String`）。
3.  为列表命名。

![](img/9098158414e2c103a8db45d98d03c395_8.png)

我们创建一个名为 `weekdays` 的列表，它是 `List<String>` 类型，并将包含七个字符串值。这样我们就定义了一个列表。

![](img/9098158414e2c103a8db45d98d03c395_10.png)

```dart
void main() {
  List<String> weekdays = List<String>(7);
}
```

![](img/9098158414e2c103a8db45d98d03c395_12.png)

这是一个固定长度的列表，我们指定了它将自动包含七个值，不能超过七个值。

![](img/9098158414e2c103a8db45d98d03c395_14.png)

那么，如何将值放入这个列表呢？你可以通过索引来赋值。例如，`weekdays[0]` 表示列表的第一个位置，我们在这里放入值 “Monday”。同样地，`weekdays[1]` 放入 “Tuesday”。

在继续插入所有值之前，让我们先插入几个值，然后解释索引的概念。

![](img/9098158414e2c103a8db45d98d03c395_16.png)

```dart
  weekdays[0] = "Monday";
  weekdays[1] = "Tuesday";
  weekdays[2] = "Wednesday";
  weekdays[3] = "Thursday";
  weekdays[4] = "Friday";
  weekdays[5] = "Saturday";
  weekdays[6] = "Sunday";
```

现在我们已经插入了所有值，让我们理解一下索引编号。我们创建了一个包含七个值的列表。索引总是从 **0** 开始。第二个值的索引是 **1**，第三个是 **2**，依此类推，直到第六个索引 **6**。索引号 **6** 对应的是第七个值，因为索引的起始位置是 **0**（即第一个值）。因此，我们通过索引 **0** 到 **6** 存储了七个值。

现在，我们想要打印这个列表。我们将使用 `print` 命令来输出 `weekdays` 列表。

```dart
  print("Values of weekdays list are:");
  print(weekdays);
```

![](img/9098158414e2c103a8db45d98d03c395_18.png)

![](img/9098158414e2c103a8db45d98d03c395_19.png)

如果你执行这个 `file4.dart` 文件，它会以逗号分隔的形式输出列表中的所有值，因为我们创建的是一个字符串数组。

![](img/9098158414e2c103a8db45d98d03c395_21.png)

![](img/9098158414e2c103a8db45d98d03c395_22.png)

![](img/9098158414e2c103a8db45d98d03c395_23.png)

但是，如果你想从用户那里接收输入，然后将值打印回命令行，该怎么办呢？为此，让我们做另一个练习。

![](img/9098158414e2c103a8db45d98d03c395_24.png)

让我们创建另一个文件，命名为 `file5.dart`。在这里，我们将从导入 `dart:io` 库开始，以便处理输入/输出操作。

```dart
import 'dart:io';

![](img/9098158414e2c103a8db45d98d03c395_26.png)

void main() {
  // 创建列表的代码将放在这里
}
```

![](img/9098158414e2c103a8db45d98d03c395_28.png)

![](img/9098158414e2c103a8db45d98d03c395_29.png)

在 `main` 函数中，我们将再次创建一个字符串类型的列表，同样命名为 `weekdays`，它是一个能容纳七个值的 `List<String>`。

```dart
  List<String> weekdays = List<String>(7);
```

![](img/9098158414e2c103a8db45d98d03c395_31.png)

这与之前没有区别，唯一的区别在于，我们不是直接在这里赋值，而是让用户输入值。

![](img/9098158414e2c103a8db45d98d03c395_33.png)

![](img/9098158414e2c103a8db45d98d03c395_35.png)

首先，我们给出提示，要求用户输入列表的第一个值。然后，我们使用 `stdin.readLineSync()` 将输入的值存储到 `weekdays[0]` 中。

![](img/9098158414e2c103a8db45d98d03c395_36.png)

![](img/9098158414e2c103a8db45d98d03c395_37.png)

以下是获取用户输入并填充列表所有七个位置的步骤：

![](img/9098158414e2c103a8db45d98d03c395_39.png)

![](img/9098158414e2c103a8db45d98d03c395_40.png)

```dart
  print("Enter the first value for the list:");
  weekdays[0] = stdin.readLineSync();

  print("Enter the second value for the list:");
  weekdays[1] = stdin.readLineSync();

  print("Enter the third value for the list:");
  weekdays[2] = stdin.readLineSync();

  print("Enter the fourth value for the list:");
  weekdays[3] = stdin.readLineSync();

  print("Enter the fifth value for the list:");
  weekdays[4] = stdin.readLineSync();

  print("Enter the sixth value for the list:");
  weekdays[5] = stdin.readLineSync();

  print("Enter the seventh value for the list:");
  weekdays[6] = stdin.readLineSync();
```

现在，我们已经从用户那里接受了所有值。为了将其打印回来，我们可以说“列表的值是”，然后打印 `weekdays` 列表。

```dart
  print("\nValues of weekdays list are:");
  print(weekdays);
```

![](img/9098158414e2c103a8db45d98d03c395_42.png)

![](img/9098158414e2c103a8db45d98d03c395_43.png)

此外，列表还有一些我们可以使用的属性和方法。例如，我们想知道列表中添加了多少个元素，可以使用 `weekdays.length` 属性。

![](img/9098158414e2c103a8db45d98d03c395_44.png)

以下是列表的一些有用属性：
*   **`length`**: 获取列表中元素的数量。
    ```dart
    print("Length of list is: ${weekdays.length}");
    ```
*   **`first`**: 获取列表中的第一个元素。
    ```dart
    print("First value in list is: ${weekdays.first}");
    ```
*   **`last`**: 获取列表中的最后一个元素。
    ```dart
    print("Last value in list is: ${weekdays.last}");
    ```

![](img/9098158414e2c103a8db45d98d03c395_46.png)

![](img/9098158414e2c103a8db45d98d03c395_47.png)

如果你执行 `file5.dart`，你不需要只输入星期几的值。例如，你可以输入月份：Jan, Feb, March, April, May, June, July。所有输入的值都会在命令行回显打印出来。列表的长度是 **7**，因为我们输入了七个元素。列表的第一个元素是 **Jan**，最后一个元素是 **July**。

在任意编程语言中创建列表的主要优势在于，能够将多个相同类型的值存储在一个列表中。这非常简单易懂。

![](img/9098158414e2c103a8db45d98d03c395_49.png)

---

![](img/9098158414e2c103a8db45d98d03c395_51.png)

本节课中我们一起学习了Dart列表的创建、赋值、访问以及基本属性。我们创建了固定长度的列表，并通过索引为其赋值。我们还学习了如何从用户输入填充列表，并使用 `length`、`first` 和 `last` 等属性来获取列表信息。列表是组织和管理一组相关数据的强大工具。接下来，我们将通过一个关于列表的练习来巩固所学知识，并结束初学者阶段的课程。