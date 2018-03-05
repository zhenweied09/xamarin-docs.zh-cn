---
title: "外观 API"
description: "iOS 使你能够应用的可视属性设置在静态类级别而不是在单个对象，以便更改适用于该控件在应用程序中的所有实例。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 6d2a454665691c028fe8307940a5662a98ab9c98
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="appearance-api"></a>外观 API

_iOS 使你能够应用的可视属性设置在静态类级别而不是在单个对象，以便更改适用于该控件在应用程序中的所有实例。_

此功能通过一个静态公开在 Xamarin.iOS`Appearance`支持它的所有 UIKit 控件上的属性。 可视外观 （如为浅色颜色和背景图像属性） 可以因此轻松地自定义为你的应用程序提供一致的外观。 在 iOS 5 中引入了外观 API，iOS 9 中已弃用它的某些部分时它仍是实现某些样式和主题效果 Xamarin.iOS 应用程序中的一种好方法。

## <a name="overview"></a>概述

iOS 允许你自定义许多 UIKit 控件可使你想要应用到你的应用程序的品牌符合标准控件的外观。

有两个不同的方法，可将应用自定义外观：

- **直接在控件实例上**– 你可以在很多控件，包括工具栏、 导航栏、 按钮和滑块上设置的色调颜色、 背景图像和标题位置 （以及其他一些属性）。

- **外观静态属性上设置默认值**– 对于每个控件的可自定义属性公开通过`Appearance`静态属性。 适用于这些属性的任何自定义将用作任何控件设置该属性后创建该类型的默认值。

外观示例应用程序演示了这三种方法，这些屏幕截图中所示：

 [ ![](introduction-to-the-appearance-api-images/appearance01.png "外观示例应用程序演示这三种方法")](introduction-to-the-appearance-api-images/appearance01.png)

截至 iOS 8，外观代理已扩展到 TraitCollections。
 `AppearanceForTraitCollection` 可以用于在特定特征集上设置的默认外观。 你可以阅读有关此信息[简介情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)指南。


## <a name="setting-appearance-properties"></a>设置外观属性

在第一个屏幕中，静态的外观类用于设置样式的按钮和黄色/橙色元素如下：

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

这样，在设置绿色元素样式`ViewDidLoad`替代的默认值的方法和*外观*静态类：

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>使用 Xamarin.Forms 中 UIAppearance

外观 API 有用时可以[样式的 iOS 应用](~/xamarin-forms/platform/ios/theme.md#uiappearance)Xamarin.Forms 解决方案中。 中的几行`AppDelegate`类可帮助实现特定的配色方案而无需创建[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。


### <a name="custom-themes-and-uiappearance"></a>自定义主题和 UIAppearance

iOS 允许多个可视特性的用户界面控件，为"主题"使用*UIAppearance* Api 来强制具有相同的外观的特定控件的所有实例。 此名称公开为外观属性上许多用户界面控件类，不能对该控件的各个实例的以下属性。 设置显示属性，静态`Appearance`属性会影响你的应用程序中该类型的所有控件。

若要更好地了解的概念，请考虑一个示例。

若要更改特定`UISegmentedControl`具有洋红色浅色，我们将引用在中应这样我们屏幕上的特定控件`ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

或者，在设计器的属性小键盘设置值： 

[ ![](introduction-to-the-appearance-api-images/propertiespadtint.png "属性填充浅色")](introduction-to-the-appearance-api-images/propertiespadtint.png)

下图阐释了这将在名为 sg1 控件上设置色调。

 [ ![](introduction-to-the-appearance-api-images/image53.png "设置单个控件浅色")](introduction-to-the-appearance-api-images/image53.png)

若要以这种方式设置很多控件，将为完全效率不高，因此我们可以改为设置静态`Appearance`在该类本身的属性。 下面的代码所示：

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

下图现在说明了设置为洋红色的外观的两个分段的控件：

 [ ![](introduction-to-the-appearance-api-images/image54.png "设置外观控件浅色")](introduction-to-the-appearance-api-images/image54.png)

`Appearance` 属性应设置尽早在应用程序生命周期，如在 AppDelegate 中的`FinishedLaunching`事件，或在 ViewController 才会显示受影响的控件。


请参阅[简介外观 API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)更多详细信息。


## <a name="related-links"></a>相关链接

- [外观 （示例）](https://developer.xamarin.com/samples/monotouch/IntroToAppearance/)
- [UIAppearance 协议参考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Xamarin.Forms 中的外观](~/xamarin-forms/platform/ios/theme.md#uiappearance)
