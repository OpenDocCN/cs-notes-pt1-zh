# 009：使用Core Data与Auto Layout

![](img/3534da3827b84c60b9d59b8320f2abfe_1.png)

在本节课中，我们将学习如何结合使用Core Data进行数据持久化，以及如何利用Auto Layout创建动态适配不同屏幕尺寸的用户界面。我们将创建一个简单的用户信息保存与展示应用。

![](img/3534da3827b84c60b9d59b8320f2abfe_3.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_5.png)

## 概述

我们将分两部分进行：首先，实现一个将用户输入（姓名和密码）保存到Core Data的功能；其次，创建一个能根据设备屏幕尺寸动态调整布局的标签列表视图。

---

![](img/3534da3827b84c60b9d59b8320f2abfe_7.png)

## 第一部分：使用Core Data保存数据

![](img/3534da3827b84c60b9d59b8320f2abfe_9.png)

上一节我们介绍了项目的基础结构，本节中我们来看看如何将用户界面中的数据保存到Core Data持久化存储中。

![](img/3534da3827b84c60b9d59b8320f2abfe_11.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_13.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_15.png)

### 1. 获取上下文与创建实体

![](img/3534da3827b84c60b9d59b8320f2abfe_17.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_19.png)

首先，我们需要获取Core Data的托管对象上下文，它是我们与数据库交互的接口。

![](img/3534da3827b84c60b9d59b8320f2abfe_21.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_23.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_25.png)

```swift
let appDelegate = UIApplication.shared.delegate as! AppDelegate
let context = appDelegate.persistentContainer.viewContext
```

![](img/3534da3827b84c60b9d59b8320f2abfe_27.png)

获取上下文后，我们需要创建一个新的实体对象来存储数据。假设我们的实体名为“User”。

```swift
let newUser = NSEntityDescription.insertNewObject(forEntityName: “User”, into: context)
```

### 2. 为实体设置属性值

创建实体后，我们需要为其属性赋值。这里我们有两个文本框：`nameTextField` 和 `passwordTextField`。

以下是设置属性值的代码：

```swift
newUser.setValue(nameTextField.text, forKey: “name”)
newUser.setValue(passwordTextField.text, forKey: “password”)
```

### 3. 保存上下文并重置界面

数据赋值完成后，需要调用上下文的保存方法将数据写入持久化存储。保存成功后，清空文本框以便下次输入。

```swift
do {
    try context.save()
    print(“数据保存成功”)
    // 清空输入框
    nameTextField.text = “”
    passwordTextField.text = “”
} catch {
    print(“保存数据时出错: \(error)”)
}
```

至此，保存功能已经实现。你可以通过设置断点来调试`context.save()`方法，确认数据是否成功保存。

---

## 第二部分：使用Auto Layout实现动态布局

上一部分我们完成了数据的保存，本节中我们来看看如何构建一个能自动适应不同iPhone屏幕尺寸的用户界面。

![](img/3534da3827b84c60b9d59b8320f2abfe_29.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_31.png)

### 1. 创建界面元素

我们在故事板中添加五个标签（Label），用于后续展示数据。为了统一管理样式，我们将它们设置为相同的背景色、对齐方式和初始高度。

![](img/3534da3827b84c60b9d59b8320f2abfe_33.png)

### 2. 设置基础约束

![](img/3534da3827b84c60b9d59b8320f2abfe_35.png)

以下是设置五个标签布局约束的核心步骤：

首先，为第一个标签（Label1）设置约束：
*   垂直间距：贴紧容器顶部。
*   水平间距：对齐容器左右边距。
*   固定高度：例如50点。

![](img/3534da3827b84c60b9d59b8320f2abfe_37.png)

然后，为后续的标签（Label2到Label5）设置约束。关键在于将它们与上一个标签建立联系，实现连锁反应。

![](img/3534da3827b84c60b9d59b8320f2abfe_38.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_40.png)

以下是设置Label2约束的逻辑：
*   垂直间距：位于Label1下方。
*   水平对齐：其左侧（Leading）与Label1的左侧对齐，右侧（Trailing）与Label1的右侧对齐。
*   等尺寸：其高度（Height）和宽度（Width）与Label1相等。

对Label3、Label4、Label5重复此过程，每个标签都基于上一个标签进行约束。这样，只要修改Label1的尺寸，所有标签的尺寸都会同步改变。

![](img/3534da3827b84c60b9d59b8320f2abfe_42.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_44.png)

### 3. 创建出口并编写动态调整代码

![](img/3534da3827b84c60b9d59b8320f2abfe_46.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_48.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_50.png)

为了能通过代码动态调整标签高度，我们需要为Label1的高度约束创建一个出口（IBOutlet）。

![](img/3534da3827b84c60b9d59b8320f2abfe_52.png)

```swift
@IBOutlet weak var commonLabelHeightConstraint: NSLayoutConstraint!
```

![](img/3534da3827b84c60b9d59b8320f2abfe_54.png)

然后，我们创建一个函数（例如 `adjustLayoutForScreenSize`），根据不同的屏幕尺寸来更新这个公共高度约束的值。

![](img/3534da3827b84c60b9d59b8320f2abfe_56.png)

```swift
func adjustLayoutForScreenSize() {
    let screenWidth = UIScreen.main.bounds.width
    let screenHeight = UIScreen.main.bounds.height

    if screenWidth == 320.0 && screenHeight == 568.0 {
        // iPhone 5/5S/SE 尺寸
        commonLabelHeightConstraint.constant = 70
    } else if screenWidth == 375.0 && screenHeight == 667.0 {
        // iPhone 6/6S/7/8 尺寸
        commonLabelHeightConstraint.constant = 60
    } else if screenWidth == 414.0 && screenHeight == 736.0 {
        // iPhone 6+/6S+/7+/8+ 尺寸
        commonLabelHeightConstraint.constant = 55
    } else {
        // 其他尺寸，如iPhone X及以上，设置一个默认值
        commonLabelHeightConstraint.constant = 50
    }
    // 更新视图布局
    self.view.layoutIfNeeded()
}
```

![](img/3534da3827b84c60b9d59b8320f2abfe_58.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_60.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_62.png)

在 `viewDidLoad` 方法中调用此函数，应用启动时就会根据当前设备调整布局。

### 4. Auto Layout的优势

通过上述方法，我们实现了：
*   **代码精简**：只需控制一个约束，即可影响所有关联视图。
*   **灵活适配**：轻松应对多种屏幕尺寸，无需为每种设备编写大量独立布局代码。
*   **维护方便**：UI调整只需修改少数约束或条件判断逻辑。

你可以将此模式扩展到其他控件，如图片视图（ImageView）、按钮（Button）等，构建出复杂且自适应的用户界面。

---

![](img/3534da3827b84c60b9d59b8320f2abfe_64.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_65.png)

## 总结

本节课中我们一起学习了两个iOS开发的核心技能：
1.  **Core Data操作**：我们掌握了如何获取托管对象上下文、创建实体、设置属性以及保存数据，实现了用户信息的持久化存储。
2.  **动态Auto Layout**：我们通过建立视图间的连锁约束，并配合代码动态修改约束常量，创建了一个能够智能适配不同iPhone屏幕尺寸的UI布局。

![](img/3534da3827b84c60b9d59b8320f2abfe_67.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_68.png)

![](img/3534da3827b84c60b9d59b8320f2abfe_70.png)

结合使用这些技术，你可以开发出数据驱动、界面美观且适配性强的iOS应用程序。