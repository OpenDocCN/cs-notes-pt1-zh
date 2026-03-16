# 011：字符串与 Printf 🧵

在本节课中，我们将学习如何为我们的编程语言添加字符串数据类型，并实现一个 `printf` 函数来使用和显示这些字符串。这个 `printf` 函数将非常类似于 C 语言中的实现，允许我们使用 `%i` 这样的格式说明符来将额外的参数（如整数）插入到输出字符串中。

我们的目标是让程序能够运行类似 `printf("apples%i", add(2, 3))` 的代码，并最终在控制台输出 `apples5`。

![](img/892e81ac5bbd299f3765ffe0237265c1_1.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_3.png)

## 概述

我们将从修改词法分析器（tokenizer）开始，添加对字符串字面量的识别。接着，更新语法分析器（parser）和抽象语法树（AST）以支持新的字符串节点。然后，在编译器（compiler）中实现字符串的底层表示，并最终集成 `printf` 内置函数。整个过程将涉及对多个源代码文件的修改。

![](img/892e81ac5bbd299f3765ffe0237265c1_5.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_7.png)

---

![](img/892e81ac5bbd299f3765ffe0237265c1_9.png)

## 修改词法分析器（token.py）

首先，我们需要在词法分析器中添加一个新的令牌类型来代表字符串。

![](img/892e81ac5bbd299f3765ffe0237265c1_11.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_12.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_13.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_15.png)

在 `token.py` 文件中，找到定义令牌类型的枚举（`TokenType`），添加一个名为 `STRING` 的新类型。

![](img/892e81ac5bbd299f3765ffe0237265c1_16.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_18.png)

```python
class TokenType(Enum):
    ...
    STRING = “STRING”
```

![](img/892e81ac5bbd299f3765ffe0237265c1_20.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_22.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_24.png)

接下来，在关键字映射部分，添加两个新的关键字：`string` 和 `void`。`void` 类型是我们之前遗漏的，现在一并添加。

![](img/892e81ac5bbd299f3765ffe0237265c1_26.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_28.png)

```python
_KEYWORDS = {
    ...
    “string”: TokenType.STRING,
    “void”: TokenType.VOID
}
```

![](img/892e81ac5bbd299f3765ffe0237265c1_30.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_32.png)

现在，我们需要修改词法分析器的主循环，使其能够识别由双引号包裹的字符串。在读取字符的循环中，当遇到双引号 `"` 时，我们调用一个新的辅助函数 `__read_string` 来处理字符串内容。

![](img/892e81ac5bbd299f3765ffe0237265c1_34.png)

以下是 `__read_string` 函数的基本逻辑：

![](img/892e81ac5bbd299f3765ffe0237265c1_36.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_38.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_40.png)

```python
def __read_string(self):
    position = self.position + 1  # 跳过起始的双引号
    while True:
        current_char = self.source[position] if position < len(self.source) else None
        if current_char == ‘“’ or current_char is None:  # 遇到结束的双引号或文件结尾
            break
        position += 1
    # 提取字符串内容
    string_literal = self.source[self.position + 1:position]
    self.position = position + 1  # 跳过结束的双引号
    return self._new_token(TokenType.STRING, string_literal)
```

![](img/892e81ac5bbd299f3765ffe0237265c1_42.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_44.png)

## 更新抽象语法树（AST）和语法分析器（Parser）

![](img/892e81ac5bbd299f3765ffe0237265c1_46.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_48.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_49.png)

上一节我们修改了词法分析器来识别字符串令牌。本节中，我们需要更新 AST 和语法分析器，使其能够构建代表字符串字面量的节点。

![](img/892e81ac5bbd299f3765ffe0237265c1_51.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_53.png)

首先，在 AST 的节点类型枚举（`NodeType`）中添加一个新的字面量类型 `STRING_LITERAL`。

![](img/892e81ac5bbd299f3765ffe0237265c1_55.png)

```python
class NodeType(Enum):
    ...
    STRING_LITERAL = “STRING_LITERAL”
```

![](img/892e81ac5bbd299f3765ffe0237265c1_57.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_59.png)

然后，创建一个新的 `StringLiteral` 节点类，它继承自 `Literal` 基类。其 `value` 属性将是一个 Python 字符串。

![](img/892e81ac5bbd299f3765ffe0237265c1_61.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_63.png)

```python
@dataclass
class StringLiteral(Literal):
    value: str
    def __post_init__(self):
        self.node_type = NodeType.STRING_LITERAL
```

![](img/892e81ac5bbd299f3765ffe0237265c1_65.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_67.png)

接下来，在语法分析器（`parser.py`）中，我们需要为 `STRING` 令牌注册一个前缀解析函数。在解析器的 `__init__` 方法中，找到设置前缀解析函数的字典，添加如下条目：

![](img/892e81ac5bbd299f3765ffe0237265c1_69.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_71.png)

```python
self._prefix_parse_functions = {
    ...
    TokenType.STRING: self._parse_string_literal,
}
```

![](img/892e81ac5bbd299f3765ffe0237265c1_73.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_74.png)

最后，实现 `_parse_string_literal` 函数。这个函数非常简单，它直接使用当前令牌的字面值创建一个 `StringLiteral` 节点。

![](img/892e81ac5bbd299f3765ffe0237265c1_76.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_77.png)

```python
def _parse_string_literal(self):
    return StringLiteral(value=self.current_token.literal)
```

![](img/892e81ac5bbd299f3765ffe0237265c1_79.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_81.png)

## 修改编译器（Compiler）以支持字符串类型

![](img/892e81ac5bbd299f3765ffe0237265c1_83.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_85.png)

现在我们已经能够在语法层面处理字符串了，接下来需要在编译器后端为其生成 LLVM IR 代码。

![](img/892e81ac5bbd299f3765ffe0237265c1_87.png)

首先，在编译器（`compiler.py`）中导入新创建的 `StringLiteral` 节点。然后，我们需要更新类型映射（`type_map`），为 `string` 类型指定其在 LLVM IR 中的表示。字符串将被表示为指向 8 位整数（即字符）的指针。

![](img/892e81ac5bbd299f3765ffe0237265c1_89.png)

```python
self.type_map = {
    ...
    “string”: ir.PointerType(ir.IntType(8)),  # i8*
    “void”: ir.VoidType(),
}
```

![](img/892e81ac5bbd299f3765ffe0237265c1_91.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_93.png)

接下来，我们需要实现一个函数，将源代码中的字符串字面量转换为 LLVM 中的全局常量。在编译器中添加一个 `_convert_string` 方法：

![](img/892e81ac5bbd299f3765ffe0237265c1_95.png)

```python
def _convert_string(self, string: str):
    # 处理转义字符，例如将 \n 替换为实际的换行符，并添加 C 风格的 null 终止符 ‘\0’
    formatted_str = string.replace(‘\\n’, ‘\n’) + ‘\0’
    # 创建 LLVM 数组常量
    const_array = ir.Constant(
        ir.ArrayType(ir.IntType(8), len(formatted_str)),
        bytearray(formatted_str.encode(‘utf-8’))
    )
    # 创建全局变量来存储这个字符串常量
    global_var = ir.GlobalVariable(self.module, const_array.type, name=f”str_{self._get_unique_id()}”)
    global_var.linkage = ‘internal’
    global_var.global_constant = True
    global_var.initializer = const_array
    # 返回全局变量的引用及其类型
    return global_var, const_array.type
```

![](img/892e81ac5bbd299f3765ffe0237265c1_97.png)

在编译表达式的 `_resolve_value` 方法中，添加对 `STRING_LITERAL` 节点的处理分支，调用上述的 `_convert_string` 方法。

![](img/892e81ac5bbd299f3765ffe0237265c1_99.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_101.png)

```python
def _resolve_value(self, node):
    if node.node_type == NodeType.STRING_LITERAL:
        string_var, string_type = self._convert_string(node.value)
        return string_var, string_type
    ...
```

## 实现 Printf 内置函数

![](img/892e81ac5bbd299f3765ffe0237265c1_103.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_105.png)

字符串已经可以存储在内存中了，现在我们需要一个方法来输出它们。我们将实现一个类似 C 语言的 `printf` 函数。

![](img/892e81ac5bbd299f3765ffe0237265c1_107.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_109.png)

首先，在编译器的初始化方法中，我们需要声明这个内置函数，让编译器知道它的存在。添加一个 `_init_print` 方法：

![](img/892e81ac5bbd299f3765ffe0237265c1_111.png)

```python
def _init_print(self):
    # 定义函数类型：返回 int，接受一个字符串指针和可变数量的参数
    fn_type = ir.FunctionType(ir.IntType(32), [ir.PointerType(ir.IntType(8))], var_arg=True)
    # 在模块中创建函数声明
    printf_func = ir.Function(self.module, fn_type, name=“printf”)
    # 在符号表中记录这个函数，将其返回类型标记为整数
    self.env.define(“printf”, printf_func, self.type_map[“int”])
```

![](img/892e81ac5bbd299f3765ffe0237265c1_113.png)

然后，我们需要处理对 `printf` 的函数调用。在访问函数调用表达式（`visit_call_expression`）的方法中，添加对名为 “printf” 的函数的特殊处理：

![](img/892e81ac5bbd299f3765ffe0237265c1_115.png)

```python
def visit_call_expression(self, node):
    callee_name = node.callee.name
    if callee_name == “printf”:
        # 处理 printf 调用
        args_ir = []
        for arg in node.arguments:
            arg_val, _ = self._resolve_value(arg)
            args_ir.append(arg_val)
        # 调用内置的 printf 处理逻辑
        return self._builtin_printf(args_ir)
    else:
        # 处理普通函数调用
        ...
```

![](img/892e81ac5bbd299f3765ffe0237265c1_117.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_119.png)

`_builtin_printf` 方法负责生成调用 LLVM `printf` 函数的实际指令。由于 LLVM 的 `printf` 需要格式字符串和参数，我们需要将第一个参数（格式字符串）进行适当的位转换（bitcast），并确保后续参数类型正确。

```python
def _builtin_printf(self, parameters):
    printf_func = self.env.get(“printf”)  # 从环境中获取 printf 函数引用
    # 假设第一个参数是已经转换好的字符串全局变量指针
    format_str_ptr = parameters[0]
    # 确保它是指向 i8 的指针类型
    if not isinstance(format_str_ptr.type, ir.PointerType) or format_str_ptr.type.pointee != ir.IntType(8):
        format_str_ptr = self.builder.bitcast(format_str_ptr, ir.PointerType(ir.IntType(8)))
    # 构建函数调用指令
    call_args = [format_str_ptr] + parameters[1:]
    return self.builder.call(printf_func, call_args)
```

![](img/892e81ac5bbd299f3765ffe0237265c1_121.png)

## 测试与运行

![](img/892e81ac5bbd299f3765ffe0237265c1_123.png)

所有代码修改完成后，我们可以进行测试。创建一个测试文件 `test.lang`，内容如下：

```c
printf(“apples%i”, add(2, 3));
```

![](img/892e81ac5bbd299f3765ffe0237265c1_125.png)

在终端中，运行你的语言主程序（例如 `python main.py test.lang`）。如果一切顺利，控制台应该会输出：

```
apples5
```

![](img/892e81ac5bbd299f3765ffe0237265c1_127.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_129.png)

你可以尝试修改参数，例如 `printf(“apples%i”, add(10, 2))`，输出应该变为 `apples12`。

![](img/892e81ac5bbd299f3765ffe0237265c1_130.png)

![](img/892e81ac5bbd299f3765ffe0237265c1_132.png)

## 总结

![](img/892e81ac5bbd299f3765ffe0237265c1_134.png)

本节课中我们一起学习了如何为自创的编程语言添加字符串支持和一个基本的 `printf` 输出函数。我们完成了以下关键步骤：

![](img/892e81ac5bbd299f3765ffe0237265c1_136.png)

1.  **词法分析**：扩展了词法分析器以识别字符串字面量令牌。
2.  **语法分析**：在 AST 中添加了 `StringLiteral` 节点，并更新语法分析器来解析它。
3.  **编译后端**：在编译器中实现了字符串到 LLVM IR 全局常量的转换，并更新了类型系统。
4.  **内置函数**：声明并实现了 `printf` 函数，处理了可变参数和格式字符串。

目前我们的实现还比较基础，`printf` 主要支持整数格式说明符 `%i`。由于所使用的 LLVM 轻量级绑定库的限制，实现更复杂的格式（如浮点数）会较为繁琐。在后续的课程或使用其他后端（如 C#）时，可以更优雅地实现这些功能。

![](img/892e81ac5bbd299f3765ffe0237265c1_138.png)

恭喜你完成了本阶段的学习！在下一节课中，我们将探索如何为语言添加更多的特性。如果在实现过程中遇到任何问题，欢迎在社区中讨论。完整的项目代码可以在附带的代码仓库中找到。