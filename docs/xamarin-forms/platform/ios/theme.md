---
title: 添加特定于 iOS 的格式设置
description: 此文章介绍了如何设置特定于 iOS 的外观，而无需使用 Xamarin.Forms 自定义呈现器。
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 74a3cdc340cb09e8adf15ed0dd09315c985d18b5
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243526"
---
# <a name="adding-ios-specific-formatting"></a>添加特定于 iOS 的格式设置

一种方法来设置特定于 iOS 的格式设置是创建[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)控制和设置特定于平台的样式和颜色的每个平台。

其他选项来控制 Xamarin.Forms iOS 应用程序的外观包括的方式：

* 配置显示在选项[ **Info.plist**](#info-plist)
* 设置控件样式通过[ `UIAppearance` API](#uiappearance)

下面介绍了这些备选方法。

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>自定义 Info.plist

**Info.plist**文件允许你配置的 iOS 应用程序的 renderering，如 （是否以及如何） 显示状态栏某些方面。

例如， [Todo 示例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)使用下面的代码在所有平台上设置导航栏的颜色和文本颜色：

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

结果显示在下面的屏幕代码段。 请注意，状态栏项目是黑色 （这不能设置在 Xamarin.Forms 内因为它是特定于平台的功能）。

![](theme-images/status-default-sml.png "iOS 主题")

理想情况下状态栏也将白色-我们可以直接在 iOS 项目中完成的内容。 添加以下条目到**Info.plist**强制状态栏上要显示为白色：

![](theme-images/info-plist.png "iOS Info.plist 条目")

或编辑相应**Info.plist**文件直接包括：

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

现在，当应用运行时，导航栏为绿色，其文本为白色 （由于 Xamarin.Forms 格式设置）*和*状态栏文本也是白色谢谢到特定于 iOS 的配置：

![](theme-images/status-white-sml.png "iOS 主题")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)可以用于在许多 iOS 控件上设置 visual 属性*而无需*无需创建[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

添加代码的单个行**AppDelegate.cs** `FinishedLaunching`方法可以设置给定的类型使用的所有控件的都样式其`Appearance`属性。 下面的代码包含两个示例-全局样式选项卡栏并切换控件：

**AppDelegate.cs** iOS 项目中

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

默认情况下中的选定选项卡栏图标[ `TabbedPage` ](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)将蓝色：

![](theme-images/tabbar-default.png "默认 iOS TabbedPage 中的选项卡栏图标")

若要更改此行为，设置`UITabBar.Appearance`属性：

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

这将导致所选的选项卡才能为绿色：

![](theme-images/tabbar-custom.png "绿色 iOS TabbedPage 中的选项卡栏图标")

使用此 API 允许你自定义的外观 Xamarin.Forms`TabbedPage`极少量的代码与在 iOS 上。 请参阅[自定义选项卡配方](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/)有关使用自定义呈现器设置选项卡上的特定字体的详细信息。

### <a name="uiswitch"></a>UISwitch

`Switch`控件是可以轻松地风格的另一个示例：

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

这些两个屏幕截图显示默认值`UISwitch`左侧和自定义的版本控制 (设置`Appearance`) 在右侧[Todo 示例](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "默认 UISwitch 颜色") ![ ](theme-images/switch-custom.png "自定义 UISwitch 颜色")

### <a name="other-controls"></a>其他控件

许多 iOS 用户界面控件可以具有其默认颜色和其他属性集使用[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)。



## <a name="related-links"></a>相关链接

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [自定义选项卡](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/)
