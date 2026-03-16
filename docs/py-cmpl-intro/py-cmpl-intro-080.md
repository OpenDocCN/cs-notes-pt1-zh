# 080：PyQt5图像处理入门 🖼️

在本节课中，我们将学习如何在PyQt5应用程序中添加和显示图像。你将学会使用`QLabel`和`QPixmap`来加载图片，并掌握调整图像大小和位置的基本技巧。

## 准备工作

![](img/f0597931941f80aaa4093f39764426b1_1.png)

![](img/f0597931941f80aaa4093f39764426b1_2.png)

![](img/f0597931941f80aaa4093f39764426b1_3.png)

在开始之前，你需要准备一张图片。你可以使用自己的个人资料图片或任何其他图片。我们将使用这张图片进行后续操作。

以下是实现此功能所需的导入语句：

```python
from PyQt5.QtWidgets import QLabel
from PyQt5.QtGui import QPixmap
```

## 创建标签并设置几何属性

上一节我们介绍了所需的导入，本节中我们来看看如何创建用于显示图像的标签。

![](img/f0597931941f80aaa4093f39764426b1_5.png)

在窗口类的构造函数中，我们首先创建一个`QLabel`对象，并设置其大小和位置。

![](img/f0597931941f80aaa4093f39764426b1_7.png)

```python
self.label = QLabel(self)
self.label.setGeometry(0, 0, 250, 250)
```

这段代码创建了一个标签，并将其放置在窗口的左上角（坐标`(0, 0)`），宽度和高度均为250像素。

![](img/f0597931941f80aaa4093f39764426b1_9.png)

## 加载并显示图像

![](img/f0597931941f80aaa4093f39764426b1_10.png)

![](img/f0597931941f80aaa4093f39764426b1_11.png)

![](img/f0597931941f80aaa4093f39764426b1_13.png)

现在标签已经创建好了，但它是空的。接下来，我们需要加载图像并将其显示在标签上。

![](img/f0597931941f80aaa4093f39764426b1_15.png)

以下是加载图像的步骤：

![](img/f0597931941f80aaa4093f39764426b1_17.png)

1.  使用`QPixmap`类加载图像文件。
2.  将加载的`QPixmap`对象设置到标签上。

```python
self.pixmap = QPixmap('profile_p.jpg')
self.label.setPixmap(self.pixmap)
```

![](img/f0597931941f80aaa4093f39764426b1_19.png)

代码中的`'profile_p.jpg'`是图像文件的路径。如果图片与你的Python文件在同一目录下，直接使用文件名即可。

![](img/f0597931941f80aaa4093f39764426b1_21.png)

![](img/f0597931941f80aaa4093f39764426b1_23.png)

## 让图像自适应标签大小

![](img/f0597931941f80aaa4093f39764426b1_25.png)

运行上述代码后，你可能会发现图像没有填满整个标签区域。为了让图像根据标签的大小进行缩放，我们需要启用一个属性。

只需调用标签的`setScaledContents`方法并传入`True`。

```python
self.label.setScaledContents(True)
```

启用此功能后，无论你如何调整标签的尺寸，图像都会自动缩放以填充整个标签区域。

![](img/f0597931941f80aaa4093f39764426b1_27.png)

## 调整图像在窗口中的位置

![](img/f0597931941f80aaa4093f39764426b1_29.png)

我们可以通过改变标签的几何属性，轻松地将图像定位在窗口的不同位置。

![](img/f0597931941f80aaa4093f39764426b1_31.png)

![](img/f0597931941f80aaa4093f39764426b1_33.png)

以下是几种常见的定位方法：

![](img/f0597931941f80aaa4093f39764426b1_35.png)

*   **右上角**：将X坐标设置为窗口宽度减去标签宽度。
*   **右下角**：将X坐标设置为窗口宽度减去标签宽度，Y坐标设置为窗口高度减去标签高度。
*   **左下角**：将X坐标设置为0，Y坐标设置为窗口高度减去标签高度。
*   **居中**：将X坐标设置为`(窗口宽度 - 标签宽度) // 2`，Y坐标设置为`(窗口高度 - 标签高度) // 2`。这里使用整数除法`//`以确保像素坐标为整数。

![](img/f0597931941f80aaa4093f39764426b1_37.png)

例如，将图像居中的代码如下：

![](img/f0597931941f80aaa4093f39764426b1_39.png)

```python
window_width = self.width()
window_height = self.height()
label_width = self.label.width()
label_height = self.label.height()

![](img/f0597931941f80aaa4093f39764426b1_41.png)

x = (window_width - label_width) // 2
y = (window_height - label_height) // 2

self.label.setGeometry(x, y, label_width, label_height)
```

## 总结

![](img/f0597931941f80aaa4093f39764426b1_43.png)

本节课中我们一起学习了在PyQt5中处理图像的基础知识。我们掌握了如何使用`QLabel`和`QPixmap`加载并显示图片，如何通过`setScaledContents(True)`让图像自适应标签大小，以及如何通过计算坐标将图像精准地定位在窗口的任意位置。这些是构建具有丰富图像界面应用程序的重要基础技能。