# 009：创建食谱 🍳

![](img/f487f8aa054dd468cd2d5e52df38c3cc_1.png)

在本节课中，我们将学习如何创建和使用Chef食谱。食谱是Chef配置管理的基本构建块，所有具体的配置指令都包含在食谱中。

上一节我们介绍了食谱的基本概念，本节中我们来看看如何实际创建一个食谱。

## 创建食谱目录

首先，需要创建一个名为`cookbooks`的目录来存放所有食谱。如果你在本地模式下运行食谱，大部分食谱都应存放在此目录下。

![](img/f487f8aa054dd468cd2d5e52df38c3cc_3.png)

```bash
mkdir cookbooks
```

![](img/f487f8aa054dd468cd2d5e52df38c3cc_5.png)

执行上述命令后，可以检查目录是否创建成功。

## 使用Chef生成器

创建食谱最简单的方法是使用Chef开发工具包自带的生成器。Chef命令`chef`随开发工具包一同安装。

![](img/f487f8aa054dd468cd2d5e52df38c3cc_7.png)

![](img/f487f8aa054dd468cd2d5e52df38c3cc_8.png)

要了解生成器的帮助信息，可以运行：

```bash
chef --help
```

这将显示所有可用的命令选项。其中，`generate`关键字用于生成食谱或其组件。

![](img/f487f8aa054dd468cd2d5e52df38c3cc_10.png)

![](img/f487f8aa054dd468cd2d5e52df38c3cc_11.png)

要详细了解`generate`命令的用法，可以运行：

```bash
chef generate --help
```

此命令会列出所有可用的生成器。目前，我们关注的是生成食谱。

![](img/f487f8aa054dd468cd2d5e52df38c3cc_13.png)

![](img/f487f8aa054dd468cd2d5e52df38c3cc_14.png)

![](img/f487f8aa054dd468cd2d5e52df38c3cc_16.png)

要生成一个名为`chef-workstation`的食谱，并指定其路径为`cookbooks`目录，可以运行以下命令：

```bash
chef generate cookbook cookbooks/chef-workstation
```

![](img/f487f8aa054dd468cd2d5e52df38c3cc_18.png)

命令执行成功后，会显示相关信息。默认情况下，Git会用来跟踪食谱中的所有更改。

## 管理食谱文件

![](img/f487f8aa054dd468cd2d5e52df38c3cc_20.png)

![](img/f487f8aa054dd468cd2d5e52df38c3cc_22.png)

食谱生成后，你可能想知道如何执行其中的配方文件。之前我们执行的是独立的配方文件，而现在配方文件位于食谱目录结构中。

假设你有一个名为`first_recipe.rb`的配方文件，需要将其移动到新创建的食谱中。可以使用`mv`命令：

![](img/f487f8aa054dd468cd2d5e52df38c3cc_24.png)

![](img/f487f8aa054dd468cd2d5e52df38c3cc_26.png)

```bash
mv first_recipe.rb cookbooks/chef-workstation/recipes/
```

![](img/f487f8aa054dd468cd2d5e52df38c3cc_28.png)

![](img/f487f8aa054dd468cd2d5e52df38c3cc_30.png)

移动后，可以进入食谱目录查看结构：

```bash
cd cookbooks/chef-workstation
ls -la
```

![](img/f487f8aa054dd468cd2d5e52df38c3cc_32.png)

![](img/f487f8aa054dd468cd2d5e52df38c3cc_34.png)

你会看到由生成器创建的多个目录。进入`recipes`目录，可以看到已存在的`default.rb`文件和我们刚移动进来的`first_recipe.rb`文件。

![](img/f487f8aa054dd468cd2d5e52df38c3cc_36.png)

## 运行食谱中的配方

![](img/f487f8aa054dd468cd2d5e52df38c3cc_38.png)

现在，是时候运行我们的配方了。你可能会尝试使用之前的方法：

```bash
sudo chef-client --local-mode --recipe-path cookbooks/chef-workstation/recipes/first_recipe.rb
```

![](img/f487f8aa054dd468cd2d5e52df38c3cc_40.png)

但是，当配方位于食谱中时，这种方法可能无法正常工作。因为Chef客户端可能会困惑于执行哪个配方，而且食谱中可能包含其他配方（如`default.rb`）。

标准的做法是，通过指定食谱名和配方名来运行。通常，一个食谱的入口点是`default.rb`文件。如果你想运行`first_recipe.rb`，一种方法是在`default.rb`文件中包含它。

![](img/f487f8aa054dd468cd2d5e52df38c3cc_42.png)

本节课中我们一起学习了如何创建Chef食谱目录、使用`chef generate`命令生成食谱结构、将配方文件移动到食谱中，并了解了在食谱上下文中运行配方的正确方法。掌握这些是组织和管理复杂基础设施代码的基础。