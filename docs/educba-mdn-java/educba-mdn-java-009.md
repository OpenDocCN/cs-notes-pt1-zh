# 009：字符串类的新方法 🆕

在本节课中，我们将学习Java 12及后续版本中为`String`类引入的几个新方法。这些方法旨在帮助开发者编写更简洁、更高效的代码。我们将逐一探讨`indent`、`transform`、`describeConstable`、`resolveConstantDesc`方法以及新的`switch`箭头表达式。

## 概述

![](img/0645e02e139bb4dfb6a1e45cb4779982_1.png)

Java 12为`String`类添加了多个实用方法，用于处理字符串的缩进、转换以及与JVM常量API的交互。此外，Java 12还引入了新的`switch`表达式语法，使代码更加简洁。本节将通过具体示例详细讲解这些新特性的用法。

---

![](img/0645e02e139bb4dfb6a1e45cb4779982_3.png)

### 1. `indent` 方法

`indent`方法用于调整字符串每一行的缩进。它接受一个整数参数`n`：
*   当 `n > 0` 时，在每行开头插入`n`个空格。
*   当 `n < 0` 时，尝试从每行开头移除最多`n`个空格。
*   当 `n = 0` 时，保持原样，但会统一换行符。
该方法还会将字符串中的换行符统一为`\n`。

以下是使用`indent`方法的示例代码：

![](img/0645e02e139bb4dfb6a1e45cb4779982_5.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_6.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_7.png)

```java
public static void main(String[] args) {
    String s = "Life is too short to work so hard.";
    System.out.println("Original String: " + s);
    System.out.println("String length: " + s.length());

    String sIndent = s.indent(5);
    System.out.println("\nIndented String: " + sIndent);
    System.out.println("Indented String length: " + sIndent.length());
}
```

![](img/0645e02e139bb4dfb6a1e45cb4779982_9.png)

执行上述代码，原始字符串长度为33。使用`indent(5)`后，每行开头添加了5个空格，因此新字符串的长度变为38（33个原字符 + 5个空格）。

![](img/0645e02e139bb4dfb6a1e45cb4779982_11.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_13.png)

---

### 2. 处理多行字符串的 `indent`

`indent`方法在处理包含换行符`\n`的多行字符串时尤为有用。我们可以创建一个辅助方法来统计字符串中特定字符的出现次数。

![](img/0645e02e139bb4dfb6a1e45cb4779982_15.png)

首先，定义一个统计字符的方法：

![](img/0645e02e139bb4dfb6a1e45cb4779982_17.png)

```java
static int countChar(String s, char c) {
    int cnt = 0;
    for (int i = 0; i < s.length(); i++) {
        if (s.charAt(i) == c) {
            cnt++;
        }
    }
    return cnt;
}
```

接着，在主方法中使用多行字符串并应用`indent`：

![](img/0645e02e139bb4dfb6a1e45cb4779982_19.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_21.png)

```java
public static void main(String[] args) {
    String s = "Life is short,\nso I am careful where I put my time.\nI don't want to waste it.";
    System.out.println("Original String:\n" + s);
    System.out.println("String length: " + s.length());
    System.out.println("Count of \\n: " + countChar(s, '\n'));

    String sIndent = s.indent(5);
    System.out.println("\nIndented String:\n" + sIndent);
    System.out.println("Indented String length: " + sIndent.length());
    System.out.println("Count of \\n after indent: " + countChar(sIndent, '\n'));
}
```

运行代码后，可以看到原始字符串有2个换行符。使用`indent(5)`后，字符串长度增加，并且`indent`方法统一了换行符，统计结果可能发生变化。

![](img/0645e02e139bb4dfb6a1e45cb4779982_23.png)

我们也可以尝试使用负值和零值参数：

![](img/0645e02e139bb4dfb6a1e45cb4779982_25.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_26.png)

```java
String s2 = s.indent(-2); // 尝试移除每行开头的2个空格
String s3 = s.indent(0);  // 不改变缩进，但统一换行符
System.out.println("String after indent(-2):\n" + s2);
System.out.println("String after indent(0):\n" + s3);
```

---

![](img/0645e02e139bb4dfb6a1e45cb4779982_28.png)

### 3. `transform` 方法

![](img/0645e02e139bb4dfb6a1e45cb4779982_30.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_32.png)

`transform`方法允许你对字符串应用一个函数（`Function`），并将该函数的返回值作为结果。这为链式字符串转换提供了更函数式的编程风格。

![](img/0645e02e139bb4dfb6a1e45cb4779982_34.png)

以下是一个示例，展示如何将字符串转换为大写，然后转换为字符数组：

```java
import java.util.Arrays;

![](img/0645e02e139bb4dfb6a1e45cb4779982_36.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_38.png)

public static void main(String[] args) {
    String str = "Life's too short";
    var result = str
            .transform(input -> input.concat(" to be sad."))
            .transform(String::toUpperCase)
            .transform(String::toCharArray);

    System.out.println(Arrays.toString(result));
}
```

![](img/0645e02e139bb4dfb6a1e45cb4779982_40.png)

在这个例子中，我们首先将字符串连接一段话，然后将其转换为大写，最后转换为字符数组。`transform`方法使得这一系列操作可以流畅地链式调用。

![](img/0645e02e139bb4dfb6a1e45cb4779982_42.png)

---

### 4. `describeConstable` 与 `resolveConstantDesc` 方法

![](img/0645e02e139bb4dfb6a1e45cb4779982_44.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_45.png)

这两个方法与JVM的常量API相关，通常用于底层库开发，日常应用较少。

![](img/0645e02e139bb4dfb6a1e45cb4779982_47.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_48.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_49.png)

*   **`describeConstable()`**: 返回一个包含该字符串实例本身描述符的`Optional`对象。
*   **`resolveConstantDesc()`**: 解析常量描述符，对于`String`类型，返回的就是字符串实例本身。

以下是它们的用法示例：

```java
import java.util.Optional;

public static void main(String[] args) {
    String str = "Life is short and we should respect every moment of it.";
    
    // 使用 describeConstable
    Optional<String> optStr = str.describeConstable();
    optStr.ifPresent(value -> System.out.println("Value from describeConstable: " + value));
    
    // 使用 resolveConstantDesc
    String str2 = str.resolveConstantDesc(null);
    System.out.println("str2.equals(str): " + str2.equals(str));
    System.out.println("str2 == str: " + (str2 == str)); // 注意：可能为true，因为字符串驻留
}
```

![](img/0645e02e139bb4dfb6a1e45cb4779982_51.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_52.png)

`describeConstable`返回的`Optional`包含字符串自身。`resolveConstantDesc`方法将常量描述符解析为`String`实例，对于字符串常量，结果通常与原始实例相同。

---

![](img/0645e02e139bb4dfb6a1e45cb4779982_54.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_55.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_57.png)

### 5. 新的 `switch` 箭头表达式 (Java 12+)

![](img/0645e02e139bb4dfb6a1e45cb4779982_59.png)

Java 12引入了`switch`表达式，并使用`->`箭头符号简化了语法。它允许`switch`直接返回值或执行一个语句块，无需`break`。

以下是新旧`switch`用法的对比示例：

![](img/0645e02e139bb4dfb6a1e45cb4779982_61.png)

```java
public static void main(String[] args) {
    // 调用示例
    getGrade('A');
    getGrade('C');
    getGrade('D');
    getGrade('E');
    getGrade('X');
}

// 使用新的 switch 表达式（箭头语法）
public static void getGrade(char grade) {
    switch (grade) {
        case 'A' -> System.out.println("Excellent");
        case 'B' -> System.out.println("Good");
        case 'C' -> System.out.println("Standard");
        case 'D' -> System.out.println("Low");
        case 'E' -> System.out.println("Very Low");
        default -> System.out.println("Invalid");
    }
    getResult(grade);
}

// 另一个使用 switch 返回值的例子
public static void getResult(char grade) {
    String result = switch (grade) {
        case 'A', 'B', 'C' -> "Success";
        case 'D', 'E' -> "Fail";
        default -> "No Result";
    };
    System.out.println("Result: " + result);
}
```

![](img/0645e02e139bb4dfb6a1e45cb4779982_63.png)

新的`switch`语法更加简洁明了。在`getGrade`方法中，每个`case`直接通过箭头`->`指向要执行的语句。在`getResult`方法中，`switch`直接计算并返回一个值，并且支持在单个`case`中匹配多个值（如`case 'A', 'B', 'C'`）。

![](img/0645e02e139bb4dfb6a1e45cb4779982_65.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_67.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_68.png)

---

## 总结

本节课我们一起学习了Java 12及后续版本中引入的多个现代`String`类方法和语言特性：

![](img/0645e02e139bb4dfb6a1e45cb4779982_70.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_71.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_72.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_73.png)

1.  **`indent(int n)`**：用于调整字符串的缩进，正数添加空格，负数移除空格，并统一换行符。
2.  **`transform(Function)`**：以函数式风格对字符串进行链式转换。
3.  **`describeConstable()` 与 `resolveConstantDesc()`**：用于JVM常量API交互，返回字符串的描述符或实例本身。
4.  **新的 `switch` 箭头表达式**：使用 `->` 语法简化了`switch`结构，支持直接返回值和多值匹配，使代码更清晰、更安全（避免遗忘`break`）。

![](img/0645e02e139bb4dfb6a1e45cb4779982_74.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_75.png)

![](img/0645e02e139bb4dfb6a1e45cb4779982_77.png)

掌握这些新特性有助于你编写出更简洁、更易读且更高效的Java代码。