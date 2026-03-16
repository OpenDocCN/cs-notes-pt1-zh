# 006：try-with-resources增强与Java 9新特性

在本节课中，我们将要学习Java 9中对`try-with-resources`语句的增强，以及其他一些重要的新特性，包括菱形操作符的扩展、`@SafeVarargs`注解的增强以及Stream API的新增方法。我们将通过简单的代码示例来理解这些特性如何帮助我们编写更简洁、更高效的代码。

## try-with-resources 增强

![](img/abbc069e6a32dad9222cddc0648fbeea_1.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_2.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_3.png)

上一节我们介绍了Java 7中引入的`try-with-resources`语句。本节中我们来看看Java 9对其进行的重大增强。

![](img/abbc069e6a32dad9222cddc0648fbeea_5.png)

`try-with-resources`语句的主要优势在于它能自动关闭所有资源（如文件、数据库连接、网络连接等），无需显式调用`close()`方法。这可以防止内存泄漏，并减少不必要的代码行，使代码更具可读性。

首先，我们回顾一下Java 7中的用法，然后看看Java 9如何解决了Java 7中存在的一些问题。

![](img/abbc069e6a32dad9222cddc0648fbeea_7.png)

以下是Java 7中使用`try-with-resources`的示例代码：

![](img/abbc069e6a32dad9222cddc0648fbeea_9.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_10.png)

```java
import java.io.FileNotFoundException;
import java.io.FileOutputStream;

![](img/abbc069e6a32dad9222cddc0648fbeea_11.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_12.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_13.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_15.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_17.png)

public class TryWithResourcesJava7 {
    public static void main(String[] args) throws FileNotFoundException {
        try (FileOutputStream fileOutputStream = new FileOutputStream("book.txt")) {
            String myString = "Writing this line in the output file.";
            byte[] byteArray = myString.getBytes();
            fileOutputStream.write(byteArray);
            System.out.println("The given string is written in the file successfully.");
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

![](img/abbc069e6a32dad9222cddc0648fbeea_19.png)

Java 7中的`try-with-resources`语句存在一个问题：它不允许在语句或块的作用域之外声明资源。这导致在某些情况下需要编写冗余的代码。

以下是Java 7中一个会引发编译错误的示例：

![](img/abbc069e6a32dad9222cddc0648fbeea_21.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_23.png)

```java
import java.io.FileNotFoundException;
import java.io.FileOutputStream;

![](img/abbc069e6a32dad9222cddc0648fbeea_25.png)

public class ProblemExample {
    public static void main(String[] args) throws FileNotFoundException {
        FileOutputStream fileOutputStream = new FileOutputStream("book.txt");
        // 错误：资源必须在try-with-resources中声明
        try (fileOutputStream) { // 编译错误
            String myString = "Writing this line in the output file.";
            byte[] byteArray = myString.getBytes();
            fileOutputStream.write(byteArray);
            System.out.println("The given string is written in the file successfully.");
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

![](img/abbc069e6a32dad9222cddc0648fbeea_27.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_29.png)

为了解决上述错误，在Java 9之前，我们需要一种变通方法，即在`try-with-resources`中再次引用已存在的资源对象，这显得非常繁琐。

![](img/abbc069e6a32dad9222cddc0648fbeea_31.png)

Java 9增强的`try-with-resources`允许我们使用在外部声明的资源，只要该资源是`final`或实际上是`final`的（即未被重新赋值）。这使得代码更加简洁。

![](img/abbc069e6a32dad9222cddc0648fbeea_33.png)

以下是Java 9中的正确写法：

```java
import java.io.FileNotFoundException;
import java.io.FileOutputStream;

public class TryWithResourcesJava9 {
    public static void main(String[] args) throws FileNotFoundException {
        // 资源在外部声明
        FileOutputStream fileOutputStream = new FileOutputStream("book.txt");
        // Java 9允许在try-with-resources中使用已存在的final或effectively final变量
        try (fileOutputStream) {
            String myString = "Writing this line in the output file.";
            byte[] byteArray = myString.getBytes();
            fileOutputStream.write(byteArray);
            System.out.println("The given string is written in the file successfully.");
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

![](img/abbc069e6a32dad9222cddc0648fbeea_35.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_36.png)

通过这个增强，我们避免了创建冗余对象，代码逻辑更加清晰。

## 菱形操作符与匿名内部类

![](img/abbc069e6a32dad9222cddc0648fbeea_38.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_39.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_40.png)

上一节我们介绍了资源管理的改进，本节中我们来看看Java 9对菱形操作符的扩展。

![](img/abbc069e6a32dad9222cddc0648fbeea_42.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_44.png)

菱形操作符（`<>`）在Java 7中引入，其目的是通过省略表达式右侧的泛型类型来避免冗余代码。然而，在Java 7中，菱形操作符不能与匿名内部类一起使用。

![](img/abbc069e6a32dad9222cddc0648fbeea_46.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_48.png)

首先，我们看一个Java 7中菱形操作符在普通类中正常工作的例子：

![](img/abbc069e6a32dad9222cddc0648fbeea_50.png)

```java
// 抽象类
abstract class MyCalc<T> {
    abstract T add(T num1, T num2);
}

public class DiamondOperatorJava7 {
    public static void main(String[] args) {
        // 使用菱形操作符，编译器推断类型为Integer
        MyCalc<Integer> obj = new MyCalc<>() {
            @Override
            Integer add(Integer x, Integer y) {
                return x + y;
            }
        };
        Integer sum = obj.add(100, 101);
        System.out.println(sum); // 输出 201
    }
}
```

但在Java 7中，如果将菱形操作符用于匿名内部类，则会导致编译错误。

![](img/abbc069e6a32dad9222cddc0648fbeea_52.png)

以下是Java 7中会出错的示例：

```java
abstract class MyCalc1<T> {
    abstract T add(T num1, T num2);
}

public class DiamondOperatorError {
    public static void main(String[] args) {
        // 在Java 7中，这行会编译错误：cannot infer type arguments for anonymous inner classes
        MyCalc1<Integer> obj = new MyCalc1<>() { // 错误！
            @Override
            Integer add(Integer x, Integer y) {
                return x + y;
            }
        };
        Integer sum = obj.add(100, 101);
        System.out.println(sum);
    }
}
```

Java 9改进了菱形操作符的使用，允许我们将其与匿名内部类一起使用。上面的代码在Java 9及更高版本中可以正常编译和运行。

![](img/abbc069e6a32dad9222cddc0648fbeea_54.png)

## @SafeVarargs 注解增强

接下来，我们看看Java 9中对`@SafeVarargs`注解的增强。

`@SafeVarargs`注解用于抑制由包含可变参数（varargs）的方法引发的“unsafe operation”警告。在Java 9之前，此注解只能用于`final`、`static`方法或构造器，因为这些方法不能被重写（重写的方法仍可能对其可变参数执行不安全的操作）。

![](img/abbc069e6a32dad9222cddc0648fbeea_56.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_57.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_58.png)

Java 9扩展了`@SafeVarargs`注解的使用范围，现在它也可以用于`private`方法，因为私有方法同样不能被重写。

![](img/abbc069e6a32dad9222cddc0648fbeea_60.png)

以下是使用`@SafeVarargs`注解的示例：

```java
import java.util.ArrayList;
import java.util.List;

![](img/abbc069e6a32dad9222cddc0648fbeea_62.png)

public class SafeVarargsExample {
    // Java 9允许对私有方法使用@SafeVarargs
    @SafeVarargs
    private void printList(List<String>... names) {
        for (List<String> nameList : names) {
            for (String name : nameList) {
                System.out.println(name);
            }
        }
    }

    public static void main(String[] args) {
        SafeVarargsExample obj = new SafeVarargsExample();
        List<String> list1 = new ArrayList<>();
        list1.add("Kevin");
        list1.add("Rick");
        List<String> list2 = new ArrayList<>();
        list2.add("Negan");
        list2.add("Suhu");
        // 传递可变数量的列表参数
        obj.printList(list1, list2);
    }
}
```

如果不使用`@SafeVarargs`注解，编译器可能会产生关于可变参数潜在堆污染的警告。使用该注解可以安全地消除这些警告。

## Stream API 增强

![](img/abbc069e6a32dad9222cddc0648fbeea_64.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_65.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_67.png)

最后，我们探讨Java 9中Stream API的几项重要增强。Java 8引入了Stream API，而Java 9为其添加了四个新方法：`dropWhile`、`takeWhile`、`iterate`和`ofNullable`。由于`Stream`是一个接口，这些新增的方法都是`default`或`static`方法。

### dropWhile 方法

![](img/abbc069e6a32dad9222cddc0648fbeea_69.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_71.png)

`dropWhile`方法会丢弃流中所有元素，直到给定的谓词（条件）返回`false`为止。

![](img/abbc069e6a32dad9222cddc0648fbeea_73.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_75.png)

以下是`dropWhile`方法的示例：

![](img/abbc069e6a32dad9222cddc0648fbeea_77.png)

```java
import java.util.stream.Stream;

public class StreamDropWhileExample {
    public static void main(String[] args) {
        Stream<Integer> myStream = Stream.of(11, 22, 40, 60, 100);
        // 丢弃所有小于50的元素
        myStream.dropWhile(num -> num < 50)
                .forEach(num -> System.out.println(num));
        // 输出：60, 100
    }
}
```

### takeWhile 方法

`takeWhile`方法与`dropWhile`相反，它会获取流中的元素，直到给定的谓词返回`false`为止。

![](img/abbc069e6a32dad9222cddc0648fbeea_79.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_80.png)

以下是`takeWhile`方法的示例：

```java
import java.util.stream.Stream;

public class StreamTakeWhileExample {
    public static void main(String[] args) {
        Stream<Integer> myStream = Stream.of(17, 20, 30, 40, 60, 91, 120);
        // 只获取小于50的元素
        myStream.takeWhile(num -> num < 50)
                .forEach(num -> System.out.println(num));
        // 输出：17, 20, 30, 40
    }
}
```

### iterate 方法

![](img/abbc069e6a32dad9222cddc0648fbeea_82.png)

Java 9为`Stream.iterate`方法添加了一个新的重载版本，它接受三个参数：初始值、谓词（迭代继续的条件）和更新函数（用于生成下一个值）。

以下是`iterate`方法的示例：

```java
import java.util.stream.Stream;

public class StreamIterateExample {
    public static void main(String[] args) {
        // 参数：初始值1，条件n<200，更新函数n->n*3
        Stream.iterate(1, n -> n < 200, n -> n * 3)
                .forEach(num -> System.out.println(num));
        // 输出：1, 3, 9, 27, 81
    }
}
```

### ofNullable 方法

![](img/abbc069e6a32dad9222cddc0648fbeea_84.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_86.png)

`ofNullable`方法用于避免`NullPointerException`。如果传入的元素为`null`，则返回一个空的流；如果非空，则返回一个包含该单个元素的顺序流。

![](img/abbc069e6a32dad9222cddc0648fbeea_88.png)

![](img/abbc069e6a32dad9222cddc0648fbeea_90.png)

以下是`ofNullable`方法的示例：

```java
import java.util.stream.Stream;

public class StreamOfNullableExample {
    public static void main(String[] args) {
        String possibleNullString = null;
        // 安全地处理可能为null的值
        Stream<String> myStream = Stream.ofNullable(possibleNullString);
        myStream.forEach(str -> System.out.println(str)); // 无输出，流为空

        String nonNullString = "Hello";
        Stream<String> myStream2 = Stream.ofNullable(nonNullString);
        myStream2.forEach(System.out::println); // 输出：Hello
    }
}
```

![](img/abbc069e6a32dad9222cddc0648fbeea_92.png)

## 总结

本节课中我们一起学习了Java 9中的几个关键特性增强：
1.  **try-with-resources增强**：允许使用在外部声明的`final`或`effectively final`资源变量，简化了代码。
2.  **菱形操作符扩展**：现在可以与匿名内部类一起使用，提高了泛型代码的简洁性。
3.  **@SafeVarargs注解增强**：可以应用于`private`方法，更灵活地抑制可变参数警告。
4.  **Stream API新方法**：引入了`dropWhile`、`takeWhile`、新的`iterate`重载和`ofNullable`方法，使得流操作更加强大和易于处理边界情况（如`null`值）。

![](img/abbc069e6a32dad9222cddc0648fbeea_94.png)

这些特性共同助力开发者编写出更简洁、更健壮、更易维护的Java代码。