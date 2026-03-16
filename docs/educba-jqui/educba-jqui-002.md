# 002：创建员工详情选项卡

在本节课中，我们将学习如何使用jQuery UI的选项卡（Tabs）和对话框（Dialog）组件来构建一个包含员工详情和公司信息的交互式页面。我们将从创建员工详情选项卡开始，然后添加一个关于公司的选项卡，并在其中集成一个可点击按钮来弹出对话框。

## 创建员工详情选项卡

上一节我们设置了基本的选项卡结构。本节中，我们来看看如何填充第一个选项卡的内容。

![](img/e0e92c87067d509adb841be5ab3953b9_1.png)

![](img/e0e92c87067d509adb841be5ab3953b9_2.png)

我们将创建一个`<div>`元素来容纳员工详情。这个`<div>`需要特定的jQuery UI类来应用样式。

以下是创建员工详情容器的代码结构：

```html
<div class="ui-widget-content">
    <div class="ui-state-highlight ui-corner-all">
        <!-- 员工详情内容将放在这里 -->
    </div>
</div>
```

现在，我们可以在内部的`<div>`中添加员工的具体信息。例如，我们可以添加员工的姓名、职位、邮箱和联系电话。

![](img/e0e92c87067d509adb841be5ab3953b9_4.png)

![](img/e0e92c87067d509adb841be5ab3953b9_5.png)

以下是员工详情的示例内容：

![](img/e0e92c87067d509adb841be5ab3953b9_7.png)

![](img/e0e92c87067d509adb841be5ab3953b9_9.png)

*   **姓名**：John Smith
*   **职位**：Full Stack Developer
*   **邮箱**：john.s@xyz.com
*   **联系电话**：1234567890

![](img/e0e92c87067d509adb841be5ab3953b9_11.png)

保存文件并在浏览器中查看，你将在“员工详情”选项卡中看到这些信息。其他选项卡目前是空白的。你可以根据需要添加CSS样式（如边距）来美化这个区域。

![](img/e0e92c87067d509adb841be5ab3953b9_13.png)

![](img/e0e92c87067d509adb841be5ab3953b9_15.png)

## 创建“关于公司”选项卡

接下来，我们填充第二个选项卡，即“关于公司”选项卡。这个选项卡将包含一段公司介绍和一个可以触发对话框的按钮。

![](img/e0e92c87067d509adb841be5ab3953b9_17.png)

![](img/e0e92c87067d509adb841be5ab3953b9_19.png)

首先，我们创建一个简单的`<div>`来放置文本内容。

![](img/e0e92c87067d509adb841be5ab3953b9_21.png)

以下是公司介绍的示例段落：

![](img/e0e92c87067d509adb841be5ab3953b9_23.png)

> 我们是一家专注于创新软件解决方案的科技公司。我们的团队致力于为客户提供高质量的产品和服务，推动行业数字化转型。

![](img/e0e92c87067d509adb841be5ab3953b9_25.png)

![](img/e0e92c87067d509adb841be5ab3953b9_27.png)

你可以在段落后面添加一个换行符`<br>`，然后继续添加更多内容。为了增加交互性，我们将在介绍下方添加一个按钮。

![](img/e0e92c87067d509adb841be5ab3953b9_29.png)

## 添加交互式对话框按钮

现在，我们添加一个按钮，当用户点击它时，会弹出一个对话框显示更多信息。

我们创建一个按钮，并为其添加jQuery UI的按钮类，使其具有标准样式。

![](img/e0e92c87067d509adb841be5ab3953b9_31.png)

以下是按钮的HTML代码：

![](img/e0e92c87067d509adb841be5ab3953b9_33.png)

```html
<button id="dialog-link" class="ui-button ui-corner-all ui-widget">关于软件</button>
```

为了使按钮居中，我们可以将其包裹在`<center>`标签中。接下来，我们需要创建这个按钮将要触发的对话框。

![](img/e0e92c87067d509adb841be5ab3953b9_35.png)

## 构建对话框内容

![](img/e0e92c87067d509adb841be5ab3953b9_37.png)

对话框是一个隐藏的`<div>`，只有在被调用时才会显示。我们为它设置一个ID，以便通过JavaScript控制。

![](img/e0e92c87067d509adb841be5ab3953b9_39.png)

以下是对话框的HTML结构：

![](img/e0e92c87067d509adb841be5ab3953b9_41.png)

![](img/e0e92c87067d509adb841be5ab3953b9_42.png)

![](img/e0e92c87067d509adb841be5ab3953b9_43.png)

```html
<div id="dialog" title="每日工作清单">
    <p>这里是关于软件日常工作和项目管理的详细信息。这个对话框展示了如何使用jQuery UI Dialog组件来显示额外内容。</p>
</div>
```

如果此时刷新页面，对话框内容会直接显示在页面上。为了将其初始化为一个真正的对话框并默认隐藏，我们需要使用jQuery脚本。

## 使用jQuery初始化对话框

我们将使用jQuery选择器选中对话框的ID，并调用`.dialog()`方法将其转换为对话框组件。同时，我们设置`autoOpen: false`，使其不会在页面加载时自动打开。

以下是初始化对话框的JavaScript代码：

![](img/e0e92c87067d509adb841be5ab3953b9_45.png)

```javascript
$("#dialog").dialog({
    autoOpen: false,
    width: 400,
    buttons: [
        {
            text: "确定",
            click: function() {
                $(this).dialog("close");
            }
        },
        {
            text: "取消",
            click: function() {
                $(this).dialog("close");
            }
        }
    ]
});
```

![](img/e0e92c87067d509adb841be5ab3953b9_47.png)

这段代码创建了一个宽度为400像素的对话框，并添加了“确定”和“取消”两个按钮，点击任意按钮都会关闭对话框。

## 绑定按钮点击事件

最后一步是将之前创建的按钮与对话框的打开动作绑定起来。我们为按钮的点击事件编写一个处理函数。

![](img/e0e92c87067d509adb841be5ab3953b9_49.png)

![](img/e0e92c87067d509adb841be5ab3953b9_51.png)

以下是绑定点击事件的JavaScript代码：

![](img/e0e92c87067d509adb841be5ab3953b9_53.png)

```javascript
$("#dialog-link").click(function(event) {
    $("#dialog").dialog("open");
    event.preventDefault(); // 防止按钮的默认行为（如表单提交）
});
```

![](img/e0e92c87067d509adb841be5ab3953b9_55.png)

![](img/e0e92c87067d509adb841be5ab3953b9_57.png)

现在，当你点击“关于软件”按钮时，将会弹出一个包含公司详细信息的对话框。你可以通过对话框上的按钮或标题栏的关闭图标来关闭它。

## 总结

![](img/e0e92c87067d509adb841be5ab3953b9_59.png)

![](img/e0e92c87067d509adb841be5ab3953b9_61.png)

本节课中我们一起学习了如何使用jQuery UI构建一个交互式选项卡页面。我们完成了以下步骤：
1.  在“员工详情”选项卡中展示了结构化的员工信息。
2.  在“关于公司”选项卡中添加了公司介绍文本。
3.  创建了一个具有jQuery UI样式的按钮。
4.  构建了一个默认隐藏的对话框组件，并为其配置了操作按钮。
5.  编写JavaScript代码，将按钮的点击事件与对话框的打开功能绑定起来。

![](img/e0e92c87067d509adb841be5ab3953b9_63.png)

通过这些步骤，我们实现了一个基本的、包含弹窗交互的Web应用界面。你可以在此基础上，进一步添加CSS样式或更多选项卡内容来丰富页面。