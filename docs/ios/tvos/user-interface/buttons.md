---
title: "使用按钮"
description: "本文介绍如何设计和使用 Xamarin.tvOS 应用内的按钮。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/07/2017
ms.openlocfilehash: 4b2a470d7fe2a1f9d4b8df40836c934547adf614
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-buttons"></a>使用按钮

_本文介绍如何设计和使用 Xamarin.tvOS 应用内的按钮。_


使用的实例`UIButton`类在 tvOS 窗口中创建一个可获得焦点，可选择按钮。 当用户选择一个按钮时，它将操作消息发送到目标对象允许你对用户进行 Xamarin.tvOS 应用响应的输入。

[![](buttons-images/buttons01.png "示例按钮")](buttons-images/buttons01.png#lightbox)

有关使用焦点和使用 Siri 远程定位的详细信息，请参阅我们[使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)和[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。

<a name="About-Buttons" />

## <a name="about-buttons"></a>关于按钮

在 tvOS，按钮用于特定于应用程序的操作，可能包含标题、 图标或两者。 当用户导航应用程序的用户界面使用[Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)，焦点将移动到给定按钮使其更改文本和背景色。 阴影也会应用于添加从而使其显示用户界面的其余部分上面迅速 3D 效果的按钮。

[![](buttons-images/buttons01.png "示例按钮")](buttons-images/buttons01.png#lightbox)

Apple 具有以下建议用于使用按钮：

- **使用标题或的图标**-时这两个图标和标题可以包含一个按钮在空间有限，因此，不要尝试将两者结合使用。
- **清楚地标记破坏性按钮**-如果按钮执行破坏性操作 （如删除的文件），清楚地将其这种情况下使用文本和/或图标标记。 破坏性操作应始终存在[警报](~/ios/tvos/user-interface/alerts.md)要求用户限制为该操作。
- **不使用返回按钮**-上使用 Siri 远程菜单按钮用于将返回到之前的屏幕。 此规则的一个例外是应用内购买或破坏性操作的其中**取消**应显示按钮。

有关使用焦点和导航的详细信息，请参阅我们[使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)文档。

<a name="Button-Icons" />

### <a name="button-icons"></a>按钮图标

Apple 提供的建议你为你的按钮图标使用简单、 高可识别的映像。 过于复杂图标很难在上一张床同一房间识别电视屏幕上，因此尝试使用最简单的表示形式可以找到问题的答案。 只要有可能，使用标准，也知道图标 （如搜索放大镜） 的映像。

<a name="Button-Titles" />

### <a name="button-titles"></a>按钮标题

创建标题按钮时，Apple 将具有以下建议：

- **显示图标按钮下面描述性文本**-如果可能，图标下方将清除的描述性文本仅按钮添加到进一步 get 跨按钮的作用。
- **用于标题的谓词或谓词短语**-清楚地状态将执行的操作，将在用户单击按钮时。
- **使用标题样式大小写**-除了文章、 连词或介词 (四个字母或更少)，应大写的按钮的标题的每个词。
- **使用较短的点到标题**-使用最短可能措辞描述按钮的操作。

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>按钮和情节提要

若要使用 Xamarin.tvOS 应用中的按钮的最简单方法是将它们添加到 Xamarin 设计器用于 iOS 的应用程序的 UI。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**按钮**从**库**并将其放在视图上： 

    [![](buttons-images/storyboard01.png "按钮")](buttons-images/storyboard01.png#lightbox)
1. 在**属性资源管理器**，你可以调整按钮的几个属性，如其**标题**和**文本颜色**: 

    [![](buttons-images/storyboard02.png "按钮属性")](buttons-images/storyboard02.png#lightbox)
1. 接下来，切换到**事件选项卡**网络最多**事件**从**按钮**和调用它`ButtonPressed`: 

    [![](buttons-images/storyboard03.png "事件选项卡")](buttons-images/storyboard03.png#lightbox)
1. 则将自动切换到`ViewController.cs`其中你可以将新操作放在你的代码使用的视图**向上**和**下**箭头键： 

    [![](buttons-images/storyboard04.png "在代码中放置新的操作")](buttons-images/storyboard04.png#lightbox)
1. 按**Enter**以选择位置： 

    [![](buttons-images/storyboard05.png "代码编辑器")](buttons-images/storyboard05.png#lightbox)
1. 将所做的更改保存到的所有文件。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**按钮**从**库**并将其放在视图上： 

    [![](buttons-images/storyboard01vs.png "按钮")](buttons-images/storyboard01vs.png#lightbox)
1. 在**属性资源管理器**，你可以调整按钮的几个属性，如其**标题**和**文本颜色**: 

    [![](buttons-images/storyboard02vs.png "属性资源管理器")](buttons-images/storyboard02vs.png#lightbox)
1. 接下来，切换到**事件选项卡**网络最多**事件**从**按钮**和调用它`ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "事件选项卡")](buttons-images/storyboard03vs.png#lightbox)
1. 将所做的更改保存到的所有文件。



编辑视图控制器 (示例`ViewController.cs`) 文件，并添加以下代码以处理按钮选择：


```

using System;
using UIKit;

namespace tvRemote
{
    public partial class ViewController : UIViewController
    {
        ...

        partial void ButtonPressed (UIButton sender) {
            // Handle click here
            ...
        }
    }
}

```

-----

只要按钮的`Enabled`属性是`true`和不受另一个控件或视图，则它可进行使用 Siri 远程处于焦点项。 如果用户选择按钮并单击 Touch 图面，`ButtonPressed`将执行上面定义的操作。

> [!IMPORTANT]
> **注意：**虽然可能如分配操作，但`TouchUpInside`到`UIButton`iOS 时创建的设计器中**事件处理程序**，它将永远不会调用因为 Apple TV 没有触摸屏幕或支持触控事件。 你应始终使用默认值**操作类型**创建时**操作**tvOS 用户界面元素。




有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>按钮和代码

（可选）`UIButton`可以在 C# 代码中创建并添加到 tvOS 应用的视图。 例如:

```csharp
var button = new UIButton(UIButtonType.System);
button.Frame = new CGRect (25, 25, 300, 150);
button.SetTitle ("Hello", UIControlState.Normal);
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
View.AddSubview (button);
```

当你创建一个新`UIButton`在代码中，你指定其`UIButtonType`为以下项之一：

- **系统**-这是由 tvOS 的按钮的标准类型，你将最常使用的类型。
- **DetailDisclosure** -显示的按钮用于隐藏或显示详细的信息"调低"类型。
- **InfoDark** -在深色详细信息按钮显示在一个圆周中的"i"。
- **InfoLight** -light 详细信息按钮显示在一个圆周中的"i"。
- **AddContact** -显示作为添加联系人按钮的按钮。
- **自定义**-允许你自定义按钮的多个特征。

接下来，定义屏幕大小和位置的按钮。 示例:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

然后，设置按钮的标题。 `UIButtons` 不同于大多数`UIKit`控件，都具有一种状态，因此你根本不能更改的标题，则必须更改它的给定`UIControlState`。 例如:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

接下来，使用`AllEvents`若要查看用户单击按钮的事件。 示例:

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

最后，将按钮添加到视图以显示它：

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> **注意：**虽然可能如分配操作，但`TouchUpInside`到`UIButton`，它将永远不会调用因为 Apple TV 没有触摸屏幕或支持触控事件。 始终应使用事件如**AllEvents**或**PrimaryActionTriggered**。




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>样式按钮

tvOS 提供的几个属性`UIButton`可用来提供其标题并设置样式与背景色和图像等事务。

<a name="Button-Titles" />

### <a name="button-titles"></a>按钮标题

正如我们所看到的更高版本，`UIButtons`不同于大多数`UIKit`控件，都具有一种状态，因此你根本不能更改的标题，则必须更改它的给定`UIControlState`。 例如:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

您可以设置标题颜色按钮使用`SetTitleColor`方法。 例如:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

并可以调整标题的卷影使用`SetTitleShadowColor`。 例如:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

你可以设置标题阴影从更改*凹进*到*阳文*按钮突出显示使用下面的代码时：

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

此外，你可以为按钮的标题使用特性化的文本。 例如:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>按钮图像

A`UIButton`可以附加到它的映像，并且可以使用映像作为其背景。

若要设置的按钮的背景图像给定`UIControlState`，使用以下代码：

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

设置`AdjustsImageWhenHiglighted`属性`true`绘制图像更轻，突出显示按钮时 （这是默认值）。 设置`AdjustsImageWhenDisabled`属性`true`要绘制图像变暗时的按钮被禁用 （同样，这是默认值）。

若要设置在按钮上显示的图像，请使用以下代码：

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

使用`TintColor`属性来设置应用于标题和按钮的图像颜色色调。 为按钮的`Custom`类型，则此属性不起作用，则必须实现`TintColor`行为自己。

<a name="Summary" />

## <a name="summary"></a>摘要

本文已覆盖设计和使用 Xamarin.tvOS 应用内的按钮。 它介绍了如何使用 iOS 设计器中的按钮以及如何在 C# 代码中创建按钮。 最后，它还介绍了如何修改按钮的标题和更改其样式和外观。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
