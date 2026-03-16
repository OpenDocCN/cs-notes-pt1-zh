# 014：前缀与赋值运算符 🧮

![](img/69db6256f9dfb864a5ccc710bf040937_1.png)

在本节课中，我们将学习如何为我们的编程语言添加两种新的运算符：**前缀运算符**（如 `-` 和 `!`）和**赋值运算符**（如 `+=`、`-=`、`*=`、`/=`）。我们将从词法分析开始，逐步修改语法分析器和编译器，最终让这些新功能能够正常工作。

---

## 概述 📋

![](img/69db6256f9dfb864a5ccc710bf040937_3.png)

本节课的目标是实现两种类型的运算符。第一部分是前缀运算符，例如 `-10` 中的负号和 `!false` 中的逻辑非运算符。第二部分是复合赋值运算符，它们允许我们以更简洁的方式修改变量的值，例如 `x += 5`。

---

## 1. 更新词法分析器（Lexer）🔤

首先，我们需要在词法分析器中识别新的运算符符号。这涉及到修改 `Token` 类型和 `Lexer` 的字符处理逻辑。

![](img/69db6256f9dfb864a5ccc710bf040937_5.png)

### 1.1 添加新的 Token 类型

![](img/69db6256f9dfb864a5ccc710bf040937_7.png)

在 `token.py` 文件的 `TokenType` 枚举中，我们需要添加新的符号。

**代码示例：**
```python
# 在符号区域添加
BANG = ‘BANG‘ # 对应 ‘!‘

![](img/69db6256f9dfb864a5ccc710bf040937_9.png)

# 在赋值运算符区域添加
PLUS_EQ = ‘PLUS_EQ‘    # ‘+=‘
MINUS_EQ = ‘MINUS_EQ‘  # ‘-=‘
ASTERISK_EQ = ‘ASTERISK_EQ‘ # ‘*=‘
SLASH_EQ = ‘SLASH_EQ‘  # ‘/=‘
```

![](img/69db6256f9dfb864a5ccc710bf040937_11.png)

### 1.2 修改 Lexer 处理逻辑

接下来，在 `lexer.py` 中，我们需要为这些新符号添加对应的 `case` 处理语句。

![](img/69db6256f9dfb864a5ccc710bf040937_13.png)

![](img/69db6256f9dfb864a5ccc710bf040937_15.png)

以下是处理 `+=` 运算符的示例逻辑：

**代码示例：**
```python
case ‘+‘:
    if self.peek_char() == ‘=‘:
        # 保存当前字符 ‘+‘
        char = self.current_char
        self.advance() # 前进到 ‘=‘
        # 创建 PLUS_EQ 类型的 Token，字面量为 “+=”
        token = Token(PLUS_EQ, char + self.current_char)
    else:
        token = Token(PLUS, self.current_char)
    self.advance()
    return token
```

![](img/69db6256f9dfb864a5ccc710bf040937_17.png)

![](img/69db6256f9dfb864a5ccc710bf040937_18.png)

我们需要为 `-`、`*`、`/` 和 `!` 实现类似的逻辑。对于 `!`，它没有对应的赋值形式，因此处理更简单。

![](img/69db6256f9dfb864a5ccc710bf040937_20.png)

---

![](img/69db6256f9dfb864a5ccc710bf040937_22.png)

## 2. 更新抽象语法树（AST）🌳

为了让解析器能构建新的语法结构，我们需要定义新的 AST 节点。

### 2.1 创建前缀表达式节点

![](img/69db6256f9dfb864a5ccc710bf040937_24.png)

![](img/69db6256f9dfb864a5ccc710bf040937_26.png)

前缀表达式节点需要记录运算符和它作用的右侧表达式。

![](img/69db6256f9dfb864a5ccc710bf040937_28.png)

**代码示例：**
```python
class PrefixExpression(Node):
    def __init__(self, operator, right):
        self.operator = operator  # 例如 ‘-‘ 或 ‘!‘
        self.right = right        # 一个表达式节点
```

![](img/69db6256f9dfb864a5ccc710bf040937_30.png)

![](img/69db6256f9dfb864a5ccc710bf040937_32.png)

### 2.2 修改赋值语句节点

原来的赋值语句节点只包含变量名和值。现在，我们需要增加一个 `operator` 字段来支持 `+=` 这类复合赋值。

![](img/69db6256f9dfb864a5ccc710bf040937_34.png)

![](img/69db6256f9dfb864a5ccc710bf040937_36.png)

**代码示例：**
```python
class AssignStatement(Node):
    def __init__(self, name, operator, value):
        self.name = name        # 变量名
        self.operator = operator # 例如 ‘=‘, ‘+=‘
        self.value = value      # 右侧的表达式
```

---

![](img/69db6256f9dfb864a5ccc710bf040937_38.png)

![](img/69db6256f9dfb864a5ccc710bf040937_39.png)

## 3. 更新语法解析器（Parser）📝

解析器需要知道如何将新的 Token 序列转换成我们刚刚定义的 AST 节点。

![](img/69db6256f9dfb864a5ccc710bf040937_41.png)

![](img/69db6256f9dfb864a5ccc710bf040937_42.png)

### 3.1 注册前缀解析函数

![](img/69db6256f9dfb864a5ccc710bf040937_44.png)

![](img/69db6256f9dfb864a5ccc710bf040937_45.png)

我们需要告诉解析器，当遇到 `-` 或 `!` 时，应该调用一个特定的函数来解析前缀表达式。

**代码示例：**
```python
# 在解析器的初始化函数中
self.prefix_parse_functions = {
    ...
    TokenType.MINUS: self.parse_prefix_expression,
    TokenType.BANG: self.parse_prefix_expression,
}
```

### 3.2 实现前缀表达式解析函数

![](img/69db6256f9dfb864a5ccc710bf040937_47.png)

这个函数负责消费运算符 Token，并解析其后的表达式作为右操作数。

![](img/69db6256f9dfb864a5ccc710bf040937_49.png)

**代码示例：**
```python
def parse_prefix_expression(self):
    # 当前 Token 是 ‘-‘ 或 ‘!‘
    operator = self.current_token.literal
    self.advance() # 消费掉运算符
    # 解析右侧的表达式
    right = self.parse_expression(PREFIX)
    return PrefixExpression(operator, right)
```

![](img/69db6256f9dfb864a5ccc710bf040937_51.png)

### 3.3 处理赋值语句中的运算符

在解析赋值语句时，我们需要检查并保存运算符。

![](img/69db6256f9dfb864a5ccc710bf040937_53.png)

**代码示例：**
```python
def parse_assignment_statement(self, name):
    # name 是左侧的标识符
    # 当前 Token 可能是 ‘=‘, ‘+=‘ 等
    operator = self.current_token.literal
    self.advance() # 消费掉运算符 Token
    # 解析右侧的值
    value = self.parse_expression(LOWEST)
    return AssignStatement(name, operator, value)
```

为了判断下一个 Token 是否是赋值运算符，我们可以创建一个辅助函数。

![](img/69db6256f9dfb864a5ccc710bf040937_55.png)

**代码示例：**
```python
def peek_token_is_assignment(self):
    assignment_ops = [TokenType.EQ, TokenType.PLUS_EQ, TokenType.MINUS_EQ,
                      TokenType.ASTERISK_EQ, TokenType.SLASH_EQ]
    return self.peek_token.type in assignment_ops
```

---

## 4. 更新编译器（Compiler）⚙️

![](img/69db6256f9dfb864a5ccc710bf040937_57.png)

最后，我们需要教编译器如何为这些新的 AST 节点生成 LLVM IR 代码。

### 4.1 编译前缀表达式

对于前缀表达式，我们需要根据运算符生成不同的 IR 指令。

![](img/69db6256f9dfb864a5ccc710bf040937_59.png)

**代码示例：**
```python
def visit_prefix_expression(self, node):
    operator = node.operator
    right_value, right_type = self.resolve_value(node.right)

    if right_type == ir.FloatType():
        # 处理浮点数
        if operator == ‘-‘:
            # 生成 ‘right_value * -1.0‘ 的 IR
            neg_one = ir.Constant(ir.FloatType(), -1.0)
            value = self.builder.fmul(right_value, neg_one)
            return value, ir.FloatType()
        elif operator == ‘!‘:
            # 逻辑非：生成比较指令，结果为 0 (false) 或 1 (true)
            zero = ir.Constant(ir.IntType(1), 0)
            value = self.builder.icmp_unsigned(‘==‘, right_value, zero)
            return value, ir.IntType(1)
    # 类似地处理整数类型...
```

### 4.2 编译复合赋值语句

![](img/69db6256f9dfb864a5ccc710bf040937_61.png)

这是最复杂的部分。我们需要：
1.  加载变量的当前值。
2.  计算右侧表达式的值。
3.  根据运算符（如 `+=`）将两者进行计算。
4.  将结果存回变量。

![](img/69db6256f9dfb864a5ccc710bf040937_63.png)

**代码示例：**
```python
def visit_assign_statement(self, node):
    var_name = node.name
    operator = node.operator
    right_value, right_type = self.resolve_value(node.value)

    # 1. 查找变量指针并加载其当前值
    var_ptr = self.environment.lookup(var_name)
    orig_value = self.builder.load(var_ptr)

    # 2. 处理类型转换（例如 int 与 float 运算）
    if isinstance(orig_value.type, ir.IntType) and right_type == ir.FloatType():
        orig_value = self.builder.sitofp(orig_value, ir.FloatType())
    # ... 其他类型转换

    # 3. 根据运算符进行计算
    if operator == ‘=‘:
        result = right_value
    elif operator == ‘+=‘:
        if isinstance(orig_value.type, ir.IntType):
            result = self.builder.add(orig_value, right_value)
        else:
            result = self.builder.fadd(orig_value, right_value)
    # ... 处理 ‘-=‘, ‘*=‘, ‘/=‘

    # 4. 将结果存储回变量
    self.builder.store(result, var_ptr)
```

![](img/69db6256f9dfb864a5ccc710bf040937_65.png)

---

![](img/69db6256f9dfb864a5ccc710bf040937_67.png)

## 测试与运行 🧪

![](img/69db6256f9dfb864a5ccc710bf040937_69.png)

![](img/69db6256f9dfb864a5ccc710bf040937_71.png)

完成所有修改后，我们可以创建一个测试文件来验证功能。

![](img/69db6256f9dfb864a5ccc710bf040937_73.png)

**测试示例 (`test.line`):**
```
x = 10
x += 5
print(x) # 应输出 15

![](img/69db6256f9dfb864a5ccc710bf040937_75.png)

y = -20
print(y) # 应输出 -20

![](img/69db6256f9dfb864a5ccc710bf040937_77.png)

flag = !false
print(flag) # 应输出 true
```

![](img/69db6256f9dfb864a5ccc710bf040937_79.png)

运行主程序：
```bash
python main.py test.line
```

![](img/69db6256f9dfb864a5ccc710bf040937_81.png)

![](img/69db6256f9dfb864a5ccc710bf040937_83.png)

如果一切正常，控制台应该能正确输出计算结果。

![](img/69db6256f9dfb864a5ccc710bf040937_85.png)

![](img/69db6256f9dfb864a5ccc710bf040937_87.png)

---

## 总结 🎉

![](img/69db6256f9dfb864a5ccc710bf040937_89.png)

![](img/69db6256f9dfb864a5ccc710bf040937_90.png)

![](img/69db6256f9dfb864a5ccc710bf040937_92.png)

在本节课中，我们一起为我们的编程语言添加了强大的新功能。

*   **前缀运算符**：我们实现了 `-`（负号）和 `!`（逻辑非）运算符，它们作用于单个表达式。
*   **赋值运算符**：我们实现了 `+=`、`-=`、`*=`、`/=` 这些复合赋值运算符，它们可以更高效地修改变量值。

![](img/69db6256f9dfb864a5ccc710bf040937_94.png)

实现过程贯穿了编译器的所有主要阶段：从词法分析识别新符号，到语法分析构建新的 AST 结构，最后在代码生成阶段生成对应的 LLVM IR 指令。下一节课，我们将探讨**后缀运算符**（如 `i++` 和 `i--`），这将在循环等场景中非常有用。