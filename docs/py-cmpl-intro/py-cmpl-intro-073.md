# 073：如何测量Python代码的执行时间 ⏱️

在本节课中，我们将学习如何在Python中测量代码的执行时间。这对于性能分析、优化代码以及比较不同算法或实现方式的效率至关重要。我们将通过一个简单的示例，演示如何精确地计算一段代码从开始到结束所花费的时间。

## 概述

测量代码执行时间的核心思想是：在目标代码执行前记录一个时间点，在代码执行后再记录一个时间点，然后计算这两个时间点之间的差值。这个差值就是代码的执行时间。

我们将使用Python内置的 `time` 模块来完成这个任务，特别是其中的 `perf_counter()` 方法，它能提供高精度的时间测量。

## 导入所需模块

![](img/c845f0f1ef028ec39c68f2f027bb94a0_1.png)

首先，我们需要导入 `time` 模块。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_2.png)

![](img/c845f0f1ef028ec39c68f2f027bb94a0_3.png)

```python
import time
```

![](img/c845f0f1ef028ec39c68f2f027bb94a0_4.png)

![](img/c845f0f1ef028ec39c68f2f027bb94a0_5.png)

## 测量执行时间的步骤

![](img/c845f0f1ef028ec39c68f2f027bb94a0_6.png)

![](img/c845f0f1ef028ec39c68f2f027bb94a0_7.png)

以下是测量代码执行时间的具体步骤。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_9.png)

### 1. 记录开始时间

![](img/c845f0f1ef028ec39c68f2f027bb94a0_11.png)

在你要测量的代码块开始执行之前，使用 `time.perf_counter()` 方法记录一个时间点，并将其赋值给一个变量（例如 `start_time`）。

```python
start_time = time.perf_counter()
```

![](img/c845f0f1ef028ec39c68f2f027bb94a0_13.png)

![](img/c845f0f1ef028ec39c68f2f027bb94a0_14.png)

`time.perf_counter()` 会返回一个任意的时间点，其值本身并不重要，重要的是它与后续时间点之间的差值。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_16.png)

### 2. 执行待测代码

![](img/c845f0f1ef028ec39c68f2f027bb94a0_18.png)

接下来，放置你想要测量执行时间的代码。为了演示，我们将创建一个简单的循环。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_20.png)

```python
# 你的代码放在这里
for i in range(1000000):
    pass
```

![](img/c845f0f1ef028ec39c68f2f027bb94a0_22.png)

这个循环迭代一百万次，但循环体内不做任何操作（`pass`语句）。我们将测量执行这个空循环所需的时间。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_24.png)

### 3. 记录结束时间

在待测代码执行完毕后，再次调用 `time.perf_counter()` 方法，记录另一个时间点，并将其赋值给另一个变量（例如 `end_time`）。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_26.png)

```python
end_time = time.perf_counter()
```

![](img/c845f0f1ef028ec39c68f2f027bb94a0_28.png)

### 4. 计算并输出耗时

![](img/c845f0f1ef028ec39c68f2f027bb94a0_30.png)

最后，计算结束时间与开始时间的差值，这个差值就是代码的执行时间（以秒为单位）。然后，我们可以将这个时间打印出来。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_32.png)

```python
elapsed_time = end_time - start_time
print(f"执行耗时：{elapsed_time:.1f} 秒")
```

![](img/c845f0f1ef028ec39c68f2f027bb94a0_33.png)

在上面的打印语句中，我们使用了格式化字符串（f-string），并通过 `:.1f` 指定了输出格式，即只显示小数点后一位数字，这样可以使输出结果更整洁。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_35.png)

## 完整代码示例

![](img/c845f0f1ef028ec39c68f2f027bb94a0_36.png)

将以上所有步骤组合起来，就得到了一个完整的测量代码执行时间的程序。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_38.png)

```python
import time

# 1. 记录开始时间
start_time = time.perf_counter()

![](img/c845f0f1ef028ec39c68f2f027bb94a0_40.png)

![](img/c845f0f1ef028ec39c68f2f027bb94a0_41.png)

# 2. 执行待测代码
for i in range(1000000):
    pass

# 3. 记录结束时间
end_time = time.perf_counter()

# 4. 计算并输出耗时
elapsed_time = end_time - start_time
print(f"执行耗时：{elapsed_time:.1f} 秒")
```

运行这段代码，你将看到类似“执行耗时：0.0 秒”的输出。由于现代计算机速度很快，一百万次空循环的执行时间可能非常短，显示为0.0秒。你可以尝试增加循环次数（例如 `range(10000000)` 或 `range(100000000)`）来观察更明显的耗时变化。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_43.png)

![](img/c845f0f1ef028ec39c68f2f027bb94a0_44.png)

## 总结

![](img/c845f0f1ef028ec39c68f2f027bb94a0_45.png)

本节课中，我们一起学习了如何在Python中测量代码的执行时间。我们掌握了以下核心步骤：
1.  使用 `import time` 导入时间模块。
2.  在代码执行前，用 `start_time = time.perf_counter()` 记录开始时间。
3.  执行你想要测量的代码块。
4.  在代码执行后，用 `end_time = time.perf_counter()` 记录结束时间。
5.  通过公式 **`elapsed_time = end_time - start_time`** 计算执行耗时，并打印结果。

![](img/c845f0f1ef028ec39c68f2f027bb94a0_47.png)

这个方法简单有效，是进行基础性能分析和代码优化的实用工具。你可以将它应用到任何你想了解其运行效率的代码片段上。