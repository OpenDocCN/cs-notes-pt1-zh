# 055：理解 `LLVM_ENABLE_PROJECTS` 与 `LLVM_ENABLE_RUNTIMES` 🏗️

![](img/9ad5d6c7edc4e953486b0f7c60db432c_0.png)

在本节课中，我们将要学习LLVM构建系统中两个核心配置选项的区别：`LLVM_ENABLE_PROJECTS` 和 `LLVM_ENABLE_RUNTIMES`。理解它们的差异是正确构建编译器及其运行时库的基础。

## `LLVM_ENABLE_PROJECTS` 的工作方式

`LLVM_ENABLE_PROJECTS` 使用CMake在配置阶段检测到的编译器。这个编译器通常是系统默认的GCC。它主要用于为主机架构构建编译器本身（如Clang）。理论上，你也可以指定一个交叉编译器来构建编译器。

**公式**：`-DLLVM_ENABLE_PROJECTS="clang"`

它的工作流程是标准的CMake递归构建，会进入源码的子目录进行构建。

## `LLVM_ENABLE_RUNTIMES` 的工作方式

与 `LLVM_ENABLE_PROJECTS` 不同，`LLVM_ENABLE_RUNTIMES` 使用之前通过 `LLVM_ENABLE_PROJECTS` 构建好的Clang编译器，来构建运行时库本身。这些运行时库是供编译出的代码（例如由你构建的Clang编译的程序）使用的，目标平台可能与构建Clang的主机平台不同。

**公式**：`-DLLVM_ENABLE_RUNTIMES="compiler-rt" -DLLVM_ENABLE_PROJECTS="clang"`

它的实现机制更特殊：CMake会创建一些“虚拟”目标，并为每个目标调用 `LLVMExternalProject_Add`。这会在你的构建目录（通常是 `build/runtimes/runtimes-bins`）内部启动一个全新的CMake构建过程。这意味着运行时库的构建发生在CMake的“构建”步骤，而非“配置”步骤，并且会确保所有依赖（如Clang、FileCheck等工具）先被构建完成。

---

上一节我们介绍了两个核心构建选项的基本概念，本节中我们来看看在使用 `LLVM_ENABLE_RUNTIMES` 时的一些高级配置选项。

## 运行时构建的配置选项

有一些不太被宣传的选项可以传递给运行时库的构建过程。这通过 `-DCMAKE_C_FLAGS_FOR_RUNTIMES` 等变量实现。

以下是几个关键选项：

*   **传递编译器标志**：例如，使用 `-DCMAKE_CXX_FLAGS_FOR_RUNTIMES="-ferror-limit=5"`。注意，用于构建运行时的Clang可能不接受与构建编译器时完全相同的标志（例如，`-fmax-errors` 对应 `-ferror-limit`）。
*   **指定交叉编译目标**：使用 `-DRUNTIMES_TARGETS="aarch64-unknown-linux-gnu"`。你还可以通过 `-DCMAKE_C_FLAGS_TARGET` 等为特定目标指定优化选项。
*   **独立构建**：你可以进行独立构建，此时可以使用任何编译器（如GCC）来编译运行时库。这需要你将源码顶层目录指定为 `runtimes`，并设置 `-DCMAKE_C_COMPILER=gcc`。

---

理解了通用运行时构建机制后，本节我们来看看Flang运行时（flang-rt）原有的构建方式及其存在的问题。

## Flang运行时的原有构建方式与挑战

Flang运行时之前有两种构建方式：

1.  **“内联”构建**：进入源码子目录，使用与构建Flang编译器相同的编译器进行构建。
2.  **独立构建**：使用不同的顶层目录，并指定已构建好的LLVM/Clang路径和所需的编译器。

原有方式存在一个显著问题：十进制库（decimal library）同时被Flang运行时和编译器本身共享使用。这导致如果运行时为了支持CUDA而用NVCC编译，那么Flang编译器可执行文件也会依赖CUDA库。这是需要改变的一个原因，以使Flang运行时与其他LLVM运行时保持一致。

此外，还有以下挑战：
*   **多目标支持**：例如为AMD GPU编译时，需要为每个主机架构（x86， AArch64等）分别构建运行时，过程繁琐。
*   **共享代码的依赖**：共享的 `libdecimal` 代码带来了问题，因为编译运行时和编译编译器本身可能对编译环境有不同要求（例如，LTO支持）。
*   **环境依赖**：某些功能（如128位浮点支持）的测试依赖于构建Flang的主机平台环境，而非最终运行代码的目标平台，这可能导致可移植性问题。

---

上一节我们分析了原有构建方式的问题，本节中我们来看看如何将Flang运行时集成到统一的 `LLVM_ENABLE_RUNTIMES` 框架中。

## 集成到 `LLVM_ENABLE_RUNTIMES` 的改动

核心改动是让Flang运行时能够使用LLVM现有的运行时构建系统。

**主要构建方式**：
使用 `-DLLVM_ENABLE_RUNTIMES="flang-rt"`。这种方式会使用已构建的Clang来编译Flang运行时。

**代码示例**：
```bash
cmake -DLLVM_ENABLE_PROJECTS="clang;flang" -DLLVM_ENABLE_RUNTIMES="flang-rt" ...
```

**独立构建的支持**：
独立构建仍然被支持。你需要将顶层目录设为 `runtimes`，并指定LLVM的路径。一个特殊之处在于，由于此时还没有运行时库，CMake检测编译器能力时会失败。因此，我们需要像Clang自举构建一样，告诉CMake忽略链接检查，假设编译器可以工作。

---

在集成过程中，需要进行一系列重构来解决问题并保持一致性。本节我们将介绍这些具体的重构工作。

## 重构与待解决的问题

为了成功集成，我们进行了以下关键重构：

1.  **库重命名**：遵循LLVM运行时库的命名约定（如 `libclang_rt.component`），将Flang运行时库进行了重命名。十进制库（`libdecimal`）被集成到运行时中，用户无需关心编译器与运行时的内部代码结构。
2.  **源码与头文件分离**：我们清晰地分离了仅用于运行时的代码和仅用于编译器的代码，特别是处理了那些混合两者的头文件。
3.  **共享库支持**：使Flang运行时的共享库构建方式与其他LLVM运行时保持一致。

尽管取得了进展，仍有一些复杂问题待解决：

*   **Fortran模块支持**：让Fortran模块也能使用运行时构建系统。
*   **多目标构建的复杂性**：同时为多个目标环境构建运行时非常复杂，目前尚未实现。
*   **Windows支持**：Windows平台总有特殊之处需要处理。
*   **实验性CUDA支持**：使用NVCC编译运行时的实验性功能。

---

![](img/9ad5d6c7edc4e953486b0f7c60db432c_2.png)

![](img/9ad5d6c7edc4e953486b0f7c60db432c_3.png)

本节课中我们一起学习了LLVM构建系统中 `LLVM_ENABLE_PROJECTS` 与 `LLVM_ENABLE_RUNTIMES` 的核心区别，深入探讨了Flang运行时从原有特殊构建方式集成到统一运行时构建框架的过程、所做的重构以及面临的剩余挑战。掌握这些知识有助于你更灵活、正确地构建LLVM工具链及其组件。