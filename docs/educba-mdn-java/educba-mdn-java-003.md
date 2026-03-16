# 003：流与接口

![](img/af25830157e35371d8bd7722b0f6912a_1.png)

在本节课中，我们将学习Java 8引入的两个核心特性：**流（Streams）** 和**接口的默认/静态方法**。我们将通过对比传统代码与使用流的代码，直观地理解流如何使代码更简洁高效。同时，我们将探讨接口的新特性如何增强其灵活性。

![](img/af25830157e35371d8bd7722b0f6912a_3.png)

## 流（Streams）简介

![](img/af25830157e35371d8bd7722b0f6912a_5.png)

![](img/af25830157e35371d8bd7722b0f6912a_7.png)

上一节我们介绍了Lambda表达式，本节中我们来看看如何将其与流（Streams）结合使用。流提供了一种声明式处理数据集合（如列表）的方式，可以极大地简化代码。

![](img/af25830157e35371d8bd7722b0f6912a_9.png)

### 流的简单示例

![](img/af25830157e35371d8bd7722b0f6912a_11.png)

我们将通过一个例子来对比传统循环和使用流的代码。目标是统计一个字符串列表中，长度小于6的字符串数量。

![](img/af25830157e35371d8bd7722b0f6912a_13.png)

以下是使用传统循环的实现代码：

![](img/af25830157e35371d8bd7722b0f6912a_14.png)

```java
import java.util.ArrayList;
import java.util.List;

public class TraditionalExample {
    public static void main(String[] args) {
        List<String> names = new ArrayList<>();
        names.add("Alice");
        names.add("Bob");
        names.add("Charlie");
        names.add("David");
        names.add("Eve");
        names.add("Steve");

        long count = 0;
        for (String name : names) {
            if (name.length() < 6) {
                count++;
            }
        }
        System.out.println("There are " + count + " strings with length less than 6.");
    }
}
```

现在，我们使用流和Lambda表达式来实现相同的功能：

![](img/af25830157e35371d8bd7722b0f6912a_16.png)

```java
import java.util.ArrayList;
import java.util.List;

public class StreamExample {
    public static void main(String[] args) {
        List<String> names = new ArrayList<>();
        names.add("Alice");
        names.add("Bob");
        names.add("Charlie");
        names.add("David");
        names.add("Eve");
        names.add("Steve");

        long count = names.stream()
                          .filter(str -> str.length() < 6)
                          .count();
        System.out.println("There are " + count + " strings with length less than 6.");
    }
}
```

两个程序输出相同的结果，但使用流的代码更加简洁。这展示了流在减少代码行数方面的优势。

### 流的工作原理

现在我们来理解流在Java中是如何工作的。流的操作可以分为三个步骤：

1.  **创建流**：从一个数据源（如集合）创建流。
2.  **中间操作**：对初始流进行转换，生成一个新的流。可以链式调用多个中间操作。在上面的例子中，`filter` 就是一个中间操作。
3.  **终端操作**：对最终的流执行操作，产生一个结果或副作用。在上面的例子中，`count` 就是一个终端操作。

![](img/af25830157e35371d8bd7722b0f6912a_18.png)

以下是流的一些关键特性：

![](img/af25830157e35371d8bd7722b0f6912a_20.png)

*   **不存储元素**：流本身不存储数据，它只是数据源的视图。
*   **不修改源数据**：对流进行的聚合操作（如 `filter`, `map`）不会改变原始数据源，它们总是返回一个新的流。
*   **惰性求值**：中间操作是惰性的，只有在终端操作需要时才会执行。例如，如果使用 `limit(2)`，流在处理完前两个元素后就会停止。

![](img/af25830157e35371d8bd7722b0f6912a_21.png)

![](img/af25830157e35371d8bd7722b0f6912a_22.png)

![](img/af25830157e35371d8bd7722b0f6912a_23.png)

### 流的操作示例

接下来，我们通过几个例子来熟悉流的常见操作。

![](img/af25830157e35371d8bd7722b0f6912a_25.png)

![](img/af25830157e35371d8bd7722b0f6912a_27.png)

**示例1：迭代与筛选**

![](img/af25830157e35371d8bd7722b0f6912a_29.png)

这个例子展示如何生成一个整数流，筛选出能被3整除的数，并限制只取前6个结果。

![](img/af25830157e35371d8bd7722b0f6912a_31.png)

```java
import java.util.stream.Stream;

![](img/af25830157e35371d8bd7722b0f6912a_33.png)

public class StreamIterateExample {
    public static void main(String[] args) {
        Stream.iterate(1, count -> count + 1)
              .filter(number -> number % 3 == 0)
              .limit(6)
              .forEach(System.out::println);
    }
}
```

**示例2：合并两个流**

这个例子展示如何使用 `concat` 方法将两个流合并成一个。

![](img/af25830157e35371d8bd7722b0f6912a_35.png)

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

public class StreamConcatExample {
    public static void main(String[] args) {
        List<String> alphabets = Arrays.asList("A", "B", "C");
        List<String> names = Arrays.asList("John", "Alice");

        Stream<String> combinedStream = Stream.concat(alphabets.stream(), names.stream());
        combinedStream.forEach(str -> System.out.print(str + " "));
    }
}
```

![](img/af25830157e35371d8bd7722b0f6912a_37.png)

![](img/af25830157e35371d8bd7722b0f6912a_39.png)

**示例3：使用 `filter` 操作**

`filter` 是一个中间操作，它根据给定条件过滤流中的元素。

以下是使用 `filter` 筛选出长度大于6的名字：

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

![](img/af25830157e35371d8bd7722b0f6912a_41.png)

![](img/af25830157e35371d8bd7722b0f6912a_43.png)

public class StreamFilterExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Melcandry", "Sansa", "John", "Catelyn");
        Stream<String> allNames = names.stream();
        Stream<String> longNames = allNames.filter(str -> str.length() > 6);
        longNames.forEach(str -> System.out.print(str + " "));
    }
}
```

![](img/af25830157e35371d8bd7722b0f6912a_45.png)

**示例4：`filter` 与 `collect` 结合**

![](img/af25830157e35371d8bd7722b0f6912a_47.png)

![](img/af25830157e35371d8bd7722b0f6912a_49.png)

我们可以将过滤后的流直接收集到一个新的列表中。

![](img/af25830157e35371d8bd7722b0f6912a_51.png)

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

![](img/af25830157e35371d8bd7722b0f6912a_52.png)

public class StreamFilterCollectExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Melcandry", "Sansa", "John", "Catelyn", "Tyrion");
        List<String> longNames = names.stream()
                                      .filter(str -> str.length() > 6)
                                      .collect(Collectors.toList());
        longNames.forEach(System.out::println);
    }
}
```

![](img/af25830157e35371d8bd7722b0f6912a_54.png)

**示例5：`filter` 中使用多个条件**

在 `filter` 中可以使用逻辑运算符组合多个条件。

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

![](img/af25830157e35371d8bd7722b0f6912a_56.png)

![](img/af25830157e35371d8bd7722b0f6912a_57.png)

![](img/af25830157e35371d8bd7722b0f6912a_58.png)

public class StreamMultiFilterExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Sansa", "John", "Catelyn", "Joffrey");
        List<String> longNames = names.stream()
                                      .filter(str -> str.length() > 6 && str.length() < 8)
                                      .collect(Collectors.toList());
        longNames.forEach(System.out::println);
    }
}
```

**示例6：`map` 操作**

`map` 操作将流中的每个元素映射成另一个元素。以下例子计算列表中每个数字的平方。

![](img/af25830157e35371d8bd7722b0f6912a_60.png)

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class StreamMapExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(2, 3, 4, 5, 6);
        List<Integer> squares = numbers.stream()
                                       .map(n -> n * n)
                                       .collect(Collectors.toList());
        System.out.println(squares);
    }
}
```

![](img/af25830157e35371d8bd7722b0f6912a_62.png)

## Java 8 接口的增强

在进入接口新特性之前，我们先回顾一下流的使用。接下来，我们看看Java 8为接口带来的重要变化。

![](img/af25830157e35371d8bd7722b0f6912a_64.png)

在Java 8之前，接口只能包含抽象方法。从Java 8开始，接口可以拥有 **默认方法（Default Methods）** 和 **静态方法（Static Methods）**。

引入默认方法的主要目的是允许开发者向接口添加新方法，而不会破坏已经实现了该接口的现有类。

### 默认方法示例

![](img/af25830157e35371d8bd7722b0f6912a_66.png)

让我们创建一个包含默认方法的接口。

![](img/af25830157e35371d8bd7722b0f6912a_68.png)

![](img/af25830157e35371d8bd7722b0f6912a_70.png)

首先，定义接口 `MyInterface`：

![](img/af25830157e35371d8bd7722b0f6912a_72.png)

```java
public interface MyInterface {
    // 默认方法
    default void newMethod() {
        System.out.println("Newly added default method");
    }
    // 抽象方法
    void existingMethod(String str);
}
```

![](img/af25830157e35371d8bd7722b0f6912a_74.png)

然后，创建一个类来实现这个接口：

![](img/af25830157e35371d8bd7722b0f6912a_76.png)

![](img/af25830157e35371d8bd7722b0f6912a_78.png)

```java
public class MyClass implements MyInterface {
    // 实现抽象方法
    @Override
    public void existingMethod(String str) {
        System.out.println("String is: " + str);
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.newMethod(); // 调用接口的默认方法
        obj.existingMethod("Java 8 is easy to learn");
    }
}
```

![](img/af25830157e35371d8bd7722b0f6912a_80.png)

![](img/af25830157e35371d8bd7722b0f6912a_82.png)

运行 `MyClass`，可以看到默认方法 `newMethod` 被成功调用。

![](img/af25830157e35371d8bd7722b0f6912a_84.png)

### 静态方法示例

![](img/af25830157e35371d8bd7722b0f6912a_86.png)

接口也可以包含静态方法。我们在上面的接口中添加一个静态方法：

```java
public interface MyInterface {
    default void newMethod() {
        System.out.println("Newly added default method");
    }
    // 静态方法
    static void anotherNewMethod() {
        System.out.println("Newly added static method");
    }
    void existingMethod(String str);
}
```

![](img/af25830157e35371d8bd7722b0f6912a_88.png)

在实现类中，可以直接通过接口名调用静态方法：

![](img/af25830157e35371d8bd7722b0f6912a_90.png)

![](img/af25830157e35371d8bd7722b0f6912a_92.png)

```java
public class MyClass implements MyInterface {
    @Override
    public void existingMethod(String str) {
        System.out.println("String is: " + str);
    }

    public static void main(String[] args) {
        MyInterface.anotherNewMethod(); // 调用接口的静态方法
        MyClass obj = new MyClass();
        obj.existingMethod("Hello World");
    }
}
```

![](img/af25830157e35371d8bd7722b0f6912a_94.png)

### 解决默认方法冲突

![](img/af25830157e35371d8bd7722b0f6912a_96.png)

![](img/af25830157e35371d8bd7722b0f6912a_97.png)

当一个类实现了多个接口，而这些接口有同名的默认方法时，就会发生冲突。实现类必须解决这个冲突。

![](img/af25830157e35371d8bd7722b0f6912a_99.png)

定义两个接口：

![](img/af25830157e35371d8bd7722b0f6912a_101.png)

![](img/af25830157e35371d8bd7722b0f6912a_102.png)

```java
interface MyInterface1 {
    default void newMethod() {
        System.out.println("Default method from MyInterface1");
    }
    void existingMethod(String str);
}

interface MyInterface2 {
    default void newMethod() {
        System.out.println("Default method from MyInterface2");
    }
    void disp(String str);
}
```

如果一个类同时实现这两个接口，编译器会报错，因为不知道应该继承哪个 `newMethod`。

```java
// 这会编译错误
public class MyClass implements MyInterface1, MyInterface2 {
    @Override
    public void existingMethod(String str) { }
    @Override
    public void disp(String str) { }
}
```

解决方法是在实现类中重写这个冲突的默认方法：

```java
public class MyClass implements MyInterface1, MyInterface2 {
    @Override
    public void existingMethod(String str) {
        System.out.println("String is: " + str);
    }
    @Override
    public void disp(String str) {
        System.out.println("String is: " + str);
    }
    // 重写冲突的默认方法
    @Override
    public void newMethod() {
        System.out.println("Implementation of default method in MyClass");
        // 也可以选择调用某个父接口的默认方法
        // MyInterface1.super.newMethod();
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.newMethod();
    }
}
```

## 总结

![](img/af25830157e35371d8bd7722b0f6912a_104.png)

本节课中我们一起学习了Java 8的两个强大特性。

![](img/af25830157e35371d8bd7722b0f6912a_105.png)

![](img/af25830157e35371d8bd7722b0f6912a_107.png)

首先，我们深入探讨了**流（Streams）**。我们了解到流通过 `创建流 -> 中间操作 -> 终端操作` 的流水线模式，以声明式风格处理数据。我们练习了 `filter`, `map`, `limit`, `concat`, `collect` 等关键操作，并理解了流**不存储数据**、**不修改源**和**惰性求值**的特点。与传统的循环和条件语句相比，流能让代码更简洁、更易读。

其次，我们学习了**接口的增强**。Java 8允许接口包含默认方法和静态方法。默认方法使接口能够向后兼容地扩展功能，而静态方法允许在接口中定义工具方法。我们还学习了当多个接口存在默认方法冲突时，需要在实现类中重写该方法以解决冲突。

![](img/af25830157e35371d8bd7722b0f6912a_109.png)

![](img/af25830157e35371d8bd7722b0f6912a_110.png)

![](img/af25830157e35371d8bd7722b0f6912a_111.png)

掌握这些现代Java特性，将帮助你编写出更干净、更高效、更易于维护的代码。