# 012：L代表AWS Lambda 🚀

在本节课中，我们将要学习AWS Lambda服务。AWS Lambda是一项无服务器计算服务，它允许您运行几乎任何类型的应用程序或后端代码，而无需预置和管理服务器。

## 什么是AWS Lambda？🤔

上一节我们介绍了AWS Lambda的基本定义，本节中我们来看看它的具体工作方式。

![](img/6976cf488686c0fa7da8fdbcb1869055_1.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_2.png)

AWS Lambda允许您创建所谓的**AWS Lambda函数**。这些函数包含您的代码，并在响应事件或触发器时运行。这意味着Lambda函数不会像托管在Amazon EC2等地方的应用程序那样持续运行代码。相反，您的代码仅在需要运行时才响应事件执行。

![](img/6976cf488686c0fa7da8fdbcb1869055_3.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_4.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_5.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_6.png)

Lambda函数的最长运行时间可达15分钟。您可以将函数的超时时间配置为1秒到15分钟之间的任意值。

![](img/6976cf488686c0fa7da8fdbcb1869055_7.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_8.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_9.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_10.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_11.png)

以下是Lambda函数的一些典型应用场景：
*   文件处理应用
*   Web应用后端
*   事件处理逻辑

## Lambda函数的核心概念 💡

上一节我们了解了Lambda的用途，本节中我们来深入探讨其核心组成部分。

关于Lambda代码，它本质上是您自己的代码，只是以AWS Lambda能够使用的方式打包。这就是为什么我们说您几乎可以在Lambda上运行任何类型的应用程序。

那么，AWS Lambda支持哪些语言呢？支持的语言列表很长，举几个例子：Python、Node.js、Java。如果您想使用AWS Lambda现有运行时环境不支持的语言，您可以提供自己的运行时环境。因此，在创建函数时，您在选择使用何种语言方面确实具有灵活性。

![](img/6976cf488686c0fa7da8fdbcb1869055_13.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_14.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_15.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_16.png)

您的代码必须包含一个称为**处理程序**的部分，这本质上是您的代码在被调用后开始运行的入口点。

![](img/6976cf488686c0fa7da8fdbcb1869055_17.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_18.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_19.png)

当您创建Lambda函数时，还需要选择一个**触发器**。这决定了事件具体将从何而来。

以下是触发器的一些例子：
*   通过Amazon API Gateway发出的API调用
*   在Amazon S3中发生的事件
*   通过Amazon EventBridge过滤的事件
*   通过Amazon Kinesis流式传输的数据

## Lambda的执行与扩展 ⚙️

上一节我们介绍了如何触发函数，本节中我们来看看函数被调用后发生了什么。

一旦您的Lambda函数被调用，AWS Lambda服务将启动您的代码，并在一个称为**执行环境**的单元中运行它。执行环境为您的Lambda函数提供了一个安全且隔离的运行时环境。在合理的情况下，执行环境会被重复使用，但管理执行环境生命周期的整个过程由AWS服务自身管理，而非由您管理。

![](img/6976cf488686c0fa7da8fdbcb1869055_21.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_22.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_23.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_24.png)

您的Lambda函数的扩展由AWS处理，这是一件好事，因为它减轻了您的一部分运维负担。但您无法通过配置来控制Lambda函数扩展的某些方面。

![](img/6976cf488686c0fa7da8fdbcb1869055_25.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_26.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_27.png)

![](img/6976cf488686c0fa7da8fdbcb1869055_28.png)

## 总结 📝

![](img/6976cf488686c0fa7da8fdbcb1869055_30.png)

本节课中我们一起学习了AWS Lambda的基础知识。我们了解到，AWS Lambda是一项无服务器计算服务，它通过响应事件来运行您的代码，无需管理服务器。我们探讨了Lambda函数、支持的编程语言、处理程序、触发器以及执行环境等核心概念。这只是开始了解AWS Lambda所需信息的很小一部分，因此强烈建议您查阅相关文档以获取更深入的知识。