# 009：函数调用 🧩

![](img/d33bc1c40be3f2bd2c963d796c889bd8_1.png)

在本节课中，我们将学习如何为我们的编程语言实现函数调用功能。我们将创建一个新的抽象语法树节点来处理函数调用，并更新解析器和编译器来支持它。最终，我们将能够编译并运行一个简单的程序，其中主函数调用另一个函数并返回其结果。

---

![](img/d33bc1c40be3f2bd2c963d796c889bd8_3.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_5.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_7.png)

## 概述

![](img/d33bc1c40be3f2bd2c963d796c889bd8_9.png)

上一节我们实现了函数定义。本节中，我们来看看如何调用这些函数。我们将通过实现一个新的“调用表达式”AST节点，并在解析器中识别左括号`(`作为函数调用的标志来完成此功能。目前，我们暂不支持传递参数，这将是下一节的内容。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_11.png)

## 实现调用表达式 AST 节点

![](img/d33bc1c40be3f2bd2c963d796c889bd8_13.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_15.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_17.png)

首先，我们需要在抽象语法树中定义一个新的节点类型来表示函数调用。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_19.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_20.png)

以下是创建 `CallExpression` 类的代码：

![](img/d33bc1c40be3f2bd2c963d796c889bd8_22.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_24.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_26.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_28.png)

```python
class CallExpression(Expression):
    def __init__(self, function, arguments):
        self.function = function  # 一个标识符字面量，表示函数名
        self.arguments = arguments  # 参数表达式列表（目前为空）
        self.type = NodeType.CALL_EXPRESSION

    def to_json(self):
        return {
            "type": self.type.value,
            "function": self.function.to_json(),
            "arguments": [arg.to_json() for arg in self.arguments]
        }
```

![](img/d33bc1c40be3f2bd2c963d796c889bd8_30.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_31.png)

这个节点包含两个主要部分：要调用的函数名（一个标识符）和一个参数列表（目前是空列表）。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_33.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_35.png)

## 更新解析器以识别函数调用

![](img/d33bc1c40be3f2bd2c963d796c889bd8_37.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_39.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_40.png)

接下来，我们需要修改解析器，使其在遇到标识符后的左括号`(`时，能将其解析为函数调用，而不是其他操作（比如乘法）。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_42.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_44.png)

以下是需要更新的解析器部分：

![](img/d33bc1c40be3f2bd2c963d796c889bd8_46.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_48.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_50.png)

1.  在优先级映射表中，为左括号`(`令牌关联`CALL`优先级。
2.  添加一个新的中缀解析函数 `parse_call_expression`。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_52.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_54.png)

首先，在优先级映射中添加条目：

![](img/d33bc1c40be3f2bd2c963d796c889bd8_56.png)

```python
# 在 parser 类的 __init__ 方法或类似位置
self.precedence_mapping = {
    ...
    TokenType.LEFT_PAREN: Precedence.CALL,
}
```

![](img/d33bc1c40be3f2bd2c963d796c889bd8_58.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_60.png)

然后，添加中缀解析函数：

![](img/d33bc1c40be3f2bd2c963d796c889bd8_62.png)

```python
def parse_call_expression(self, function):
    """解析函数调用表达式"""
    # 创建调用表达式节点，目前参数列表为空
    expr = CallExpression(function, [])
    # 期望下一个令牌是右括号，目前不支持参数
    if not self._expect_peek(TokenType.RIGHT_PAREN):
        return None
    return expr
```

![](img/d33bc1c40be3f2bd2c963d796c889bd8_64.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_66.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_68.png)

最后，将这个函数注册到中缀解析函数映射中：

![](img/d33bc1c40be3f2bd2c963d796c889bd8_70.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_72.png)

```python
self.infix_parse_functions = {
    ...
    TokenType.LEFT_PAREN: self.parse_call_expression,
}
```

![](img/d33bc1c40be3f2bd2c963d796c889bd8_74.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_76.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_78.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_80.png)

现在，解析器就能将类似 `test()` 的结构识别为函数调用了。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_82.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_84.png)

## 更新编译器以生成调用代码

![](img/d33bc1c40be3f2bd2c963d796c889bd8_86.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_88.png)

解析器生成AST后，编译器需要知道如何将 `CallExpression` 节点转换为LLVM IR代码。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_90.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_92.png)

以下是编译器中需要添加的 `visit_call_expression` 方法：

![](img/d33bc1c40be3f2bd2c963d796c889bd8_94.png)

```python
def visit_call_expression(self, node):
    """编译函数调用表达式"""
    # 获取函数名
    func_name = node.function.value
    # 获取参数列表（目前为空）
    args = node.arguments

    # 从当前作用域中查找函数定义
    func, return_type = self.env.lookup(func_name)

    # 使用LLVM构建器生成函数调用指令
    return_value = self.builder.call(func, [])
    # 返回调用结果
    return return_value, return_type
```

![](img/d33bc1c40be3f2bd2c963d796c889bd8_96.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_98.png)

同时，需要在 `resolve_value` 方法中添加对 `CALL_EXPRESSION` 节点的处理，以调用上述访问方法。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_100.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_102.png)

## 测试函数调用功能

![](img/d33bc1c40be3f2bd2c963d796c889bd8_104.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_106.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_108.png)

完成以上步骤后，我们可以编写一个简单的测试程序。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_110.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_112.png)

测试代码示例：
```
function test() {
    return 69;
}

function main() {
    return test(); // 调用 test 函数
}
```

![](img/d33bc1c40be3f2bd2c963d796c889bd8_114.png)

![](img/d33bc1c40be3f2bd2c963d796c889bd8_115.png)

运行编译器后，程序应成功执行并返回 `69`。这证明我们的函数调用机制已基本实现。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_117.png)

## 总结

本节课中我们一起学习了如何实现编程语言中的函数调用功能。我们创建了 `CallExpression` AST节点，更新了解析器来识别调用语法，并修改了编译器来生成调用其他函数所需的LLVM IR代码。目前，函数调用还不支持参数，这是我们接下来要解决的问题。

![](img/d33bc1c40be3f2bd2c963d796c889bd8_119.png)

在下一节中，我们将为函数添加参数和实参传递的功能，使函数变得更加实用和强大。