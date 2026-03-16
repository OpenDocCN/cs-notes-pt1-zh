# 023：Dart列表第三部分 - 员工信息录入程序 📝

![](img/96dc0372395b6dec4e47a586d38434db_0.png)

![](img/96dc0372395b6dec4e47a586d38434db_2.png)

在本节课中，我们将完成一个综合性的编程作业。我们将创建一个程序，用于接收并存储5名员工的姓名、年龄和地址信息，然后将其显示出来。通过这个练习，我们将巩固对Dart列表的理解和应用。

## 概述

我们将创建一个程序，它需要完成以下任务：
1.  创建三个独立的列表，分别用于存储员工的姓名、年龄和地址。
2.  通过控制台依次接收5名员工的上述信息。
3.  将接收到的信息分别存入对应的列表中。
4.  在所有信息录入完毕后，将存储的信息完整地显示出来。

![](img/96dc0372395b6dec4e47a586d38434db_4.png)

这个练习将综合运用我们之前学到的列表创建、数据输入和输出等知识。

![](img/96dc0372395b6dec4e47a586d38434db_6.png)

![](img/96dc0372395b6dec4e47a586d38434db_7.png)

![](img/96dc0372395b6dec4e47a586d38434db_9.png)

## 程序设计与实现

首先，我们需要导入`dart:io`库以支持控制台输入输出操作。然后，在`main`函数中开始构建我们的程序。

![](img/96dc0372395b6dec4e47a586d38434db_11.png)

![](img/96dc0372395b6dec4e47a586d38434db_12.png)

### 第一步：创建列表

程序的第一步是创建三个列表。根据需求，我们需要一个字符串列表来存储姓名，一个整数列表来存储年龄，以及另一个字符串列表来存储地址。每个列表都需要能够容纳5个值。

![](img/96dc0372395b6dec4e47a586d38434db_14.png)

以下是创建列表的代码：

![](img/96dc0372395b6dec4e47a586d38434db_16.png)

![](img/96dc0372395b6dec4e47a586d38434db_18.png)

```dart
import 'dart:io';

![](img/96dc0372395b6dec4e47a586d38434db_20.png)

void main() {
  // 创建存储员工姓名的列表，类型为String，容量为5
  List<String> empName = List.filled(5, '');
  // 创建存储员工年龄的列表，类型为int，容量为5
  List<int> empAge = List.filled(5, 0);
  // 创建存储员工地址的列表，类型为String，容量为5
  List<String> empAddress = List.filled(5, '');
}
```

### 第二步：接收用户输入

![](img/96dc0372395b6dec4e47a586d38434db_22.png)

列表创建完成后，我们需要接收用户输入。由于尚未学习循环结构，我们将为每位员工重复编写输入代码。对于每位员工，我们需要提示用户输入姓名、年龄和地址，并将输入的值存储到列表的相应索引位置。

以下是接收第一位员工信息的代码示例：

![](img/96dc0372395b6dec4e47a586d38434db_24.png)

![](img/96dc0372395b6dec4e47a586d38434db_25.png)

```dart
  // 接收第一位员工的信息（索引为0）
  print('Enter value for Employee 1:');
  
  print('Enter name:');
  empName[0] = stdin.readLineSync()!;
  
  print('Enter age:');
  empAge[0] = int.parse(stdin.readLineSync()!);
  
  print('Enter address:');
  empAddress[0] = stdin.readLineSync()!;
  
  print('-------------------------'); // 分隔线
```

![](img/96dc0372395b6dec4e47a586d38434db_27.png)

**关键点说明：**
*   `stdin.readLineSync()` 用于从控制台读取一行字符串输入。
*   对于年龄（整数），我们需要使用 `int.parse()` 将输入的字符串转换为整数类型。
*   代码末尾的 `print('-------------------------')` 用于在控制台输出中分隔不同员工的信息，使界面更清晰。

![](img/96dc0372395b6dec4e47a586d38434db_29.png)

按照相同的模式，我们需要为第2到第5位员工编写类似的代码块，只需将列表索引分别改为1、2、3、4。

### 第三步：显示存储的信息

所有信息录入完毕后，我们需要将存储的数据打印出来，以验证程序是否正确工作。

![](img/96dc0372395b6dec4e47a586d38434db_31.png)

以下是显示第一位员工信息的代码：

![](img/96dc0372395b6dec4e47a586d38434db_32.png)

![](img/96dc0372395b6dec4e47a586d38434db_33.png)

![](img/96dc0372395b6dec4e47a586d38434db_35.png)

```dart
  // 显示第一位员工的信息
  print('Value entered for Employee 1 is:');
  print('Employee name: ${empName[0]}');
  print('Employee age: ${empAge[0]}');
  print('Employee address: ${empAddress[0]}');
  print('========================='); // 分隔线
```

![](img/96dc0372395b6dec4e47a586d38434db_37.png)

同样，我们需要为其他四位员工复制并修改这段代码，调整索引值。

![](img/96dc0372395b6dec4e47a586d38434db_39.png)

![](img/96dc0372395b6dec4e47a586d38434db_40.png)

## 程序运行示例

当程序运行时，控制台交互可能如下所示：

![](img/96dc0372395b6dec4e47a586d38434db_42.png)

```
Enter value for Employee 1:
Enter name:
张三
Enter age:
30
Enter address:
北京路1号
-------------------------
Enter value for Employee 2:
Enter name:
李四
Enter age:
40
Enter address:
上海路2号
-------------------------
... (依次输入3、4、5号员工信息)

Value entered for Employee 1 is:
Employee name: 张三
Employee age: 30
Employee address: 北京路1号
=========================
Value entered for Employee 2 is:
Employee name: 李四
Employee age: 40
Employee address: 上海路2号
=========================
... (依次显示3、4、5号员工信息)
```

![](img/96dc0372395b6dec4e47a586d38434db_44.png)

## 总结与展望

![](img/96dc0372395b6dec4e47a586d38434db_45.png)

![](img/96dc0372395b6dec4e47a586d38434db_46.png)

本节课中，我们一起完成了一个Dart列表的综合应用作业。我们创建了多个列表来存储不同类型的数据，通过标准输入接收用户信息，并最终将数据输出。

这个程序虽然功能完整，但代码存在大量重复。正如视频中所提到的，目前我们通过手动复制代码块来处理5位员工的信息，这使程序显得冗长。在后续的中级课程中，我们将学习**循环结构**（例如 `for` 或 `while` 循环）。使用循环，可以用几行代码替代现在的大量重复代码，让程序变得简洁高效。例如，接收5位员工信息的核心逻辑可以简化为：

```dart
for (int i = 0; i < 5; i++) {
  print('Enter value for Employee ${i + 1}:');
  print('Enter name:');
  empName[i] = stdin.readLineSync()!;
  // ... 接收年龄和地址
}
```

![](img/96dc0372395b6dec4e47a586d38434db_48.png)

通过本课程的基础部分，我们已经学习了Dart编程的概述、语法、关键字、变量与数据类型、各类运算符（算术、赋值、逻辑、关系、位运算）以及常量的使用，并通过大量示例进行了实践。这个员工信息管理程序为初学者阶段画上了一个圆满的句号，也为学习更高级的编程概念打下了坚实的基础。