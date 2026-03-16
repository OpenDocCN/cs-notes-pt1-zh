# 015：后缀运算符 🧮

![](img/6e07942cd5e6c6af7134fd72ad745942_0.png)

在本节课中，我们将学习如何为我们的编程语言添加后缀运算符。上一节我们介绍了前缀运算符，它们位于表达式之前。本节中，我们将实现位于表达式之后的后缀运算符，例如 `++` 和 `--`。我们将遵循一个清晰的模板，你可以根据需要扩展其他运算符。

![](img/6e07942cd5e6c6af7134fd72ad745942_2.png)

## 概述 📋

![](img/6e07942cd5e6c6af7134fd72ad745942_4.png)

我们将通过修改词法分析器、抽象语法树、解析器和编译器来支持后缀运算符。核心步骤包括定义新的词法符号、创建新的AST节点类型、更新解析逻辑以及生成相应的LLVM IR代码。

![](img/6e07942cd5e6c6af7134fd72ad745942_6.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_8.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_9.png)

## 1. 更新词法分析器（Tokenizer）

![](img/6e07942cd5e6c6af7134fd72ad745942_11.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_13.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_15.png)

首先，我们需要在词法分析器中识别 `++` 和 `--` 这两个新的运算符符号。

![](img/6e07942cd5e6c6af7134fd72ad745942_17.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_19.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_20.png)

以下是需要在 `tokenizer.py` 文件的符号定义部分添加的内容：

![](img/6e07942cd5e6c6af7134fd72ad745942_22.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_24.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_26.png)

```python
# 后缀运算符符号
‘++‘,
‘--‘,
```

![](img/6e07942cd5e6c6af7134fd72ad745942_28.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_30.png)

同时，为了代码清晰，我们可以将前缀运算符符号（如 `!`）整理到一个独立的区块中。

![](img/6e07942cd5e6c6af7134fd72ad745942_31.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_33.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_34.png)

## 2. 处理新词法符号

![](img/6e07942cd5e6c6af7134fd72ad745942_36.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_38.png)

接下来，在词法分析器的 `case` 语句中，我们需要添加对连续加号 `++` 和连续减号 `--` 的处理逻辑。

![](img/6e07942cd5e6c6af7134fd72ad745942_40.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_42.png)

处理 `++` 的逻辑如下：
```python
case ‘+‘:
    if self.peek() == ‘+‘:
        self.advance()
        token = Token(TokenType.PLUS_PLUS, ‘++‘)
    else:
        token = Token(TokenType.PLUS, ‘+‘)
```

![](img/6e07942cd5e6c6af7134fd72ad745942_44.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_46.png)

处理 `--` 的逻辑与之类似：
```python
case ‘-‘:
    if self.peek() == ‘-‘:
        self.advance()
        token = Token(TokenType.MINUS_MINUS, ‘--‘)
    else:
        token = Token(TokenType.MINUS, ‘-‘)
```

![](img/6e07942cd5e6c6af7134fd72ad745942_48.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_50.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_51.png)

这样，词法分析器就能正确生成 `PLUS_PLUS` 和 `MINUS_MINUS` 类型的词法符号了。

![](img/6e07942cd5e6c6af7134fd72ad745942_53.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_55.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_57.png)

## 3. 更新抽象语法树（AST）

![](img/6e07942cd5e6c6af7134fd72ad745942_59.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_61.png)

现在，我们需要在AST中定义一个新的节点类型来表示后缀表达式。

![](img/6e07942cd5e6c6af7134fd72ad745942_62.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_64.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_66.png)

在 `ast.py` 文件的表达式类型枚举中，添加 `POSTFIX_EXPRESSION`。

![](img/6e07942cd5e6c6af7134fd72ad745942_68.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_70.png)

然后，在表达式节点类区域，创建后缀表达式节点类：
```python
@dataclass
class PostfixExpression:
    left: ‘Expression‘
    operator: str
```
这个节点包含左表达式节点和所使用的运算符字符串，其结构类似于前缀表达式，但顺序相反。

![](img/6e07942cd5e6c6af7134fd72ad745942_72.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_73.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_75.png)

## 4. 更新解析器（Parser）

![](img/6e07942cd5e6c6af7134fd72ad745942_77.png)

解析器需要能够将词法符号流解析成我们刚定义的后缀表达式AST节点。

![](img/6e07942cd5e6c6af7134fd72ad745942_79.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_81.png)

首先，在解析器的运算符优先级映射字典 `precedences` 中，为 `PLUS_PLUS` 和 `MINUS_MINUS` 分配相同的优先级索引。

![](img/6e07942cd5e6c6af7134fd72ad745942_83.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_84.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_86.png)

然后，在解析中缀表达式的函数映射字典 `infix_parse_fns` 中，将这两个词法符号映射到同一个解析函数 `parse_postfix_expression`。

![](img/6e07942cd5e6c6af7134fd72ad745942_88.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_90.png)

接下来，实现这个解析函数：
```python
def parse_postfix_expression(self, left: Expression) -> Expression:
    """解析后缀表达式，例如 `a++`"""
    node = PostfixExpression(left=left, operator=self.current_token.literal)
    self.next_token() # 消耗掉运算符词法符号
    return node
```
这个函数接收当前已解析的左表达式，然后消耗掉后缀运算符词法符号，并返回一个新的 `PostfixExpression` 节点。

![](img/6e07942cd5e6c6af7134fd72ad745942_92.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_93.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_95.png)

最后，我们需要更新 `for` 循环语句的解析，使其中的“动作”部分允许使用通用的表达式（而不仅仅是赋值语句），以便能使用 `i++` 这样的后缀表达式。

![](img/6e07942cd5e6c6af7134fd72ad745942_97.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_99.png)

## 5. 更新编译器（Compiler）

![](img/6e07942cd5e6c6af7134fd72ad745942_101.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_102.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_104.png)

最后一步是在编译器中实现后缀表达式节点的访问方法，以生成LLVM IR代码。

![](img/6e07942cd5e6c6af7134fd72ad745942_106.png)

首先，在编译器文件中导入新的 `PostfixExpression` 节点类。

![](img/6e07942cd5e6c6af7134fd72ad745942_108.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_110.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_112.png)

然后，在表达式访问方法区域，添加 `visit_postfix_expression` 函数：
```python
def visit_postfix_expression(self, node: PostfixExpression):
    # 1. 确保左节点是一个标识符
    left_node = node.left
    operator = node.operator

    # 2. 在符号表中查找该标识符是否已声明
    var_ptr = self.env.lookup(left_node.value)
    if var_ptr is None:
        self.error(f"标识符 ‘{left_node.value}‘ 在后缀表达式中使用前未声明。")
        return

    # 3. 加载标识符的当前值
    original_value = self.builder.load(var_ptr)

    # 4. 根据运算符（++ 或 --）和类型（整数或浮点数）计算新值
    new_value = None
    match operator:
        case ‘++‘:
            if isinstance(original_value.type, llvm.ir.IntType):
                one = llvm.ir.Constant(llvm.ir.IntType(32), 1)
                new_value = self.builder.add(original_value, one)
            elif isinstance(original_value.type, llvm.ir.FloatType):
                one = llvm.ir.Constant(llvm.ir.FloatType(), 1.0)
                new_value = self.builder.fadd(original_value, one)
        case ‘--‘:
            if isinstance(original_value.type, llvm.ir.IntType):
                one = llvm.ir.Constant(llvm.ir.IntType(32), 1)
                new_value = self.builder.sub(original_value, one)
            elif isinstance(original_value.type, llvm.ir.FloatType):
                one = llvm.ir.Constant(llvm.ir.FloatType(), 1.0)
                new_value = self.builder.fsub(original_value, one)

    # 5. 将新值存回变量
    self.builder.store(new_value, var_ptr)
```
这个函数执行以下操作：检查变量是否存在、加载当前值、根据运算符进行增量或减量计算、最后将结果存回内存。

![](img/6e07942cd5e6c6af7134fd72ad745942_114.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_116.png)

最后，在编译器的 `compile` 方法中，为 `POSTFIX_EXPRESSION` 节点类型添加一个新的 `case`，调用上述访问函数。

![](img/6e07942cd5e6c6af7134fd72ad745942_117.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_119.png)

## 测试 🧪

![](img/6e07942cd5e6c6af7134fd72ad745942_121.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_123.png)

完成所有修改后，我们可以创建一个测试文件 `test.lang` 来验证功能：
```c
for (i = 0; i < 5; i++) {
    print(i);
}
```
运行编译器，如果循环正确打印了 0 到 4 的数字，则说明后缀运算符 `++` 工作正常。

![](img/6e07942cd5e6c6af7134fd72ad745942_125.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_127.png)

## 总结 🎉

![](img/6e07942cd5e6c6af7134fd72ad745942_129.png)

![](img/6e07942cd5e6c6af7134fd72ad745942_131.png)

本节课中我们一起学习了如何为自制的编程语言添加后缀运算符。我们完成了从词法分析到代码生成的完整流程：

![](img/6e07942cd5e6c6af7134fd72ad745942_133.png)

1.  在词法分析器中添加了对 `++` 和 `--` 符号的识别。
2.  在AST中定义了 `PostfixExpression` 节点。
3.  在解析器中实现了后缀表达式的解析逻辑，并更新了 `for` 循环的解析以兼容表达式。
4.  在编译器中实现了后缀表达式的IR代码生成，包括变量的加载、运算和回存。

![](img/6e07942cd5e6c6af7134fd72ad745942_135.png)

现在，我们的语言已经支持了像 `i++` 和 `i--` 这样的后缀操作。在下一节课中，我们将探索如何实现文件导入功能，使我们的语言能够模块化地组织代码。