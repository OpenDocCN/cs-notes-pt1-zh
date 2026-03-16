# 005：数组并行排序与Java 9接口私有方法 🚀

![](img/fa26a2e7d764e756452b84587ff71b0f_1.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_3.png)

在本节课中，我们将学习Java 8引入的数组并行排序功能，以及Java 9中接口私有方法的新特性。我们将通过具体示例来理解这些概念，并学习如何编写更简洁、高效的代码。

![](img/fa26a2e7d764e756452b84587ff71b0f_5.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_6.png)

## 字符串合并器（StringJoiner）进阶用法

![](img/fa26a2e7d764e756452b84587ff71b0f_7.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_8.png)

上一节我们介绍了`StringJoiner`的基本用法，本节中我们来看看它的其他实用方法，例如如何设置空值、获取长度以及转换为字符串。

![](img/fa26a2e7d764e756452b84587ff71b0f_10.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_11.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_12.png)

以下是`StringJoiner`类中`setEmptyValue`、`length`和`toString`方法的示例：

![](img/fa26a2e7d764e756452b84587ff71b0f_14.png)

```java
import java.util.StringJoiner;

![](img/fa26a2e7d764e756452b84587ff71b0f_16.png)

public class StringJoinerExample {
    public static void main(String[] args) {
        StringJoiner myString = new StringJoiner(",");
        myString.setEmptyValue("这是一个默认字符串");
        System.out.println("默认字符串: " + myString);

        myString.add("Apple");
        myString.add("Banana");
        myString.add("Orange");
        myString.add("Kiwi");
        myString.add("Grapes");
        System.out.println("添加值后的字符串: " + myString);

        int length = myString.length();
        System.out.println("StringJoiner的长度: " + length);

        String s = myString.toString();
        System.out.println("转换为字符串: " + s);
    }
}
```

运行此代码，首先会输出设置的默认字符串，然后输出添加了所有水果名称的合并字符串。接着，程序会计算并打印`StringJoiner`对象的字符长度，最后展示通过`toString`方法转换后的标准字符串。

## 数组并行排序（Arrays.parallelSort）

现在，我们进入Java 8的核心新特性之一：数组并行排序。此方法位于`java.util.Arrays`类中，旨在利用多线程并行处理来加速大型数组的排序。

**并行排序的原理**是：将一个大数组递归地分割成多个子数组，直到每个子数组达到一个最小粒度。然后，多个线程并行地对这些子数组进行排序，最后使用归并排序算法将已排序的子数组合并起来。

![](img/fa26a2e7d764e756452b84587ff71b0f_18.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_19.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_20.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_22.png)

### 示例1：对基本数据类型数组进行并行排序

![](img/fa26a2e7d764e756452b84587ff71b0f_24.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_25.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_27.png)

让我们看一个对整型数组进行并行排序的简单例子。

![](img/fa26a2e7d764e756452b84587ff71b0f_29.png)

```java
import java.util.Arrays;

![](img/fa26a2e7d764e756452b84587ff71b0f_31.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_33.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_35.png)

public class ParallelSortExample1 {
    public static void main(String[] args) {
        int[] numbers = {9, 2, 7, 1, 5, 8, 3, 6, 4};
        Arrays.parallelSort(numbers);
        Arrays.stream(numbers).forEach(n -> System.out.print(n + " "));
    }
}
```

![](img/fa26a2e7d764e756452b84587ff71b0f_37.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_39.png)

执行这段代码，输出结果将是按升序排列的数组：`1 2 3 4 5 6 7 8 9`。`Arrays.parallelSort(numbers)`方法完成了所有工作。

![](img/fa26a2e7d764e756452b84587ff71b0f_40.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_42.png)

### 示例2：指定范围进行并行排序

`parallelSort`方法还允许我们只对数组的特定区间进行排序，通过指定起始索引（包含）和结束索引（不包含）来实现。

![](img/fa26a2e7d764e756452b84587ff71b0f_44.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_46.png)

```java
import java.util.Arrays;

![](img/fa26a2e7d764e756452b84587ff71b0f_48.png)

public class ParallelSortExample2 {
    public static void main(String[] args) {
        int[] numbers = {9, 2, 7, 1, 5, 8, 3, 6, 4};
        // 对索引从 1（第二个元素）到 5（第六个元素，不包含）的部分进行排序
        Arrays.parallelSort(numbers, 1, 5);
        Arrays.stream(numbers).forEach(n -> System.out.print(n + " "));
    }
}
```

在这个例子中，只有原数组中索引1到4的元素（即值`2, 7, 1, 5`）被排序。输出结果为：`9 1 2 5 7 8 3 6 4`。第一个元素`9`和索引5之后的元素`8, 3, 6, 4`保持了原来的顺序。

![](img/fa26a2e7d764e756452b84587ff71b0f_50.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_51.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_52.png)

## Java 9 接口私有方法 🔒

![](img/fa26a2e7d764e756452b84587ff71b0f_53.png)

Java 8允许在接口中定义`default`和`static`方法，以便向后兼容。Java 9在此基础上更进一步，引入了**接口私有方法**。

**引入私有方法的主要目的**是：消除多个`default`或`static`方法中的重复代码，通过私有方法来共享这些公共逻辑，从而使接口设计更清晰，代码更易维护。

![](img/fa26a2e7d764e756452b84587ff71b0f_55.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_56.png)

### 示例1：Java 8中重复代码的问题

![](img/fa26a2e7d764e756452b84587ff71b0f_58.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_60.png)

首先，我们看一个Java 8接口的例子，其中两个`default`方法包含了相同的代码块。

![](img/fa26a2e7d764e756452b84587ff71b0f_62.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_64.png)

```java
// Java 8 接口示例
interface MyInterfaceP1 {
    default void method1() {
        System.out.println("Starting method");
        System.out.println("Doing something");
        System.out.println("This is method 1");
    }

    default void method2() {
        System.out.println("Starting method");
        System.out.println("Doing something");
        System.out.println("This is method 2");
    }
}

![](img/fa26a2e7d764e756452b84587ff71b0f_66.png)

class DemoClass26 implements MyInterfaceP1 {
    public static void main(String[] args) {
        DemoClass26 obj = new DemoClass26();
        obj.method1();
        obj.method2();
    }
}
```

输出两段相同的“Starting method”和“Doing something”。这导致了代码冗余。

![](img/fa26a2e7d764e756452b84587ff71b0f_68.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_69.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_70.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_71.png)

### 示例2：Java 9使用私有方法优化

![](img/fa26a2e7d764e756452b84587ff71b0f_73.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_75.png)

现在，我们用Java 9的私有方法来重构上面的接口，将公共代码提取到一个私有方法中。

![](img/fa26a2e7d764e756452b84587ff71b0f_77.png)

```java
// Java 9 接口示例
interface MyInterfaceP2 {
    default void method1() {
        printCommonLines(); // 调用私有方法
        System.out.println("This is method 1");
    }

    default void method2() {
        printCommonLines(); // 调用同一个私有方法
        System.out.println("This is method 2");
    }

    // Java 9 允许的私有实例方法
    private void printCommonLines() {
        System.out.println("Starting method");
        System.out.println("Doing something");
    }
}

![](img/fa26a2e7d764e756452b84587ff71b0f_79.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_81.png)

class DemoClass27 implements MyInterfaceP2 {
    public static void main(String[] args) {
        DemoClass27 obj = new DemoClass27();
        obj.method1();
        obj.method2();
    }
}
```

输出结果与之前相同，但接口内部的代码消除了重复，更加简洁。

### 示例3：接口中的私有静态方法

![](img/fa26a2e7d764e756452b84587ff71b0f_83.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_84.png)

Java 9还支持**私有静态方法**，主要用于在接口的多个`static`方法之间共享代码。

![](img/fa26a2e7d764e756452b84587ff71b0f_86.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_87.png)

```java
// Java 9 接口示例（包含私有静态方法）
interface MyInterfaceP3 {
    static void method1() {
        printStaticCommonLines(); // 调用私有静态方法
        System.out.println("This is static method 1");
    }

    static void method2() {
        printStaticCommonLines(); // 调用同一个私有静态方法
        System.out.println("This is static method 2");
    }

    // Java 9 允许的私有静态方法
    private static void printStaticCommonLines() {
        System.out.println("Starting static method");
        System.out.println("Doing something static");
    }

    default void myMethods() {
        method1(); // 调用静态方法
        method2();
    }
}

![](img/fa26a2e7d764e756452b84587ff71b0f_88.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_90.png)

class DemoClass28 implements MyInterfaceP3 {
    public static void main(String[] args) {
        DemoClass28 obj = new DemoClass28();
        obj.myMethods(); // 通过默认方法调用静态方法
    }
}
```

![](img/fa26a2e7d764e756452b84587ff71b0f_92.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_94.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_96.png)

在这个例子中，两个静态方法`method1`和`method2`共享了私有静态方法`printStaticCommonLines`中的代码。`default`方法`myMethods`则展示了如何在实例方法中调用这些接口静态方法。

![](img/fa26a2e7d764e756452b84587ff71b0f_98.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_100.png)

## 总结 📚

![](img/fa26a2e7d764e756452b84587ff71b0f_102.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_104.png)

本节课中我们一起学习了：
1.  **`StringJoiner`的进阶功能**：包括设置空值、获取长度和转换为字符串。
2.  **Java 8 数组并行排序**：使用`Arrays.parallelSort()`方法高效排序大型数组，并可以指定排序范围。
3.  **Java 9 接口私有方法**：包括私有实例方法和私有静态方法。它们的主要价值在于**消除接口中`default`和`static`方法的代码冗余**，提升代码的复用性和可读性，是编写整洁高效Java代码的重要工具。

![](img/fa26a2e7d764e756452b84587ff71b0f_106.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_107.png)

![](img/fa26a2e7d764e756452b84587ff71b0f_108.png)

通过掌握这些现代Java特性，你可以让代码更加模块化、高效，并易于维护。