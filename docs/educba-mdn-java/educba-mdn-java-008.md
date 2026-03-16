# 008：Java 12中的紧凑数字格式 📊

![](img/ee09b2c2e51f74aee8e63de8e19cacca_1.png)

在本节课中，我们将要学习Java 12引入的一个新特性：紧凑数字格式。这是一种将十进制数字格式化为紧凑形式的工具，例如将“10000”显示为“10K”。我们将通过多个示例，了解如何创建和使用紧凑数字格式，以及如何控制其显示方式。

![](img/ee09b2c2e51f74aee8e63de8e19cacca_3.png)

## 概述

紧凑数字格式是`NumberFormat`类的一个子类，它提供了一种将长数字（如千、百万、十亿）格式化为更短、更易读形式的方法。`NumberFormat`是所有数字格式的抽象基类，它提供了格式化和解析数字的接口。Java 12通过`NumberFormat.getCompactNumberInstance()`方法扩展了此功能。

![](img/ee09b2c2e51f74aee8e63de8e19cacca_5.png)

## 创建基本的紧凑数字格式

上一节我们介绍了紧凑数字格式的基本概念，本节中我们来看看如何创建一个基本的紧凑数字格式实例。

以下是创建一个使用美国英语区域设置和短样式格式的示例代码：

![](img/ee09b2c2e51f74aee8e63de8e19cacca_7.png)

```java
import java.text.NumberFormat;
import java.util.Locale;

![](img/ee09b2c2e51f74aee8e63de8e19cacca_9.png)

public class BasicCompactNumberExample {
    public static void main(String[] args) {
        // 创建短样式的紧凑数字格式实例
        NumberFormat nfShort = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);
        System.out.println("Short Style:");
        System.out.println("Result: " + nfShort.format(10000));
        System.out.println("Result: " + nfShort.format(120300));
        System.out.println("Result: " + nfShort.format(2120000));
        System.out.println("Result: " + nfShort.format(1950000300L));

        // 创建长样式的紧凑数字格式实例
        NumberFormat nfLong = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.LONG);
        System.out.println("\nLong Style:");
        System.out.println("Result: " + nfLong.format(10000));
        System.out.println("Result: " + nfLong.format(120300));
        System.out.println("Result: " + nfLong.format(2120000));
        System.out.println("Result: " + nfLong.format(1950000300L));
    }
}
```

![](img/ee09b2c2e51f74aee8e63de8e19cacca_11.png)

运行此代码，短样式输出为“10K”、“120K”、“2M”、“2B”，而长样式输出为“10 thousand”、“120 thousand”、“2 million”、“2 billion”。

## 为不同区域设置应用格式

![](img/ee09b2c2e51f74aee8e63de8e19cacca_13.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_15.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_17.png)

了解了基本用法后，我们可以探索如何为不同的区域设置（如法国、中国）创建紧凑数字格式。

![](img/ee09b2c2e51f74aee8e63de8e19cacca_18.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_20.png)

以下是一个展示不同区域设置下数字格式差异的示例：

![](img/ee09b2c2e51f74aee8e63de8e19cacca_22.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_23.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_25.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_27.png)

```java
import java.text.NumberFormat;
import java.util.Locale;

![](img/ee09b2c2e51f74aee8e63de8e19cacca_29.png)

public class LocaleCompactNumberExample {

    private static void printCompactNumberFormat(long number) {
        // 创建不同区域和样式的格式实例
        NumberFormat nfDefault = NumberFormat.getCompactNumberInstance();
        NumberFormat nfUSLong = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.LONG);
        NumberFormat nfFRShort = NumberFormat.getCompactNumberInstance(Locale.FRANCE, NumberFormat.Style.SHORT);
        NumberFormat nfFRLong = NumberFormat.getCompactNumberInstance(Locale.FRANCE, NumberFormat.Style.LONG);
        NumberFormat nfCHShort = NumberFormat.getCompactNumberInstance(Locale.CHINA, NumberFormat.Style.SHORT);
        NumberFormat nfCHLong = NumberFormat.getCompactNumberInstance(Locale.CHINA, NumberFormat.Style.LONG);

        System.out.println("Compact Formatting for " + number + ":");
        System.out.println("Default: " + nfDefault.format(number));
        System.out.println("US Long: " + nfUSLong.format(number));
        System.out.println("FR Short: " + nfFRShort.format(number));
        System.out.println("FR Long: " + nfFRLong.format(number));
        System.out.println("CH Short: " + nfCHShort.format(number));
        System.out.println("CH Long: " + nfCHLong.format(number));
        System.out.println();
    }

    public static void main(String[] args) {
        printCompactNumberFormat(23450000);
        printCompactNumberFormat(123000000);
    }
}
```

![](img/ee09b2c2e51f74aee8e63de8e19cacca_31.png)

此示例展示了同一个数字在不同语言和文化环境下的不同紧凑表示形式。

## 获取可用的区域设置

![](img/ee09b2c2e51f74aee8e63de8e19cacca_33.png)

有时，我们可能需要知道系统支持哪些区域设置。`NumberFormat`类提供了相应的方法。

以下是列出所有可用数字格式区域设置的代码：

```java
import java.text.NumberFormat;
import java.util.Locale;

![](img/ee09b2c2e51f74aee8e63de8e19cacca_35.png)

public class AvailableLocalesExample {
    public static void main(String[] args) {
        System.out.println("Number Format Locales:");
        Locale[] numberLocales = NumberFormat.getAvailableLocales();
        for (Locale locale : numberLocales) {
            System.out.println(locale.toString());
        }
    }
}
```

## 控制小数位数

![](img/ee09b2c2e51f74aee8e63de8e19cacca_37.png)

我们可以控制紧凑数字格式中小数部分的位数。这通过`setMinimumFractionDigits`和`setMaximumFractionDigits`方法实现。

以下是设置小数位数的示例：

![](img/ee09b2c2e51f74aee8e63de8e19cacca_39.png)

```java
import java.text.NumberFormat;
import java.util.Locale;

![](img/ee09b2c2e51f74aee8e63de8e19cacca_41.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_43.png)

public class FractionDigitsExample {
    public static void main(String[] args) {
        NumberFormat nf = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);

        // 打印默认的最小和最大小数位数
        System.out.println("Minimum: " + nf.getMinimumFractionDigits());
        System.out.println("Maximum: " + nf.getMaximumFractionDigits());

        // 使用默认设置格式化数字
        System.out.println("\nResult: " + nf.format(123454));
        System.out.println("Result: " + nf.format(5156835));

        // 设置最大小数位数为1
        nf.setMaximumFractionDigits(1);
        System.out.println("\nResult: " + nf.format(123454));
        System.out.println("Result: " + nf.format(5156835));

        // 设置最大小数位数为2
        nf.setMaximumFractionDigits(2);
        System.out.println("\nResult: " + nf.format(123454));
        System.out.println("Result: " + nf.format(5156835));

        // 同时设置最小和最大小数位数为2
        nf.setMinimumFractionDigits(2);
        nf.setMaximumFractionDigits(2);
        System.out.println("\nResult: " + nf.format(123454));
        System.out.println("Result: " + nf.format(5156835));
    }
}
```

![](img/ee09b2c2e51f74aee8e63de8e19cacca_45.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_47.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_49.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_51.png)

默认情况下，最小和最大小数位数均为0。调整这些值可以控制输出数字的精度。

## 设置舍入模式

![](img/ee09b2c2e51f74aee8e63de8e19cacca_53.png)

紧凑数字格式支持设置舍入模式。默认模式是`HALF_EVEN`。我们可以使用`setRoundingMode`方法进行更改。

![](img/ee09b2c2e51f74aee8e63de8e19cacca_55.png)

以下是演示不同舍入模式效果的示例：

![](img/ee09b2c2e51f74aee8e63de8e19cacca_56.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_57.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_58.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_60.png)

```java
import java.math.RoundingMode;
import java.text.NumberFormat;
import java.util.Locale;

public class RoundingModeExample {
    public static void main(String[] args) {
        NumberFormat nf = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);

        // 无小数位格式化
        System.out.println("\nResult: " + nf.format(123454));
        System.out.println("Result: " + nf.format(56835));

        // 设置小数位数为2
        nf.setMinimumFractionDigits(2);
        nf.setMaximumFractionDigits(2);
        System.out.println("\nResult: " + nf.format(123454));
        System.out.println("Result: " + nf.format(56835));

        // 设置舍入模式为 UP
        nf.setRoundingMode(RoundingMode.UP);
        System.out.println("\nResult (UP): " + nf.format(123454));
        System.out.println("Result (UP): " + nf.format(56835));

        // 设置小数位数为1
        nf.setMinimumFractionDigits(1);
        nf.setMaximumFractionDigits(1);
        System.out.println("\nResult: " + nf.format(123454));
        System.out.println("Result: " + nf.format(56835));

        // 设置舍入模式为 DOWN
        nf.setRoundingMode(RoundingMode.DOWN);
        System.out.println("\nResult (DOWN): " + nf.format(123454));
        System.out.println("Result (DOWN): " + nf.format(56835));
    }
}
```

![](img/ee09b2c2e51f74aee8e63de8e19cacca_62.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_64.png)

## 解析紧凑数字字符串

![](img/ee09b2c2e51f74aee8e63de8e19cacca_66.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_68.png)

除了格式化，`NumberFormat`还可以将紧凑数字字符串（如“1K”）解析回数字。这是通过`parse`方法实现的。

以下是使用`parse`方法的示例：

![](img/ee09b2c2e51f74aee8e63de8e19cacca_70.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_71.png)

```java
import java.text.NumberFormat;
import java.text.ParseException;
import java.util.Locale;

![](img/ee09b2c2e51f74aee8e63de8e19cacca_73.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_75.png)

public class ParseExample {
    public static void main(String[] args) throws ParseException {
        // 短样式解析
        NumberFormat nfShort = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);
        System.out.println("US Short Parsing:");
        System.out.println(nfShort.parse("1K"));
        System.out.println(nfShort.parse("1M"));
        System.out.println(nfShort.parse("1B"));

        // 长样式解析
        NumberFormat nfLong = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.LONG);
        System.out.println("\nUS Long Parsing:");
        System.out.println(nfLong.parse("1 thousand"));
        System.out.println(nfLong.parse("1 million"));
        System.out.println(nfLong.parse("1 billion"));
    }
}
```

## 启用分组分隔符解析

默认情况下，解析紧凑数字字符串时不支持分组分隔符（如逗号）。可以通过`setGroupingUsed(true)`来启用。

以下是启用分组分隔符解析的示例：

```java
import java.text.NumberFormat;
import java.text.ParseException;
import java.util.Locale;

![](img/ee09b2c2e51f74aee8e63de8e19cacca_77.png)

![](img/ee09b2c2e51f74aee8e63de8e19cacca_79.png)

public class ParseWithGroupingExample {
    public static void main(String[] args) throws ParseException {
        NumberFormat nf = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);

        System.out.println("Without Grouping:");
        System.out.println(nf.parse("1000K"));
        System.out.println(nf.parse("1000M"));
        System.out.println(nf.parse("1000B"));

        // 启用分组
        nf.setGroupingUsed(true);
        System.out.println("\nWith Grouping:");
        System.out.println(nf.parse("1,000K"));
        System.out.println(nf.parse("1,000M"));
        System.out.println(nf.parse("1,000B"));
    }
}
```

## 总结

![](img/ee09b2c2e51f74aee8e63de8e19cacca_81.png)

本节课中我们一起学习了Java 12引入的紧凑数字格式。我们了解了如何创建不同区域和样式的紧凑数字格式实例，如何控制小数位数和舍入模式，以及如何将紧凑数字字符串解析回数字。这个特性使得显示和解析大数字变得更加简洁和符合本地化习惯，是编写国际化应用程序时的有用工具。