# 015：O代表AWS Organizations 🏢

在本节课中，我们将要学习AWS Organizations服务。当大规模使用AWS时，将所有资源托管在单个账户中并非最佳实践。AWS Organizations是一项账户管理服务，它能帮助你将多个AWS账户整合到一个组织中，并进行集中管理。我们将了解其核心概念、主要功能以及如何简化多账户环境下的计费、权限和标签管理。

## 组织结构与核心概念

上一节我们介绍了多账户管理的必要性，本节中我们来看看AWS Organizations如何构建这种管理结构。

![](img/a755744fff96cea49cb7c01dcdeda99d_1.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_2.png)

AWS Organizations允许你创建一个由AWS账户组成的层次结构。这个结构以一个**管理账户**（也称为组织根账户）为起点。在该根账户之下，你可以创建**组织单元**（Organizational Units, OUs），这些OU可以包含成员AWS账户或其他嵌套的OU。

![](img/a755744fff96cea49cb7c01dcdeda99d_3.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_4.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_5.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_6.png)

这种层级结构可以用一个简单的树状图表示：
```
管理账户 (根)
├── 组织单元A (OU A)
│   ├── 成员账户 1
│   └── 成员账户 2
└── 组织单元B (OU B)
    └── 成员账户 3
```

![](img/a755744fff96cea49cb7c01dcdeda99d_7.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_8.png)

## 使用服务控制策略管理权限

了解了组织结构后，我们来看看如何利用它来集中管理权限。这是通过服务控制策略实现的。

服务控制策略（Service Control Policies, SCPs）是附加到组织根、OU或单个账户上的策略。它们的主要作用是定义**成员账户内身份（用户或角色）可用的最大权限**。SCP本身并不授予权限，而是作为“护栏”来限制权限范围。

例如，你可以创建一个SCP，明确拒绝所有账户访问某个特定的AWS服务（如Amazon S3）。即使某个成员账户内的IAM策略允许S3访问，该SCP也会覆盖它，最终拒绝访问。SCP的评估逻辑遵循“显式拒绝优先”的原则。

![](img/a755744fff96cea49cb7c01dcdeda99d_10.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_11.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_12.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_13.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_14.png)

以下是SCP的一个JSON策略示例，它允许除S3外的所有操作：
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": "s3:*",
      "Resource": "*"
    }
  ]
}
```

![](img/a755744fff96cea49cb7c01dcdeda99d_15.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_16.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_17.png)

## 集中计费功能

除了权限管理，AWS Organizations另一个主要优势是提供集中计费功能。

你可以使用管理账户来整合并支付所有成员账户的费用，无需为每个账户单独处理账单。管理账户能够访问其组织内所有成员账户的账单信息、账户活动和账户详情。这大大简化了财务管理和成本追踪。

## 标签策略标准化

![](img/a755744fff96cea49cb7c01dcdeda99d_19.png)

AWS Organizations还简化了另一项常见的治理任务：标签标准化。

![](img/a755744fff96cea49cb7c01dcdeda99d_20.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_21.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_22.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_23.png)

你可以使用**标签策略**（Tag Policies）来确保跨所有AWS账户的标签保持一致。这包括为标签键和标签值定义首选的命名规范（如大小写）。例如，你可以通过策略强制要求所有“Environment”标签的值必须为“Prod”、“Dev”或“Test”，从而避免拼写不一致。

## 总结与费用说明

本节课中我们一起学习了AWS Organizations服务。我们了解到，它是一个免费的服务，能帮助你将多个AWS账户整合到一个组织中进行集中管理。其核心功能包括：通过组织单元构建账户层级、使用服务控制策略集中管控权限边界、实现所有成员账户的集中计费，以及利用标签策略标准化资源标签。

![](img/a755744fff96cea49cb7c01dcdeda99d_25.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_26.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_27.png)

![](img/a755744fff96cea49cb7c01dcdeda99d_28.png)

最重要的是，使用AWS Organizations本身是免费的，它为你管理复杂的多账户环境提供了强大而经济的基础设施。