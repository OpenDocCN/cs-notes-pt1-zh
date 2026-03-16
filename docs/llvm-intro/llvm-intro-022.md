# 022：LLVM测试套件 🧪

![](img/4d08b9f3dceda9c0e88977d044051e21_0.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_2.png)

在本节课中，我们将要学习如何安装和使用LLVM测试套件。LLVM测试套件是一组用于测试LLVM编译器正确性和性能的程序集合，它也可以作为辅助工具，在开发过程中测试你的自定义工具或优化。

![](img/4d08b9f3dceda9c0e88977d044051e21_4.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_5.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_6.png)

## 什么是LLVM测试套件？

![](img/4d08b9f3dceda9c0e88977d044051e21_8.png)

LLVM测试套件是一组用C、C++或Fortran编写的程序集合。通过这些程序，我们可以测试LLVM编译器的正确性或性能。同时，我们也可以使用测试套件作为开发过程的辅助工具，用它来测试你的工具或优化过程。

## 安装LLVM测试套件

安装LLVM测试套件的过程可以分为三个步骤：满足要求、下载源码、编译构建。

![](img/4d08b9f3dceda9c0e88977d044051e21_10.png)

### 步骤一：安装要求

![](img/4d08b9f3dceda9c0e88977d044051e21_12.png)

以下是安装前需要满足的要求：

![](img/4d08b9f3dceda9c0e88977d044051e21_14.png)

*   **CMake**：用于生成编译文件。
*   **LLVM集成测试器（lit）**：这是我们将用来运行测试的测试运行器。获取此工具有两种方式：
    *   使用 `pip` 安装。
    *   如果你已经有一个LLVM构建版本，可以在其二进制文件目录中找到它。

![](img/4d08b9f3dceda9c0e88977d044051e21_15.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_16.png)

### 步骤二：下载源码

有两种方式可以下载测试套件源码：

![](img/4d08b9f3dceda9c0e88977d044051e21_18.png)

1.  从GitHub克隆仓库。
2.  使用 `wget` 下载 `.tgz` 压缩包并解压。

![](img/4d08b9f3dceda9c0e88977d044051e21_19.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_20.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_21.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_22.png)

**重要提示**：请确保下载与你使用的LLVM编译器版本兼容的测试套件版本。例如，如果你使用LLVM 17版，请克隆或检出 `release/17.x` 分支的测试套件，或者下载17版的 `.tgz` 压缩包。

### 步骤三：编译源码

![](img/4d08b9f3dceda9c0e88977d044051e21_24.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_26.png)

现在我们已经有了源码文件，让我们学习如何编译它们。

首先，创建一个构建目录，例如 `test-suite-build`。然后，在该目录中使用 `cmake` 命令配置编译文件。命令示例如下：

```bash
cmake -DCMAKE_C_COMPILER=/path/to/your/llvm-build/bin/clang -C../test-suite/cmake/caches/O3.cmake ../test-suite
```

![](img/4d08b9f3dceda9c0e88977d044051e21_28.png)

在这个命令中：
*   `-DCMAKE_C_COMPILER` 指定了你的LLVM编译器（clang）的路径。
*   `-C` 指定了要使用的CMake缓存配置文件（例如 `O3.cmake`）。
*   最后一个参数 `../test-suite` 是你之前下载的测试套件源码目录的路径。

![](img/4d08b9f3dceda9c0e88977d044051e21_29.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_31.png)

**一个关键注意事项**：如果你使用自己构建的LLVM，需要在构建LLVM时启用 `LLVM_INSTALL_UTILS` 标志。这是因为测试套件中的微基准测试需要此标志才能完成编译过程。没有它，你只能编译大约一半的基准测试。

![](img/4d08b9f3dceda9c0e88977d044051e21_33.png)

配置完成后，只需运行 `make` 命令并等待编译过程结束。

## 测试套件结构解析

![](img/4d08b9f3dceda9c0e88977d044051e21_35.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_36.png)

现在我们已经构建好了LLVM测试套件，让我们了解一下它的目录结构。

![](img/4d08b9f3dceda9c0e88977d044051e21_38.png)

*   **SingleSource**：此目录包含仅由单个源文件组成的简单程序。
*   **MultiSource**：此目录包含由多个源文件编写的大型基准测试和完整应用程序。
*   **MicroBenchmarks**：这是之前提到的需要启用 `LLVM_INSTALL_UTILS` 标志才能编译的基准测试目录。其中的程序使用了Google Benchmark库，需要运行多次以获得具有统计意义的结果。
*   **External**：如果你想添加新的基准测试套件，可以将它们放在这个文件夹中。

![](img/4d08b9f3dceda9c0e88977d044051e21_40.png)

## 使用LLVM集成测试器（lit）

正如之前提到的，`lit` 工具是运行测试的必要条件。它作为LLVM测试套件的测试运行器，可以运行单个测试或整个目录的测试。

`lit` 的一些常用选项包括：
*   `--output`：指定输出文件，通常是一个JSON文件。
*   `--num-workers`：指定运行测试时要使用的CPU核心数。
*   `-v`：启用详细输出模式。当测试失败时，输出信息会更详细。

![](img/4d08b9f3dceda9c0e88977d044051e21_42.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_44.png)

## 运行测试示例

为了简化说明，我们来看几个使用 `lit` 运行测试的例子。

![](img/4d08b9f3dceda9c0e88977d044051e21_46.png)

**示例1：运行整个测试套件**
```bash
llvm-lit -v --num-workers=1 --output=results.json .
```
这个命令在 `test-suite-build` 目录内运行。`lit` 将搜索整个构建目录树中的测试文件，并为每个文件运行测试。

**示例2：运行特定基准测试集**
```bash
llvm-lit -v --num-workers=4 ./SingleSource
```
这个命令将只运行 `SingleSource` 目录下的所有基准测试。

![](img/4d08b9f3dceda9c0e88977d044051e21_48.png)

**示例3：运行单个基准测试**
```bash
llvm-lit -v --num-workers=4 ./SingleSource/Benchmarks/Polybench
```
这个命令将仅运行 `Polybench` 这一个基准测试。

![](img/4d08b9f3dceda9c0e88977d044051e21_50.png)

运行结果通常如下所示：第一行显示在目录中找到的测试文件数量和使用的核心数。对于每个测试，会显示是通过还是失败，并可以找到一些执行指标，如编译时间、执行时间等。

![](img/4d08b9f3dceda9c0e88977d044051e21_52.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_53.png)

## 添加外部测试套件

![](img/4d08b9f3dceda9c0e88977d044051e21_55.png)

最后，让我们学习如何添加外部测试套件。首先需要知道，你要添加的测试或套件必须与LLVM测试套件兼容。

这意味着它们必须遵循与测试套件中基准测试相同的结构，以便 `lit` 能够运行它们。我们可以将套件添加到 `External` 文件夹中。基本上，你需要将想要运行的套件的源码文件放到 `External` 目录下。

此外，还有一个链接套件的选项。LLVM支持链接像 `SPEC` 这样的外部套件。在编译过程中，我们可以使用一个标志来指定 `SPEC` 构建的路径，这样LLVM测试套件就会集成 `SPEC` 而无需额外步骤。

![](img/4d08b9f3dceda9c0e88977d044051e21_57.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_58.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_59.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_60.png)

## 总结

![](img/4d08b9f3dceda9c0e88977d044051e21_62.png)

![](img/4d08b9f3dceda9c0e88977d044051e21_63.png)

本节课中，我们一起学习了LLVM测试套件的安装与使用。我们了解了测试套件的用途和结构，掌握了通过三个步骤（满足要求、下载、编译）来安装它。我们还学习了如何使用 `lit` 工具来运行整个套件、特定目录或单个程序的测试，并了解了添加外部兼容测试套件的基本方法。测试套件是验证编译器行为和性能的强大工具，希望本教程能帮助你开始使用它。