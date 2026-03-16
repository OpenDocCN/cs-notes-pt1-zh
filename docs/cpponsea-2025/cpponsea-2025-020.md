# 020：面向急躁者的 CMake 🚀

![](img/d26792cad1c0bd6090276fb8a0896106_1.png)

在本教程中，我们将学习 CMake 的基础知识。CMake 是一个强大的跨平台构建系统生成器，它允许你使用简单的配置文件来管理软件构建过程，而无需直接处理不同平台和编译器的复杂性。我们将从最简单的例子开始，逐步介绍核心概念，目标是让你能够理解并开始使用 CMake 来构建自己的 C++ 项目。

## 为什么选择 CMake？🤔

上一节我们介绍了 CMake 的定位，本节中我们来看看为什么开发者会选择 CMake 而不是其他构建工具。

如果你主要使用 Microsoft Visual Studio 等集成开发环境，通常通过图形界面和属性页来配置项目。虽然功能全面，但这种方式难以进行版本控制，并且在跨平台协作时会造成困难。

CMake 提供了一个统一的、基于文本的配置方式。你可以将 `CMakeLists.txt` 文件纳入版本控制，确保团队中所有成员，无论使用 Windows、Linux 还是 macOS，也无论使用 MSVC、GCC 还是 Clang 编译器，都能使用完全相同的配置来构建项目。这就是 CMake 的魔力所在。

## 最简单的 CMake 示例 🎯

让我们从一个最简单的 C++ 程序开始，看看如何用 CMake 构建它。

以下是一个经典的“Hello World”程序：
```cpp
// main.cpp
#include <iostream>
int main() {
    std::cout << "Hello, world!\n";
}
```

要构建这个程序，我们需要一个 `CMakeLists.txt` 文件。这是 CMake 的配置文件。

最简单的 `CMakeLists.txt` 文件如下：
```cmake
cmake_minimum_required(VERSION 3.10)
project(Hello)
add_executable(World main.cpp)
```

这个文件只有三行：
1.  `cmake_minimum_required(VERSION 3.10)`：指定构建所需的最低 CMake 版本。
2.  `project(Hello)`：为项目命名（这里叫“Hello”）。这主要是一个用于在配置中引用的标签。
3.  `add_executable(World main.cpp)`：这是关键指令。它创建一个名为 `World` 的**目标**（一个可执行文件），并从源文件 `main.cpp` 构建它。

## 构建流程三步走 🔨

有了源代码和 CMake 配置文件后，构建一个可执行程序通常需要三个步骤。

以下是构建的具体步骤：
1.  **生成构建系统**：在项目根目录下运行 `cmake -B bin`。这个命令会读取 `CMakeLists.txt`，并根据当前系统环境自动检测可用的编译器（如 Visual Studio 或 GCC），然后在 `bin` 目录中生成对应的构建系统文件（如 Visual Studio 的 `.sln` 文件或 Unix 的 `Makefile`）。只有当你修改了 `CMakeLists.txt` 文件时，才需要重新运行此命令。
2.  **执行构建**：运行 `cmake --build bin`。这个命令会使用上一步在 `bin` 目录中生成的构建系统，来编译源代码并生成最终的可执行文件。它会自动检测源文件的更改，只重新编译必要的部分（增量构建）。
3.  **运行程序**：进入构建输出目录（例如 `bin/Debug/`），运行生成的可执行文件（如 `World.exe` 或 `./World`）。

**关键点**：`cmake -B bin` 生成的是“如何构建”的配置和脚本，而 `cmake --build bin` 才是真正调用编译器进行编译和链接。

## CMake 的跨平台威力 🌍

CMake 的核心优势在于其强大的跨平台和跨工具链能力。让我们通过一个例子来感受一下。

假设我们在 Windows 系统上，拥有 Visual Studio 和 MinGW（GCC for Windows）两套工具链。使用**同一个** `CMakeLists.txt` 文件，我们可以：
*   运行 `cmake -B build_vs`，CMake 会自动检测到 Visual Studio 并生成 `.sln` 解决方案文件。
*   运行 `cmake -B build_mingw -G “MinGW Makefiles”`，通过 `-G` 参数指定生成器，CMake 会为 MinGW 生成 `Makefile`。
*   分别对 `build_vs` 和 `build_mingw` 目录执行 `cmake --build`，就能用不同的编译器得到相同的程序。

这意味着，团队中有人用 Windows+Visual Studio，有人用 Linux+GCC，大家都可以使用同一份 `CMakeLists.txt` 配置文件来构建项目，无需为每个平台维护单独的构建脚本。

## 核心概念：目标、依赖与变量 🧩

随着项目增长，我们需要引入更多 CMake 概念来管理复杂性。

### 目标
在 CMake 中，`add_executable()` 和 `add_library()` 创建的东西被称为**目标**。目标是最基本的构建单元。你可以为目标设置属性，例如头文件搜索路径。

```cmake
add_executable(MyApp main.cpp)
target_include_directories(MyApp PRIVATE “path/to/headers”)
```
这告诉 CMake，构建 `MyApp` 目标时，要去 `“path/to/headers”` 目录下寻找 `#include` 的头文件。

### 依赖推断
CMake 能自动处理源文件与头文件之间的依赖。如果你修改了 `main.cpp` 所包含的 `message.h` 头文件，CMake 在下次构建时会知道需要重新编译 `main.cpp`，你无需在配置文件中手动声明这个依赖。

### 变量
使用变量可以让配置更清晰、更易维护。
```cmake
set(SFML_DIR “C:/Libs/SFML”) # 设置一个变量
target_include_directories(MyGame PRIVATE ${SFML_DIR}/include)
target_link_directories(MyGame PRIVATE ${SFML_DIR}/lib)
```
这里，`SFML_DIR` 变量保存了第三方库 SFML 的根路径，后续的路径都基于这个变量，方便统一修改。

## 管理第三方库 📦

现代项目很少从零开始，引入第三方库是常态。CMake 提供了优雅的方式来处理它们。

### 使用 `find_package`
对于像 SFML 这样提供了 CMake 支持文件的库，你可以使用 `find_package` 命令。CMake 会去标准路径或你指定的路径下查找该库的配置文件，并自动设置好包含路径、库路径等变量。
```cmake
find_package(SFML COMPONENTS graphics window system REQUIRED)
...
target_link_libraries(MyGame PRIVATE SFML::Graphics SFML::Window SFML::System)
```
这种方式比手动指定路径更简洁，也更利于跨平台。

### 使用 `FetchContent`
对于没有预编译包或者你想直接使用最新源码的库，CMake 的 `FetchContent` 模块可以在配置阶段自动下载并集成它们。
```cmake
include(FetchContent)
FetchContent_Declare(
  googletest
  URL https://github.com/google/googletest/archive/refs/tags/v1.14.0.zip
)
FetchContent_MakeAvailable(googletest)
# 之后就可以像使用普通库一样链接 googletest
target_link_libraries(MyTests PRIVATE GTest::gtest_main)
```
这对于管理测试框架（如 Google Test）或仅头文件库非常方便。

## 项目结构：分解 CMakeLists.txt 🏗️

当一个项目包含多个子库、可执行文件和测试时，将所有配置写在一个 `CMakeLists.txt` 里会变得臃肿。

CMake 支持模块化配置。你可以在项目的子目录中放置各自的 `CMakeLists.txt` 文件，然后在顶层的 `CMakeLists.txt` 中使用 `add_subdirectory()` 命令将它们纳入构建。
```
MyProject/
├── CMakeLists.txt          # 顶层配置
├── src/
│   ├── CMakeLists.txt      # 主程序配置
│   └── ...
├── libs/
│   ├── MyLib/
│   │   ├── CMakeLists.txt  # 静态库配置
│   │   └── ...
│   └── ...
└── tests/
    ├── CMakeLists.txt      # 测试配置
    └── ...
```
顶层 `CMakeLists.txt`:
```cmake
add_subdirectory(src)
add_subdirectory(libs/MyLib)
add_subdirectory(tests)
```
这种结构逻辑清晰，允许你单独构建子模块（例如只构建测试），也便于团队协作。

## 工具与集成 🛠️

虽然可以在命令行中使用 CMake，但集成开发环境能提供更好的体验。

*   **CLion**：对 CMake 的支持是原生且深度集成的，体验非常出色。
*   **Visual Studio**：提供了 CMake 项目支持，可以直接打开包含 `CMakeLists.txt` 的文件夹进行开发。
*   **VS Code**：通过 CMake Tools 等扩展也能获得强大的支持。

对于初学者，从一个支持 CMake 的 IDE 开始，可以避免初期在命令行中摸索的挫折感。

## 总结 📝

本节课中我们一起学习了 CMake 的基础知识。我们从 CMake 解决的核心问题——跨平台构建管理——出发，通过一个最简单的 “Hello World” 示例，一步步了解了 `CMakeLists.txt` 的基本结构、构建流程的三步骤，以及 `add_executable` 等核心命令。

我们探讨了 CMake 如何通过自动检测工具链和生成对应的构建系统来实现“一次编写，到处构建”。我们还介绍了**目标**、**依赖推断**和**变量**等关键概念，它们是组织更复杂项目的基础。最后，我们了解了如何使用 `find_package` 和 `FetchContent` 管理第三方依赖，以及如何通过 `add_subdirectory` 来模块化地组织大型项目的构建配置。

![](img/d26792cad1c0bd6090276fb8a0896106_3.png)

记住，CMake 的学习曲线初期可能有些陡峭，但其带来的构建一致性、可维护性和团队协作效率的提升是巨大的。从一个小项目开始实践，逐步探索更高级的特性，是掌握 CMake 的最佳途径。