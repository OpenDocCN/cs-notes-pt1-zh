# 020：数字类型属性与方法详解 🔢

![](img/b56565a9afa2d26d40234c6b946e2216_1.png)

![](img/b56565a9afa2d26d40234c6b946e2216_2.png)

![](img/b56565a9afa2d26d40234c6b946e2216_4.png)

![](img/b56565a9afa2d26d40234c6b946e2216_5.png)

在本节课中，我们将学习Dart语言中数字类型（`int`和`double`）的常用属性和方法。通过理解这些内置功能，你可以更高效地处理和操作数字数据。

上一节我们介绍了Dart中的常量和变量，本节中我们来看看数字类型本身提供了哪些有用的工具。

![](img/b56565a9afa2d26d40234c6b946e2216_7.png)

![](img/b56565a9afa2d26d40234c6b946e2216_8.png)

## 数字类型的属性

![](img/b56565a9afa2d26d40234c6b946e2216_10.png)

![](img/b56565a9afa2d26d40234c6b946e2216_11.png)

![](img/b56565a9afa2d26d40234c6b946e2216_13.png)

![](img/b56565a9afa2d26d40234c6b946e2216_15.png)

数字类型提供了一系列属性，用于获取关于该数字的特定信息，例如它是否为负数、是否为偶数等。

![](img/b56565a9afa2d26d40234c6b946e2216_16.png)

以下是数字类型的一些常用属性及其说明：

![](img/b56565a9afa2d26d40234c6b946e2216_18.png)

![](img/b56565a9afa2d26d40234c6b946e2216_19.png)

![](img/b56565a9afa2d26d40234c6b946e2216_20.png)

*   **hashCode**：返回该数字的哈希码。这是一个唯一的整数值，常用于集合（如`Set`或`Map`键）中。
    *   代码示例：`print(‘hashCode: ${numVar.hashCode}’);`
*   **isFinite**：检查数字是否为有限值（非无穷大）。返回布尔值`true`或`false`。
    *   代码示例：`print(‘isFinite: ${numVar.isFinite}’);`
*   **isInfinite**：检查数字是否为无穷大。返回布尔值`true`或`false`。
    *   代码示例：`print(‘isInfinite: ${numVar.isInfinite}’);`
*   **isNaN**：检查数字是否为“非数字”（Not-a-Number）。返回布尔值`true`或`false`。
    *   代码示例：`print(‘isNaN: ${numVar.isNaN}’);`
*   **isNegative**：检查数字是否为负数。返回布尔值`true`或`false`。
    *   代码示例：`print(‘isNegative: ${numVar.isNegative}’);`
*   **sign**：返回数字的符号。如果数字为负数，返回`-1`；如果为`0`，返回`0`；如果为正数，返回`1`。
    *   代码示例：`print(‘sign: ${numVar.sign}’);`
*   **isEven**：检查整数是否为偶数。仅适用于`int`类型，返回布尔值`true`或`false`。
    *   代码示例：`print(‘isEven: ${intVar.isEven}’);`
*   **isOdd**：检查整数是否为奇数。仅适用于`int`类型，返回布尔值`true`或`false`。
    *   代码示例：`print(‘isOdd: ${intVar.isOdd}’);`

**示例运行结果**：假设我们有一个整数变量 `intVar = -56`，上述属性检查的结果可能为：`isFinite: true`，`isNegative: true`，`sign: -1`，`isEven: true`，`isOdd: false`。

![](img/b56565a9afa2d26d40234c6b946e2216_22.png)

![](img/b56565a9afa2d26d40234c6b946e2216_23.png)

![](img/b56565a9afa2d26d40234c6b946e2216_24.png)

![](img/b56565a9afa2d26d40234c6b946e2216_26.png)

![](img/b56565a9afa2d26d40234c6b946e2216_28.png)

## 数字类型的方法

![](img/b56565a9afa2d26d40234c6b946e2216_30.png)

![](img/b56565a9afa2d26d40234c6b946e2216_32.png)

![](img/b56565a9afa2d26d40234c6b946e2216_34.png)

除了属性，数字类型还提供了多种方法，用于执行数学运算和转换。

![](img/b56565a9afa2d26d40234c6b946e2216_36.png)

![](img/b56565a9afa2d26d40234c6b946e2216_37.png)

以下是数字类型的一些常用方法及其说明：

![](img/b56565a9afa2d26d40234c6b946e2216_39.png)

![](img/b56565a9afa2d26d40234c6b946e2216_41.png)

![](img/b56565a9afa2d26d40234c6b946e2216_43.png)

*   **abs()**：返回数字的绝对值（正数）。
    *   代码示例：`print(‘abs(): ${numVar.abs()}’);`
*   **ceil()**：返回不小于当前数字的最小整数（向上取整）。
    *   代码示例：`print(‘ceil(): ${numVar.ceil()}’);`
*   **floor()**：返回不大于当前数字的最大整数（向下取整）。
    *   代码示例：`print(‘floor(): ${numVar.floor()}’);`
*   **compareTo(other)**：将当前数字与另一个数字`other`进行比较。如果相等返回`0`，如果当前数字大返回`1`，如果小返回`-1`。
    *   代码示例：`print(‘compareTo(56.66): ${numVar.compareTo(56.66)}’);`
*   **remainder(other)**：返回当前数字除以`other`后的余数。
    *   代码示例：`print(‘remainder(10): ${numVar.remainder(10)}’);`
*   **round()**：返回最接近当前数字的整数（四舍五入）。
    *   代码示例：`print(‘round(): ${numVar.round()}’);`
*   **toInt()**：将数字转换为整数（`int`类型），直接舍弃小数部分。
    *   代码示例：`print(‘toInt(): ${numVar.toInt()}’);`
*   **truncate()**：返回舍弃小数部分后的整数。对于正数，效果同`floor()`；对于负数，效果同`ceil()`。
    *   代码示例：`print(‘truncate(): ${numVar.truncate()}’);`

![](img/b56565a9afa2d26d40234c6b946e2216_45.png)

![](img/b56565a9afa2d26d40234c6b946e2216_47.png)

![](img/b56565a9afa2d26d40234c6b946e2216_49.png)

![](img/b56565a9afa2d26d40234c6b946e2216_51.png)

![](img/b56565a9afa2d26d40234c6b946e2216_53.png)

**示例运行结果**：假设我们有一个双精度浮点数变量 `doubleVar = 65.66`，上述方法调用的结果可能为：`abs(): 65.66`，`ceil(): 66`，`floor(): 65`，`round(): 66`，`toInt(): 65`，`truncate(): 65`。

![](img/b56565a9afa2d26d40234c6b946e2216_54.png)

![](img/b56565a9afa2d26d40234c6b946e2216_55.png)

---

![](img/b56565a9afa2d26d40234c6b946e2216_57.png)

![](img/b56565a9afa2d26d40234c6b946e2216_58.png)

![](img/b56565a9afa2d26d40234c6b946e2216_59.png)

本节课中我们一起学习了Dart数字类型的核心属性和方法。属性用于查询数字的状态（如正负、奇偶），而方法用于对数字进行运算和转换（如取整、取绝对值）。熟练掌握这些工具，能让你在编写涉及数值计算的Dart程序时更加得心应手。