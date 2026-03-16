# 062：装饰器 🍦

在本节课中，我们将要学习Python中的装饰器。装饰器是一种强大的工具，它允许我们扩展一个函数的功能，而无需修改该函数本身的代码。我们将通过一个制作冰淇淋的生动例子来理解其核心概念和工作原理。

## 什么是装饰器？🤔

装饰器是一个函数，它能够**扩展另一个函数的行为，而不修改那个基础函数**。

我们可以将基础函数作为参数传递给装饰器函数。例如，假设我们有一个获取冰淇淋的基础函数 `get_ice_cream`，你可以传入一个冰淇淋口味。有些人可能想在冰淇淋上加糖屑，而另一些人可能只想要普通的香草冰淇淋。我们可以通过使用装饰器来添加糖屑，从而扩展获取冰淇淋这个函数的行为，但我们可能不想改变基础函数本身，因为有些人不喜欢糖屑。

请这样理解装饰器：我们是在**为基础函数添加一些东西，而不改变它**。

## 如何创建装饰器？🔨

让我们从基础函数开始创建装饰器。

![](img/460272a5d085c620652be12f9c399d3e_1.png)

![](img/460272a5d085c620652be12f9c399d3e_2.png)

首先，我们创建一个基础函数 `get_ice_cream`，目前它没有参数。这个函数只打印一条消息：“这是你的冰淇淋🍦”。

```python
def get_ice_cream():
    print("这是你的冰淇淋🍦")
```

要调用这个函数，只需执行 `get_ice_cream()`。

现在，我们来创建一个装饰器。装饰器本身也是一个函数。

![](img/460272a5d085c620652be12f9c399d3e_4.png)

我们定义一个名为 `add_sprinkles` 的装饰器函数。这个函数将接收一个参数，即我们要装饰的函数，我们将其简称为 `func`。

在装饰器函数内部，我们需要定义一个名为 `wrapper` 的内部函数。目前它没有参数，稍后会设置。在这个 `wrapper` 函数内部，我们将调用接收到的那个函数（`func`）。最后，装饰器函数返回这个 `wrapper` 函数。

![](img/460272a5d085c620652be12f9c399d3e_6.png)

```python
def add_sprinkles(func):
    def wrapper():
        func()
    return wrapper
```

![](img/460272a5d085c620652be12f9c399d3e_8.png)

这就是创建装饰器的基本形式。

![](img/460272a5d085c620652be12f9c399d3e_10.png)

## 如何应用装饰器？✨

![](img/460272a5d085c620652be12f9c399d3e_12.png)

![](img/460272a5d085c620652be12f9c399d3e_14.png)

要将装饰器应用到基础函数上，你需要在定义该函数之前，使用 `@` 符号加上装饰器的名称。

所以，`add_sprinkles` 是装饰器，基础函数是 `get_ice_cream`。

![](img/460272a5d085c620652be12f9c399d3e_16.png)

```python
@add_sprinkles
def get_ice_cream():
    print("这是你的冰淇淋🍦")
```

![](img/460272a5d085c620652be12f9c399d3e_18.png)

目前，我们的装饰器还没有做任何额外的事情。当我们调用 `get_ice_cream()` 时，它仍然只打印“这是你的冰淇淋🍦”。

![](img/460272a5d085c620652be12f9c399d3e_20.png)

为了真正“添加糖屑”，我们需要在装饰器的 `wrapper` 函数中添加一些代码。例如，在调用基础函数之前，打印一条“你添加了糖屑🎉”的消息。

![](img/460272a5d085c620652be12f9c399d3e_22.png)

```python
def add_sprinkles(func):
    def wrapper():
        print("你添加了糖屑🎉")
        func()
    return wrapper
```

![](img/460272a5d085c620652be12f9c399d3e_24.png)

现在，当我们调用 `get_ice_cream()` 时，输出将是：
```
你添加了糖屑🎉
这是你的冰淇淋🍦
```

![](img/460272a5d085c620652be12f9c399d3e_26.png)

![](img/460272a5d085c620652be12f9c399d3e_27.png)

我们装饰了基础函数 `get_ice_cream`，但没有修改它，只是扩展了它。

## 为什么需要 `wrapper` 函数？🛡️

![](img/460272a5d085c620652be12f9c399d3e_29.png)

你可能注意到，我们在装饰器内部定义了一个嵌套的 `wrapper` 函数。这是必要的。

如果我们去掉 `wrapper` 函数，直接在装饰器里调用 `func()`，那么**一旦应用装饰器，函数就会立即被执行**，而不是在我们真正调用 `get_ice_cream` 时才执行。

![](img/460272a5d085c620652be12f9c399d3e_31.png)

![](img/460272a5d085c620652be12f9c399d3e_32.png)

`wrapper` 函数的作用是将代码“包装”起来，确保装饰器添加的行为只在目标函数被调用时才会执行。

![](img/460272a5d085c620652be12f9c399d3e_33.png)

## 应用多个装饰器 🎨

![](img/460272a5d085c620652be12f9c399d3e_35.png)

你可以为一个基础函数应用多个装饰器。让我们再创建一个添加巧克力酱的装饰器。

![](img/460272a5d085c620652be12f9c399d3e_37.png)

以下是 `add_fudge` 装饰器的定义：

```python
def add_fudge(func):
    def wrapper():
        print("你添加了巧克力酱🍫")
        func()
    return wrapper
```

![](img/460272a5d085c620652be12f9c399d3e_39.png)

现在，我们可以将两个装饰器都应用到 `get_ice_cream` 函数上。装饰器的应用顺序是从下往上（或从内到外）执行的。

```python
@add_fudge
@add_sprinkles
def get_ice_cream():
    print("这是你的冰淇淋🍦")
```

![](img/460272a5d085c620652be12f9c399d3e_41.png)

调用 `get_ice_cream()` 将输出：
```
你添加了巧克力酱🍫
你添加了糖屑🎉
这是你的冰淇淋🍦
```

![](img/460272a5d085c620652be12f9c399d3e_43.png)

## 处理带参数的函数 🧩

![](img/460272a5d085c620652be12f9c399d3e_45.png)

如果基础函数接受参数怎么办？例如，我们的 `get_ice_cream` 函数可能需要接收一个口味参数。

![](img/460272a5d085c620652be12f9c399d3e_47.png)

我们需要修改基础函数和装饰器中的 `wrapper` 函数来接收参数。

![](img/460272a5d085c620652be12f9c399d3e_49.png)

首先，修改基础函数：

```python
def get_ice_cream(flavor):
    print(f"这是你的{flavor}味冰淇淋🍦")
```

![](img/460272a5d085c620652be12f9c399d3e_51.png)

![](img/460272a5d085c620652be12f9c399d3e_53.png)

然后，我们需要修改装饰器中的 `wrapper` 函数，使其能够接收任意数量的位置参数和关键字参数。这通过 `*args` 和 `**kwargs` 来实现。

![](img/460272a5d085c620652be12f9c399d3e_55.png)

更新后的 `add_sprinkles` 装饰器：

![](img/460272a5d085c620652be12f9c399d3e_57.png)

```python
def add_sprinkles(func):
    def wrapper(*args, **kwargs):
        print("你添加了糖屑🎉")
        func(*args, **kwargs)
    return wrapper
```

![](img/460272a5d085c620652be12f9c399d3e_59.png)

同样地，更新 `add_fudge` 装饰器：

![](img/460272a5d085c620652be12f9c399d3e_61.png)

```python
def add_fudge(func):
    def wrapper(*args, **kwargs):
        print("你添加了巧克力酱🍫")
        func(*args, **kwargs)
    return wrapper
```

![](img/460272a5d085c620652be12f9c399d3e_63.png)

现在，我们可以调用带口味的函数了：

![](img/460272a5d085c620652be12f9c399d3e_65.png)

```python
@add_fudge
@add_sprinkles
def get_ice_cream(flavor):
    print(f"这是你的{flavor}味冰淇淋🍦")

![](img/460272a5d085c620652be12f9c399d3e_67.png)

![](img/460272a5d085c620652be12f9c399d3e_69.png)

get_ice_cream("香草")
# 输出：
# 你添加了巧克力酱🍫
# 你添加了糖屑🎉
# 这是你的香草味冰淇淋🍦

![](img/460272a5d085c620652be12f9c399d3e_71.png)

get_ice_cream("巧克力")
# 输出：
# 你添加了巧克力酱🍫
# 你添加了糖屑🎉
# 这是你的巧克力味冰淇淋🍦
```

![](img/460272a5d085c620652be12f9c399d3e_73.png)

## 总结 📚

本节课中我们一起学习了Python装饰器。

![](img/460272a5d085c620652be12f9c399d3e_75.png)

*   **装饰器**是一个函数，它**扩展另一个函数的行为，而不修改那个基础函数**。
*   创建装饰器需要定义一个接收函数作为参数的函数，并在其内部定义一个 `wrapper` 函数来包装核心逻辑，最后返回这个 `wrapper` 函数。
*   使用 `@decorator_name` 的语法将装饰器应用到函数上。
*   你可以为**一个函数应用多个装饰器**，它们会按顺序执行。
*   为了使装饰器能处理带参数的函数，需要在 `wrapper` 函数中使用 `*args` 和 `**kwargs` 来接收和传递所有参数。

![](img/460272a5d085c620652be12f9c399d3e_77.png)

装饰器是Python中非常强大和优雅的特性，广泛应用于日志记录、权限检查、性能测试等场景，能够帮助你写出更清晰、更模块化的代码。