# 038：Streamlit 数据可视化Web应用 - 数据提取与处理

在本节课中，我们将继续开发数据可视化Web应用。我们将学习如何从用户上传的Excel文件中提取特定数据，并对其进行处理，为后续的可视化步骤做准备。核心内容包括使用Pandas库读取数据、筛选用户选择的文件、提取特定列的数据以及处理日期格式。

---

## 概述与目标

上一节我们完成了文件上传和选项选择界面的搭建。本节中，我们将重点实现数据的后端处理逻辑。具体来说，我们需要：
1.  遍历用户上传的所有文件。
2.  仅处理用户通过多选框选定的文件。
3.  使用Pandas读取Excel文件。
4.  提取用户通过单选按钮选择的特定列数据。
5.  正确处理日期列，为绘制图表做好准备。

---

## 遍历与筛选用户选择的文件

![](img/3ba1c4078fb9cae311755117b4003432_1.png)

首先，我们需要遍历所有上传的文件，但只处理用户明确选中的那些。以下是实现步骤：

我们使用一个`for`循环来遍历`uploaded_files`列表中的每个文件。在循环内部，通过一个`if`语句检查当前文件名是否存在于用户选择的文件列表`selected_files`中。

```python
for file in uploaded_files:
    if file.name in selected_files:
        # 处理这个文件
        pass
    else:
        # 跳过这个文件
        continue
```

![](img/3ba1c4078fb9cae311755117b4003432_3.png)

![](img/3ba1c4078fb9cae311755117b4003432_4.png)

![](img/3ba1c4078fb9cae311755117b4003432_5.png)

这样，只有被选中的文件才会进入后续的数据处理流程。

---

## 使用Pandas读取Excel数据

![](img/3ba1c4078fb9cae311755117b4003432_7.png)

![](img/3ba1c4078fb9cae311755117b4003432_8.png)

为了高效地处理表格数据（如Excel、CSV），我们使用强大的`pandas`库。如果尚未安装，请在终端中运行以下命令：

```bash
pip install pandas
```

![](img/3ba1c4078fb9cae311755117b4003432_10.png)

安装后，在代码中导入pandas，并使用`pd.read_excel()`函数读取文件。为了避免pandas自动生成行索引（0, 1, 2...），我们需要指定文件中的某一列作为索引。在本例中，第一列“ID”适合作为索引，我们使用参数`index_col=0`。

```python
import pandas as pd
# 读取Excel文件，并指定第一列为索引
shop_data = pd.read_excel(file, index_col=0)
```

---

![](img/3ba1c4078fb9cae311755117b4003432_12.png)

![](img/3ba1c4078fb9cae311755117b4003432_13.png)

## 提取特定列的数据

用户通过单选按钮选择他们想要可视化的数据列（如CPU、GPU）。我们需要根据这个选择，从DataFrame中提取相应的列数据。

提取单列数据非常简单，只需将列名（即`option`变量的值）放入方括号中即可。提取出的数据是一个Pandas Series对象。

![](img/3ba1c4078fb9cae311755117b4003432_15.png)

![](img/3ba1c4078fb9cae311755117b4003432_16.png)

```python
# 提取用户选择的列数据
selected_column_data = shop_data[option]
```

为了后续绘图方便，我们通常需要将Series转换为Python列表。

![](img/3ba1c4078fb9cae311755117b4003432_18.png)

```python
# 将数据转换为列表
data_list = selected_column_data.tolist()
```

![](img/3ba1c4078fb9cae311755117b4003432_19.png)

![](img/3ba1c4078fb9cae311755117b4003432_20.png)

---

## 处理日期数据

![](img/3ba1c4078fb9cae311755117b4003432_22.png)

我们的数据表中包含一个“Date”列，但其在Excel中的格式是“日期时间”类型（例如 `2023-10-27 00:00:00`）。直接转换为列表会得到复杂的datetime对象，不利于在图表轴上显示。

![](img/3ba1c4078fb9cae311755117b4003432_23.png)

**解决方案如下：**
1.  我们首先提取该列的所有原始值。
2.  然后，将每个datetime对象转换为字符串。
3.  接着，利用日期和时间之间的分隔符“T”进行分割（ISO格式日期字符串如`2023-10-27T00:00:00`）。
4.  最后，只取分割后的第一部分，即纯日期部分。

我们创建一个专门的函数`date_converter`来完成这个任务：

```python
def date_converter(date_column):
    """
    将日期时间列转换为纯日期字符串列表。
    参数 date_column: pandas Series，包含日期时间数据。
    返回: 纯日期字符串列表。
    """
    date_values = date_column.values # 获取原始值数组
    result = [] # 初始化结果列表
    for value in date_values:
        # 转换为字符串并按‘T’分割，取日期部分
        date_only = str(value).split('T')[0]
        result.append(date_only)
    return result

# 使用函数处理‘Date’列
dates_list = date_converter(shop_data['Date'])
print("处理后的日期列表：", dates_list)
```

运行后，我们将得到一个干净的日期字符串列表，例如 `[‘2023-10-26’， ‘2023-10-27’]`，这非常适合用作图表的X轴数据。

---

## 本节总结

![](img/3ba1c4078fb9cae311755117b4003432_25.png)

本节课中我们一起学习了数据可视化应用的核心数据处理步骤。
1.  我们学会了如何筛选用户选择的文件进行针对性处理。
2.  掌握了使用Pandas读取Excel文件并设置正确索引的方法。
3.  实现了根据用户交互动态提取特定数据列的功能。
4.  最关键的是，我们创建了一个函数来清洗和转换日期时间数据，得到了干净的日期列表，为下一步绘制图表做好了充分准备。

![](img/3ba1c4078fb9cae311755117b4003432_27.png)

![](img/3ba1c4078fb9cae311755117b4003432_28.png)

至此，数据的提取和预处理工作已经完成。在下一节课中，我们将使用Matplotlib库，把处理好的数据（`dates_list`作为X轴，`data_list`作为Y轴）绘制成直观的折线图，并集成到Streamlit应用中展示给用户。