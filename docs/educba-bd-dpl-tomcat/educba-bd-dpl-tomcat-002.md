# 002：创建“关于我们”与“服务列表”页面 🏗️

![](img/de356d1dadfe6b8d1992040e4026aece_1.png)

![](img/de356d1dadfe6b8d1992040e4026aece_3.png)

在本节课中，我们将学习如何为网站创建两个核心页面：“关于我们”页面和“服务列表”页面。我们将使用JSP文件，并应用CSS样式来美化页面布局。

![](img/de356d1dadfe6b8d1992040e4026aece_5.png)

## 创建“关于我们”页面 📄

![](img/de356d1dadfe6b8d1992040e4026aece_7.png)

![](img/de356d1dadfe6b8d1992040e4026aece_9.png)

![](img/de356d1dadfe6b8d1992040e4026aece_10.png)

![](img/de356d1dadfe6b8d1992040e4026aece_12.png)

上一节我们创建了网站首页。本节中，我们将创建一个“关于我们”页面。

![](img/de356d1dadfe6b8d1992040e4026aece_14.png)

首先，在Web Content目录上右键，选择创建一个新的JSP文件，命名为 `about.jsp`。

![](img/de356d1dadfe6b8d1992040e4026aece_16.png)

![](img/de356d1dadfe6b8d1992040e4026aece_18.png)

我们将复制首页的基本结构，包括页眉、主体和页脚，并移除其中不必要的部分（如part1和part2）。接着，为页面链接外部样式表。

![](img/de356d1dadfe6b8d1992040e4026aece_20.png)

![](img/de356d1dadfe6b8d1992040e4026aece_22.png)

![](img/de356d1dadfe6b8d1992040e4026aece_24.png)

![](img/de356d1dadfe6b8d1992040e4026aece_26.png)

![](img/de356d1dadfe6b8d1992040e4026aece_28.png)

以下是链接样式表的代码：
```html
<link rel="stylesheet" type="text/css" href="mystyle.css">
```

![](img/de356d1dadfe6b8d1992040e4026aece_30.png)

![](img/de356d1dadfe6b8d1992040e4026aece_32.png)

![](img/de356d1dadfe6b8d1992040e4026aece_33.png)

![](img/de356d1dadfe6b8d1992040e4026aece_35.png)

![](img/de356d1dadfe6b8d1992040e4026aece_37.png)

运行页面后，我们发现内容区域需要进一步设计。因此，我们在主体部分创建一个 `div` 容器，并为其设置ID `content`。

![](img/de356d1dadfe6b8d1992040e4026aece_39.png)

![](img/de356d1dadfe6b8d1992040e4026aece_41.png)

![](img/de356d1dadfe6b8d1992040e4026aece_43.png)

![](img/de356d1dadfe6b8d1992040e4026aece_45.png)

在 `content` 容器内，我们添加一个居中的标题和一个水平分隔线 `<hr>`。

![](img/de356d1dadfe6b8d1992040e4026aece_47.png)

![](img/de356d1dadfe6b8d1992040e4026aece_49.png)

![](img/de356d1dadfe6b8d1992040e4026aece_51.png)

![](img/de356d1dadfe6b8d1992040e4026aece_53.png)

以下是添加标题和分隔线的示例：
```html
<div id="content">
    <h1 align="center">关于XYZ公司</h1>
    <hr>
</div>
```

![](img/de356d1dadfe6b8d1992040e4026aece_55.png)

![](img/de356d1dadfe6b8d1992040e4026aece_57.png)

接下来，我们添加几个段落来填充页面内容。为了改善段落的外观，我们尝试通过CSS为其添加边距。

![](img/de356d1dadfe6b8d1992040e4026aece_59.png)

我们尝试在外部样式表中为 `#content` 添加样式，但发现样式未生效。因此，我们改为在页面内部使用 `<style>` 标签直接为段落添加样式。

![](img/de356d1dadfe6b8d1992040e4026aece_61.png)

![](img/de356d1dadfe6b8d1992040e4026aece_63.png)

以下是内部CSS样式的示例：
```html
<style>
    p {
        margin-left: 10%;
        margin-right: 10%;
        font-size: 18px;
    }
</style>
```

应用样式后，段落有了合适的边距和字体大小，页面布局变得更加美观。

![](img/de356d1dadfe6b8d1992040e4026aece_65.png)

![](img/de356d1dadfe6b8d1992040e4026aece_67.png)

## 创建“服务列表”页面 📋

![](img/de356d1dadfe6b8d1992040e4026aece_69.png)

上一节我们完成了“关于我们”页面。本节中，我们来创建“服务列表”页面。

![](img/de356d1dadfe6b8d1992040e4026aece_71.png)

![](img/de356d1dadfe6b8d1992040e4026aece_73.png)

同样，我们右键点击Web Content目录，创建一个新的JSP文件，命名为 `service.jsp`。我们复制相同的页眉和页脚结构，并链接外部样式表。

![](img/de356d1dadfe6b8d1992040e4026aece_75.png)

![](img/de356d1dadfe6b8d1992040e4026aece_77.png)

![](img/de356d1dadfe6b8d1992040e4026aece_79.png)

![](img/de356d1dadfe6b8d1992040e4026aece_81.png)

在页面主体中，我们计划展示五项服务。为此，我们创建五个 `div` 容器，每个代表一项服务，并分别设置ID（如 `ser1`, `ser2` 等）。

![](img/de356d1dadfe6b8d1992040e4026aece_83.png)

![](img/de356d1dadfe6b8d1992040e4026aece_85.png)

![](img/de356d1dadfe6b8d1992040e4026aece_87.png)

![](img/de356d1dadfe6b8d1992040e4026aece_89.png)

在每个服务 `div` 中，我们添加一个服务标题（如“潜在客户开发服务”）。

![](img/de356d1dadfe6b8d1992040e4026aece_91.png)

为了将这些服务水平排列，我们使用CSS的绝对定位（`position: absolute`）并设置不同的 `left` 值。

![](img/de356d1dadfe6b8d1992040e4026aece_93.png)

![](img/de356d1dadfe6b8d1992040e4026aece_95.png)

![](img/de356d1dadfe6b8d1992040e4026aece_96.png)

![](img/de356d1dadfe6b8d1992040e4026aece_98.png)

![](img/de356d1dadfe6b8d1992040e4026aece_100.png)

![](img/de356d1dadfe6b8d1992040e4026aece_102.png)

以下是服务容器的CSS定位示例：
```css
#ser1 { position: absolute; top: 10%; left: 0%; width: 20%; }
#ser2 { position: absolute; top: 10%; left: 20%; width: 20%; }
/* ... 以此类推 */
```

![](img/de356d1dadfe6b8d1992040e4026aece_103.png)

![](img/de356d1dadfe6b8d1992040e4026aece_105.png)

![](img/de356d1dadfe6b8d1992040e4026aece_107.png)

运行后，我们得到了水平排列的服务标题。为了美化，我们在每个标题下添加了宽度为90%且居中的水平线 `<hr>`。

![](img/de356d1dadfe6b8d1992040e4026aece_109.png)

接着，我们为每项服务添加图片和描述文字。我们使用 `<img>` 标签插入图片，并设置合适的高度和宽度。

![](img/de356d1dadfe6b8d1992040e4026aece_111.png)

![](img/de356d1dadfe6b8d1992040e4026aece_113.png)

![](img/de356d1dadfe6b8d1992040e4026aece_115.png)

![](img/de356d1dadfe6b8d1992040e4026aece_117.png)

以下是添加图片的代码示例：
```html
<center>
    <img src="lead.jpg" height="200" width="150">
</center>
```

![](img/de356d1dadfe6b8d1992040e4026aece_119.png)

![](img/de356d1dadfe6b8d1992040e4026aece_120.png)

在图片下方，我们添加一个段落 `<p>` 来描述服务。为了使段落文字在容器内更美观，我们为其创建一个CSS类 `.txt`，并设置左右边距。

![](img/de356d1dadfe6b8d1992040e4026aece_122.png)

![](img/de356d1dadfe6b8d1992040e4026aece_123.png)

![](img/de356d1dadfe6b8d1992040e4026aece_124.png)

![](img/de356d1dadfe6b8d1992040e4026aece_126.png)

![](img/de356d1dadfe6b8d1992040e4026aece_128.png)

![](img/de356d1dadfe6b8d1992040e4026aece_130.png)

![](img/de356d1dadfe6b8d1992040e4026aece_131.png)

![](img/de356d1dadfe6b8d1992040e4026aece_133.png)

以下是段落样式的示例：
```css
.txt {
    margin-left: 5%;
    margin-right: 5%;
}
```

最后，我们在所有服务上方添加一个居中的总标题“我们的服务列表”，并调整了各服务容器的顶部距离，使布局更加协调。

![](img/de356d1dadfe6b8d1992040e4026aece_135.png)

本节课中我们一起学习了如何创建“关于我们”和“服务列表”两个功能页面。我们掌握了使用JSP文件结构、应用CSS进行定位和样式美化，以及插入图片和文本内容的方法，为构建完整的网站打下了基础。