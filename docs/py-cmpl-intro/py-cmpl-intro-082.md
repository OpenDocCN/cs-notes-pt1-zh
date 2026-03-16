# 082：PyQt5按钮控件入门指南 🎯

![](img/b2915c83b39b7a7a9f25533af61ae851_1.png)

在本节课中，我们将学习如何在PyQt5中创建和使用按钮控件。我们将从导入必要的模块开始，逐步创建一个带有交互功能的按钮，并学习如何通过信号与槽机制将按钮点击事件与特定功能连接起来。

---

## 导入必要模块

首先，我们需要导入创建按钮和标签所需的PyQt5模块。

```python
from PyQt5.QtWidgets import QPushButton, QLabel
```

上一节我们介绍了必要的导入，本节中我们来看看如何创建主窗口和初始化用户界面。

---

## 初始化主窗口与用户界面

在之前关于布局管理器的主题中，我们在主窗口的构造函数中定义了一个用于初始化用户界面的方法 `initializeUI`。确保在构造函数中调用此方法，所有用户界面的创建和管理都将在此方法中进行。

```python
class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()
        self.initializeUI()

    def initializeUI(self):
        # 在这里创建和管理用户界面
        pass
```

---

![](img/b2915c83b39b7a7a9f25533af61ae851_3.png)

## 创建按钮控件

![](img/b2915c83b39b7a7a9f25533af61ae851_5.png)

以下是创建按钮控件的步骤。

首先，调用 `QPushButton` 构造函数来创建一个按钮。通常，在创建控件时，我们会使用 `self` 前缀将其定义为实例变量，例如 `self.button`。但为了演示不使用 `self` 前缀时会发生什么，我们先创建一个局部变量 `button`。

```python
button = QPushButton('点击我')
```

接着，将这个按钮添加到窗口中，并设置其几何位置。为了简化，我们直接使用坐标而非布局管理器。

```python
self.setGeometry(150, 200, 200, 100)
```

![](img/b2915c83b39b7a7a9f25533af61ae851_7.png)

按钮的字体可能较小，我们可以通过设置样式表来调整。

![](img/b2915c83b39b7a7a9f25533af61ae851_9.png)

```python
button.setStyleSheet('font-size: 30px;')
```

现在，我们有了一个可显示的按钮。接下来，我们需要为其添加点击功能。

---

## 定义按钮点击功能

![](img/b2915c83b39b7a7a9f25533af61ae851_11.png)

我们需要在 `MainWindow` 类中定义一个方法，作为按钮点击时执行的功能。

![](img/b2915c83b39b7a7a9f25533af61ae851_13.png)

```python
def on_click(self):
    print('按钮被点击了')
```

目前，点击按钮不会有任何反应。我们需要建立按钮的信号与槽连接。

---

## 连接信号与槽

信号是控件交互时发出的事件，槽是响应信号执行的动作或方法。我们将按钮的 `clicked` 信号连接到 `on_click` 方法。

```python
button.clicked.connect(self.on_click)
```

![](img/b2915c83b39b7a7a9f25533af61ae851_15.png)

现在，每次点击按钮，都会执行 `on_click` 方法，并在控制台打印消息。

然而，由于 `button` 是局部变量，`on_click` 方法无法识别它。为了解决这个问题，我们需要将按钮定义为实例变量。

![](img/b2915c83b39b7a7a9f25533af61ae851_17.png)

```python
self.button = QPushButton('点击我')
```

相应地，更新所有对 `button` 的引用为 `self.button`。

![](img/b2915c83b39b7a7a9f25533af61ae851_19.png)

![](img/b2915c83b39b7a7a9f25533af61ae851_20.png)

---

## 完善按钮功能

除了打印消息，我们还可以在点击时改变按钮的文本。

```python
def on_click(self):
    print('按钮被点击了')
    self.button.setText('已点击')
```

![](img/b2915c83b39b7a7a9f25533af61ae851_22.png)

我们还可以在点击后禁用按钮，使其无法再次点击。

![](img/b2915c83b39b7a7a9f25533af61ae851_24.png)

```python
self.button.setDisabled(True)
```

---

## 与标签控件交互

为了展示按钮如何影响其他控件，我们创建一个标签，并在按钮点击时改变其文本。

首先，在构造函数中创建标签并设置其初始文本和样式。

![](img/b2915c83b39b7a7a9f25533af61ae851_26.png)

![](img/b2915c83b39b7a7a9f25533af61ae851_28.png)

```python
self.label = QLabel('你好')
self.label.setGeometry(150, 300, 200, 100)
self.label.setStyleSheet('font-size: 50px;')
```

然后，在 `on_click` 方法中更新标签的文本。

![](img/b2915c83b39b7a7a9f25533af61ae851_30.png)

![](img/b2915c83b39b7a7a9f25533af61ae851_32.png)

```python
def on_click(self):
    print('按钮被点击了')
    self.button.setText('已点击')
    self.button.setDisabled(True)
    self.label.setText('再见')
```

现在，点击按钮不仅会改变按钮自身的状态，还会更新标签的显示内容。

![](img/b2915c83b39b7a7a9f25533af61ae851_34.png)

---

## 总结

![](img/b2915c83b39b7a7a9f25533af61ae851_36.png)

本节课中我们一起学习了PyQt5中按钮控件的基本使用。我们了解了如何创建按钮、设置其样式和位置，以及如何通过信号与槽机制为其添加交互功能。关键点包括：

1.  **创建按钮**：使用 `QPushButton` 构造函数。
2.  **设置样式**：通过 `setStyleSheet` 方法调整外观。
3.  **信号与槽**：使用 `clicked.connect()` 将按钮点击事件连接到自定义方法。
4.  **实例变量**：确保控件定义为实例变量（使用 `self` 前缀），以便在类方法中访问。
5.  **交互功能**：按钮可以触发自身文本变化、禁用状态，以及更新其他控件（如标签）的内容。

![](img/b2915c83b39b7a7a9f25533af61ae851_38.png)

通过掌握这些基础知识，你已经能够为PyQt5应用程序创建基本的交互界面了。