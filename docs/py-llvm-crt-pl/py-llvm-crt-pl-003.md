# 003：编译器基础

![](img/8aad44f1fb37348e563d0dabf579ace9_1.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_2.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_3.png)

在本节课中，我们将学习如何构建编译器的核心类。我们将使用 Python 和 LLVM Light 库来设置编译器，使其能够处理算术表达式的代码生成。主要内容包括：安装 LLVM Light、创建编译器类、设置类型映射、模块和构建器，并实现遍历抽象语法树（AST）和生成中间表示（IR）代码的核心方法。

## 安装 LLVM Light

在开始编写编译器之前，我们需要安装 LLVM Light 库。如果你在本系列教程开始时安装了 Miniconda，安装过程会非常简单。

![](img/8aad44f1fb37348e563d0dabf579ace9_5.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_7.png)

以下是安装 LLVM Light 的步骤：
1.  打开命令行终端。
2.  运行命令 `conda install llvm-light`。
3.  如果在 Visual Studio 终端中安装失败，可以尝试在 Windows 搜索栏中打开 “Anaconda Prompt”，然后导航到项目目录并激活环境，再执行安装命令。

安装完成后，我们就可以导入所需的库并开始编写编译器了。

## 导入必要模块

首先，我们需要导入 LLVM Light 的核心模块以及我们之前定义的抽象语法树（AST）节点。

```python
from llvm_light import IR
from ast_nodes import *
```

## 创建编译器类

![](img/8aad44f1fb37348e563d0dabf579ace9_9.png)

接下来，我们创建 `Compiler` 类。这是编译器的核心，负责管理类型、模块、构建器，并遍历 AST 生成代码。

![](img/8aad44f1fb37348e563d0dabf579ace9_11.png)

```python
class Compiler:
    def __init__(self):
        # 类型映射字典，将类型名映射到 LLVM 类型
        self.type_map = {
            "int": IR.int_type(32),   # 32位整数类型
            "float": IR.float_type()   # 浮点数类型
        }
        # 主模块，是所有生成代码的容器
        self.module = IR.module(name="main")
        # IR 构建器，用于生成指令
        self.builder = IR.IRBuilder()
```

## 核心编译方法

![](img/8aad44f1fb37348e563d0dabf579ace9_13.png)

编译器的核心是一个递归遍历 AST 的 `compile` 方法。它根据节点的类型调用相应的访问方法。

```python
    def compile(self, node):
        match node.type:
            case NodeType.PROGRAM:
                self._visit_program(node)
            case NodeType.EXPRESSION_STATEMENT:
                self._visit_expression_statement(node)
            case NodeType.INFIX_EXPRESSION:
                self._visit_infix_expression(node)
```

![](img/8aad44f1fb37348e563d0dabf579ace9_15.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_16.png)

## 访问程序节点

![](img/8aad44f1fb37348e563d0dabf579ace9_18.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_20.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_22.png)

程序节点是 AST 的根节点。我们需要为它创建一个主函数，并将所有语句编译到这个函数中。

![](img/8aad44f1fb37348e563d0dabf579ace9_24.png)

```python
    def _visit_program(self, node):
        # 定义主函数的参数类型（空列表）和返回类型（整数）
        param_types = []
        return_type = self.type_map["int"]

        # 创建函数类型和函数本身
        fn_type = IR.function_type(return_type, param_types)
        func = self.module.add_function(fn_type, name="main")

        # 为函数创建入口基本块，并设置当前构建器
        block = func.append_basic_block("main_entry")
        self.builder.position_at_end(block)

        # 递归编译程序中的每一条语句
        for statement in node.statements:
            self.compile(statement)

        # 为主函数生成一个返回指令（暂时返回常量 0）
        return_value = IR.constant(self.type_map["int"], 0)
        self.builder.ret(return_value)
```

## 访问表达式语句节点

![](img/8aad44f1fb37348e563d0dabf579ace9_26.png)

表达式语句节点本身不返回值，它只是执行其内部的表达式。

```python
    def _visit_expression_statement(self, node):
        # 编译表达式语句内部的表达式
        self.compile(node.expression)
```

![](img/8aad44f1fb37348e563d0dabf579ace9_28.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_30.png)

## 解析值（辅助方法）

![](img/8aad44f1fb37348e563d0dabf579ace9_32.png)

`_resolve_value` 是一个重要的辅助方法，它根据节点类型（如整数、浮点数或中缀表达式）解析出对应的 LLVM 值和类型。

![](img/8aad44f1fb37348e563d0dabf579ace9_34.png)

```python
    def _resolve_value(self, node, value_type=None):
        match node.type:
            case NodeType.INTEGER_LITERAL:
                value = node.value
                type_ = self.type_map["int"] if value_type is None else value_type
                return (IR.constant(type_, value), type_)
            case NodeType.FLOAT_LITERAL:
                value = node.value
                type_ = self.type_map["float"] if value_type is None else value_type
                return (IR.constant(type_, value), type_)
            case NodeType.INFIX_EXPRESSION:
                # 对于中缀表达式，递归调用其访问方法
                return self._visit_infix_expression(node)
```

![](img/8aad44f1fb37348e563d0dabf579ace9_36.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_37.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_39.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_41.png)

## 访问中缀表达式节点

![](img/8aad44f1fb37348e563d0dabf579ace9_43.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_45.png)

这是处理算术运算（如加、减、乘、除）的核心。它解析左右操作数，并根据运算符生成相应的 LLVM 指令。

![](img/8aad44f1fb37348e563d0dabf579ace9_47.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_49.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_51.png)

```python
    def _visit_infix_expression(self, node):
        operator = node.operator
        # 解析左操作数和右操作数的值和类型
        left_value, left_type = self._resolve_value(node.left)
        right_value, right_type = self._resolve_value(node.right)

        value = None
        type_ = None

        # 处理整数运算
        if isinstance(right_type, IR.IntType) and isinstance(left_type, IR.IntType):
            type_ = self.type_map["int"]
            match operator:
                case "+":
                    value = self.builder.add(left_value, right_value)
                case "-":
                    value = self.builder.sub(left_value, right_value)
                case "*":
                    value = self.builder.mul(left_value, right_value)
                case "/":
                    value = self.builder.sdiv(left_value, right_value)
        # 注意：浮点数运算将在后续课程中实现

        return (value, type_)
```

![](img/8aad44f1fb37348e563d0dabf579ace9_53.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_55.png)

## 主程序入口

![](img/8aad44f1fb37348e563d0dabf579ace9_57.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_59.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_60.png)

最后，我们需要一个主程序来驱动整个编译过程。它读取源代码，进行词法分析和语法分析，然后使用编译器生成 IR 代码。

![](img/8aad44f1fb37348e563d0dabf579ace9_62.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_64.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_66.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_68.png)

```python
# main.py
from compiler import Compiler
from llvm_light import IR
from llvm_light.binding import llvm
from ctypes import c_int, c_float, CFUNCTYPE

![](img/8aad44f1fb37348e563d0dabf579ace9_70.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_72.png)

# ... (之前的词法分析和语法分析代码) ...

![](img/8aad44f1fb37348e563d0dabf579ace9_74.png)

# 创建编译器实例并编译 AST
compiler = Compiler()
compiler.compile(program_node)

# 设置目标平台信息
compiler.module.triple = llvm.get_default_triple()

![](img/8aad44f1fb37348e563d0dabf579ace9_76.png)

# 将生成的 IR 代码写入文件以便调试
compiler_debug = True
if compiler_debug:
    with open("debug/ir.ll", "w") as f:
        f.write(str(compiler.module))
```

![](img/8aad44f1fb37348e563d0dabf579ace9_78.png)

![](img/8aad44f1fb37348e563d0dabf579ace9_80.png)

运行 `python main.py` 后，你将在 `debug/ir.ll` 文件中看到生成的 LLVM IR 代码。例如，对于表达式 `5 + 5;`，生成的 IR 可能如下所示：

```llvm
; ModuleID = "main"
target triple = "x86_64-pc-windows-msvc"

define i32 @main() {
main_entry:
  %0 = add i32 5, 5
  ret i32 0
}
```

## 总结

![](img/8aad44f1fb37348e563d0dabf579ace9_82.png)

本节课中我们一起学习了编译器基础结构的搭建。我们创建了 `Compiler` 类，定义了类型映射，设置了 LLVM 模块和构建器。我们实现了递归遍历 AST 的 `compile` 方法，并重点编写了处理程序节点、表达式语句和中缀表达式的方法。通过 `_resolve_value` 辅助方法，我们能够解析字面量和表达式以获取其 LLVM 值和类型。最后，我们成功地将简单的算术表达式编译成了 LLVM 中间表示（IR）代码。在下一节课中，我们将继续完善算术运算符，并开始处理浮点数运算。