# 019：Clang Tooling接口概览 🛠️

![](img/4fb2a053d98aee2561a3567417a3601e_1.png)

![](img/4fb2a053d98aee2561a3567417a3601e_2.png)

![](img/4fb2a053d98aee2561a3567417a3601e_3.png)

在本节课中，我们将要学习如何将Clang作为库来构建工具。我们将介绍Clang官方文档中描述的三种主要接口：LibClang、LibTooling和Clang插件，并重点了解它们各自的特点和适用场景。

## 概述

![](img/4fb2a053d98aee2561a3567417a3601e_5.png)

上一节我们介绍了抽象语法树（AST）以及Clang如何使用它来表示代码。本节中，我们来看看利用Clang库构建工具的不同方式。

根据Clang官方文档（版本12），有三种主要方式来编写我们的工具：
1.  LibClang
2.  LibTooling
3.  Clang插件

![](img/4fb2a053d98aee2561a3567417a3601e_7.png)

本课程的重点是Clang插件，但也会对其他两种接口进行简要概述。本次概述的目的不是让你能够使用每一种接口编写工具，而是让你了解在何种情况下应该选择使用哪一种。

![](img/4fb2a053d98aee2561a3567417a3601e_9.png)

## LibClang接口

首先介绍LibClang。文档将其描述为“一个稳定的、高层次的C接口”。

![](img/4fb2a053d98aee2561a3567417a3601e_11.png)

这意味着，你使用该库旧版本编写的程序，可以预期在未来的版本中继续工作。这与某些其他接口形成对比，那些接口的版本间变更可能非常剧烈，迫使你重写工具才能使其继续运行。

![](img/4fb2a053d98aee2561a3567417a3601e_13.png)

LibClang允许你使用所谓的“游标”来遍历AST。根据文档，游标允许你在无需了解Clang AST细节的情况下遍历AST，但缺点是，你无法获得对AST的完全控制。

![](img/4fb2a053d98aee2561a3567417a3601e_14.png)

以下是LibClang接口的核心概念：
*   **稳定性**：API保持向后兼容。
*   **抽象层**：使用`CXCursor`等高级对象，而非直接操作Clang的C++ AST节点。

LibClang不会暴露AST中保存的所有信息。其背后的理由很简单，如果我们记得LibClang的目标是“稳定”的话：它在API中暴露的AST细节越少，受到Clang内部变更影响的可能性就越小。因此，如果你需要更深入地使用AST，可能需要考虑Clang提供的其他接口。

## LibTooling接口

![](img/4fb2a053d98aee2561a3567417a3601e_16.png)

接下来我们介绍LibTooling。LibTooling的API允许你完全控制AST。

![](img/4fb2a053d98aee2561a3567417a3601e_18.png)

LibTooling在代码上运行前端操作。前端操作充当我们工具的入口点，也就是说，你在这里编写希望Clang在编译期间为你运行的代码。

![](img/4fb2a053d98aee2561a3567417a3601e_20.png)

它们被实现为一个抽象类`FrontendAction`，其他类会实现它，例如`ASTFrontendAction`。这个类允许我们编写依赖于遍历Clang AST的操作。

![](img/4fb2a053d98aee2561a3567417a3601e_22.png)

当我们深入研究Clang插件时，会看到它使用了一种称为`PluginASTAction`的操作，而它实际上实现了`ASTFrontendAction`。

![](img/4fb2a053d98aee2561a3567417a3601e_24.png)

需要说明的是，LibTooling不像LibClang那样稳定。

![](img/4fb2a053d98aee2561a3567417a3601e_26.png)

## Clang插件接口

![](img/4fb2a053d98aee2561a3567417a3601e_27.png)

![](img/4fb2a053d98aee2561a3567417a3601e_29.png)

最后，我们来到本课程的主题：Clang插件。

如前所述，Clang插件在代码上运行`PluginASTAction`。这意味着Clang插件实际上是你可以使用LibTooling构建的一种工具。

![](img/4fb2a053d98aee2561a3567417a3601e_31.png)

![](img/4fb2a053d98aee2561a3567417a3601e_32.png)

我们也已经看到，`PluginASTAction`实现了另一个名为`ASTFrontendAction`的类，而`ASTFrontendAction`又实现了`FrontendAction`。

![](img/4fb2a053d98aee2561a3567417a3601e_34.png)

在下一节视频中，我们将最终实现我们的第一个Clang插件，届时会确切地看到如何实现这些类。

现在，重要的是要知道，当我们编写Clang插件时，我们编写的是在代码上运行`PluginASTAction`的程序。这意味着Clang将读取你的代码，将其解析成AST，然后将这个AST交给你的`PluginASTAction`类。

Clang插件作为LibTooling的一部分，也让你能够完全控制Clang AST。

![](img/4fb2a053d98aee2561a3567417a3601e_36.png)

![](img/4fb2a053d98aee2561a3567417a3601e_37.png)

Clang中还有另一个巧妙的功能，即注册你的插件。不必担心细节，这将在下一节视频中详细讲解。基本上，这个功能允许你将插件编译成一个共享库，然后只需在命令行传递一个参数，就能让Clang运行它。

![](img/4fb2a053d98aee2561a3567417a3601e_39.png)

例如，像这样的命令：
```bash
clang -fsyntax-only -Xclang -load -Xclang path/to/your/plugin.so -Xclang -plugin -Xclang your-plugin-name example.c
```
我们将在下一节视频中详细讲解这行命令的每一部分，但基本上，这行命令调用clang，并要求它运行你注册为`your-plugin-name`的插件，对文件`example.c`中的程序进行分析。那个`plugin.so`文件就是你的插件被编译后生成的所谓共享库。

![](img/4fb2a053d98aee2561a3567417a3601e_41.png)

最后，Clang插件也不像LibClang那样稳定。

![](img/4fb2a053d98aee2561a3567417a3601e_43.png)

## 总结与下节预告

![](img/4fb2a053d98aee2561a3567417a3601e_45.png)

本节课中我们一起学习了如果想编写Clang工具，我们有哪些选项以及它们各自的优缺点。

![](img/4fb2a053d98aee2561a3567417a3601e_47.png)

总结一下今天的内容：
*   **LibClang**是一个非常稳定的库，允许你构建一些不依赖于获取AST每个细节的有趣工具。
*   **LibTooling**虽然不像LibClang那样稳定，但允许你访问Clang在解析代码时保存在AST中的几乎所有内容。
*   **Clang插件**是使用LibTooling的一种方式，并具有一些特定功能，例如注册机制，我们将在接下来的视频中更详细地了解。

![](img/4fb2a053d98aee2561a3567417a3601e_49.png)

在下一节课中，我们将终于开始构建我们的第一个Clang插件。在深入更有趣的内容之前，我将介绍让我们的插件启动和运行所需的最基本要素。在那之前，你可以通过电子邮件向我提出任何问题、建议或评论。

![](img/4fb2a053d98aee2561a3567417a3601e_51.png)

谢谢。