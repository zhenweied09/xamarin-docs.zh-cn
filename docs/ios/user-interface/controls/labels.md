---
title: "标签"
ms.topic: article
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 0fdeecc4465aa5709b452ef0b591ec4e5c262e3d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="labels"></a>标签

`UILabel`控件用于显示单个和多行，读取纯文本。 

## <a name="implementing-a-label"></a>实现一个标签

新的标签创建方法是实例化[ `UILabel` ](https://developer.xamarin.com/api/type/UIKit.UILabel/):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>标签和情节提要

使用 iOS 设计器时，还可以将标签添加到你的 UI。 搜索**标签**中**工具箱**并将其拖到你的视图：

![中的工具箱标签](labels-images/image3.png)

属性小键盘上，可以调整以下属性：

![标签属性面板](labels-images/image2.png)

- **文本上下文**– 纯文本或特性化。 纯文本可以设置[格式设置特性](#Formatting_Text_and_Label)上的整个字符串。 特性化的文本可以设置格式设置为不同的字符或字符串中的单词。
- **颜色、 字体对齐**– 可以应用于该标签的格式设置的属性。
- **行**– 设置标签可以跨的行数。 将此属性设置为 0 以允许使用所需的尽可能多的行的标签。
- **行为**– 可以将设置为已启用或突出显示。 启用是默认设置，已禁用的文本将显示在较浅的灰色颜色。 突出显示默认处于禁用状态，并允许重绘的突出显示的状态时用户选择的标签。
- **Baselane 和换行符**– 
    - 基准确定如何将文本定位如果不同于指定的字体大小。
    - 分行符确定如何包装或如果它的长度大于单个行截断字符串。
- **自动收缩**– 确定将如何在了标签内最小大小的字体，如有必要。
- **突出显示，卷影，偏移量**– 允许你设置处于突出显示状态和卷影颜色和阴影偏移量。

## <a name="truncating-and-wrapping"></a>截断和包装

有关在 iOS 中中断上使用行的信息，请参阅[截断和文本换行](https://developer.xamarin.com/recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text/)配方。

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>设置文本和标签的格式

要格式化在标签中使用的字符串，你可以设置格式设置特性上的整个字符串，或者你可以使用特性化的字符串。 下面的示例演示如何实现这些：

```csharp
label = new UILabel(){
                Text = "Hello, this is a string",
                Font = UIFont.FromName("Papyrus", 20f),
                TextColor = UIColor.Magenta,
                TextAlignment = UITextAlignment.Center
            };
```

```csharp
label.AttributedText = new NSAttributedString(
                "This is some formatted text",
                font: UIFont.FromName("GillSans", 16.0f),
                foregroundColor: UIColor.Blue,
                backgroundColor: UIColor.White
            );
```

有关详细信息样式文本使用`NSAttributedString`指[文本的样式](https://developer.xamarin.com/recipes/ios/standard_controls/text_field/style_text/)配方。

默认情况下标签具有`Enabled`设置为 true，但它可以将其设置为禁用可以向用户授予某些控制是否已禁用的提示：

```csharp
label.Enabled = false;
```

这将设置标签浅灰色，如以下示例图中的 iOS 中的限制屏幕中所示：

![在 iOS 中的禁用的按钮](labels-images/image1.png)

此外可以向其他效果的标签文本设置突出显示和卷影文本颜色：

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

这将显示如下的文本：

![突出显示和文本上设置的卷影](labels-images/image4.png)

有关更改 UILabel 的字体的详细信息，请参阅[更改字体](https://developer.xamarin.com/recipes/ios/standard_controls/labels/change_the_font/)配方。





