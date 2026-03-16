# 001：项目简介 🚀

在本教程中，我们将学习一个基于Apache Tomcat的项目。我们将创建一个公司网站。

## 概述
本节课我们将学习Apache Tomcat是什么，以及如何利用它来构建和部署一个Java Web应用。我们将从一个简单的案例研究开始，逐步完成一个公司网站的开发。

## 什么是Apache Tomcat？
Apache Tomcat是一个用于执行Java应用程序的Web服务器。它专门用于运行基于Java的程序，如Servlet、JSP等。

Apache Tomcat与Apache HTTP服务器不同。Apache HTTP服务器主要用于静态Web应用，而Apache Tomcat则专注于服务基于Java的网站或Web应用程序。

## 项目难度与先决技能
本项目的难度级别为中级。要开始这个项目，你需要具备以下技能：

以下是开始本项目所需的技能集：
*   具备Java基础知识。
*   了解Servlet和JSP。
*   了解HTML和CSS。
*   了解Apache Tomcat，例如如何配置Tomcat、如何创建服务器等。

## 你将学到什么？
通过这个项目，你将学习以下内容：

以下是本项目涵盖的主要场景：
*   使用Tomcat服务器。
*   使用Apache Tomcat服务器。
*   使用Java创建Web应用程序。
*   使用CSS进行设计。
*   配置Apache Tomcat服务器。

## 目标学员
本课程适合以下人群：

以下是本项目的目标学员：
*   任何希望成为全栈开发者的参与者。全栈开发涉及Web应用程序，并且会用到Apache Tomcat服务器。
*   希望成为Java开发者的人。由于Apache Tomcat运行基于Java的应用程序，因此Java开发者有必要学习使用Apache Tomcat。
*   任何希望创建一个能在Apache Tomcat上运行的项目的学生。

## 项目内容
在本项目中，我们将学习以下基本内容：

以下是本项目的基本学习内容：
*   在Java中创建Web应用程序。
*   在Apache Tomcat中执行应用程序。
*   Apache Tomcat的配置。

## 案例研究
为了详细理解项目，我们从一个简单的案例研究开始。

一个线索生成公司希望创建其公司网站。网站应包含以下页面：主页、关于我们、服务列表和联系我们页面。

基于这个案例研究，我们将创建一个使用Java（JSP和Servlet）的应用程序，并在Apache Tomcat服务器上执行。

以下是本案例研究将涵盖的场景：
*   创建Web应用程序：我们将学习如何使用JSP和Servlet创建Web应用程序。
*   使用CSS进行设计：我们将使用CSS来设计整个概念。
*   在Apache Tomcat中进行配置。

现在，让我们开始这个项目并进行编码。

## 第一步：创建项目与配置服务器
在这一步中，我们将创建一个新项目，添加所有必需的JAR文件，并为我们的项目创建一个Apache Tomcat服务器。

我们将使用Eclipse IDE。启动Eclipse后，首先需要添加一个服务器视图。

在“窗口”菜单中，选择“显示视图”，然后找到“服务器”选项卡。如果找不到，可以在搜索框中输入“服务器”。

找到服务器选项卡后，会显示“没有可用的服务器。单击此链接创建新服务器”。点击该链接。

现在，你会看到不同类型的服务器。我们选择“Apache”下的“Tomcat服务器”。

此时，我们需要下载并安装Apache Tomcat。如果你还没有Tomcat服务器，需要先安装它。

打开浏览器，搜索“下载 Apache Tomcat”。你可以从Apache Tomcat官网获取不同版本（如8.5、9.0等）的安装文件。

例如，你可以选择适用于Windows的32位/64位ZIP文件。下载后，将其解压到指定目录（例如`E:\demo`）。

回到Eclipse的服务器创建向导，点击“浏览”按钮，选择你解压的Tomcat目录（例如`E:\demo\apache-tomcat-8.5.xx`）。然后点击“下一步”和“完成”。

现在，我们已经创建了Apache Tomcat服务器。双击服务器可以进行配置，例如修改服务器名称、主机名（通常是localhost）、端口号（默认8080，可以改为80）、超时设置等。

如果你不想单独配置服务器，还有另一种选择：创建一个Maven项目。Maven项目内置了Apache Tomcat支持。但为了理解本项目，我们将使用我们刚刚配置的Tomcat服务器。

接下来，创建一个新项目。选择“文件” -> “新建” -> “动态Web项目”。

将项目命名为“XYZ_Lead_Generation”。这是我们的线索生成公司名称。

选择目标运行时为我们刚配置的Apache Tomcat服务器，动态Web模块版本保持默认，然后点击“下一步”。确保勾选“生成web.xml部署描述符”，然后点击“完成”。

现在，我们已经为项目创建了Apache Tomcat服务器和一个动态Web项目。

在这个项目中，我们还需要添加两个必要的JAR文件：Servlet API和JSP API的JAR文件，因为我们将使用JSP和Servlet。

右键单击项目，选择“构建路径” -> “配置构建路径”。在“库”选项卡下，点击“添加外部JAR...”。

导航到你的Tomcat安装目录下的`lib`文件夹（例如`E:\demo\apache-tomcat-8.5.xx\lib`），选择`servlet-api.jar`和`jsp-api.jar`（可能名称略有不同），点击“应用并关闭”。

![](img/566d291f94f595e9f09b18439ae429c0_1.png)

现在，我们已经添加了所需的JAR文件。

![](img/566d291f94f595e9f09b18439ae429c0_3.png)

![](img/566d291f94f595e9f09b18439ae429c0_5.png)

![](img/566d291f94f595e9f09b18439ae429c0_7.png)

回到服务器视图，可以双击服务器进行更多配置。例如，我将端口号改为80。保存配置。

![](img/566d291f94f595e9f09b18439ae429c0_9.png)

右键单击服务器，选择“启动”。你会看到Tomcat已启动并同步。

![](img/566d291f94f595e9f09b18439ae429c0_11.png)

要运行页面，可以右键单击项目中的`index.html`或`index.jsp`文件，选择“运行方式” -> “在服务器上运行”。Eclipse会打开一个内部浏览器显示页面。你也可以在“窗口” -> “Web浏览器”设置中更改为使用外部浏览器（如Google Chrome）。

![](img/566d291f94f595e9f09b18439ae429c0_13.png)

![](img/566d291f94f595e9f09b18439ae429c0_14.png)

## 第二步：创建简单主页
在这一步中，我们将创建一个简单的主页。我们将使用不同的HTML标签。

![](img/566d291f94f595e9f09b18439ae429c0_16.png)

![](img/566d291f94f595e9f09b18439ae429c0_18.png)

首先，在WebContent文件夹下，我们有一个`index.html`（或`index.jsp`）文件。我们将在其中编写代码。

![](img/566d291f94f595e9f09b18439ae429c0_20.png)

![](img/566d291f94f595e9f09b18439ae429c0_22.png)

![](img/566d291f94f595e9f09b18439ae429c0_24.png)

我们将使用`<header>`、`<div>`和`<footer>`标签来构建页面结构。

![](img/566d291f94f595e9f09b18439ae429c0_26.png)

![](img/566d291f94f595e9f09b18439ae429c0_28.png)

```html
<!DOCTYPE html>
<html>
<head>
    <title>XYZ Lead Generation</title>
</head>
<body>
    <header>
        <h1 id="main">XYZ Company</h1>
        <h2 id="sub">Lead Generation Company</h2>
    </header>
    <div id="body">
        <!-- 主要内容将放在这里 -->
    </div>
    <footer>
        © XYZ Company
    </footer>
</body>
</html>
```

这是一个基本的HTML结构。接下来，我们希望添加CSS来美化它。

![](img/566d291f94f595e9f09b18439ae429c0_30.png)

![](img/566d291f94f595e9f09b18439ae429c0_31.png)

![](img/566d291f94f595e9f09b18439ae429c0_33.png)

![](img/566d291f94f595e9f09b18439ae429c0_35.png)

## 第三步：添加CSS样式
为了添加CSS，我们在`WebContent`文件夹上右键，选择“新建” -> “其他”，在过滤器中输入“CSS”，创建一个新的CSS文件，命名为`style.css`。

![](img/566d291f94f595e9f09b18439ae429c0_37.png)

然后，在HTML文件的`<head>`部分链接这个CSS文件。

```html
<head>
    <title>XYZ Lead Generation</title>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
```

![](img/566d291f94f595e9f09b18439ae429c0_39.png)

![](img/566d291f94f595e9f09b18439ae429c0_41.png)

现在，在`style.css`文件中，我们为各个部分添加样式。

![](img/566d291f94f595e9f09b18439ae429c0_43.png)

首先设置`header`的样式：
```css
header {
    position: absolute;
    top: 0%;
    left: 0%;
    height: 20%;
    width: 100%;
    background-color: red;
}
```

![](img/566d291f94f595e9f09b18439ae429c0_45.png)

![](img/566d291f94f595e9f09b18439ae429c0_46.png)

保存并刷新浏览器页面，可以看到页头变成了红色。使用服务器的一个好处是，修改代码后只需刷新页面即可看到变化，无需重新运行。

![](img/566d291f94f595e9f09b18439ae429c0_48.png)

接下来设置`body`部分的样式：
```css
#body {
    position: absolute;
    top: 20%;
    left: 0%;
    height: 70%;
    width: 100%;
    background-color: gray;
    color: white; /* 设置文字颜色为白色 */
}
```

![](img/566d291f94f595e9f09b18439ae429c0_50.png)

![](img/566d291f94f595e9f09b18439ae429c0_51.png)

![](img/566d291f94f595e9f09b18439ae429c0_52.png)

然后设置`footer`的样式：
```css
footer {
    position: absolute;
    top: 90%; /* 20% + 70% */
    left: 0%;
    height: 5%;
    width: 100%;
    background-color: black;
    color: white;
    text-align: center;
}
```

![](img/566d291f94f595e9f09b18439ae429c0_54.png)

![](img/566d291f94f595e9f09b18439ae429c0_56.png)

![](img/566d291f94f595e9f09b18439ae429c0_58.png)

现在调整`header`中标题的对齐方式。我们之前为`<h1>`和`<h2>`设置了ID。
```css
#main {
    position: absolute;
    top: 10%;
    left: 5%;
}
#sub {
    position: absolute;
    top: 15%;
    left: 20%;
}
```

![](img/566d291f94f595e9f09b18439ae429c0_60.png)

![](img/566d291f94f595e9f09b18439ae429c0_61.png)

## 第四步：添加主要内容与导航栏
现在，我们在`<div id="body">`中添加一些内容，比如一张图片和一段文字。我们将使用两个并排的`<div>`来布局。

![](img/566d291f94f595e9f09b18439ae429c0_63.png)

修改`index.html`的`body`部分：
```html
<div id="body">
    <div id="part1">
        <br/><br/>
        <img src="your-image.jpg" height="400" width="500"/>
    </div>
    <div id="part2">
        <br/><br/><br/>
        <p>这里是关于线索生成公司的一些介绍文本。可以在这里描述公司的服务、理念等。</p>
    </div>
</div>
```

![](img/566d291f94f595e9f09b18439ae429c0_65.png)

在`style.css`中为这两个部分添加样式，使它们各占一半宽度：
```css
#part1 {
    position: absolute;
    top: 0%;
    left: 0%;
    height: 100%;
    width: 50%;
}
#part2 {
    position: absolute;
    top: 0%;
    left: 50%;
    height: 100%;
    width: 50%;
}
```

![](img/566d291f94f595e9f09b18439ae429c0_67.png)

最后，在`header`中添加一个导航栏。在`<header>`标签内的标题后面添加：
```html
<nav>
    Home | About | Services | Contact
</nav>
```

![](img/566d291f94f595e9f09b18439ae429c0_69.png)

![](img/566d291f94f595e9f09b18439ae429c0_71.png)

![](img/566d291f94f595e9f09b18439ae429c0_73.png)

为导航栏添加CSS样式，将其定位到右上角：
```css
nav {
    position: absolute;
    top: 35%;
    left: 70%;
    color: white;
}
```

![](img/566d291f94f595e9f09b18439ae429c0_75.png)

![](img/566d291f94f595e9f09b18439ae429c0_77.png)

现在，一个简单的带有导航栏、内容区和页脚的公司主页就创建完成了。你可以进一步调整图片大小、文字间距和颜色来完善它。

![](img/566d291f94f595e9f09b18439ae429c0_79.png)

![](img/566d291f94f595e9f09b18439ae429c0_81.png)

![](img/566d291f94f595e9f09b18439ae429c0_83.png)

## 总结
本节课我们一起学习了Apache Tomcat的基本概念，它是一个用于运行Java Web应用的服务器。我们从一个案例研究出发，逐步完成了在Eclipse中创建动态Web项目、配置Tomcat服务器、添加必要JAR文件的过程。然后，我们创建了一个简单的主页，使用HTML构建结构，并用CSS进行样式设计，最终实现了一个包含页头、导航栏、内容区和页脚的公司网站雏形。在接下来的课程中，我们将学习如何为导航栏添加功能链接，并创建“关于我们”、“服务”和“联系我们”等页面。