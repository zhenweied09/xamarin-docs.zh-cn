---
title: 在 Xamarin.iOS 的按钮
description: UIButton 类用于表示在 iOS 屏幕中的按钮的各种不同的样式。 本部分介绍使用按钮在 iOS 中的不同选项。
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: bf9a36c63e0c153ed950f4c3531e99e6baf77687
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789474"
---
# <a name="buttons-in-xamarinios"></a>在 Xamarin.iOS 的按钮

_UIButton 类用于表示在 iOS 屏幕中的按钮的各种不同的样式。本部分介绍使用按钮在 iOS 中的不同选项。_

`UIButton`类表示在 iOS 中的按钮控件。 

可以在中编辑按钮属性`Properties Pad`iOS 设计器：


![](buttons-images/properties.png "IOS 设计器属性小键盘")

## <a name="creating-a-button"></a>创建按钮

通过只几行代码，可以在创建 UIButton。

首先，实例化一个新按钮，并指定所需的按钮的类型：

```csharp
UIButton myButton = new UIButton(UIButtonType.System);
```

UIButtonType 应指定为以下项之一：

- **系统**-这是使用 iOS 的标准按钮类型，你将最常使用的类型。
- **DetailDisclosure** -显示的按钮用于隐藏或显示详细的信息"调低"类型。
- **InfoDark** -在深色详细信息按钮显示在一个圆周中的"i"。
- **InfoLight** -light 详细信息按钮显示在一个圆周中的"i"。
- **AddContact** -显示作为添加联系人按钮的按钮。
- **自定义**-允许你自定义按钮的多个特征。

按钮类型详细信息可在[按钮类型](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/create_different_types_of_buttons/)配方。

接下来，定义屏幕大小和位置的按钮。 示例:

```csharp
myButton.Frame = new CGRect (25, 25, 300, 150);
```

若要更改按钮中的文本，请使用`SetTitle`按钮，这就需要你设置的文本字符串的属性和`UIControlStyle`。 例如：

```csharp
myButton.SetTitle("Hello, World!", UIControlState.Normal);
```

设置为每个状态的不同属性让你能够通信 （如向用户的详细信息 请的文本颜色灰色为禁用状态）。 您可以使用 iOS 设计器中，每个状态之间切换也可以编程方式执行操作。 有关设置按钮文本和状态的详细信息，请参阅[设置按钮文本](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/set_button_text/)配方。

## <a name="dealing-with-user-interactions"></a>处理用户交互


按钮不是非常有用的除非它们执行某些单击时操作 ！ 

在 iOS 上按钮的事件几乎始终是触控事件，当使用其屏幕上的按钮与交互通过触摸它。 列出所有可能的 UIControl 事件的列表[此处](https://developer.apple.com/documentation/uikit/uicontrolevents)，但在 iOS 上的最常用事件`TouchUpInside`。 然后，你可以创建事件处理程序来执行一些操作按下了按钮：


```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

### <a name="adding-events-in-the-ios-designer"></a>在 iOS 设计器中添加事件
 
在属性板中使用事件选项卡将事件添加到控件。

选择该事件，然后输入一个新的事件处理程序或从列表中的选择一个的名称。 执行此操作将视图控制器类中创建新的分部方法。

![事件选项卡](buttons-images/image1.png)

## <a name="styling-a-button"></a>样式按钮

UIButtons 不同于大多数 UIKit 控制，因此根本不能更改标题都具有一种状态，你必须更改每个`UIControlState`。 设置标题颜色和阴影颜色以类似的方式完成：

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

此外，你可以为按钮的标题使用特性化的文本。 例如：

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>自定义按钮类型


当你将设置`Custom`按钮类型，该对象具有没有默认呈现。 你可以通过设置图像的不同状态配置按钮的外观。 例如，下面的代码演示如何添加不同映像`Normal`，`Highlighted`和`Selected`状态：


```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```


具体取决于是否用户接触到按钮，或不，它将呈现为以下映像之一 (`Normal`，`Highlighted`和`Selected`分别状态):


![](buttons-images/image22.png "UIButton 状态正常")
![](buttons-images/image23.png "突出显示的 UIButton 状态")
![](buttons-images/image24.png "选择的 UIButton 状态")

有关使用自定义按钮的详细信息，请参阅[使用按钮图像](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/use_an_image_for_a_button/)。


## <a name="related-links"></a>相关链接

- [UIButton 工作簿](https://developer.xamarin.com/workbooks/ios/user-interface/UIbutton/uibutton.workbook)
