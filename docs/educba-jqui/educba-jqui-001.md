# 001：项目三导论

在本节课中，我们将学习如何使用 jQuery UI 来构建一个交互式的 Web 应用程序。我们将从一个简单的“待办事项列表”项目开始，逐步了解 jQuery UI 的核心概念、功能以及它与 jQuery 的区别。

## 课程概述

jQuery UI 是一个基于 jQuery 的开源用户界面库，它提供了丰富的交互组件、效果和工具，帮助我们轻松创建具有高度交互性的 Web 应用。与 jQuery 主要处理 JavaScript 逻辑不同，jQuery UI 专注于用户界面的构建和交互。

## 课程目标

完成本课程后，你将能够：
*   创建一个使用 jQuery UI 的 Web 应用程序。
*   理解 jQuery UI 相较于 jQuery 的优势。

## 你将学习的内容

以下是本课程涵盖的核心知识点：
*   jQuery UI 的功能特性。
*   jQuery 与 jQuery UI 的区别。
*   使用 jQuery UI 创建简单的 Web 应用程序。
*   jQuery UI 带来的好处。

## jQuery UI 的核心特性

jQuery UI 提供了以下关键特性，使其成为构建交互式应用的强大工具：
*   **高度交互性**：便于创建交互丰富的 Web 应用。
*   **开源免费**：可免费使用。
*   **强大的主题机制**：支持自定义界面外观。
*   **稳定且易于维护**。
*   **广泛的浏览器支持**。
*   **完善的文档**。

## jQuery UI 的四大模块

jQuery UI 的功能主要分为四大类：

**1. 交互 (Interactions)**
这是一组插件，用于实现用户与 DOM 元素的交互。核心交互包括：
*   `draggable`：使元素可拖动。
*   `droppable`：创建可放置目标。
*   `resizable`：使元素可调整大小。
*   `selectable`：使元素可选择。
*   `sortable`：使列表可排序。

**2. 部件 (Widgets)**
这是一组插件，用于创建标准的用户界面组件。常用部件包括：
*   `accordion`：手风琴折叠面板。
*   `autocomplete`：自动完成输入框。
*   `dialog`：对话框。
*   `datepicker`：日期选择器。
*   `progressbar`：进度条。
*   `tabs`：选项卡。
*   `slider`：滑块。

**3. 效果 (Effects)**
jQuery UI 包含一套完整的自定义动画和 DOM 元素过渡效果。核心方法包括：
*   `hide()` / `show()`：隐藏/显示元素。
*   `addClass()` / `removeClass()`：添加/移除 CSS 类。
*   `toggleClass()`：切换 CSS 类。
*   `effect()`：应用特定效果（如爆炸、高亮）。

**4. 工具 (Utilities)**
这是一些模块化工具，被 jQuery UI 内部使用，例如 `position` 工具用于精确定位元素。

## jQuery 与 jQuery UI 的区别

简单来说，两者的核心区别在于：
*   **jQuery**：主要处理 JavaScript 逻辑和 DOM 操作，是 JavaScript 的查询和操作库。
*   **jQuery UI**：构建在 jQuery 之上，专注于**用户界面**和**用户交互**，提供现成的 UI 组件和交互效果。

## 课程要求与先决条件

为了顺利进行本项目，你需要具备以下知识：
*   HTML
*   CSS
*   jQuery
*   jQuery UI
*   JavaScript（因为 jQuery 基于 JavaScript）

此外，你需要在系统中安装一个代码编辑器，例如：
*   Notepad++
*   Sublime Text
*   或其他你熟悉的 IDE。

## 目标受众

本课程适合以下人群：
*   希望成为或已经是 Web 开发者的参与者。
*   希望从事或已经是 Web 设计师的参与者。
*   希望成为或已经是前端开发者的参与者。
*   希望成为或已经是全栈开发者的参与者。

## 项目案例：XYZ 公司待办事项列表

现在，让我们开始具体的项目实践。我们将基于以下案例创建一个 Web 应用：

**案例描述**：XYZ 公司希望创建一个应用程序，用于维护员工的每日待办事项列表。员工可以在应用中写下当天要执行的任务，并能在完成后将其移除。

**项目目标**：基于以上案例，使用 jQuery UI 创建一个 Web 应用程序。

## 项目涵盖内容

在本项目中，我们将具体学习和应用以下技术：
*   jQuery UI
*   jQuery
*   使用 HTML 进行网页设计。
*   使用 CSS 进行样式设计。

## 项目初始化与设置

上一节我们介绍了项目背景，本节中我们来看看如何搭建项目环境。以下是创建项目基础结构的步骤：

**1. 创建项目文件夹结构**
首先，在你的计算机上创建一个名为 `project` 的文件夹。在该文件夹内，创建以下子文件夹：
*   `css`：用于存放样式表文件。
*   `js`：用于存放 JavaScript 文件。
*   `images`：用于存放图片资源。

**2. 下载 jQuery UI 库文件**
为了离线工作，我们需要下载 jQuery UI 的核心文件。你可以从 jQuery UI 官网获取，或直接使用 CDN 链接。这里我们演示离线下载方式：
*   **CSS 文件**：下载 jQuery UI 的主题样式文件（例如 `jquery-ui.css`），并保存到 `css` 文件夹。
*   **JavaScript 文件**：需要两个 JS 文件：
    *   `jquery.js`：jQuery 核心库。**注意**：jQuery UI 依赖于 jQuery，所以必须先引入它。
    *   `jquery-ui.js`：jQuery UI 核心库。
    将这两个文件下载并保存到 `js` 文件夹。

![](img/59e82dd7e76bf427cfdfddc0584639fa_1.png)

**3. 创建基础 HTML 文件**
在项目根目录下，创建一个名为 `index.html` 的文件，并写入基本的 HTML 结构。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>XYZ Company - Daily To-Do List</title>
</head>
<body>
    <h1>XYZ Company</h1>
    <h3>Daily To-Do List</h3>
</body>
</html>
```

## 构建第一个 jQuery UI 组件：手风琴 (Accordion)

现在，让我们开始使用 jQuery UI 构建界面。我们将创建一个手风琴组件来组织内容。

**1. 添加库文件引用**
在 `index.html` 文件的 `<head>` 部分，引入我们下载的 CSS 和 JS 文件。

```html
<!-- 引入 jQuery UI CSS 主题 -->
<link rel="stylesheet" href="css/jquery-ui.css">
<!-- 引入 jQuery 库 -->
<script src="js/jquery.js"></script>
<!-- 引入 jQuery UI 库 -->
<script src="js/jquery-ui.js"></script>
```

![](img/59e82dd7e76bf427cfdfddc0584639fa_3.png)

**2. 编写手风琴的 HTML 结构**
在 `<body>` 中，我们创建一个 `<div>` 并为其设置一个 id（例如 `accordion`）。内部使用 `<h3>` 作为标题，`<div>` 作为内容面板。

```html
<div id="accordion">
    <h3>Employee Details</h3>
    <div>
        <p>Content for employee details goes here.</p>
    </div>
    <h3>Daily Tasks</h3>
    <div>
        <p>Content for daily tasks goes here.</p>
    </div>
    <h3>About XYZ Company</h3>
    <div>
        <p>Content about the company goes here.</p>
    </div>
</div>
```

![](img/59e82dd7e76bf427cfdfddc0584639fa_5.png)

**3. 使用 jQuery 初始化手风琴**
在页面底部（`</body>` 标签之前），添加一个 `<script>` 标签，并编写 jQuery 代码来将我们定义的 `div` 转换为手风琴组件。

```html
<script>
    $(function() {
        $("#accordion").accordion();
    });
</script>
```
**代码解释**：
*   `$(function() { ... })`：这是 `$(document).ready()` 的简写，确保在 DOM 完全加载后执行代码。
*   `$("#accordion")`：这是一个 jQuery 选择器，用于选取 id 为 `accordion` 的元素。
*   `.accordion()`：这是 jQuery UI 提供的方法，它将选中的元素转换成一个手风琴组件。

![](img/59e82dd7e76bf427cfdfddc0584639fa_7.png)

![](img/59e82dd7e76bf427cfdfddc0584639fa_8.png)

**4. 添加简单样式**
为了让页面更美观，我们可以添加一些基本的 CSS 样式。

```html
<style>
    body {
        background-color: #f0f0f0;
        font-family: Arial, sans-serif;
    }
    h1 {
        text-align: center;
        color: #333;
    }
    hr {
        border: 1px solid #ccc;
    }
</style>
```

![](img/59e82dd7e76bf427cfdfddc0584639fa_10.png)

保存所有更改并在浏览器中打开 `index.html` 文件。你现在应该能看到一个功能完整、可折叠展开的手风琴组件。

## 总结

![](img/59e82dd7e76bf427cfdfddc0584639fa_12.png)

在本节课中，我们一起学习了 jQuery UI 的基础知识，并动手创建了第一个交互式组件。我们了解到：
1.  **jQuery UI** 是一个专注于用户界面交互的库，构建在 **jQuery** 之上。
2.  它主要包含**交互**、**部件**、**效果**和**工具**四大模块。
3.  我们完成了项目的环境搭建，包括文件夹结构创建和库文件引入。
4.  我们成功使用 `accordion()` 方法将一个普通的 HTML 结构转换成了交互式的手风琴面板。

![](img/59e82dd7e76bf427cfdfddc0584639fa_14.png)

![](img/59e82dd7e76bf427cfdfddc0584639fa_15.png)

这只是一个开始。在接下来的课程中，我们将在这个手风琴的“Daily Tasks”面板里，继续使用 jQuery UI 的其他部件（如按钮、对话框）和交互功能来构建完整的待办事项列表应用。