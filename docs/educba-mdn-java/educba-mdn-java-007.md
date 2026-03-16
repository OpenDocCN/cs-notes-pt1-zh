# 007：Java 9 模块 🧩

在本节课中，我们将要学习Java 9引入的一个重要新特性：模块系统。我们将探讨为什么需要模块、模块是什么，以及如何在Java中创建和使用模块。

## 为什么需要模块？

Java最重要的特性之一是代码的可重用性。它允许我们通过继承和接口来重用已创建的类。为了高效地重用这些类，Java将它们组织在包中，将相似类型的类放在同一个包里。

随着代码规模的增长，Java中的包数量也随之增加。在处理使用数百个包的大型程序时，很难理解哪个类使用了什么。包是组织类的好方法，但当我们需要在代码中使用多个包时，也需要一种方式来组织包本身。

此外，使类在包之间可重用的唯一方法是将其声明为`public`，但这意味着任何人都可以使用它。这个问题也需要解决。

## 什么是模块？

Java 9引入了一个名为“模块”的新特性来解决上述问题。**模块是一组包的集合**。模块不仅组织包，还负责控制可访问性。这样，我们希望被重用的模块部分可以被使用，而不希望被重用的部分则不能被外部访问。

## 模块的类型

模块中的包主要分为两种类型：
*   **导出包**：旨在模块外部使用。这意味着任何其他模块中的程序都可以使用这些包。
*   **隐藏包**：不打算在模块外部使用。它们是模块内部的，只能在模块内部使用。

为了更好地理解导出包和隐藏包的概念，我们可以以`java.base`模块为例。该模块有许多导出包（如`java.util`、`java.time`），也有一些隐藏包（如`sun.security.provider`）。

## 如何创建和使用模块

![](img/ca263c1ccd13d71e928c17c04d930195_1.png)

模块定义在名为`module-info.java`的文件中。要声明一个包为导出包，需要在`exports`关键字后指定包名。

例如，在`module-info.java`中定义`java.base`模块可能如下所示：
```java
module java.base {
    exports java.util;
    exports java.time;
    exports java.net;
    exports java.lang;
}
```

![](img/ca263c1ccd13d71e928c17c04d930195_3.png)

![](img/ca263c1ccd13d71e928c17c04d930195_4.png)

![](img/ca263c1ccd13d71e928c17c04d930195_5.png)

![](img/ca263c1ccd13d71e928c17c04d930195_6.png)

接下来，我们通过一个具体示例来演示如何创建和使用模块。

![](img/ca263c1ccd13d71e928c17c04d930195_8.png)

### 示例：创建和使用模块

![](img/ca263c1ccd13d71e928c17c04d930195_10.png)

我们将创建两个模块：`hello.world`和`test`。在`hello.world`模块中创建一个类，然后在`test`模块中使用它。

**第一步：创建 `hello.world` 模块**
1.  创建一个名为`hello.world`的模块。
2.  在该模块内，创建一个包`com.hello.world.app`。
3.  在该包中，创建一个名为`HelloWorld`的类，其中包含一个静态方法。
4.  在`hello.world`模块的`module-info.java`文件中，导出`com.hello.world.app`包。

![](img/ca263c1ccd13d71e928c17c04d930195_12.png)

`HelloWorld.java` 内容：
```java
package com.hello.world.app;

public class HelloWorld {
    public static void sayHello() {
        System.out.println("Welcome to module");
    }
}
```

`module-info.java` (位于 `hello.world` 模块) 内容：
```java
module hello.world {
    exports com.hello.world.app;
}
```

![](img/ca263c1ccd13d71e928c17c04d930195_14.png)

**第二步：创建 `test` 模块并使用 `hello.world` 模块**
1.  创建一个名为`test`的模块。
2.  在`test`模块的`module-info.java`文件中，声明需要`hello.world`模块。
3.  在`test`模块内创建一个包（例如`com.test`）和一个主类。
4.  在主类中，导入`com.hello.world.app.HelloWorld`并调用其静态方法。

![](img/ca263c1ccd13d71e928c17c04d930195_16.png)

`module-info.java` (位于 `test` 模块) 内容：
```java
module test {
    requires hello.world;
}
```

![](img/ca263c1ccd13d71e928c17c04d930195_18.png)

`TestMain.java` 内容：
```java
package com.test;

import com.hello.world.app.HelloWorld;

public class TestMain {
    public static void main(String[] args) {
        HelloWorld.sayHello(); // 输出：Welcome to module
    }
}
```

执行`TestMain`类的`main`方法，将成功打印出“Welcome to module”。这个示例展示了如何在Java 9及更高版本中，通过模块系统来组织代码并控制包的可访问性。

---

![](img/ca263c1ccd13d71e928c17c04d930195_20.png)

# 现代Java特性：07：Java 12 新特性示例 🚀

上一节我们介绍了Java 9的模块系统，本节中我们来看看Java 12引入的一些新特性示例，首先是Stream API中Collectors的`teeing`方法。

## Collectors.teeing 方法

![](img/ca263c1ccd13d71e928c17c04d930195_22.png)

![](img/ca263c1ccd13d71e928c17c04d930195_23.png)

Java 12为`Collectors`类添加了一个新的静态方法`teeing`。该方法接受两个收集器和一个合并函数，用于合并这两个收集器的结果。

以下是使用`teeing`方法计算数字列表平均值的示例。

**示例1：计算平均值**

```java
import java.util.Arrays;
import java.util.List;
import static java.util.stream.Collectors.*;

![](img/ca263c1ccd13d71e928c17c04d930195_25.png)

public class TeeingExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(10, 20, 30, 40);

        // 传统方式计算平均值
        double average = 0d;
        for (Integer number : numbers) {
            average += number.doubleValue() / numbers.size();
        }
        System.out.println("Average (traditional): " + average);

        // 使用 teeing 方法计算平均值
        average = numbers.stream()
                .collect(teeing(
                        summingDouble(i -> i), // 第一个收集器：求和
                        counting(),            // 第二个收集器：计数
                        (sum, n) -> sum / n)); // 合并函数：和/计数
        System.out.println("Average (teeing): " + average);
    }
}
```

![](img/ca263c1ccd13d71e928c17c04d930195_27.png)

运行上述代码，两种方式计算出的平均值结果是相同的。`teeing`方法提供了一种更函数式、更简洁的方式在一次流操作中组合多个收集结果。

![](img/ca263c1ccd13d71e928c17c04d930195_29.png)

## 结合过滤与下游收集器

`teeing`方法可以很好地与过滤等操作结合，进行更复杂的数据处理。

![](img/ca263c1ccd13d71e928c17c04d930195_31.png)

![](img/ca263c1ccd13d71e928c17c04d930195_32.png)

![](img/ca263c1ccd13d71e928c17c04d930195_33.png)

![](img/ca263c1ccd13d71e928c17c04d930195_34.png)

以下是筛选出亚洲地区且生活成本低于等于1000的城市示例。

**示例2：筛选并统计城市**

```java
import java.util.stream.Stream;
import static java.util.stream.Collectors.*;

![](img/ca263c1ccd13d71e928c17c04d930195_36.png)

public class TeeingFilterExample {
    public static void main(String[] args) {
        var result = Stream.of(
                        new City("Colombo", "South Asia", 987),
                        new City("Delhi", "South Asia", 914),
                        new City("London", "Western Europe", 1334),
                        new City("Melbourne", "Australia", 3050),
                        new City("Shanghai", "East Asia", 1998),
                        new City("Algiers", "North Africa", 1067),
                        new City("Hanoi", "Southeast Asia", 1331))
                .collect(teeing(
                        // 第一个下游收集器：过滤并映射出符合条件的城市名列表
                        filtering(c -> c.region.contains("Asia") && c.costOfLiving <= 1000,
                                mapping(City::name, toList())),
                        // 第二个下游收集器：计算符合条件的城市数量
                        filtering(c -> c.region.contains("Asia") && c.costOfLiving <= 1000,
                                counting()),
                        // 合并函数：将列表和计数组合成一个结果字符串
                        (list, count) -> "Cities: " + list + ", Count: " + count
                ));
        System.out.println(result); // 输出：Cities: [Colombo, Delhi], Count: 2
    }

    static class City {
        private final String name;
        private final String region;
        private final int costOfLiving;

        public City(String name, String region, int costOfLiving) {
            this.name = name;
            this.region = region;
            this.costOfLiving = costOfLiving;
        }

        public String name() { return name; }
        public String region() { return region; }
        public int costOfLiving() { return costOfLiving; }
    }
}
```

![](img/ca263c1ccd13d71e928c17c04d930195_38.png)

![](img/ca263c1ccd13d71e928c17c04d930195_40.png)

![](img/ca263c1ccd13d71e928c17c04d930195_41.png)

在这个例子中，我们使用`teeing`同时执行了两个操作：一个是获取符合条件的城市名称列表，另一个是计算这些城市的数量。最后通过合并函数将两者结果组合输出。

## 本节课总结

本节课中我们一起学习了两个主要部分：
1.  **Java 9 模块系统**：了解了模块出现的背景、概念（导出包与隐藏包），并通过一个完整的示例掌握了如何创建模块、导出包以及在另一个模块中声明依赖并使用它。模块化是管理大型Java应用复杂性的关键特性。
2.  **Java 12 `Collectors.teeing` 方法**：学习了这个新方法的作用——它允许在一次流操作中组合两个独立的收集器，并通过一个合并函数处理它们的结果。我们通过计算平均值和复杂过滤统计两个例子，演示了其用法，这有助于编写更简洁、表达力更强的流处理代码。

![](img/ca263c1ccd13d71e928c17c04d930195_43.png)

![](img/ca263c1ccd13d71e928c17c04d930195_44.png)

![](img/ca263c1ccd13d71e928c17c04d930195_45.png)

通过掌握这些现代Java特性，你可以编写出结构更清晰、更高效且易于维护的代码。