# 004：Stream收集器与字符串连接器

![](img/824b9a4e3845cecdad3fbce1316edf44_0.png)

在本节课中，我们将学习Java中两个强大的工具：Stream收集器（Collectors）和字符串连接器（StringJoiner）。我们将通过具体示例，了解如何使用它们来简化数据处理和字符串拼接任务。

## 概述

![](img/824b9a4e3845cecdad3fbce1316edf44_2.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_3.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_4.png)

Stream收集器是`java.util.stream.Collectors`类提供的工具，用于将流（Stream）中的元素汇总成各种形式的结果，例如集合、映射或统计值。字符串连接器是Java 8引入的`StringJoiner`类，用于以指定的分隔符、前缀和后缀高效地连接多个字符串。

---

![](img/824b9a4e3845cecdad3fbce1316edf44_6.png)

## Stream收集器示例

![](img/824b9a4e3845cecdad3fbce1316edf44_8.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_10.png)

上一节我们介绍了Stream API的基本概念，本节中我们来看看如何使用收集器对数据进行分组、转换和聚合。

### 示例1：使用`groupingBy`和`counting`进行分组计数

此示例演示如何使用`Collectors.groupingBy`方法对列表元素进行分组，并计算每个元素的出现次数。

![](img/824b9a4e3845cecdad3fbce1316edf44_12.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_13.png)

以下是实现步骤：

![](img/824b9a4e3845cecdad3fbce1316edf44_15.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_17.png)

1.  导入必要的包：`java.util.*`, `java.util.function.Function`, `java.util.stream.Collectors`。
2.  创建一个字符串列表。
3.  使用流的`collect`方法，传入`Collectors.groupingBy(Function.identity(), Collectors.counting())`。
4.  打印结果映射。

![](img/824b9a4e3845cecdad3fbce1316edf44_18.png)

```java
import java.util.*;
import java.util.function.Function;
import java.util.stream.Collectors;

![](img/824b9a4e3845cecdad3fbce1316edf44_20.png)

public class GroupingByExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Steve", "John", "Aji", "John", "Aji", "Aji");
        Map<String, Long> result = names.stream()
                                        .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
        System.out.println(result);
    }
}
```
执行上述代码，输出结果为：`{Steve=1, John=2, Aji=3}`。

![](img/824b9a4e3845cecdad3fbce1316edf44_22.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_24.png)

### 示例2：从对象列表中提取特定字段到新列表

这个例子展示如何从一个学生对象列表中，提取所有学生的姓名到一个新的字符串列表中。

![](img/824b9a4e3845cecdad3fbce1316edf44_26.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_28.png)

以下是实现步骤：

![](img/824b9a4e3845cecdad3fbce1316edf44_30.png)

1.  创建一个`Student`类，包含`id`、`name`、`age`字段。
2.  创建一个`Student`对象列表。
3.  使用流的`map`方法将每个`Student`对象转换为其`name`字段。
4.  使用`Collectors.toList()`将流转为一个新的列表。

![](img/824b9a4e3845cecdad3fbce1316edf44_32.png)

```java
import java.util.*;
import java.util.stream.Collectors;

![](img/824b9a4e3845cecdad3fbce1316edf44_34.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_35.png)

class Student {
    int id;
    String name;
    int age;
    public Student(int id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
}

public class MapExample {
    public static void main(String[] args) {
        List<Student> studentList = new ArrayList<>();
        studentList.add(new Student(11, "Jon", 22));
        studentList.add(new Student(22, "Steve", 18));
        studentList.add(new Student(33, "Lucy", 22));
        studentList.add(new Student(44, "Sana", 23));
        studentList.add(new Student(55, "Maggie", 18));

        List<String> names = studentList.stream()
                                        .map(n -> n.name)
                                        .collect(Collectors.toList());
        System.out.println(names);
    }
}
```
输出结果为：`[Jon, Steve, Lucy, Sana, Maggie]`。

### 示例3：使用过滤器与收集器获取符合条件的对象集合

![](img/824b9a4e3845cecdad3fbce1316edf44_37.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_38.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_39.png)

此示例在上一个例子的基础上，增加了过滤条件，只收集年龄大于22岁的学生，并将结果存入一个`Set`。

![](img/824b9a4e3845cecdad3fbce1316edf44_40.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_42.png)

以下是实现步骤：

![](img/824b9a4e3845cecdad3fbce1316edf44_43.png)

1.  沿用之前的`Student`类和列表。
2.  使用流的`filter`方法，筛选出`id > 22`的学生。
3.  使用`Collectors.toSet()`将结果收集到一个`Set`集合中。
4.  遍历并打印`Set`中的学生信息。

![](img/824b9a4e3845cecdad3fbce1316edf44_45.png)

```java
// ... Student 类定义同上
import java.util.Set;

![](img/824b9a4e3845cecdad3fbce1316edf44_47.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_48.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_49.png)

public class FilterExample {
    public static void main(String[] args) {
        List<Student> studentList = new ArrayList<>();
        // ... 添加学生对象同上

        Set<Student> students = studentList.stream()
                                           .filter(n -> n.id > 22)
                                           .collect(Collectors.toSet());
        for(Student stu : students) {
            System.out.println(stu.id + " " + stu.name + " " + stu.age);
        }
    }
}
```
输出结果将显示`id`大于22的学生信息。

![](img/824b9a4e3845cecdad3fbce1316edf44_51.png)

### 示例4：使用`averagingInt`计算平均值

![](img/824b9a4e3845cecdad3fbce1316edf44_53.png)

这个例子演示如何使用`Collectors.averagingInt`方法计算学生列表的平均年龄。

![](img/824b9a4e3845cecdad3fbce1316edf44_55.png)

以下是实现步骤：

![](img/824b9a4e3845cecdad3fbce1316edf44_57.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_59.png)

1.  创建一个新的`Student1`类（或复用之前的`Student`类）。
2.  创建学生列表并添加数据。
3.  使用流的`collect`方法，传入`Collectors.averagingInt(s -> s.age)`。
4.  打印计算出的平均年龄。

```java
import java.util.*;
import java.util.stream.Collectors;

class Student1 {
    int id;
    String name;
    int age;
    public Student1(int id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
}

![](img/824b9a4e3845cecdad3fbce1316edf44_61.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_63.png)

public class AverageExample {
    public static void main(String[] args) {
        List<Student1> studentList = new ArrayList<>();
        studentList.add(new Student1(11, "Jon", 20));
        studentList.add(new Student1(22, "Steve", 18));
        studentList.add(new Student1(33, "Lucy", 22));
        studentList.add(new Student1(44, "Sana", 23));
        studentList.add(new Student1(55, "Maggie", 18));

        Double avgAge = studentList.stream()
                                   .collect(Collectors.averagingInt(s -> s.age));
        System.out.println("The average age of students is: " + avgAge);
    }
}
```
输出结果为：`The average age of students is: 20.4`。

![](img/824b9a4e3845cecdad3fbce1316edf44_65.png)

---

## 字符串连接器示例

了解了Stream收集器后，我们再来看看另一个实用工具——字符串连接器（StringJoiner），它专门用于优雅地拼接字符串。

### 示例1：使用指定分隔符连接字符串

![](img/824b9a4e3845cecdad3fbce1316edf44_67.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_68.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_69.png)

此示例展示如何使用`StringJoiner`，以连字符“-”作为分隔符来连接多个字符串。

![](img/824b9a4e3845cecdad3fbce1316edf44_70.png)

以下是实现步骤：

1.  导入`java.util.StringJoiner`。
2.  创建`StringJoiner`实例，指定分隔符为“-”。
3.  使用`add`方法添加多个字符串。
4.  打印最终连接好的字符串。

![](img/824b9a4e3845cecdad3fbce1316edf44_72.png)

```java
import java.util.StringJoiner;

public class StringJoinerDemo1 {
    public static void main(String[] args) {
        StringJoiner myString = new StringJoiner("-");
        myString.add("Logan");
        myString.add("Magneto");
        myString.add("Rogue");
        myString.add("Storm");
        System.out.println(myString);
    }
}
```
输出结果为：`Logan-Magneto-Rogue-Storm`。

### 示例2：添加前缀和后缀

这个例子在连接字符串的同时，为最终结果添加方括号作为前缀和后缀。

以下是实现步骤：

![](img/824b9a4e3845cecdad3fbce1316edf44_74.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_75.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_76.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_77.png)

1.  创建`StringJoiner`实例时，依次传入分隔符“,”、前缀“\[”和后缀“\]”。
2.  添加多个字符串。
3.  打印结果。

```java
import java.util.StringJoiner;

public class StringJoinerDemo2 {
    public static void main(String[] args) {
        StringJoiner myString = new StringJoiner(",", "[", "]");
        myString.add("Logan");
        myString.add("Magneto");
        myString.add("Rogue");
        myString.add("Storm");
        System.out.println(myString);
    }
}
```
输出结果为：`[Logan,Magneto,Rogue,Storm]`。

![](img/824b9a4e3845cecdad3fbce1316edf44_79.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_80.png)

### 示例3：合并两个StringJoiner对象

![](img/824b9a4e3845cecdad3fbce1316edf44_82.png)

此示例演示如何将两个`StringJoiner`对象的内容合并到一起。

以下是实现步骤：

![](img/824b9a4e3845cecdad3fbce1316edf44_84.png)

1.  创建第一个`StringJoiner`对象`myString`，使用逗号分隔并添加前缀后缀。
2.  创建第二个`StringJoiner`对象`myAnotherString`，使用连字符分隔并添加不同的前缀后缀。
3.  使用第一个对象的`merge`方法合并第二个对象。
4.  分别打印合并前后的字符串。

![](img/824b9a4e3845cecdad3fbce1316edf44_85.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_86.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_87.png)

```java
import java.util.StringJoiner;

![](img/824b9a4e3845cecdad3fbce1316edf44_89.png)

public class StringJoinerDemo3 {
    public static void main(String[] args) {
        StringJoiner myString = new StringJoiner(",", "[", "]");
        myString.add("Logan");
        myString.add("Magneto");
        myString.add("Rogue");
        myString.add("Storm");
        System.out.println("First String: " + myString);

        StringJoiner myAnotherString = new StringJoiner("-", "P", "S");
        myAnotherString.add("Sansa");
        myAnotherString.add("Imp");
        myAnotherString.add("Jon");
        myAnotherString.add("Ned");
        System.out.println("Second String: " + myAnotherString);

        StringJoiner mergedString = myString.merge(myAnotherString);
        System.out.println("Merged String: " + mergedString);
    }
}
```
输出结果将显示两个独立的字符串以及合并后的新字符串。

![](img/824b9a4e3845cecdad3fbce1316edf44_91.png)

---

## 总结

![](img/824b9a4e3845cecdad3fbce1316edf44_93.png)

本节课中我们一起学习了Java Stream收集器（Collectors）和字符串连接器（StringJoiner）的核心用法。

![](img/824b9a4e3845cecdad3fbce1316edf44_95.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_97.png)

*   **Stream收集器** 提供了强大的终端操作，可以轻松实现分组、计数、映射、过滤和聚合（如求平均值）等复杂的数据转换操作，让流处理代码更加简洁高效。
*   **字符串连接器** 则提供了一种清晰、可控的方式来拼接多个字符串，支持自定义分隔符、前缀和后缀，避免了传统`StringBuilder`手动处理分隔符的繁琐。

![](img/824b9a4e3845cecdad3fbce1316edf44_99.png)

![](img/824b9a4e3845cecdad3fbce1316edf44_101.png)

掌握这两个工具，能显著提升处理集合数据和字符串拼接任务的代码质量和开发效率。