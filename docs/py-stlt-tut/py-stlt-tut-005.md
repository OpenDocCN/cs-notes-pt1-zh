# 005：Streamlit显示元素

在本节课中，我们将要学习Streamlit中的几个核心显示元素：`st.write()`函数、`st.metric()`函数、`st.table()`函数和`st.dataframe()`函数。这些函数是构建交互式应用界面的基础。

![](img/0ef371f9a194089f3d8a8a962d2dd399_1.png)

## 🛠️ `st.write()`：Streamlit的瑞士军刀

![](img/0ef371f9a194089f3d8a8a962d2dd399_2.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_3.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_4.png)

上一节我们介绍了Streamlit的基本布局，本节中我们来看看一个功能强大的通用函数——`st.write()`。这个函数是Streamlit的“瑞士军刀”，因为它允许我们实现多种功能。

`st.write()`函数的基本语法如下：
```python
st.write(content)
```
它不仅能输出纯文本，还允许我们为文本添加不同的样式。例如，我们可以用它来渲染Markdown、JSON数据或代码。

以下是`st.write()`函数的一些常见用法：

*   **渲染Markdown**：通过在文本前添加`#`等符号，可以将其渲染为Markdown标题。
    ```python
    st.write(‘## 这是一个H2标题‘)
    ```
*   **输出JSON**：可以直接传入Python字典，它会以格式化的JSON形式显示。
*   **显示代码**：可以传入代码字符串，它会以代码块的形式高亮显示。

你可以通过查阅Streamlit官方文档来探索`st.write()`函数的更多玩法。

![](img/0ef371f9a194089f3d8a8a962d2dd399_6.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_7.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_8.png)

## 📊 `st.metric()`：显示指标与变化

![](img/0ef371f9a194089f3d8a8a962d2dd399_9.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_10.png)

接下来，我们讨论`st.metric()`函数。这个函数主要用于展示一个关键指标及其变化值，常用于仪表板中显示如温度、速度、销售额等数据。

`st.metric()`函数接受三个重要参数：
```python
st.metric(label, value, delta)
```
*   `label`：指标的标签，例如“风速”。
*   `value`：指标的值，例如“120”。
*   `delta`：指标值的变化量，例如“-1.4”。正值显示为绿色上升箭头，负值显示为红色下降箭头。

![](img/0ef371f9a194089f3d8a8a962d2dd399_12.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_13.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_14.png)

例如，要显示风速及其变化，可以这样写：
```python
st.metric(label=“风速”, value=“120 m s⁻¹”, delta=“-1.4 m s⁻¹”)
```
**注意**：为了在字符串中正确显示上标（如`s⁻¹`），你可能需要在VS Code中安装名为“Fast Unicode Math Characters”的扩展，并使用特定快捷键（如输入`^`后按`Tab`键）来生成上标格式。

运行后，你会看到一个清晰的指标卡片，其中变化值`-1.4`会以红色和向下箭头显示，直观地表明了下降趋势。

![](img/0ef371f9a194089f3d8a8a962d2dd399_16.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_17.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_18.png)

## 📈 `st.table()` 与 `st.dataframe()`：展示表格数据

现在，让我们探讨用于展示表格数据的两个函数：`st.table()`和`st.dataframe()`。在开始之前，我们需要先创建一个数据表格。这里我们使用Pandas库来生成一个简单的DataFrame。

首先，确保已安装Pandas库：
```bash
pip install pandas
```
然后在代码中导入并创建DataFrame：
```python
import pandas as pd
import streamlit as st

![](img/0ef371f9a194089f3d8a8a962d2dd399_20.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_21.png)

data = {
    ‘column1‘: [1, 2, 3, 4, 5, 6, 7],
    ‘column2‘: [11, 12, 13, 14, 15, 16, 17]
}
df = pd.DataFrame(data)
```

### 使用 `st.table()` 显示静态表格

![](img/0ef371f9a194089f3d8a8a962d2dd399_23.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_24.png)

`st.table()`函数用于显示一个静态的、样式美观的表格。它直接将数据渲染为HTML表格，不支持交互。
```python
st.table(df)
```
运行后，你会看到一个格式整洁的表格，清晰地列出了所有数据和索引。

### 使用 `st.dataframe()` 显示交互式表格

`st.dataframe()`函数则用于显示一个交互式的数据框。这是它与`st.table()`的主要区别。
```python
st.dataframe(df)
```
运行后，显示的表格在列标题处会有一个排序图标。点击该图标，你可以对该列数据进行升序或降序排序。这种交互功能使得数据探索变得更加方便。

![](img/0ef371f9a194089f3d8a8a962d2dd399_26.png)

**总结一下**：`st.table()`适合展示最终的、无需交互的静态表格；而`st.dataframe()`适合展示需要用户进行排序、筛选等交互操作的数据。

![](img/0ef371f9a194089f3d8a8a962d2dd399_28.png)

## 🎯 课程总结

本节课中我们一起学习了Streamlit的四个核心显示元素：
1.  **`st.write()`**：作为多功能函数，可以输出文本、Markdown、JSON和代码。
2.  **`st.metric()`**：用于突出显示一个关键指标及其变化趋势，非常适合数据仪表板。
3.  **`st.table()`**：用于渲染静态的、美观的数据表格。
4.  **`st.dataframe()`**：用于渲染交互式数据框，支持列排序等功能。

![](img/0ef371f9a194089f3d8a8a962d2dd399_30.png)

![](img/0ef371f9a194089f3d8a8a962d2dd399_31.png)

掌握这些基本的显示元素，是构建Streamlit应用界面的第一步。在接下来的教程中，我们将探索更多有趣的组件。