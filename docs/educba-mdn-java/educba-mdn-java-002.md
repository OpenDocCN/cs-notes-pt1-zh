# 002：Lambda表达式与方法引用

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_0.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_2.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_3.png)

在本节课中，我们将继续学习Java 8的Lambda表达式，并深入了解方法引用这一简化Lambda表达式的强大工具。我们将通过多个实例，学习如何将Lambda表达式应用于集合迭代，以及如何使用四种不同类型的方法引用来编写更简洁的代码。

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_5.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_7.png)

## 使用Lambda表达式迭代集合

上一节我们介绍了Lambda表达式的基本语法，本节中我们来看看如何将其应用于集合的迭代操作。

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_9.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_11.png)

以下是一个使用`forEach`方法和Lambda表达式遍历列表的示例：

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_13.png)

```java
import java.util.*;

public class LambdaExample {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("Apple");
        list.add("Banana");
        list.add("Cherry");
        list.add("Date");

        list.forEach(str -> System.out.println(str));
    }
}
```

执行上述代码，会依次打印出列表中的每个元素。通过这种方式，我们可以简洁地迭代Map或其他集合类。

## 方法引用简介

我们已经学习了多种创建Lambda表达式的方式。现在，让我们转向Java 8中的方法引用。方法引用本质上是Lambda表达式的一种简写形式，用于直接调用一个已存在的方法。

例如，一个打印字符串的Lambda表达式 `str -> System.out.println(str)` 可以替换为方法引用 `System.out::println`。这里的双冒号 `::` 是方法引用操作符，用于分隔类（或对象）与方法名。

Java 8提供了四种主要的方法引用类型。

## 方法引用类型一：引用特定对象的实例方法

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_15.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_17.png)

第一种类型是引用特定对象的实例方法，格式为 `对象::实例方法名`。

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_19.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_21.png)

以下是实现此功能的步骤：

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_23.png)

1.  定义一个函数式接口。
2.  创建一个包含目标实例方法的类。
3.  在主类中创建该类的对象。
4.  使用方法引用将该对象的方法赋值给接口引用。
5.  通过接口引用调用方法。

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_25.png)

```java
// 1. 定义函数式接口
interface MyInterface {
    void display();
}

// 2. 创建包含实例方法的类
class MyClass {
    public void myMethod() {
        System.out.println("Instance Method");
    }
}

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_27.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_28.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_29.png)

// 3. 主类中使用方法引用
public class MethodRefExample1 {
    public static void main(String[] args) {
        // 创建对象
        MyClass obj = new MyClass();
        // 使用方法引用
        MyInterface ref = obj::myMethod;
        // 调用方法
        ref.display(); // 输出：Instance Method
    }
}
```

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_31.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_33.png)

## 方法引用类型二：引用类的静态方法

第二种类型是引用类的静态方法，格式为 `类名::静态方法名`。

以下是实现此功能的步骤：

1.  创建一个包含静态方法的类。
2.  使用函数式接口（如`BiFunction`）来引用该静态方法。
3.  通过接口的`apply`方法传入参数并获取结果。

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_35.png)

```java
import java.util.function.BiFunction;

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_37.png)

// 1. 创建包含静态方法的类
class Multiplication {
    public static int multiply(int a, int b) {
        return a * b;
    }
}

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_39.png)

// 2. 主类中使用静态方法引用
public class MethodRefExample2 {
    public static void main(String[] args) {
        // 引用静态方法
        BiFunction<Integer, Integer, Integer> product = Multiplication::multiply;
        // 应用参数并获取结果
        int pr = product.apply(11, 5);
        System.out.println("Product of given number is: " + pr); // 输出：55
    }
}
```

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_41.png)

## 方法引用类型三：引用特定类型的任意对象的实例方法

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_43.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_44.png)

第三种类型是引用特定类型的任意对象的实例方法，格式为 `类名::实例方法名`。这在处理集合排序等操作时非常有用。

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_46.png)

以下是实现此功能的步骤：

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_48.png)

1.  创建一个字符串数组。
2.  使用`Arrays.sort`方法并传入`String::compareToIgnoreCase`方法引用进行不区分大小写的排序。
3.  遍历并打印排序后的数组。

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_50.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_52.png)

```java
import java.util.Arrays;

public class MethodRefExample3 {
    public static void main(String[] args) {
        String[] strArray = {"Steve", "Rick", "Aditya", "Lucy", "John"};
        // 使用方法引用进行排序
        Arrays.sort(strArray, String::compareToIgnoreCase);
        // 打印排序后的数组
        for (String str : strArray) {
            System.out.println(str);
        }
        // 输出顺序为：Aditya, John, Lucy, Rick, Steve
    }
}
```

## 方法引用类型四：引用构造函数

第四种类型是引用构造函数，格式为 `类名::new`。

以下是实现此功能的步骤：

1.  定义一个函数式接口，其方法返回类型与目标构造函数匹配。
2.  创建一个类，其构造函数包含所需逻辑。
3.  使用方法引用将构造函数赋值给接口引用。
4.  通过接口引用调用方法，从而触发构造函数。

```java
// 1. 定义函数式接口
interface MyInterface1 {
    Hello display(String s);
}

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_54.png)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_55.png)

// 2. 创建类
class Hello {
    // 构造函数
    public Hello(String s) {
        System.out.println(s);
    }
}

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_57.png)

// 3. 主类中使用构造函数引用
public class MethodRefExample4 {
    public static void main(String[] args) {
        // 引用构造函数
        MyInterface1 ref = Hello::new;
        // 调用方法，会创建Hello对象并执行其构造函数
        ref.display("Hello World"); // 输出：Hello World
    }
}
```

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_59.png)

## 总结

本节课中我们一起学习了Lambda表达式在集合迭代中的应用，并深入探讨了Java 8方法引用的四种类型：
1.  **特定对象的实例方法引用** (`对象::实例方法`)
2.  **类的静态方法引用** (`类名::静态方法`)
3.  **特定类型的任意对象的实例方法引用** (`类名::实例方法`)
4.  **构造函数引用** (`类名::new`)

![](img/9fff66dcfa89fcd1a18f28dd0e3cbd16_61.png)

方法引用提供了一种更简洁、更直接的方式来指向已有的方法或构造函数，是编写高效、清晰Java代码的重要工具。在接下来的课程中，我们将开始学习Java 8的另一个核心特性：Stream API。