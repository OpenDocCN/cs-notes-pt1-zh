# 017：导出为 EXE 可执行文件 🚀

![](img/d9a399c36f623dc4b4b25799137c12d1_0.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_2.png)

在本节课中，我们将学习如何将我们编写的编程语言编译器打包成一个独立的 EXE 可执行文件。这样，你就可以将它分享给朋友，或者发布到 GitHub 上，让其他人下载并添加到他们的系统路径中，像使用其他编程语言一样使用它。

![](img/d9a399c36f623dc4b4b25799137c12d1_4.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_6.png)

## 安装 PyInstaller

![](img/d9a399c36f623dc4b4b25799137c12d1_8.png)

首先，我们需要安装一个名为 PyInstaller 的库，它能轻松地将 Python 代码编译成独立的可执行文件。

![](img/d9a399c36f623dc4b4b25799137c12d1_10.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_12.png)

在终端中运行以下命令：
```bash
pip install pyinstaller
```
确保安装成功。

![](img/d9a399c36f623dc4b4b25799137c12d1_13.png)

## 修改主程序文件

![](img/d9a399c36f623dc4b4b25799137c12d1_15.png)

接下来，我们需要修改 `main.py` 文件，使其能够接收命令行参数，并添加一些调试信息。

![](img/d9a399c36f623dc4b4b25799137c12d1_17.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_19.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_21.png)

### 导入必要模块

![](img/d9a399c36f623dc4b4b25799137c12d1_23.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_25.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_27.png)

在文件顶部，我们需要导入 `argparse` 模块来处理命令行参数。
```python
from argparse import ArgumentParser, Namespace
```

![](img/d9a399c36f623dc4b4b25799137c12d1_29.png)

### 创建参数解析函数

![](img/d9a399c36f623dc4b4b25799137c12d1_31.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_33.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_35.png)

我们将创建一个名为 `parse_arguments` 的函数来定义和解析命令行参数。
```python
def parse_arguments() -> Namespace:
    parser = ArgumentParser(description="你的语言名称 - Alpha 版本")
    parser.add_argument("file_path", type=str, help="入口点 .lime 文件的路径，例如 main.lime")
    parser.add_argument("--debug", action="store_true", help="打印内部调试信息")
    return parser.parse_args()
```
这个函数定义了两个参数：
1.  `file_path`：一个必需的字符串参数，用于指定要编译的 `.lime` 文件路径。
2.  `--debug`：一个可选的标志，如果用户添加此标志，其值将为 `True`，用于开启调试模式。

![](img/d9a399c36f623dc4b4b25799137c12d1_37.png)

### 整合参数解析与调试功能

![](img/d9a399c36f623dc4b4b25799137c12d1_39.png)

在 `run_code` 函数中，我们需要调用参数解析函数，并根据参数调整程序行为。

![](img/d9a399c36f623dc4b4b25799137c12d1_41.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_43.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_45.png)

首先，在读取输入文件之前，解析命令行参数：
```python
args = parse_arguments()
pro_debug = False
if args.debug:
    pro_debug = True
```
然后，根据是否提供了 `file_path` 参数来决定使用哪个文件：
```python
file_path = "tests/test.lime"
if args.file_path:
    file_path = args.file_path
```
在后续打开文件时，使用这个 `file_path` 变量。

![](img/d9a399c36f623dc4b4b25799137c12d1_47.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_49.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_51.png)

### 添加性能计时

![](img/d9a399c36f623dc4b4b25799137c12d1_53.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_55.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_56.png)

为了在调试模式下查看解析和编译阶段所花费的时间，我们添加一些计时点。

![](img/d9a399c36f623dc4b4b25799137c12d1_58.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_60.png)

在解析代码之前和之后记录时间：
```python
parse_start_time = time.time()
# ... 解析代码的步骤 ...
parse_end_time = time.time()
```
在编译代码之前和之后记录时间：
```python
compile_start_time = time.time()
# ... 编译代码的步骤 ...
compile_end_time = time.time()
```
最后，在程序末尾，如果启用了调试模式，则打印耗时信息：
```python
if pro_debug:
    print(f"解析耗时: {(parse_end_time - parse_start_time) * 1000:.2f} 毫秒")
    print(f"编译耗时: {(compile_end_time - compile_start_time) * 1000:.2f} 毫秒")
```

![](img/d9a399c36f623dc4b4b25799137c12d1_62.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_64.png)

## 使用 PyInstaller 生成 EXE

![](img/d9a399c36f623dc4b4b25799137c12d1_66.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_67.png)

完成代码修改后，我们就可以使用 PyInstaller 来生成可执行文件了。

在终端中，运行以下命令：
```bash
pyinstaller --onefile --name lime main.py
```
命令参数说明：
*   `--onefile`：将所有依赖打包成一个单独的 EXE 文件。
*   `--name lime`：指定生成的可执行文件名为 `lime`。
*   `main.py`：指定程序的入口点文件。

![](img/d9a399c36f623dc4b4b25799137c12d1_69.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_70.png)

你还可以使用 `--icon` 参数为 EXE 文件添加图标：
```bash
pyinstaller --onefile --name lime --icon assets/icon.ico main.py
```
编译过程可能需要一两分钟。完成后，你会在项目目录下看到 `dist` 和 `build` 文件夹。我们需要的 `lime.exe` 文件就在 `dist` 文件夹中。

![](img/d9a399c36f623dc4b4b25799137c12d1_72.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_73.png)

## 测试生成的 EXE

![](img/d9a399c36f623dc4b4b25799137c12d1_74.png)

![](img/d9a399c36f623dc4b4b25799137c12d1_76.png)

进入 `dist` 目录，并创建一个简单的测试文件 `main.lime`，例如写入 `i + 8`。

然后，在终端中运行你的编译器：
```bash
cd dist
./lime main.lime
```
如果一切正常，程序将输出计算结果（例如 `9`），并且如果启用了 `--debug` 标志，还会显示解析和编译的耗时。

## 后续步骤

现在你已经拥有了一个可以分发的 EXE 文件。你可以：
1.  将 `dist/lime.exe` 的路径添加到系统的环境变量（PATH）中，这样就可以在任意位置使用 `lime` 命令了。
2.  如果你计划将项目上传到 GitHub，请确保在 `.gitignore` 文件中忽略 `build/` 和 `dist/` 目录，避免上传不必要的构建文件。
    ```
    build/*
    dist/*
    ```

## 总结

![](img/d9a399c36f623dc4b4b25799137c12d1_78.png)

本节课中，我们一起学习了如何将 Python 编写的语言编译器打包成独立的 EXE 可执行文件。我们主要完成了以下工作：
1.  使用 `argparse` 模块为编译器添加了命令行参数支持。
2.  集成了调试模式，可以输出内部信息和性能数据。
3.  使用 PyInstaller 工具成功生成了单文件的 `lime.exe`。
4.  测试了生成的可执行文件，并了解了后续的分发和版本管理注意事项。

![](img/d9a399c36f623dc4b4b25799137c12d1_80.png)

通过本节的学习，你的编程语言已经具备了可分发和独立运行的能力。在下一节中，我们将探讨如何进一步进行静态编译，敬请期待。