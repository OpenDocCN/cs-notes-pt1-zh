# 002：卡片视图设计 🃏

![](img/62e280e317210264b004e62c80692128_0.png)

在本节课中，我们将学习如何为滑动匹配应用设计核心的卡片视图。我们将从设置应用的入口点开始，逐步创建卡片容器、卡片视图以及相关的协议和数据源，最终构建出可滑动的用户资料卡片界面。

![](img/62e280e317210264b004e62c80692128_2.png)

---

![](img/62e280e317210264b004e62c80692128_4.png)

## 设置应用入口点

![](img/62e280e317210264b004e62c80692128_6.png)

上一节我们完成了项目的基础搭建，本节我们首先来解决运行应用时出现的黑屏警告。这个警告是因为没有设置应用的初始视图控制器。

![](img/62e280e317210264b004e62c80692128_8.png)

![](img/62e280e317210264b004e62c80692128_0.png)

![](img/62e280e317210264b004e62c80692128_9.png)

![](img/62e280e317210264b004e62c80692128_10.png)

警告信息是“未能实例化默认视图控制器”。这意味着故事板的入口点没有设置。

![](img/62e280e317210264b004e62c80692128_12.png)

![](img/62e280e317210264b004e62c80692128_13.png)

![](img/62e280e317210264b004e62c80692128_2.png)

![](img/62e280e317210264b004e62c80692128_15.png)

可以看到，故事板中没有入口点箭头。这就是运行时显示黑屏的原因。

![](img/62e280e317210264b004e62c80692128_17.png)

以下是设置入口点的步骤：
1.  在项目导航器中，选择 `Main.storyboard` 文件。
2.  在画布上，选中你想要作为应用启动时第一个显示的视图控制器。
3.  在右侧的“属性检查器”中，勾选 **“Is Initial View Controller”** 选项。

![](img/62e280e317210264b004e62c80692128_19.png)

![](img/62e280e317210264b004e62c80692128_21.png)

![](img/62e280e317210264b004e62c80692128_4.png)

勾选后，你会在该视图控制器的左侧看到一个箭头。这个箭头就代表应用的入口点。

![](img/62e280e317210264b004e62c80692128_23.png)

![](img/62e280e317210264b004e62c80692128_24.png)

![](img/62e280e317210264b004e62c80692128_6.png)

![](img/62e280e317210264b004e62c80692128_26.png)

设置完成后，再次运行应用，屏幕将变为白色。

![](img/62e280e317210264b004e62c80692128_8.png)

## 自定义标签栏

现在屏幕是白色的，因为标签栏的两个标签项（Tab Bar Item 1 和 2）背景都是白色。我们来自定义一下它们的颜色。

![](img/62e280e317210264b004e62c80692128_12.png)

以下是自定义标签栏的步骤：
1.  在故事板中，选中第一个标签栏项目（Tab Bar Item 1）。
2.  在“属性检查器”中，找到 **“Image Tint”** 属性。
3.  将颜色更改为你喜欢的颜色，例如粉色。

![](img/62e280e317210264b004e62c80692128_28.png)

![](img/62e280e317210264b004e62c80692128_19.png)

![](img/62e280e317210264b004e62c80692128_30.png)

运行应用，你将看到标签栏图标的颜色变成了粉色。

![](img/62e280e317210264b004e62c80692128_23.png)

## 连接视图控制器与类

现在，我们来为第一个标签页的视图控制器添加功能。这个视图控制器将显示可滑动的用户资料卡片。

![](img/62e280e317210264b004e62c80692128_26.png)

我们需要将这个视图控制器与一个Swift类关联起来。

以下是关联的步骤：
1.  在故事板中，选中第一个标签页的视图控制器。
2.  在右侧的“身份检查器”中，找到 **“Class”** 字段。
3.  输入 `ViewController` 并按回车键。

这样，`ViewController` 类中的代码就可以控制这个界面了。我们的目标是创建5到6个用户资料卡片，每张卡片包含用户照片、姓名、职业和一个信息按钮。用户可以通过向左或向右滑动来浏览这些卡片。当所有卡片都被滑过后，我们将显示一条提示信息。在顶部，我们还会显示浏览每个用户的日期和时间。

## 创建卡片容器视图

接下来，我们开始设计卡片视图。首先创建一个卡片容器视图，它将承载所有可滑动的卡片。

![](img/62e280e317210264b004e62c80692128_30.png)

以下是创建卡片容器类的步骤：
1.  点击 **File -> New -> File...**。
2.  选择 **Swift File**，点击 **Next**。
3.  将文件命名为 `SwipeableCardContainerView`，点击 **Create**。

在新创建的Swift文件中，定义一个类：

```swift
import UIKit

![](img/62e280e317210264b004e62c80692128_32.png)

class SwipeableCardContainerView: UIView {
    override func awakeFromNib() {
        super.awakeFromNib()
        // 初始化代码将写在这里
    }
}
```

`awakeFromNib()` 方法会在视图从故事板或XIB文件加载完成后被调用，适合进行一些初始化设置。

## 定义卡片交互协议

为了让卡片能够响应用户的滑动和点击操作，我们需要定义一些协议（Protocol）。协议就像一份合同，规定了需要实现哪些功能。

![](img/62e280e317210264b004e62c80692128_34.png)

首先，创建一个处理滑动事件的协议。

![](img/62e280e317210264b004e62c80692128_32.png)

以下是创建滑动代理协议的步骤：
1.  新建一个Swift文件，命名为 `SwipeableViewDelegate`。
2.  在文件中定义协议：

```swift
import Foundation

protocol SwipeableViewDelegate: class {
    func didBeginSwipe(onView view: UIView)
    func didEndSwipe(onView view: UIView)
}
```
这个协议声明了两个方法：`didBeginSwipe`（开始滑动时调用）和 `didEndSwipe`（结束滑动时调用）。方法的签名（参数和返回值）可能会在后续开发中调整。

为了让 `SwipeableCardContainerView` 遵循这个协议，我们使用扩展（Extension）来组织代码：

```swift
extension SwipeableCardContainerView: SwipeableViewDelegate {
    func didBeginSwipe(onView view: UIView) {
        // 处理开始滑动的逻辑
    }
    
    func didEndSwipe(onView view: UIView) {
        // 处理结束滑动的逻辑
    }
}
```

接着，创建一个处理卡片选中事件的协议。

![](img/62e280e317210264b004e62c80692128_34.png)

以下是创建卡片选中代理协议的步骤：
1.  新建一个Swift文件，命名为 `SwipeableCardViewDelegate`。
2.  在文件中定义协议：

```swift
import Foundation

![](img/62e280e317210264b004e62c80692128_36.png)

protocol SwipeableCardViewDelegate: class {
    func didSelect(card: SwipeableCardViewCard)
}
```
`didSelect` 方法将在用户点击卡片时被调用。

![](img/62e280e317210264b004e62c80692128_38.png)

## 定义卡片数据源协议

![](img/62e280e317210264b004e62c80692128_40.png)

数据源协议负责向卡片容器提供数据，例如总共有多少张卡片、每张卡片的内容是什么。

![](img/62e280e317210264b004e62c80692128_42.png)

![](img/62e280e317210264b004e62c80692128_36.png)

以下是创建数据源协议的步骤：
1.  新建一个Swift文件，命名为 `SwipeableCardViewDataSource`。
2.  在文件中定义协议：

```swift
import UIKit

protocol SwipeableCardViewDataSource: class {
    // 返回卡片的总数量
    func numberOfCards() -> Int
    
    // 返回指定索引位置的卡片视图
    func card(forItemAt index: Int) -> SwipeableCardViewCard
    
    // 当没有卡片可显示时返回的视图
    func viewForEmptyCard() -> UIView
}
```
*   `numberOfCards`: 返回应用中可用的卡片总数。注意，同一时间通常只显示最上面的一张卡片。
*   `card(forItemAt:)`: 根据索引返回对应的卡片视图对象。
*   `viewForEmptyCard`: 当用户滑完了所有卡片后，显示一个空状态视图。

## 创建卡片视图

现在，我们来创建代表单个用户资料的卡片视图本身。

![](img/62e280e317210264b004e62c80692128_44.png)

![](img/62e280e317210264b004e62c80692128_38.png)

首先，创建一个关联的XIB文件来设计卡片界面。

![](img/62e280e317210264b004e62c80692128_46.png)

以下是创建卡片XIB文件的步骤：
1.  点击 **File -> New -> File...**。
2.  选择 **Empty** 文件模板，点击 **Next**。
3.  将文件命名为 `SwipeableCardViewCard.xib`，点击 **Create**。

在XIB文件中进行界面设计：
1.  从对象库中拖拽一个 **UIView** 到画布上。
2.  在“属性检查器”中，将 **Size** 设置为 **Freeform**。
3.  将视图的宽度设置为335，高度设置为400。
4.  为视图添加自动布局约束，确保它在不同屏幕尺寸上能正确适配。
5.  可以修改视图的 **Background** 颜色以便预览。

![](img/62e280e317210264b004e62c80692128_48.png)

![](img/62e280e317210264b004e62c80692128_48.png)

接下来，创建对应的Swift类来管理这个XIB。

![](img/62e280e317210264b004e62c80692128_40.png)

以下是创建卡片视图类的步骤：
1.  新建一个Swift文件，命名为 `SwipeableCardViewCard`。
2.  定义一个继承自 `UIView` 的类，并实现必要的初始化方法：

![](img/62e280e317210264b004e62c80692128_50.png)

```swift
import UIKit

class SwipeableCardViewCard: UIView {
    // 从故事板或XIB加载时使用的初始化器
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
    }
    
    // 用代码创建视图时使用的初始化器
    override init(frame: CGRect) {
        super.init(frame: frame)
    }
}
```
3.  回到 `SwipeableCardViewCard.xib` 文件。
4.  选中画布上的根视图（UIView）。
5.  在“身份检查器”中，将 **Class** 设置为 `SwipeableCardViewCard`。

![](img/62e280e317210264b004e62c80692128_52.png)

这样，XIB文件就和Swift类关联起来了。

## 创建可滑动的卡片视图基类

最后，我们创建一个可滑动视图的基类，它将处理滑动手势和动画。

![](img/62e280e317210264b004e62c80692128_52.png)

以下是创建可滑动视图基类的步骤：
1.  新建一个Swift文件，命名为 `SwipeableView`。
2.  定义类并添加一个代理属性：

![](img/62e280e317210264b004e62c80692128_54.png)

```swift
import UIKit

class SwipeableView: UIView {
    // 滑动代理，用于通知滑动开始和结束事件
    weak var delegate: SwipeableViewDelegate?
    
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
    }
    
    override init(frame: CGRect) {
        super.init(frame: frame)
    }
}
```
**`required init?(coder:)` 和 `override init(frame:)` 的区别：**
*   `required` 关键字表示，任何继承自 `SwipeableView` 的子类都必须实现这个初始化器。这通常用于支持从故事板或XIB文件加载视图。
*   `override` 关键字表示，这个初始化器重写了父类 `UIView` 的指定初始化器，用于通过代码创建视图时设置其框架（frame）。

在这个类中，我们声明了一个 `delegate` 属性，类型是我们之前定义的 `SwipeableViewDelegate`。后续我们将在这里添加手势识别器（`UIPanGestureRecognizer`）来处理用户的拖拽滑动操作，并管理卡片的动画效果。

---

![](img/62e280e317210264b004e62c80692128_56.png)

本节课中我们一起学习了如何为滑动匹配应用构建卡片视图系统。我们从设置应用入口点开始，自定义了标签栏，然后创建了卡片容器视图、定义了处理交互的代理协议和数据源协议，最后设计了卡片视图的界面和基类。下一节，我们将实现卡片的滑动逻辑和动画效果。