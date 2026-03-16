# 002：Pratt 解析器 🧩

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_1.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_3.png)

在本节课中，我们将为我们的算术语言构建解析器。我们将使用一种称为 **Pratt 解析** 的技术。Pratt 解析是一种高效的运算符优先级解析算法，非常适合处理表达式。我们将从设置解析器的基础结构开始，包括定义优先级、创建抽象语法树节点，并最终实现解析逻辑。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_5.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_7.png)

## 导入与基础设置

首先，我们需要导入必要的模块并设置解析器的基本框架。

```python
from lexer import Lexer
from token import Token, TokenType
from typing import Callable
from enum import Enum, auto
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_9.png)

接下来，我们定义运算符的优先级。优先级决定了表达式中运算符的执行顺序。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_11.png)

```python
class PrecedenceType(Enum):
    P_LOWEST = auto()
    P_LESSGREATER = auto()
    P_SUM = auto()
    P_PRODUCT = auto()
    P_EXPONENT = auto()
    P_PREFIX = auto()
    P_CALL = auto()
    P_INDEX = auto()
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_13.png)

然后，我们创建一个字典来映射具体的 Token 类型到其优先级。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_15.png)

```python
PRECEDENCE_MAPPING = {
    TokenType.PLUS: PrecedenceType.P_SUM,
    TokenType.MINUS: PrecedenceType.P_SUM,
    TokenType.SLASH: PrecedenceType.P_PRODUCT,
    TokenType.ASTERISK: PrecedenceType.P_PRODUCT,
    TokenType.MODULUS: PrecedenceType.P_PRODUCT,
    TokenType.POW: PrecedenceType.P_EXPONENT,
}
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_17.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_19.png)

## 解析器类结构

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_21.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_23.png)

现在，我们开始构建解析器类。解析器将接收词法分析器生成的 Token 流，并将其转换为抽象语法树。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_25.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_27.png)

```python
class Parser:
    def __init__(self, lexer: Lexer):
        self.lexer = lexer
        self.errors: list[str] = []
        self.current_token: Token = None
        self.peek_token: Token = None
        self.prefix_parse_functions: dict[TokenType, Callable] = {}
        self.infix_parse_functions: dict[TokenType, Callable] = {}
        self._next_token()
        self._next_token()
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_29.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_31.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_33.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_35.png)

解析器需要一些辅助方法来管理 Token 流和错误处理。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_37.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_39.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_41.png)

```python
    def _next_token(self):
        self.current_token = self.peek_token
        self.peek_token = self.lexer.next_token()

    def _peek_token_is(self, token_type: TokenType) -> bool:
        return self.peek_token.token_type == token_type

    def _expect_peek(self, token_type: TokenType) -> bool:
        if self._peek_token_is(token_type):
            self._next_token()
            return True
        else:
            self._peek_error(token_type)
            return False

    def _peek_error(self, token_type: TokenType):
        self.errors.append(f"Expected next token to be {token_type}, got {self.peek_token.token_type} instead.")

    def _no_prefix_parse_fn_error(self, token_type: TokenType):
        self.errors.append(f"No prefix parse function for {token_type} found.")

    def _current_precedence(self) -> PrecedenceType:
        return PRECEDENCE_MAPPING.get(self.current_token.token_type, PrecedenceType.P_LOWEST)

    def _peek_precedence(self) -> PrecedenceType:
        return PRECEDENCE_MAPPING.get(self.peek_token.token_type, PrecedenceType.P_LOWEST)
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_43.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_44.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_46.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_47.png)

## 抽象语法树节点定义

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_49.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_51.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_53.png)

在实现解析逻辑之前，我们需要定义抽象语法树的节点结构。这些节点将代表我们程序中的不同元素。

```python
from abc import ABC, abstractmethod
from enum import Enum

class NodeType(Enum):
    PROGRAM = "PROGRAM"
    EXPRESSION_STATEMENT = "EXPRESSION_STATEMENT"
    INFIX_EXPRESSION = "INFIX_EXPRESSION"
    INTEGER_LITERAL = "INTEGER_LITERAL"
    FLOAT_LITERAL = "FLOAT_LITERAL"

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_55.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_57.png)

class Node(ABC):
    @abstractmethod
    def node_type(self) -> NodeType:
        pass

    @abstractmethod
    def json(self) -> dict:
        pass

class Statement(Node):
    pass

class Expression(Node):
    pass

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_59.png)

class Program(Node):
    def __init__(self):
        self.statements: list[Statement] = []

    def node_type(self) -> NodeType:
        return NodeType.PROGRAM

    def json(self) -> dict:
        return {
            "type": self.node_type().value,
            "statements": [{"type": stmt.node_type().value, **stmt.json()} for stmt in self.statements]
        }

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_61.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_62.png)

class ExpressionStatement(Statement):
    def __init__(self, expression: Expression = None):
        self.expression = expression

    def node_type(self) -> NodeType:
        return NodeType.EXPRESSION_STATEMENT

    def json(self) -> dict:
        return {"expression": self.expression.json()}

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_64.png)

class InfixExpression(Expression):
    def __init__(self, left: Expression, operator: str, right: Expression = None):
        self.left = left
        self.operator = operator
        self.right = right

    def node_type(self) -> NodeType:
        return NodeType.INFIX_EXPRESSION

    def json(self) -> dict:
        return {
            "type": self.node_type().value,
            "left": self.left.json(),
            "operator": self.operator,
            "right": self.right.json() if self.right else None
        }

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_66.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_68.png)

class IntegerLiteral(Expression):
    def __init__(self, value: int):
        self.value = value

    def node_type(self) -> NodeType:
        return NodeType.INTEGER_LITERAL

    def json(self) -> dict:
        return {"value": self.value}

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_70.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_71.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_72.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_74.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_76.png)

class FloatLiteral(Expression):
    def __init__(self, value: float):
        self.value = value

    def node_type(self) -> NodeType:
        return NodeType.FLOAT_LITERAL

    def json(self) -> dict:
        return {"value": self.value}
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_78.png)

## 核心解析方法

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_80.png)

有了 AST 节点后，我们可以实现解析器的核心方法。`parse_program` 方法是解析的入口点。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_82.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_84.png)

```python
    def parse_program(self) -> Program:
        program = Program()
        while self.current_token.token_type != TokenType.EOF:
            stmt = self._parse_statement()
            if stmt:
                program.statements.append(stmt)
            self._next_token()
        return program
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_86.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_88.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_90.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_92.png)

`_parse_statement` 方法根据当前 Token 类型决定如何解析语句。目前，我们只处理表达式语句。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_94.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_96.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_98.png)

```python
    def _parse_statement(self) -> Statement:
        return self._parse_expression_statement()

    def _parse_expression_statement(self) -> ExpressionStatement:
        expr = self._parse_expression(PrecedenceType.P_LOWEST)
        if self._peek_token_is(TokenType.SEMICOLON):
            self._next_token()
        stmt = ExpressionStatement(expression=expr)
        return stmt
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_100.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_102.png)

`_parse_expression` 方法是 Pratt 解析的核心。它根据优先级递归地解析表达式。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_104.png)

```python
    def _parse_expression(self, precedence: PrecedenceType) -> Expression:
        prefix_fn = self.prefix_parse_functions.get(self.current_token.token_type)
        if not prefix_fn:
            self._no_prefix_parse_fn_error(self.current_token.token_type)
            return None

        left_expr = prefix_fn()

        while (not self._peek_token_is(TokenType.SEMICOLON) and
               precedence.value < self._peek_precedence().value):
            infix_fn = self.infix_parse_functions.get(self.peek_token.token_type)
            if not infix_fn:
                return left_expr
            self._next_token()
            left_expr = infix_fn(left_expr)

        return left_expr
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_106.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_108.png)

## 前缀与中缀解析函数

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_110.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_112.png)

我们需要为不同的 Token 类型注册前缀和中缀解析函数。前缀函数处理像 `-5` 这样的表达式，中缀函数处理像 `5 + 5` 这样的表达式。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_114.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_116.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_118.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_119.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_121.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_122.png)

以下是前缀解析函数的注册和实现：

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_124.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_126.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_128.png)

```python
        self.prefix_parse_functions = {
            TokenType.INT: self._parse_integer_literal,
            TokenType.FLOAT: self._parse_float_literal,
            TokenType.LPAREN: self._parse_grouped_expression,
        }

    def _parse_integer_literal(self) -> IntegerLiteral:
        try:
            value = int(self.current_token.literal)
            return IntegerLiteral(value)
        except ValueError:
            self.errors.append(f"Could not parse {self.current_token.literal} as integer.")
            return None

    def _parse_float_literal(self) -> FloatLiteral:
        try:
            value = float(self.current_token.literal)
            return FloatLiteral(value)
        except ValueError:
            self.errors.append(f"Could not parse {self.current_token.literal} as float.")
            return None

    def _parse_grouped_expression(self) -> Expression:
        self._next_token()
        expr = self._parse_expression(PrecedenceType.P_LOWEST)
        if not self._expect_peek(TokenType.RPAREN):
            return None
        return expr
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_130.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_132.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_134.png)

以下是中缀解析函数的注册和实现：

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_136.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_138.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_140.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_142.png)

```python
        self.infix_parse_functions = {
            TokenType.PLUS: self._parse_infix_expression,
            TokenType.MINUS: self._parse_infix_expression,
            TokenType.SLASH: self._parse_infix_expression,
            TokenType.ASTERISK: self._parse_infix_expression,
            TokenType.MODULUS: self._parse_infix_expression,
            TokenType.POW: self._parse_infix_expression,
        }

    def _parse_infix_expression(self, left: Expression) -> InfixExpression:
        expr = InfixExpression(left=left, operator=self.current_token.literal)
        precedence = self._current_precedence()
        self._next_token()
        expr.right = self._parse_expression(precedence)
        return expr
```

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_143.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_145.png)

## 测试解析器

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_147.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_149.png)

最后，我们可以在主程序中测试我们的解析器，并将生成的 AST 输出为 JSON 格式以便调试。

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_151.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_153.png)

```python
from parser import Parser
from ast import Program
import json

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_155.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_157.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_158.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_160.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_161.png)

# ... 读取源代码并初始化词法分析器 ...

parser = Parser(lexer)
program = parser.parse_program()

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_163.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_165.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_167.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_169.png)

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_171.png)

if parser_debug:
    with open('debug/ast.json', 'w') as f:
        json.dump(program.json(), f, indent=4)
    print("AST written to debug/ast.json")
```

运行程序后，我们可以检查 `debug/ast.json` 文件来查看解析 `5 + 5` 或更复杂表达式 `(5 + 5) * 10` 所生成的抽象语法树。这验证了我们的解析器能够正确处理运算符优先级和分组。

## 总结

![](img/5e7a69b6b58793dc708ed1c66d1fb00a_173.png)

本节课中，我们一起学习了如何为我们的编程语言构建一个 **Pratt 解析器**。我们定义了运算符的**优先级**，创建了代表程序结构的**抽象语法树节点**，并实现了核心的解析逻辑。解析器现在能够将像 `5 + 5` 和 `(5 + 5) * 10` 这样的算术表达式正确地转换为 AST。在下一节课中，我们将学习如何遍历这个 AST，并使用 LLVM 来生成中间代码。