# 044：在 Streamlit 中使用自定义 CSS 🎨

![](img/060a5be1fdc466ad54d1f4de77167c30_1.png)

在本节课中，我们将学习如何为 Streamlit 应用程序添加自定义 CSS 样式，以改变其外观和布局。我们将通过修改一个 URL 缩短器应用来演示整个过程，从检查元素到编写并嵌入 CSS 代码。

## 概述

![](img/060a5be1fdc466ad54d1f4de77167c30_3.png)

我们将通过以下步骤学习如何自定义 Streamlit 应用的样式：
1.  运行应用并使用浏览器开发者工具检查其 HTML 和 CSS 结构。
2.  识别需要修改的特定元素及其类名。
3.  创建一个独立的 CSS 文件并编写样式规则。
4.  使用 Streamlit 的 Markdown 组件将 CSS 样式内部嵌入到应用中。
5.  应用各种 CSS 属性（如背景色、字体、边框）来美化应用。

![](img/060a5be1fdc466ad54d1f4de77167c30_5.png)

## 检查应用结构

首先，运行你的 Streamlit 应用程序。为了理解其结构，我们需要使用浏览器的开发者工具。

![](img/060a5be1fdc466ad54d1f4de77167c30_7.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_8.png)

以下是操作步骤：
*   在浏览器中打开你的 Streamlit 应用。
*   在页面上右键点击，选择“检查”或“Inspect”。
*   这将打开开发者工具，显示应用的 HTML 和 CSS 代码。

通过检查，我们可以找到想要修改的元素的类名或标签。例如，要修改应用标题，可以右键点击标题，选择“检查元素”，找到对应的 `<span>` 标签及其类名。

![](img/060a5be1fdc466ad54d1f4de77167c30_10.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_11.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_12.png)

## 创建并编写 CSS 文件

找到目标元素的类名后，我们可以在项目中创建一个单独的 CSS 文件来编写样式规则。

![](img/060a5be1fdc466ad54d1f4de77167c30_14.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_15.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_16.png)

1.  在项目目录中创建一个新文件，例如 `designing.css`。
2.  在 CSS 文件中，我们通过选择器来定位元素并定义样式。选择器可以是标签名、类名或 ID。
    *   使用类名选择器时，需要在类名前加一个点 `.`，例如 `.className`。
    *   如果元素有多个类，需要用点替换空格，例如 `.class1.class2`。

以下是一个基本的 CSS 规则结构：
```css
span.class1.class2 {
    background-color: blueviolet;
    color: lightyellow;
    border-radius: 20px;
    font-family: Impact;
}
```
在这个例子中，我们修改了背景色、文字颜色、边框圆角和字体。

![](img/060a5be1fdc466ad54d1f4de77167c30_18.png)

## 将 CSS 嵌入 Streamlit 应用

![](img/060a5be1fdc466ad54d1f4de77167c30_20.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_21.png)

创建好 CSS 文件后，我们需要将其内容嵌入到 Streamlit 应用中。我们将使用内部样式表的方式，通过 Markdown 组件来实现。

上一节我们介绍了如何编写 CSS 文件，本节中我们来看看如何将其嵌入到 Python 代码中。

![](img/060a5be1fdc466ad54d1f4de77167c30_23.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_24.png)

操作步骤如下：
1.  在 Python 脚本中，使用 `open()` 函数读取 CSS 文件的内容。
2.  使用 `st.markdown()` 函数，并设置 `unsafe_allow_html=True` 参数，将 CSS 代码包裹在 `<style>` 标签中插入到页面。

![](img/060a5be1fdc466ad54d1f4de77167c30_26.png)

以下是关键代码示例：
```python
import streamlit as st

# 读取 CSS 文件内容
with open(‘designing.css’) as source_design:
    css_content = source_design.read()

![](img/060a5be1fdc466ad54d1f4de77167c30_28.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_29.png)

# 使用 Markdown 嵌入 CSS 样式
st.markdown(f‘<style>{css_content}</style>’, unsafe_allow_html=True)
```
这样，CSS 文件中定义的样式就会应用到整个 Streamlit 应用上。

## 使用 CSS 选择器定位嵌套元素

![](img/060a5be1fdc466ad54d1f4de77167c30_31.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_32.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_33.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_34.png)

有时我们需要修改的元素没有直接的类名或 ID。这时，可以使用 CSS 选择器通过其父元素来定位。

![](img/060a5be1fdc466ad54d1f4de77167c30_36.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_37.png)

例如，要修改一个在特定 `<div>` 内的 `<hr>`（水平线）标签的颜色，可以按以下步骤操作：
1.  在开发者工具中找到包含 `<hr>` 的 `<div>` 的类名。
2.  在 CSS 中使用后代选择器（空格）或子元素选择器（`>`）来定位。

以下是使用子元素选择器的 CSS 代码示例：
```css
/* 选择具有特定类名的 div 下的直接子元素 hr */
div.parentClass > hr {
    background-color: red;
}
```
通过这种方式，我们可以精确地控制页面中任何元素的样式。

## 探索更多 CSS 属性

CSS 提供了丰富的属性来控制页面样式。你可以尝试修改以下属性来进一步定制你的应用：

以下是你可以尝试修改的一些常见 CSS 属性：
*   **`padding` / `margin`**: 控制元素的内边距和外边距。
*   **`font-size`**: 改变字体大小。
*   **`text-align`**: 设置文本对齐方式（如居中）。
*   **`box-shadow`**: 为元素添加阴影效果。
*   **`width` / `height`**: 调整元素的宽度和高度。

![](img/060a5be1fdc466ad54d1f4de77167c30_39.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_40.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_41.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_43.png)

建议访问 [W3Schools CSS 教程](https://www.w3schools.com/css/) 来学习和实验更多属性。

## 总结

![](img/060a5be1fdc466ad54d1f4de77167c30_45.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_46.png)

![](img/060a5be1fdc466ad54d1f4de77167c30_48.png)

本节课中我们一起学习了如何为 Streamlit 应用添加自定义 CSS 样式。我们从检查应用结构开始，学会了如何识别元素并获取其类名。接着，我们创建了独立的 CSS 文件，并编写了样式规则。然后，我们通过 `st.markdown()` 将 CSS 代码内部嵌入到应用中。最后，我们还探讨了如何使用 CSS 选择器定位复杂元素，并鼓励大家尝试更多 CSS 属性来创造独特的应用界面。通过自定义 CSS，你可以让 Streamlit 应用摆脱默认样式，展现出个性化的视觉效果。