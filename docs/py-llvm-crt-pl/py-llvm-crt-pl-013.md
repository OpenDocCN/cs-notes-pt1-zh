# 013：实现 For 循环与 Break 语句 🚀

![](img/dc94e1841bbf497ba6534dc3dc3b4751_1.png)

在本节课中，我们将学习如何为我们的编程语言实现 `for` 循环以及 `break` 和 `continue` 语句。这些控制流语句是构建复杂程序逻辑的基础。

## 概述

我们将从一个简单的测试程序开始，目标是实现一个基本的 `for` 循环，并在特定条件下使用 `break` 语句提前退出循环。以下是我们的测试代码 `test.lang`：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_3.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_4.png)

```c
fn main() {
    let a = 0;
    for (let i = 0; i < 10; i = i + 1) {
        if (i == 5) {
            break;
        }
        print(i);
        a = i;
    }
    return a;
}
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_6.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_8.png)

这个循环将从 0 迭代到 9，但当 `i` 等于 5 时，会执行 `break` 语句并提前退出循环，因此数字 5 不会被打印。最终，函数将返回变量 `a` 的值。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_10.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_12.png)

上一节我们实现了 `while` 循环，本节中我们来看看更复杂的 `for` 循环。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_14.png)

## 第一步：更新词法分析器 (token.py)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_16.png)

首先，我们需要在词法分析器中添加三个新的关键字对应的 Token 类型。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_17.png)

以下是需要添加的新 Token 类型：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_19.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_21.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_23.png)

```python
# 在 TokenType 枚举类中添加
For = auto()
Break = auto()
Continue = auto()
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_25.png)

接下来，我们需要将这些类型映射到实际的关键字字符串。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_27.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_29.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_30.png)

以下是需要更新的关键字映射字典：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_32.png)

```python
# 在 keywords 字典中添加
'for': TokenType.For,
'break': TokenType.Break,
'continue': TokenType.Continue,
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_34.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_35.png)

此外，为了增加趣味性，我们还可以添加一些“Gen Z”风格的替代关键字（可选）。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_37.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_39.png)

以下是可选的替代关键字映射：

```python
# 在 gen_z_keywords 字典中添加（可选）
'yeet': TokenType.Break,    # 表示跳出循环
'skip': TokenType.Continue, # 表示跳过本次迭代
'dab': TokenType.For,       # 表示 for 循环
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_41.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_42.png)

保存 `token.py` 文件，词法分析器的更新就完成了。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_44.png)

## 第二步：更新抽象语法树 (AST.py)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_46.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_48.png)

接下来，我们需要在抽象语法树中定义三种新的语句节点。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_50.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_52.png)

首先，在节点类型枚举中添加对应的类型。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_54.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_55.png)

以下是需要添加的新节点类型：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_57.png)

```python
# 在 NodeType 枚举类中添加
BreakStatement = auto()
ContinueStatement = auto()
ForStatement = auto()
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_59.png)

然后，在文件底部的语句节点区域，创建这三个新节点的类。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_61.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_63.png)

以下是 `BreakStatement` 节点的定义：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_65.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_67.png)

```python
class BreakStatement(Node):
    def __init__(self):
        super().__init__(NodeType.BreakStatement)

    def __repr__(self):
        return self._to_json({
            "type": "BreakStatement"
        })
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_69.png)

`ContinueStatement` 节点的结构与 `BreakStatement` 几乎相同。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_71.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_73.png)

以下是 `ContinueStatement` 节点的定义：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_75.png)

```python
class ContinueStatement(Node):
    def __init__(self):
        super().__init__(NodeType.ContinueStatement)

    def __repr__(self):
        return self._to_json({
            "type": "ContinueStatement"
        })
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_77.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_79.png)

`ForStatement` 节点相对复杂，它需要包含初始化声明、循环条件、每次迭代后的操作以及循环体。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_80.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_82.png)

以下是 `ForStatement` 节点的定义：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_84.png)

```python
class ForStatement(Node):
    def __init__(self, declaration, condition, action, body):
        super().__init__(NodeType.ForStatement)
        self.declaration = declaration  # 变量声明，如 let i = 0
        self.condition = condition      # 条件表达式，如 i < 10
        self.action = action            # 每次迭代后执行的操作，如 i = i + 1
        self.body = body                # 循环体，一个 BlockStatement

    def __repr__(self):
        return self._to_json({
            "declaration": self.declaration,
            "condition": self.condition,
            "action": self.action,
            "body": self.body
        })
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_86.png)

保存 `AST.py` 文件，抽象语法树的更新就完成了。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_88.png)

## 第三步：更新语法分析器 (parser.py)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_90.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_91.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_93.png)

现在，我们需要在语法分析器中解析这些新语句。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_95.png)

首先，导入新定义的节点类。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_97.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_99.png)

以下是需要添加的导入语句：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_101.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_103.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_105.png)

```python
from AST import BreakStatement, ContinueStatement, ForStatement
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_107.png)

然后，在 `parse_statement` 方法的 `match-case` 块中，为新的关键字添加处理分支。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_109.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_111.png)

以下是 `parse_statement` 方法中需要添加的 case：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_113.png)

```python
case TokenType.Break:
    return self.parse_break_statement()
case TokenType.Continue:
    return self.parse_continue_statement()
case TokenType.For:
    return self.parse_for_statement()
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_115.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_116.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_118.png)

接下来，我们需要实现这三个对应的解析函数。我们先从最简单的 `break` 和 `continue` 开始。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_120.png)

以下是 `parse_break_statement` 方法的实现：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_122.png)

```python
def parse_break_statement(self):
    """解析 break 语句"""
    self.advance()  # 跳过 'break' 关键字
    return BreakStatement()
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_124.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_126.png)

`parse_continue_statement` 方法与之类似。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_128.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_129.png)

以下是 `parse_continue_statement` 方法的实现：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_131.png)

```python
def parse_continue_statement(self):
    """解析 continue 语句"""
    self.advance()  # 跳过 'continue' 关键字
    return ContinueStatement()
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_133.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_135.png)

最后，实现最复杂的 `parse_for_statement` 方法。我们需要按照 `for (初始化; 条件; 操作) { 循环体 }` 的结构来解析。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_137.png)

以下是 `parse_for_statement` 方法的实现：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_139.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_141.png)

```python
def parse_for_statement(self):
    """解析 for 循环语句"""
    # 1. 创建 ForStatement 节点
    for_stmt = ForStatement(None, None, None, None)

    # 2. 跳过 'for' 关键字，并期望一个左括号 '('
    self.advance()
    if not self.expect_peek(TokenType.LeftParen):
        return None

    # 3. 解析初始化声明（一个 let 语句）
    if not self.expect_peek(TokenType.Let):
        return None
    for_stmt.declaration = self.parse_let_statement()

    # 4. 跳过声明后的分号
    self.next_token()  # 跳过 ';'

    # 5. 解析条件表达式
    for_stmt.condition = self.parse_expression(Precedence.LOWEST)
    # 跳过条件后的分号
    if not self.expect_peek(TokenType.Semicolon):
        return None
    self.next_token()

    # 6. 解析迭代操作（一个赋值语句）
    for_stmt.action = self.parse_assignment_statement()

    # 7. 期望一个右括号 ')'
    if not self.expect_peek(TokenType.RightParen):
        return None
    self.next_token()

    # 8. 解析循环体（一个块语句）
    for_stmt.body = self.parse_block_statement()

    return for_stmt
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_143.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_144.png)

注意：在解析过程中，我们使用 `expect_peek` 来预检查下一个 Token 的类型，使用 `next_token` 来消费（跳过）已知的 Token（如分号、括号）。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_146.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_148.png)

保存 `parser.py` 文件，语法分析器的更新就完成了。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_150.png)

## 第四步：更新编译器 (compiler.py)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_152.png)

最后，也是最关键的一步，我们需要在编译器中将 AST 节点转换为 LLVM IR 代码。

首先，导入新的节点类。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_154.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_155.png)

以下是需要添加的导入语句：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_157.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_159.png)

```python
from AST import BreakStatement, ContinueStatement, ForStatement
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_161.png)

为了处理 `break` 和 `continue` 的跳转，我们需要在编译器类中维护两个列表，分别记录当前循环的“跳出块”和“继续块”的引用。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_163.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_165.png)

以下是在编译器 `__init__` 方法中添加的初始化代码：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_167.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_169.png)

```python
self.break_points = []   # 存储 break 语句应跳转到的目标块（循环出口）
self.continues = []      # 存储 continue 语句应跳转到的目标块（循环入口）
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_171.png)

然后，在 `compile` 方法的 `match-case` 语句块中，添加对新节点类型的处理。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_173.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_174.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_176.png)

以下是 `compile` 方法中需要添加的 case：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_178.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_179.png)

```python
case NodeType.BreakStatement:
    self.visit_break_statement(node)
case NodeType.ContinueStatement:
    self.visit_continue_statement(node)
case NodeType.ForStatement:
    self.visit_for_statement(node)
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_181.png)

现在，我们来实现这三个访问方法。`break` 和 `continue` 的实现很简单，它们只是跳转到之前记录好的目标块。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_183.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_185.png)

以下是 `visit_break_statement` 和 `visit_continue_statement` 方法的实现：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_187.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_189.png)

```python
def visit_break_statement(self, node):
    """编译 break 语句：跳转到最近的 break_points 中的块"""
    target_block = self.break_points[-1]
    self.builder.branch(target_block)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_191.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_193.png)

def visit_continue_statement(self, node):
    """编译 continue 语句：跳转到最近的 continues 中的块"""
    target_block = self.continues[-1]
    self.builder.branch(target_block)
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_195.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_197.png)

`visit_for_statement` 方法是核心。我们需要为循环创建入口块和出口块，设置环境，并按顺序编译初始化、条件判断、循环体和迭代操作。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_199.png)

以下是 `visit_for_statement` 方法的实现：

![](img/dc94e1841bbf497ba6534dc3dc3b4751_201.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_202.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_204.png)

```python
def visit_for_statement(self, node):
    """编译 for 循环语句"""
    # 1. 保存当前环境，并为循环体创建新的子环境
    previous_env = self.env
    self.env = Environment(previous_env)

    # 2. 编译初始化声明（在父环境中）
    self.env = previous_env
    self.compile(node.declaration)
    self.env = Environment(previous_env)  # 切换回循环体环境

    # 3. 创建循环的入口块和出口块
    loop_entry_block = self.builder.append_basic_block(f"for_loop_entry_{self.counter()}")
    loop_exit_block = self.builder.append_basic_block(f"for_loop_exit_{self.counter()}")

    # 4. 将出口块和入口块分别加入 break_points 和 continues 列表
    self.break_points.append(loop_exit_block)
    self.continues.append(loop_entry_block)

    # 5. 生成一个无条件跳转，跳转到循环入口
    self.builder.branch(loop_entry_block)

    # 6. 将构建器定位到循环入口块，开始编译循环体
    self.builder.position_at_start(loop_entry_block)
    self.compile(node.body)       # 编译循环体
    self.compile(node.action)     # 编译迭代操作（如 i = i + 1）

    # 7. 计算条件表达式的值，并根据结果跳转
    test_value = self.resolve_value(node.condition)
    self.builder.cbranch(test_value, loop_entry_block, loop_exit_block)

    # 8. 将构建器定位到循环出口块
    self.builder.position_at_start(loop_exit_block)

    # 9. 循环结束，从列表中移除当前循环的 break 和 continue 目标
    self.break_points.pop()
    self.continues.pop()

    # 10. 恢复之前的环境
    self.env = previous_env
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_206.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_208.png)

**注意**：在实现 `continue` 语句时，视频作者遇到了一个与 LLVM 版本相关的问题，导致 `continue` 功能未能完全正常工作。他建议观众如果发现问题，可以到他的 Discord 社区讨论。本教程保留了 `continue` 的基本框架，但实际运行时可能需要调试。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_210.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_211.png)

保存 `compiler.py` 文件，编译器的更新就完成了。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_213.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_215.png)

## 测试运行

![](img/dc94e1841bbf497ba6534dc3dc3b4751_217.png)

![](img/dc94e1841bbf497ba6534dc3dc3b4751_218.png)

现在，我们可以运行主程序来测试我们的实现了。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_220.png)

在命令行中执行：
```bash
python main.py
```

![](img/dc94e1841bbf497ba6534dc3dc3b4751_222.png)

如果一切正确，程序将解析 `test.lang` 文件，生成 LLVM IR，并执行。输出应该打印数字 0 到 4，然后在 `i == 5` 时触发 `break` 语句，跳出循环。最终函数返回值应为 4。

## 总结

本节课中我们一起学习了如何为自研编程语言实现 `for` 循环、`break` 和 `continue` 语句。我们逐步完成了以下工作：

1.  **扩展词法分析器**：添加了 `for`、`break`、`continue` 三个关键字的 Token。
2.  **扩展抽象语法树**：定义了对应的语句节点，特别是包含了多个子节点的 `ForStatement`。
3.  **扩展语法分析器**：实现了对新语句结构的解析逻辑。
4.  **扩展编译器**：这是最复杂的一步，我们学习了如何用 LLVM IR 构建循环结构，包括创建基本块、管理跳转目标以及处理嵌套环境。

![](img/dc94e1841bbf497ba6534dc3dc3b4751_224.png)

通过本节课，我们的语言拥有了更强大的流程控制能力。下一节课，我们将实现前缀操作符（如 `-` 负号、`!` 逻辑非），让我们的表达式系统更加完善。