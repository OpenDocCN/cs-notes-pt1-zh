# 012：While 循环 🔄

![](img/08fdd146d5749c528b60643192da451e_1.png)

![](img/08fdd146d5749c528b60643192da451e_3.png)

在本节课中，我们将要学习如何为我们的编程语言实现 `while` 循环。循环是编程语言的核心控制结构之一，它允许我们重复执行一段代码，直到某个条件不再满足。我们将从定义新的语法开始，逐步完成词法分析、语法分析，并最终在 LLVM IR 层面生成对应的循环控制流。

## 概述

![](img/08fdd146d5749c528b60643192da451e_5.png)

![](img/08fdd146d5749c528b60643192da451e_7.png)

我们将编译一个简单的 `while` 循环程序。该程序初始化一个变量 `a` 为 0，只要 `a` 小于 10，就打印 `a` 的值并将其加 1。循环结束后，程序返回 `a` 的最终值。

![](img/08fdd146d5749c528b60643192da451e_9.png)

![](img/08fdd146d5749c528b60643192da451e_10.png)

```c
int main() {
    int a = 0;
    while (a < 10) {
        print(a);
        a = a + 1;
    }
    return a;
}
```

![](img/08fdd146d5749c528b60643192da451e_12.png)

![](img/08fdd146d5749c528b60643192da451e_14.png)

![](img/08fdd146d5749c528b60643192da451e_16.png)

上一节我们介绍了条件语句，本节中我们来看看如何实现循环结构。

![](img/08fdd146d5749c528b60643192da451e_18.png)

![](img/08fdd146d5749c528b60643192da451e_20.png)

## 步骤一：更新词法分析器（token.py）

![](img/08fdd146d5749c528b60643192da451e_22.png)

![](img/08fdd146d5749c528b60643192da451e_24.png)

首先，我们需要让词法分析器能够识别 `while` 关键字。

![](img/08fdd146d5749c528b60643192da451e_26.png)

![](img/08fdd146d5749c528b60643192da451e_28.png)

在 `token.py` 文件中，我们需要进行以下修改：

![](img/08fdd146d5749c528b60643192da451e_30.png)

![](img/08fdd146d5749c528b60643192da451e_32.png)

![](img/08fdd146d5749c528b60643192da451e_33.png)

1.  在 `keywords` 列表中添加 `while`。
2.  在 `keywords` 字典中将 `‘while’` 映射到 `TokenType.WHILE`。
3.  为了增加趣味性，我们还可以在 `alt_keywords` 字典中添加一个缩写 `‘we’` 也映射到 `TokenType.WHILE`。

![](img/08fdd146d5749c528b60643192da451e_35.png)

![](img/08fdd146d5749c528b60643192da451e_37.png)

以下是需要添加的代码：

![](img/08fdd146d5749c528b60643192da451e_39.png)

![](img/08fdd146d5749c528b60643192da451e_41.png)

![](img/08fdd146d5749c528b60643192da451e_43.png)

![](img/08fdd146d5749c528b60643192da451e_44.png)

```python
# 在 keywords 列表中添加
keywords = [..., ‘while’]

![](img/08fdd146d5749c528b60643192da451e_46.png)

![](img/08fdd146d5749c528b60643192da451e_48.png)

# 在 keywords 字典中添加
keywords = {..., ‘while’: TokenType.WHILE}

![](img/08fdd146d5749c528b60643192da451e_50.png)

![](img/08fdd146d5749c528b60643192da451e_52.png)

# 在 alt_keywords 字典中添加
alt_keywords = {..., ‘we’: TokenType.WHILE}
```

![](img/08fdd146d5749c528b60643192da451e_54.png)

![](img/08fdd146d5749c528b60643192da451e_56.png)

![](img/08fdd146d5749c528b60643192da451e_58.png)

![](img/08fdd146d5749c528b60643192da451e_59.png)

## 步骤二：更新抽象语法树（ast.py）

![](img/08fdd146d5749c528b60643192da451e_61.png)

![](img/08fdd146d5749c528b60643192da451e_63.png)

接下来，我们需要在抽象语法树中定义 `while` 语句的节点结构。

![](img/08fdd146d5749c528b60643192da451e_65.png)

![](img/08fdd146d5749c528b60643192da451e_67.png)

![](img/08fdd146d5749c528b60643192da451e_69.png)

在 `ast.py` 文件中，我们需要进行以下修改：

![](img/08fdd146d5749c528b60643192da451e_71.png)

![](img/08fdd146d5749c528b60643192da451e_72.png)

![](img/08fdd146d5749c528b60643192da451e_73.png)

1.  在 `NodeType` 枚举中添加 `WHILE_STATEMENT`。
2.  创建一个 `WhileStatement` 类，它包含两个属性：`condition`（条件表达式）和 `body`（循环体，是一个语句块）。

![](img/08fdd146d5749c528b60643192da451e_75.png)

![](img/08fdd146d5749c528b60643192da451e_77.png)

以下是 `WhileStatement` 类的定义：

![](img/08fdd146d5749c528b60643192da451e_79.png)

```python
class WhileStatement(Node):
    def __init__(self, condition: Node, body: BlockStatement):
        self.condition = condition
        self.body = body

    def type(self) -> NodeType:
        return NodeType.WHILE_STATEMENT

    def debug_json(self) -> dict:
        return {
            ‘type’: self.type().value,
            ‘condition’: self.condition.debug_json(),
            ‘body’: self.body.debug_json()
        }
```

![](img/08fdd146d5749c528b60643192da451e_81.png)

![](img/08fdd146d5749c528b60643192da451e_83.png)

![](img/08fdd146d5749c528b60643192da451e_85.png)

## 步骤三：更新语法分析器（parser.py）

![](img/08fdd146d5749c528b60643192da451e_87.png)

![](img/08fdd146d5749c528b60643192da451e_89.png)

![](img/08fdd146d5749c528b60643192da451e_91.png)

![](img/08fdd146d5749c528b60643192da451e_93.png)

现在，我们需要让语法分析器能够解析 `while` 语句。

![](img/08fdd146d5749c528b60643192da451e_95.png)

![](img/08fdd146d5749c528b60643192da451e_96.png)

在 `parser.py` 文件中，我们需要进行以下修改：

![](img/08fdd146d5749c528b60643192da451e_98.png)

![](img/08fdd146d5749c528b60643192da451e_100.png)

![](img/08fdd146d5749c528b60643192da451e_102.png)

1.  从 `ast` 模块导入 `WhileStatement`。
2.  在 `_parse_statement` 方法中，为 `TokenType.WHILE` 添加一个处理分支，调用 `_parse_while_statement` 方法。
3.  实现 `_parse_while_statement` 方法。该方法会：
    *   消耗掉 `while` 关键字。
    *   解析条件表达式。
    *   期望并消耗一个左花括号 `{`。
    *   解析循环体（一个语句块）。
    *   返回一个 `WhileStatement` 节点。

![](img/08fdd146d5749c528b60643192da451e_104.png)

以下是 `_parse_while_statement` 方法的实现：

![](img/08fdd146d5749c528b60643192da451e_106.png)

![](img/08fdd146d5749c528b60643192da451e_108.png)

```python
def _parse_while_statement(self) -> Optional[WhileStatement]:
    # 当前 token 是 ‘while’，前进到下一个 token（条件表达式的开始）
    self.advance()
    # 解析条件表达式
    condition = self._parse_expression()
    if condition is None:
        return None

    # 期望一个左花括号 ‘{‘ 来开始循环体
    if not self._expect_peek(TokenType.LBRACE):
        return None

    # 解析循环体（一个语句块）
    body = self._parse_block_statement()
    if body is None:
        return None

    return WhileStatement(condition, body)
```

![](img/08fdd146d5749c528b60643192da451e_110.png)

![](img/08fdd146d5749c528b60643192da451e_112.png)

## 步骤四：更新编译器（compiler.py）

![](img/08fdd146d5749c528b60643192da451e_114.png)

最后，也是最关键的一步，我们需要在编译器中将 `WhileStatement` 节点编译成 LLVM IR 指令。

![](img/08fdd146d5749c528b60643192da451e_116.png)

![](img/08fdd146d5749c528b60643192da451e_117.png)

![](img/08fdd146d5749c528b60643192da451e_119.png)

在 `compiler.py` 文件中，我们需要进行以下修改：

![](img/08fdd146d5749c528b60643192da451e_121.png)

![](img/08fdd146d5749c528b60643192da451e_122.png)

1.  从 `ast` 模块导入 `WhileStatement`。
2.  在 `_compile` 方法的语句处理部分，为 `NodeType.WHILE_STATEMENT` 添加一个处理分支，调用 `_visit_while_statement` 方法。
3.  实现 `_visit_while_statement` 方法。该方法的核心逻辑是创建两个基本块（Basic Block）并设置条件跳转：
    *   **循环入口块（while_loop_entry）**：如果条件为真，则跳转至此执行循环体。
    *   **循环出口块（while_loop_otherwise）**：如果条件为假，则跳转至此，结束循环。
    *   在循环体执行完毕后，再次检查条件，以决定是跳回入口继续循环，还是跳出循环。

![](img/08fdd146d5749c528b60643192da451e_124.png)

![](img/08fdd146d5749c528b60643192da451e_125.png)

![](img/08fdd146d5749c528b60643192da451e_127.png)

![](img/08fdd146d5749c528b60643192da451e_129.png)

以下是 `_visit_while_statement` 方法的实现：

![](img/08fdd146d5749c528b60643192da451e_131.png)

![](img/08fdd146d5749c528b60643192da451e_132.png)

![](img/08fdd146d5749c528b60643192da451e_134.png)

```python
def _visit_while_statement(self, node: WhileStatement):
    condition = node.condition
    body = node.body

    # 解析初始条件值
    test_value = self._resolve_value(self._visit(condition))

    # 创建循环入口和出口基本块
    entry_block = self.builder.append_basic_block(f“while_loop_entry_{self._increment_counter()}”)
    otherwise_block = self.builder.append_basic_block(f“while_loop_otherwise_{self._increment_counter()}”)

    # 根据初始条件值进行条件跳转
    self.builder.cbranch(test_value, entry_block, otherwise_block)

    # 将构建器定位到循环入口块，开始编译循环体
    self.builder.position_at_start(entry_block)
    self._visit(body) # 编译循环体

    # 循环体执行完毕后，再次计算条件值
    test_value_after_body = self._resolve_value(self._visit(condition))
    # 根据新的条件值决定是继续循环还是退出
    self.builder.cbranch(test_value_after_body, entry_block, otherwise_block)

    # 将构建器定位到循环出口块，继续编译后续代码
    self.builder.position_at_start(otherwise_block)
```

![](img/08fdd146d5749c528b60643192da451e_136.png)

## 测试与运行

![](img/08fdd146d5749c528b60643192da451e_138.png)

![](img/08fdd146d5749c528b60643192da451e_140.png)

完成以上所有步骤后，我们可以运行编译器来测试我们的 `while` 循环。

![](img/08fdd146d5749c528b60643192da451e_142.png)

![](img/08fdd146d5749c528b60643192da451e_144.png)

在终端中执行以下命令：
```bash
python main.py
```

![](img/08fdd146d5749c528b60643192da451e_146.png)

如果一切正确，程序将输出从 0 到 9 的数字，并最终返回 10。这是因为当 `a` 等于 10 时，条件 `a < 10` 不再成立，循环终止。

![](img/08fdd146d5749c528b60643192da451e_148.png)

![](img/08fdd146d5749c528b60643192da451e_150.png)

## 总结

![](img/08fdd146d5749c528b60643192da451e_152.png)

本节课中我们一起学习了如何为我们的编程语言实现 `while` 循环。我们回顾一下关键步骤：

![](img/08fdd146d5749c528b60643192da451e_154.png)

![](img/08fdd146d5749c528b60643192da451e_155.png)

1.  **词法分析**：添加了 `while` 关键字的识别。
2.  **语法定义**：在 AST 中创建了 `WhileStatement` 节点。
3.  **语法分析**：实现了 `while` 语句的解析逻辑。
4.  **代码生成**：在编译器中将循环结构翻译成 LLVM IR 的控制流图，核心是使用 `cbranch` 指令和多个基本块来实现条件跳转和循环。

`while` 循环的实现模式与 `if` 语句类似，但多了一个从循环体末尾跳回条件检查处的“回边”，从而形成了循环。掌握了这个模式，实现 `do-while` 循环也将是类似的挑战。

![](img/08fdd146d5749c528b60643192da451e_157.png)

在下一节课中，我们将为循环添加 `break` 和 `continue` 关键字，并最终实现 `for` 循环，从而完善我们语言中的所有循环结构。