# 003：创建联系页面 📇

![](img/0dd5eacc140f31e51343507e3846ad8a_1.png)

在本节课中，我们将学习如何创建一个联系页面。这个页面将包含一个简单的表单，允许用户输入姓名、邮箱和留言信息，并最终将这些信息通过邮件发送出去。

![](img/0dd5eacc140f31e51343507e3846ad8a_3.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_5.png)

---

上一节我们完成了服务页面的创建，本节中我们来看看如何构建联系页面。

![](img/0dd5eacc140f31e51343507e3846ad8a_7.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_8.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_9.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_11.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_13.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_15.png)

首先，我们需要创建一个新的GSP文件。在项目中右键点击，选择新建一个GSP文件，并将其命名为 `contact.gsp`。

![](img/0dd5eacc140f31e51343507e3846ad8a_17.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_19.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_20.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_22.png)

接下来，我们将复制之前服务页面的头部结构到联系页面，并移除其中的主要内容区域。

为了保持页面样式一致，我们需要在头部添加CSS样式表的链接。代码如下：

![](img/0dd5eacc140f31e51343507e3846ad8a_24.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_25.png)

```html
<link rel="stylesheet" type="text/css" href="mystyle.css">
```

现在，我们开始构建联系页面的主体内容。页面将被分为左右两个部分。

![](img/0dd5eacc140f31e51343507e3846ad8a_27.png)

以下是页面布局的规划：
*   左侧区域将放置一张图片。
*   右侧区域将放置一个联系表单。

![](img/0dd5eacc140f31e51343507e3846ad8a_28.png)

为了实现左右分栏布局，我们将使用两个 `<div>` 元素，并为其应用CSS样式。

![](img/0dd5eacc140f31e51343507e3846ad8a_30.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_31.png)

首先，创建第一个 `<div>`，其ID为 `content1`。在这个 `<div>` 中，我们将添加一张图片。

![](img/0dd5eacc140f31e51343507e3846ad8a_33.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_35.png)

```html
<div id="content1">
    <img src="contact.jpg" alt="Contact Image">
</div>
```

![](img/0dd5eacc140f31e51343507e3846ad8a_37.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_39.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_41.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_43.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_45.png)

为了控制图片的显示，我们可以设置其高度和宽度，并将其居中对齐。

![](img/0dd5eacc140f31e51343507e3846ad8a_47.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_49.png)

接着，创建第二个 `<div>`，其ID为 `content2`。这个区域将用来放置表单。

![](img/0dd5eacc140f31e51343507e3846ad8a_51.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_53.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_55.png)

为了使这两个 `<div>` 各占屏幕宽度的一半，我们使用内联CSS样式进行定位。

```html
<style>
    #content1 {
        position: absolute;
        top: 0%;
        left: 0%;
        height: 100%;
        width: 50%;
    }
    #content2 {
        position: absolute;
        top: 0%;
        left: 50%;
        height: 100%;
        width: 50%;
    }
</style>
```

![](img/0dd5eacc140f31e51343507e3846ad8a_57.png)

现在，我们在 `content2` 这个 `<div>` 中创建表单。为了使表单元素对齐整齐，我们将表单放在一个HTML表格 `<table>` 中。

![](img/0dd5eacc140f31e51343507e3846ad8a_59.png)

表单将包含以下字段：
*   全名（文本输入框）
*   邮箱地址（邮箱输入框）
*   留言（文本域）
*   提交按钮

![](img/0dd5eacc140f31e51343507e3846ad8a_61.png)

以下是表单的基本HTML结构：

![](img/0dd5eacc140f31e51343507e3846ad8a_63.png)

```html
<form method="post" action="">
    <table>
        <tr>
            <td>Full Name</td>
            <td><input type="text" name="fname"></td>
        </tr>
        <tr>
            <td>Email</td>
            <td><input type="email" name="mail"></td>
        </tr>
        <tr>
            <td>Your Message</td>
            <td><textarea name="msg" rows="5" cols="28"></textarea></td>
        </tr>
        <tr>
            <td colspan="2" align="center"><input type="submit" value="Contact Us"></td>
        </tr>
    </table>
</form>
```

我们可以通过CSS进一步调整表单的边距、字体大小和输入框尺寸，使其看起来更美观。

![](img/0dd5eacc140f31e51343507e3846ad8a_65.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_66.png)

---

![](img/0dd5eacc140f31e51343507e3846ad8a_68.png)

![](img/0dd5eacc140f31e51343507e3846ad8a_69.png)

现在，联系页面的前端部分已经完成。接下来，我们需要将网站的所有页面链接起来，实现导航功能。

我们将之前创建好的导航菜单代码复制到联系页面、首页、关于页面和服务页面的相同位置。这样，每个页面顶部都会有一个统一的导航栏，包含“首页”、“关于”、“服务”和“联系”的链接。

导航菜单的链接代码如下：

```html
<nav>
    <a href="index.gsp">Home</a>
    <a href="about.gsp">About</a>
    <a href="services.gsp">Services</a>
    <a href="contact.gsp">Contact</a>
</nav>
```

同时，我们为这些链接添加一些基础CSS样式，例如设置颜色和激活状态。

完成链接后，网站的所有页面（首页、关于、服务、联系）都可以通过顶部的导航栏相互访问了。

---

页面链接完成后，下一步是实现表单的后台功能。当用户点击“提交”按钮时，我们需要将表单数据发送到服务器，并通过邮件API将信息发送出去。

首先，我们需要修改表单的 `action` 属性，使其指向一个处理邮件发送的Servlet。我们创建一个名为 `EmailSend` 的Servlet类。

在 `EmailSend` Servlet中，我们将主要处理 `doPost` 方法（因为表单方法为`post`）。在该方法中，我们需要执行以下步骤：

1.  获取表单提交的数据（姓名、邮箱、留言）。
2.  配置JavaMail API所需的属性（如邮件服务器主机、端口、认证方式）。
3.  创建邮件会话（Session），并进行身份验证。
4.  构建邮件消息（Message），设置发件人、收件人、主题和正文。
5.  使用Transport类发送邮件。

为了使用JavaMail API，我们需要在项目中导入两个必要的JAR文件：`activation.jar` 和 `mail.jar`。

以下是Servlet中处理邮件发送的核心代码逻辑概述：

```java
// 1. 获取请求参数
String name = request.getParameter("fname");
String email = request.getParameter("mail");
String message = request.getParameter("msg");

// 2. 设置邮件属性
Properties props = new Properties();
props.put("mail.smtp.host", "smtp.gmail.com");
props.put("mail.smtp.socketFactory.port", "465");
props.put("mail.smtp.socketFactory.class", "javax.net.ssl.SSLSocketFactory");
props.put("mail.smtp.auth", "true");
props.put("mail.smtp.port", "465");

// 3. 创建会话并进行认证
Session session = Session.getDefaultInstance(props,
    new javax.mail.Authenticator() {
        protected PasswordAuthentication getPasswordAuthentication() {
            return new PasswordAuthentication("your-email@gmail.com", "your-password");
        }
    });

// 4. 构建并发送邮件
try {
    MimeMessage msg = new MimeMessage(session);
    msg.addRecipient(Message.RecipientType.TO, new InternetAddress("receiver@example.com"));
    msg.setSubject("New Contact Form Submission");
    msg.setText("Name: " + name + "\nEmail: " + email + "\nMessage: " + message);
    Transport.send(msg);
    System.out.println("Message sent successfully.");
} catch (MessagingException e) {
    throw new RuntimeException(e);
}
```

最后，在邮件发送成功后，我们可以使用 `response.sendRedirect()` 将用户重定向到一个成功页面。

---

本节课中我们一起学习了如何创建一个完整的联系页面。我们从创建GSP文件开始，设计了左右分栏的页面布局，并在右侧集成了一个功能完整的联系表单。随后，我们为网站添加了统一的导航链接。最后，我们深入后端，创建了一个Servlet来处理表单提交，并利用JavaMail API实现了邮件发送功能，从而完成了从用户界面到后台数据处理的全流程。

![](img/0dd5eacc140f31e51343507e3846ad8a_71.png)

通过本节的学习，你已经掌握了在Tomcat网站项目中构建交互式页面的基本方法。