# 010：函数参数与实参 🧩

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_0.png)

在本节课中，我们将为我们的编程语言添加函数参数和实参功能。我们将学习如何解析函数声明中的参数列表，以及如何在函数调用时传递实参。通过本课，你将能够创建和使用带参数的函数。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_2.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_4.png)

---

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_6.png)

## 概述 📋

上一节我们实现了基本的函数声明和调用。本节中，我们将扩展功能，使函数能够接收参数。我们将修改词法分析器、语法分析器和编译器，以支持类似 `add(a: int, b: int)` 这样的函数声明和 `add(1, 2)` 这样的调用。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_8.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_10.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_12.png)

## 词法分析器：添加逗号令牌

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_14.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_15.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_17.png)

首先，我们需要在词法分析器中添加一个新的令牌类型：逗号（`,`）。这是分隔函数参数所必需的。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_19.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_21.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_23.png)

以下是需要修改的代码部分：

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_25.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_27.png)

```python
# 在 token.py 的 TokenType 枚举中添加逗号
class TokenType(Enum):
    # ... 其他令牌 ...
    COMMA = ','
```

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_29.png)

接下来，在词法分析器的主循环中识别逗号字符：

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_31.png)

```python
# 在 lexer.py 的 `_next_token` 方法中添加对逗号的处理
elif self.current_char == ',':
    tok = Token(TokenType.COMMA, self.current_char)
    self.advance()
    return tok
```

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_33.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_35.png)

## 抽象语法树：创建函数参数节点

为了在AST中表示函数参数，我们需要创建一个新的节点类 `FunctionParameterNode`。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_37.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_39.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_41.png)

以下是该节点的定义：

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_43.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_45.png)

```python
# 在 ast.py 中定义 FunctionParameterNode 类
class FunctionParameterNode(ASTNode):
    def __init__(self, name: str, value_type: str):
        self.name = name
        self.value_type = value_type

    def _to_json(self) -> Dict:
        return {
            'node': 'FunctionParameterNode',
            'name': self.name,
            'value_type': self.value_type
        }
```

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_47.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_49.png)

然后，我们需要更新 `FunctionStatementNode`，使其 `parameters` 属性是 `FunctionParameterNode` 的列表，而不仅仅是标识符字符串。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_51.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_53.png)

## 语法分析器：解析函数参数列表

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_55.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_56.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_57.png)

现在，我们需要在语法分析器中添加一个函数来解析函数声明中的参数列表。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_59.png)

以下是解析函数参数列表的步骤：

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_61.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_63.png)

1.  检查下一个令牌是否是右括号 `)`，如果是，则返回空列表（表示无参数）。
2.  跳过左括号 `(`。
3.  解析第一个参数：获取标识符名称，期望一个冒号 `:`，然后获取类型标识符。
4.  将第一个参数添加到列表中。
5.  循环检查下一个令牌是否为逗号 `,`，如果是，则继续解析更多参数。
6.  最后，期望一个右括号 `)` 来结束参数列表。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_65.png)

核心解析函数 `_parse_function_parameters` 的伪代码如下：

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_67.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_69.png)

```python
def _parse_function_parameters(self) -> Optional[List[FunctionParameterNode]]:
    params = []
    # 如果下一个令牌是 )，则没有参数
    if self._peek_token.type == TokenType.RPAREN:
        self._next_token() # 跳过 )
        return params

    self._next_token() # 跳过 (
    # 解析第一个参数
    param_name = self.current_token.literal
    self._expect_peek(TokenType.COLON)
    self._next_token() # 跳过 :
    param_type = self.current_token.literal
    params.append(FunctionParameterNode(param_name, param_type))

    # 循环解析后续参数
    while self._peek_token.type == TokenType.COMMA:
        self._next_token() # 跳过 ,
        self._next_token() # 移动到下一个参数名
        param_name = self.current_token.literal
        self._expect_peek(TokenType.COLON)
        self._next_token()
        param_type = self.current_token.literal
        params.append(FunctionParameterNode(param_name, param_type))

    self._expect_peek(TokenType.RPAREN)
    return params
```

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_71.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_72.png)

## 语法分析器：解析调用表达式中的实参列表

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_74.png)

函数调用时，我们需要解析传递给函数的实参列表。我们将创建一个通用的 `_parse_expression_list` 函数，它接受一个结束令牌（例如右括号 `)`）并返回一个表达式节点列表。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_76.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_78.png)

以下是该函数的实现思路：

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_80.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_82.png)

1.  检查下一个令牌是否是结束令牌，如果是，则跳过左括号并返回空列表。
2.  否则，跳过左括号，解析第一个表达式。
3.  循环检查下一个令牌是否为逗号 `,`，如果是，则继续解析更多表达式。
4.  最后，确保下一个令牌是结束令牌。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_84.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_86.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_88.png)

这个函数可以用于函数调用、数组字面量等多种场景。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_90.png)

## 编译器：处理函数参数与实参

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_92.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_93.png)

在编译器部分，我们需要更新几个访问者方法。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_95.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_97.png)

首先，在 `visit_FunctionStatementNode` 中：
*   获取每个参数的LLVM类型。
*   在函数入口块中，为每个参数分配内存（`builder.alloca`）并将传入的值存储进去。
*   将每个参数的指针和类型添加到函数的新作用域环境中。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_99.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_101.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_103.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_105.png)

其次，在 `visit_CallExpressionNode` 中：
*   解析每个实参表达式，获取其值和LLVM类型。
*   将这些值收集到列表中，作为调用函数时的实参。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_107.png)

关键步骤是为函数参数创建内存空间并将其与环境关联：

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_109.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_111.png)

```python
# 在 visit_FunctionStatementNode 中
param_types = [self.type_map[p.value_type] for p in node.parameters]
param_names = [p.name for p in node.parameters]

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_113.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_115.png)

# 为参数分配内存并存储初始值
param_pointers = []
for i, param_type in enumerate(param_types):
    param_ptr = self.builder.alloca(param_type, name=param_names[i])
    self.builder.store(function.args[i], param_ptr) # function 是当前的 llvm.Function
    param_pointers.append(param_ptr)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_117.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_119.png)

# 将参数添加到新环境中
for (param_name, param_type), param_ptr in zip(zip(param_names, param_types), param_pointers):
    self.env.define(param_name, (param_ptr, param_type))
```

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_121.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_123.png)

## 测试与运行 🧪

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_125.png)

完成所有修改后，我们可以编写一个测试程序：

```python
fn add(a: int, b: int): int {
    return a + b;
}

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_127.png)

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_129.png)

fn main(): int {
    return add(1, 2);
}
```

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_131.png)

运行编译器，应该能成功生成LLVM IR代码并输出结果 `3`。你也可以尝试创建更多带参数的函数，例如减法函数。

## 总结 🎉

本节课我们一起学习了如何为编程语言添加函数参数和实参功能。我们：
1.  在词法分析器中添加了逗号令牌。
2.  在AST中创建了 `FunctionParameterNode` 来表示参数。
3.  在语法分析器中实现了函数参数列表和调用实参列表的解析。
4.  在编译器中处理了参数的存储、环境绑定以及实参的传递。

![](img/93fee7ca9a35ce8b29b816ec2ebfb1f0_133.png)

现在，我们的函数可以接收输入并基于参数进行计算了。下一节课，我们将为语言添加字符串类型和 `print` 函数，让程序能够输出信息。