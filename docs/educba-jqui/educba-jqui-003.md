# 003：创建每日任务选项卡 📝

在本节课中，我们将学习如何创建一个每日任务列表页面。我们将构建一个允许用户查看、添加和标记任务完成状态的交互式界面。

上一节我们介绍了员工详情选项卡的创建，本节中我们来看看如何构建一个功能性的每日任务列表。

## 概述与结构搭建

首先，我们创建一个简单的`<div>`容器来承载整个任务列表模块。

```html
<div id="dailyTask">
  <div class="header">
    <h2>每日任务列表</h2>
    <input type="text" id="myInput" placeholder="添加新任务...">
    <span class="addBtn" onclick="addNewTask()">添加</span>
  </div>
</div>
```

![](img/c82e331d340ebac9eb7b45e695374be2_1.png)

上述代码创建了一个标题为“每日任务列表”的区域，包含一个文本输入框和一个“添加”按钮。用户可以在输入框中填写新任务，点击按钮将其加入列表。

![](img/c82e331d340ebac9eb7b45e695374be2_3.png)

接下来，我们创建一个无序列表来展示预设的每日任务。

```html
<ul id="myUL">
  <li>考勤</li>
  <li>登录系统</li>
  <li>午餐</li>
  <li>茶歇</li>
  <li>登出系统</li>
</ul>
```

这个列表包含了员工每日需要完成的基本任务项。

## 应用基础样式

![](img/c82e331d340ebac9eb7b45e695374be2_5.png)

为了使列表更美观和实用，我们需要添加一些CSS样式。以下是核心的样式定义：

![](img/c82e331d340ebac9eb7b45e695374be2_6.png)

```css
* {
  box-sizing: border-box;
}

ul {
  margin: 0;
  padding: 0;
}

ul li {
  cursor: pointer;
  position: relative;
  padding: 12px 8px 12px 40px;
  background: #eee;
  font-size: 18px;
  transition: 0.2s;
  list-style-type: none;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
```

这些样式移除了列表的默认样式，为列表项设置了背景、内边距，并禁止了文本选择，提升了交互体验。

![](img/c82e331d340ebac9eb7b45e695374be2_8.png)

![](img/c82e331d340ebac9eb7b45e695374be2_9.png)

![](img/c82e331d340ebac9eb7b45e695374be2_11.png)

## 设计列表交互视觉效果

![](img/c82e331d340ebac9eb7b45e695374be2_13.png)

![](img/c82e331d340ebac9eb7b45e695374be2_15.png)

为了增强可读性和交互反馈，我们为列表项设置斑马纹效果，并为已完成的任务添加视觉标记。

![](img/c82e331d340ebac9eb7b45e695374be2_16.png)

以下是实现斑马纹和完成状态的样式：

```css
/* 斑马纹效果 */
ul li:nth-child(odd) {
  background: #f9f9f9;
}

![](img/c82e331d340ebac9eb7b45e695374be2_18.png)

/* 任务完成时的样式 */
ul li.checked {
  background: #888;
  color: #fff;
  text-decoration: line-through;
}

![](img/c82e331d340ebac9eb7b45e695374be2_20.png)

![](img/c82e331d340ebac9eb7b45e695374be2_22.png)

/* 任务完成时的勾选标记 */
ul li.checked::before {
  content: '';
  position: absolute;
  border-color: #fff;
  border-style: solid;
  border-width: 0 2px 2px 0;
  top: 10px;
  left: 16px;
  transform: rotate(45deg);
  height: 15px;
  width: 7px;
}
```

![](img/c82e331d340ebac9eb7b45e695374be2_24.png)

当用户点击一个任务项时，通过JavaScript为其添加`.checked`类，该任务项的背景会变暗，文字会加上删除线，并在左侧显示一个勾选图标，清晰地表明任务已完成。

![](img/c82e331d340ebac9eb7b45e695374be2_26.png)

## 添加删除功能与输入区域样式

![](img/c82e331d340ebac9eb7b45e695374be2_28.png)

每个任务项都需要一个删除按钮，同时输入区域的样式也需要优化以提升用户体验。

![](img/c82e331d340ebac9eb7b45e695374be2_30.png)

![](img/c82e331d340ebac9eb7b45e695374be2_32.png)

以下是删除按钮和输入区域的样式代码：

![](img/c82e331d340ebac9eb7b45e695374be2_34.png)

```css
/* 关闭（删除）按钮 */
.close {
  position: absolute;
  right: 0;
  top: 0;
  padding: 12px 16px 12px 16px;
}

![](img/c82e331d340ebac9eb7b45e695374be2_35.png)

.close:hover {
  background-color: #f44336;
  color: white;
}

/* 头部输入区域样式 */
.header {
  background-color: #4CAF50;
  padding: 30px 40px;
  color: white;
  text-align: center;
}

![](img/c82e331d340ebac9eb7b45e695374be2_37.png)

.header:after {
  content: "";
  display: table;
  clear: both;
}

![](img/c82e331d340ebac9eb7b45e695374be2_39.png)

/* 输入框样式 */
input {
  margin: 0;
  border: none;
  border-radius: 0;
  width: 75%;
  padding: 10px;
  float: left;
  font-size: 16px;
}

/* 添加按钮样式 */
.addBtn {
  padding: 10px;
  width: 25%;
  background: #d9d9d9;
  color: #555;
  float: left;
  text-align: center;
  font-size: 16px;
  cursor: pointer;
  transition: 0.3s;
  border-radius: 0;
}

.addBtn:hover {
  background-color: #bbb;
}
```

删除按钮位于每个任务项的右上角，悬停时会变为红色。输入框和添加按钮并排排列，宽度比例为3:1，按钮在悬停时有背景色变化反馈。

## 功能实现与整合

![](img/c82e331d340ebac9eb7b45e695374be2_41.png)

目前，我们已经完成了静态结构和样式的构建。完整的交互功能（如点击添加任务、点击标记完成、点击删除任务）需要通过JavaScript来实现，这将是后续课程的内容。

至此，我们创建了一个包含以下元素的每日任务列表界面：
*   一个清晰的标题。
*   一个用于添加新任务的输入框和按钮。
*   一个带有预设任务的列表。
*   为列表项设计了斑马纹和完成状态的高亮显示。
*   为每个任务项预留了删除按钮的位置。

![](img/c82e331d340ebac9eb7b45e695374be2_43.png)

本节课中我们一起学习了如何使用HTML和CSS构建一个每日任务列表的静态界面，并为其设计了完整的视觉样式和交互状态。在接下来的课程中，我们将使用jQuery为这个列表添加动态交互功能。