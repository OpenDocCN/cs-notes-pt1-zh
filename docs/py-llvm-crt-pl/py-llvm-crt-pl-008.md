# 008：条件语句与布尔值 🚦

![](img/1f8d0b70015acef737e03658515eb7f9_1.png)

在本节课中，我们将为我们的编程语言添加条件判断能力。我们将实现 `if` 语句、条件运算符（如 `==`、`<`、`>=` 等）以及布尔字面量 `true` 和 `false`。课程结束时，我们将能够编译并运行包含条件逻辑的代码。

![](img/1f8d0b70015acef737e03658515eb7f9_3.png)

## 概述

![](img/1f8d0b70015acef737e03658515eb7f9_5.png)

我们将从扩展词法分析器开始，使其能识别新的关键字和运算符。接着，我们会更新抽象语法树（AST）和解析器，以构建条件语句的语法结构。最后，在编译器后端，我们将为这些新结构生成相应的 LLVM IR 代码，并实现布尔类型的全局常量。

![](img/1f8d0b70015acef737e03658515eb7f9_7.png)

![](img/1f8d0b70015acef737e03658515eb7f9_9.png)

---

![](img/1f8d0b70015acef737e03658515eb7f9_11.png)

![](img/1f8d0b70015acef737e03658515eb7f9_13.png)

![](img/1f8d0b70015acef737e03658515eb7f9_15.png)

## 1. 扩展词法分析器

![](img/1f8d0b70015acef737e03658515eb7f9_17.png)

![](img/1f8d0b70015acef737e03658515eb7f9_19.png)

首先，我们需要在 `Token` 类中添加新的词法单元类型，并在词法分析器中识别它们。

![](img/1f8d0b70015acef737e03658515eb7f9_21.png)

![](img/1f8d0b70015acef737e03658515eb7f9_22.png)

![](img/1f8d0b70015acef737e03658515eb7f9_24.png)

### 1.1 添加新的 Token 类型

![](img/1f8d0b70015acef737e03658515eb7f9_25.png)

![](img/1f8d0b70015acef737e03658515eb7f9_27.png)

在 `token.py` 文件中，我们需要添加以下新的词法单元类型：

![](img/1f8d0b70015acef737e03658515eb7f9_29.png)

![](img/1f8d0b70015acef737e03658515eb7f9_30.png)

*   **比较运算符**：`<`、`>`、`==`、`!=`、`<=`、`>=`
*   **关键字**：`if`、`else`
*   **布尔字面量**：`true`、`false`

![](img/1f8d0b70015acef737e03658515eb7f9_31.png)

![](img/1f8d0b70015acef737e03658515eb7f9_33.png)

![](img/1f8d0b70015acef737e03658515eb7f9_34.png)

以下是需要添加到 `TokenType` 枚举类中的代码：

![](img/1f8d0b70015acef737e03658515eb7f9_36.png)

![](img/1f8d0b70015acef737e03658515eb7f9_38.png)

```python
# 在 TokenType 枚举类中添加
LT = “LT”        # <
GT = “GT”        # >
EQ = “EQ”        # ==
NEQ = “NEQ”      # !=
LTE = “LTE”      # <=
GTE = “GTE”      # >=
IF = “IF”        # if
ELSE = “ELSE”    # else
TRUE = “TRUE”    # true
FALSE = “FALSE”  # false
```

![](img/1f8d0b70015acef737e03658515eb7f9_40.png)

![](img/1f8d0b70015acef737e03658515eb7f9_41.png)

### 1.2 更新关键字字典

![](img/1f8d0b70015acef737e03658515eb7f9_43.png)

![](img/1f8d0b70015acef737e03658515eb7f9_44.png)

在词法分析器中，我们需要将字符串关键字映射到对应的 `TokenType`。

![](img/1f8d0b70015acef737e03658515eb7f9_46.png)

![](img/1f8d0b70015acef737e03658515eb7f9_48.png)

![](img/1f8d0b70015acef737e03658515eb7f9_49.png)

```python
# 在 lexer.py 的关键字字典中添加
keywords = {
    “if”: TokenType.IF,
    “else”: TokenType.ELSE,
    “true”: TokenType.TRUE,
    “false”: TokenType.FALSE,
    # ... 其他已有关键字
}
```

![](img/1f8d0b70015acef737e03658515eb7f9_51.png)

![](img/1f8d0b70015acef737e03658515eb7f9_53.png)

![](img/1f8d0b70015acef737e03658515eb7f9_54.png)

### 1.3 识别比较运算符

![](img/1f8d0b70015acef737e03658515eb7f9_56.png)

在词法分析器的 `_next_token` 方法中，我们需要添加处理比较运算符的逻辑。这通常涉及查看下一个字符以区分单字符和双字符运算符（例如 `=` 和 `==`）。

![](img/1f8d0b70015acef737e03658515eb7f9_58.png)

![](img/1f8d0b70015acef737e03658515eb7f9_60.png)

以下是处理 `<` 和 `<=` 的示例：

![](img/1f8d0b70015acef737e03658515eb7f9_62.png)

![](img/1f8d0b70015acef737e03658515eb7f9_63.png)

```python
# 在 lexer.py 的 _next_token 方法中添加
elif self.current_char == ‘<’:
    if self.peek() == ‘=’:
        self.advance()
        token = Token(TokenType.LTE, ‘<=‘)
    else:
        token = Token(TokenType.LT, ‘<’)
```

![](img/1f8d0b70015acef737e03658515eb7f9_65.png)

![](img/1f8d0b70015acef737e03658515eb7f9_66.png)

![](img/1f8d0b70015acef737e03658515eb7f9_67.png)

你需要为 `>`、`=` 和 `!` 添加类似的逻辑，以处理 `>`、`>=`、`==` 和 `!=`。

![](img/1f8d0b70015acef737e03658515eb7f9_69.png)

![](img/1f8d0b70015acef737e03658515eb7f9_71.png)

![](img/1f8d0b70015acef737e03658515eb7f9_73.png)

---

![](img/1f8d0b70015acef737e03658515eb7f9_74.png)

## 2. 更新抽象语法树（AST）

![](img/1f8d0b70015acef737e03658515eb7f9_76.png)

![](img/1f8d0b70015acef737e03658515eb7f9_77.png)

![](img/1f8d0b70015acef737e03658515eb7f9_78.png)

![](img/1f8d0b70015acef737e03658515eb7f9_79.png)

接下来，我们需要定义两种新的 AST 节点：`IfStatement` 和 `BooleanLiteral`。

![](img/1f8d0b70015acef737e03658515eb7f9_81.png)

![](img/1f8d0b70015acef737e03658515eb7f9_83.png)

![](img/1f8d0b70015acef737e03658515eb7f9_84.png)

### 2.1 创建 IfStatement 节点

![](img/1f8d0b70015acef737e03658515eb7f9_86.png)

![](img/1f8d0b70015acef737e03658515eb7f9_87.png)

![](img/1f8d0b70015acef737e03658515eb7f9_89.png)

`IfStatement` 节点包含三个部分：条件表达式、条件为真时执行的代码块（consequence），以及可选的 `else` 代码块（alternative）。

![](img/1f8d0b70015acef737e03658515eb7f9_91.png)

![](img/1f8d0b70015acef737e03658515eb7f9_92.png)

![](img/1f8d0b70015acef737e03658515eb7f9_94.png)

```python
# 在 ast.py 中添加
class IfStatement(ASTNode):
    def __init__(self, condition, consequence, alternative=None):
        self.condition = condition
        self.consequence = consequence
        self.alternative = alternative
        self.node_type = “IfStatement”

    def to_json(self):
        return {
            “node”: self.node_type,
            “condition”: self.condition.to_json(),
            “consequence”: self.consequence.to_json(),
            “alternative”: self.alternative.to_json() if self.alternative else None
        }
```

![](img/1f8d0b70015acef737e03658515eb7f9_96.png)

![](img/1f8d0b70015acef737e03658515eb7f9_98.png)

![](img/1f8d0b70015acef737e03658515eb7f9_100.png)

### 2.2 创建 BooleanLiteral 节点

![](img/1f8d0b70015acef737e03658515eb7f9_102.png)

![](img/1f8d0b70015acef737e03658515eb7f9_104.png)

![](img/1f8d0b70015acef737e03658515eb7f9_106.png)

`BooleanLiteral` 节点非常简单，它只存储一个布尔值。

![](img/1f8d0b70015acef737e03658515eb7f9_108.png)

![](img/1f8d0b70015acef737e03658515eb7f9_110.png)

![](img/1f8d0b70015acef737e03658515eb7f9_112.png)

```python
# 在 ast.py 中添加
class BooleanLiteral(ASTNode):
    def __init__(self, value):
        self.value = value
        self.node_type = “BooleanLiteral”

    def to_json(self):
        return {
            “node”: self.node_type,
            “value”: self.value
        }
```

![](img/1f8d0b70015acef737e03658515eb7f9_114.png)

![](img/1f8d0b70015acef737e03658515eb7f9_116.png)

![](img/1f8d0b70015acef737e03658515eb7f9_118.png)

---

![](img/1f8d0b70015acef737e03658515eb7f9_120.png)

## 3. 更新解析器

![](img/1f8d0b70015acef737e03658515eb7f9_122.png)

![](img/1f8d0b70015acef737e03658515eb7f9_124.png)

现在，我们需要让解析器能够根据新的词法单元构建对应的 AST 节点。

![](img/1f8d0b70015acef737e03658515eb7f9_126.png)

![](img/1f8d0b70015acef737e03658515eb7f9_127.png)

![](img/1f8d0b70015acef737e03658515eb7f9_128.png)

### 3.1 添加运算符优先级

![](img/1f8d0b70015acef737e03658515eb7f9_130.png)

在解析器中，我们需要为比较运算符设置优先级，以确保表达式被正确解析。

![](img/1f8d0b70015acef737e03658515eb7f9_132.png)

![](img/1f8d0b70015acef737e03658515eb7f9_134.png)

![](img/1f8d0b70015acef737e03658515eb7f9_136.png)

![](img/1f8d0b70015acef737e03658515eb7f9_138.png)

```python
# 在 parser.py 的 PRECEDENCE 字典中添加
PRECEDENCE = {
    TokenType.EQ: 10,   # ==
    TokenType.NEQ: 10,  # !=
    TokenType.LT: 20,   # <
    TokenType.GT: 20,   # >
    TokenType.LTE: 20,  # <=
    TokenType.GTE: 20,  # >=
    # ... 其他已有优先级
}
```

![](img/1f8d0b70015acef737e03658515eb7f9_140.png)

### 3.2 解析前缀表达式

![](img/1f8d0b70015acef737e03658515eb7f9_141.png)

![](img/1f8d0b70015acef737e03658515eb7f9_142.png)

![](img/1f8d0b70015acef737e03658515eb7f9_144.png)

我们需要注册处理 `if`、`true` 和 `false` 前缀的函数。

![](img/1f8d0b70015acef737e03658515eb7f9_146.png)

![](img/1f8d0b70015acef737e03658515eb7f9_147.png)

![](img/1f8d0b70015acef737e03658515eb7f9_149.png)

```python
# 在 parser.py 的 PREFIX_PARSERS 字典中添加
PREFIX_PARSERS = {
    TokenType.IF: parse_if_statement,
    TokenType.TRUE: parse_boolean,
    TokenType.FALSE: parse_boolean,
    # ... 其他已有解析器
}
```

![](img/1f8d0b70015acef737e03658515eb7f9_151.png)

![](img/1f8d0b70015acef737e03658515eb7f9_152.png)

![](img/1f8d0b70015acef737e03658515eb7f9_154.png)

### 3.3 实现 parse_if_statement 函数

![](img/1f8d0b70015acef737e03658515eb7f9_156.png)

![](img/1f8d0b70015acef737e03658515eb7f9_158.png)

这个函数负责解析 `if` 语句的完整结构。

![](img/1f8d0b70015acef737e03658515eb7f9_160.png)

![](img/1f8d0b70015acef737e03658515eb7f9_162.png)

![](img/1f8d0b70015acef737e03658515eb7f9_163.png)

```python
def parse_if_statement(self):
    self.advance()  # 跳过 ‘if’ token
    condition = self.parse_expression(PRECEDENCE[TokenType.LOWEST])
    self.expect_peek(TokenType.LBRACE)  # 期望 ‘{‘
    consequence = self.parse_block_statement()

    alternative = None
    if self.peek_token.type == TokenType.ELSE:
        self.advance()  # 跳过 ‘else’ token
        self.expect_peek(TokenType.LBRACE)  # 期望 ‘{‘
        alternative = self.parse_block_statement()

    return IfStatement(condition, consequence, alternative)
```

![](img/1f8d0b70015acef737e03658515eb7f9_165.png)

![](img/1f8d0b70015acef737e03658515eb7f9_166.png)

### 3.4 实现 parse_boolean 函数

![](img/1f8d0b70015acef737e03658515eb7f9_168.png)

这个函数根据当前词法单元返回一个 `BooleanLiteral` 节点。

![](img/1f8d0b70015acef737e03658515eb7f9_170.png)

![](img/1f8d0b70015acef737e03658515eb7f9_172.png)

![](img/1f8d0b70015acef737e03658515eb7f9_173.png)

```python
def parse_boolean(self):
    value = self.current_token.type == TokenType.TRUE
    return BooleanLiteral(value)
```

![](img/1f8d0b70015acef737e03658515eb7f9_175.png)

---

![](img/1f8d0b70015acef737e03658515eb7f9_177.png)

![](img/1f8d0b70015acef737e03658515eb7f9_179.png)

## 4. 更新编译器后端

![](img/1f8d0b70015acef737e03658515eb7f9_181.png)

![](img/1f8d0b70015acef737e03658515eb7f9_182.png)

![](img/1f8d0b70015acef737e03658515eb7f9_183.png)

最后，我们需要在编译器后端为新的 AST 节点生成 LLVM IR 代码。

![](img/1f8d0b70015acef737e03658515eb7f9_185.png)

![](img/1f8d0b70015acef737e03658515eb7f9_186.png)

![](img/1f8d0b70015acef737e03658515eb7f9_188.png)

### 4.1 添加布尔类型映射

![](img/1f8d0b70015acef737e03658515eb7f9_190.png)

![](img/1f8d0b70015acef737e03658515eb7f9_192.png)

首先，在编译器的类型映射中注册布尔类型。在 LLVM IR 中，布尔类型通常用 `i1`（1位整数）表示。

![](img/1f8d0b70015acef737e03658515eb7f9_193.png)

![](img/1f8d0b70015acef737e03658515eb7f9_195.png)

![](img/1f8d0b70015acef737e03658515eb7f9_197.png)

```python
# 在 compiler.py 的 __init__ 方法中添加
self.type_map[“bool”] = ir.IntType(1)
```

![](img/1f8d0b70015acef737e03658515eb7f9_199.png)

![](img/1f8d0b70015acef737e03658515eb7f9_200.png)

### 4.2 初始化内置布尔常量

![](img/1f8d0b70015acef737e03658515eb7f9_202.png)

![](img/1f8d0b70015acef737e03658515eb7f9_203.png)

我们需要创建全局的 `true` 和 `false` 常量，并将它们添加到符号表中，使其成为语言中的保留关键字。

![](img/1f8d0b70015acef737e03658515eb7f9_205.png)

![](img/1f8d0b70015acef737e03658515eb7f9_207.png)

```python
def _initialize_builtins(self):
    bool_type = self.type_map[“bool”]

    # 创建全局常量 true
    true_var = ir.GlobalVariable(self.module, bool_type, name=“true”)
    true_var.initializer = ir.Constant(bool_type, 1)
    true_var.global_constant = True

    # 创建全局常量 false
    false_var = ir.GlobalVariable(self.module, bool_type, name=“false”)
    false_var.initializer = ir.Constant(bool_type, 0)
    false_var.global_constant = True

    # 将 true 和 false 添加到符号表
    self.env.define(“true”, true_var, bool_type)
    self.env.define(“false”, false_var, bool_type)
```

![](img/1f8d0b70015acef737e03658515eb7f9_209.png)

![](img/1f8d0b70015acef737e03658515eb7f9_211.png)

![](img/1f8d0b70015acef737e03658515eb7f9_213.png)

确保在编译器初始化时调用 `_initialize_builtins()` 方法。

![](img/1f8d0b70015acef737e03658515eb7f9_215.png)

![](img/1f8d0b70015acef737e03658515eb7f9_217.png)

![](img/1f8d0b70015acef737e03658515eb7f9_219.png)

### 4.3 编译 BooleanLiteral 节点

![](img/1f8d0b70015acef737e03658515eb7f9_221.png)

![](img/1f8d0b70015acef737e03658515eb7f9_223.png)

当遇到布尔字面量时，我们直接从符号表中加载对应的全局常量。

![](img/1f8d0b70015acef737e03658515eb7f9_225.png)

![](img/1f8d0b70015acef737e03658515eb7f9_226.png)

![](img/1f8d0b70015acef737e03658515eb7f9_228.png)

```python
def visit_boolean_literal(self, node):
    # 根据节点值决定加载 true 还是 false 常量
    var_name = “true” if node.value else “false”
    var = self.env.lookup(var_name)
    return self.builder.load(var, name=var_name)
```

![](img/1f8d0b70015acef737e03658515eb7f9_230.png)

![](img/1f8d0b70015acef737e03658515eb7f9_232.png)

### 4.4 编译比较运算符

![](img/1f8d0b70015acef737e03658515eb7f9_234.png)

![](img/1f8d0b70015acef737e03658515eb7f9_236.png)

我们需要为整数和浮点数实现比较运算符的代码生成。这通常使用 LLVM 的 `icmp`（整数比较）和 `fcmp`（浮点数比较）指令。

![](img/1f8d0b70015acef737e03658515eb7f9_238.png)

![](img/1f8d0b70015acef737e03658515eb7f9_239.png)

以下是整数相等比较 `==` 的示例：

![](img/1f8d0b70015acef737e03658515eb7f9_241.png)

![](img/1f8d0b70015acef737e03658515eb7f9_243.png)

```python
def visit_infix_expression(self, node):
    left = self.visit(node.left)
    right = self.visit(node.right)

    if node.operator == “==”:
        if isinstance(left.type, ir.IntType) and isinstance(right.type, ir.IntType):
            # 整数比较
            return self.builder.icmp_signed(‘==’, left, right, name=“cmptmp”)
        elif isinstance(left.type, ir.FloatType) and isinstance(right.type, ir.FloatType):
            # 浮点数比较
            return self.builder.fcmp_ordered(‘==’, left, right, name=“fcmp”)
    # … 处理其他运算符 <, >, !=, <=, >=
```

![](img/1f8d0b70015acef737e03658515eb7f9_245.png)

你需要为每个比较运算符（`<`、`>`、`!=`、`<=`、`>=`）实现类似的逻辑。

![](img/1f8d0b70015acef737e03658515eb7f9_247.png)

![](img/1f8d0b70015acef737e03658515eb7f9_249.png)

### 4.5 编译 IfStatement 节点

![](img/1f8d0b70015acef737e03658515eb7f9_251.png)

![](img/1f8d0b70015acef737e03658515eb7f9_252.png)

这是最核心的部分。我们使用 LLVM 的 `if_then` 或 `if_else` 构建器方法来创建条件分支。

![](img/1f8d0b70015acef737e03658515eb7f9_254.png)

![](img/1f8d0b70015acef737e03658515eb7f9_255.png)

```python
def visit_if_statement(self, node):
    condition = self.resolve_value(node.condition)  # 计算条件表达式的值
    bool_type = self.type_map[“bool”]
    # 确保条件结果是布尔类型（i1）
    if condition.type != bool_type:
        condition = self.builder.icmp_signed(‘!=’, condition, ir.Constant(condition.type, 0))

    if node.alternative is None:
        # 只有 if，没有 else
        with self.builder.if_then(condition):
            self.visit(node.consequence)
    else:
        # 有 if 和 else
        with self.builder.if_else(condition) as (then, otherwise):
            with then:
                self.visit(node.consequence)
            with otherwise:
                self.visit(node.alternative)
```

![](img/1f8d0b70015acef737e03658515eb7f9_257.png)

`resolve_value` 方法用于确保表达式的结果是一个可以用于条件判断的值。

---

## 5. 测试与验证

![](img/1f8d0b70015acef737e03658515eb7f9_259.png)

![](img/1f8d0b70015acef737e03658515eb7f9_260.png)

![](img/1f8d0b70015acef737e03658515eb7f9_262.png)

完成所有代码后，我们可以编写一个测试程序来验证功能。

![](img/1f8d0b70015acef737e03658515eb7f9_264.png)

```python
# test.lang
let a = 5;
if (a == 5) {
    a = 69;
} else {
    a = 420;
}
return a;
```

![](img/1f8d0b70015acef737e03658515eb7f9_266.png)

![](img/1f8d0b70015acef737e03658515eb7f9_267.png)

![](img/1f8d0b70015acef737e03658515eb7f9_269.png)

运行编译器，你应该得到正确的 LLVM IR 代码。最终程序应返回 `69`。如果将 `let a = 5;` 改为 `let a = 1;`，程序应返回 `420`。

![](img/1f8d0b70015acef737e03658515eb7f9_271.png)

![](img/1f8d0b70015acef737e03658515eb7f9_273.png)

![](img/1f8d0b70015acef737e03658515eb7f9_275.png)

---

![](img/1f8d0b70015acef737e03658515eb7f9_277.png)

![](img/1f8d0b70015acef737e03658515eb7f9_278.png)

![](img/1f8d0b70015acef737e03658515eb7f9_280.png)

## 总结

在本节课中，我们一起为我们的编程语言添加了核心的条件逻辑功能。我们：

![](img/1f8d0b70015acef737e03658515eb7f9_282.png)

![](img/1f8d0b70015acef737e03658515eb7f9_284.png)

1.  **扩展了词法分析器**，使其能识别 `if`、`else`、`true`、`false` 关键字以及各种比较运算符。
2.  **更新了 AST**，定义了 `IfStatement` 和 `BooleanLiteral` 节点。
3.  **增强了解析器**，使其能够解析条件语句的语法结构。
4.  **实现了编译器后端**，为条件语句和布尔值生成正确的 LLVM IR 代码，包括创建全局布尔常量和处理条件分支。

![](img/1f8d0b70015acef737e03658515eb7f9_286.png)

现在，我们的语言已经具备了基本的流程控制能力。在下一节课中，我们将探索函数调用，让我们的程序能够执行更复杂的操作。