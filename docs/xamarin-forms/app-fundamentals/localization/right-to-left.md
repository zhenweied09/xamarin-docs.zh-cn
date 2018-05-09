---
title: 从右到左本地化
description: 从右到左本地化将从右到左排列方向对支持添加到 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 3201c3161d66163cabffdb36465356192bdd3843
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="right-to-left-localization"></a>从右到左本地化

_从右到左本地化将从右到左排列方向对支持添加到 Xamarin.Forms 应用程序。_

> [!NOTE]
> 从右到左本地化需要 iOS 9 或更高版本和 API 17 或更高版本在 Android 上的使用。

流方向是人眼扫描的页上的 UI 元素的方向。 某些语言，如阿拉伯语和希伯莱语，需要用户界面元素的布局中从右到左排列方向。 这可以通过设置来实现[ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。 此属性获取或设置的方向在任何父元素来控制其布局，并且应设置为其中一个的用户界面元素流[ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection)枚举值：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToRight`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

设置[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性[ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft)在元素上通常设置的对齐方式到右、 右到左的阅读顺序和控件的布局，可以从流从右到左：

[![中与右到左排列方向阿拉伯语 TodoItemPage](rtl-images/TodoItemPage-Arabic.png "中使用从右到左排列方向阿拉伯语 TodoItemPage")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage 以阿拉伯数字表示与右到左排列方向")

> [!TIP]
> 您应该只设置[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)对初始布局的属性。 更改此值在运行会导致将影响性能一个昂贵的布局过程。

默认值[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)一个没有父元素的属性值是[ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight)，而默认值`FlowDirection`具有父级的元素为[ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). 因此，元素继承`FlowDirection`从其父级在可视化树中，并且任何元素的属性值可以重写它从其父级中获取的值。

> [!TIP]
> 在本地化应用程序从右到左语言，设置[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)页或根布局上的属性。 这将导致所有页上或根布局，以与流动方向做出相应响应中包含的元素。

## <a name="respecting-device-flow-direction"></a>遵从设备流方向

遵循设备的流方向基于所选语言的区域是一个显式的开发人员选项，并不会自动发生。 它可以通过设置[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性页上或根布局到`static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection)值：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

页上或根布局的所有子元素将按默认然后都继承[ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection)值。

## <a name="platform-setup"></a>平台安装程序

特定平台安装程序需要启用从右向左的区域设置。

### <a name="ios"></a>iOS

需要从右到左区域设置应作为支持的语言添加到的数组项`CFBundleLocalizations`中的键**Info.plist**。 下面的示例演示具有已添加到的数组的阿拉伯语`CFBundleLocalizations`密钥：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![支持的 Info.plist 语言](rtl-images/ios-locales.png "Info.plist 支持的语言")

有关详细信息，请参阅[本地化在 iOS 中的基础知识](https://docs.microsoft.com/en-gb/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios)。

从右到左本地化可以然后来测试更改为从右向左的区域设置中指定的语言和设备/模拟器上的地区**Info.plist**。

> [!WARNING]
> 请注意，当更改语言和区域为从右向左的区域设置在 iOS 上任何[ `DatePicker` ](xref:Xamarin.Forms.DatePicker)视图将引发异常，如果不包括区域设置所需的资源。 例如，在具有阿拉伯语中测试应用程序时`DatePicker`，确保**mideast**中选择**国际化**部分**iOS 生成**窗格。

### <a name="android"></a>Android

应用程序的**AndroidManifest.xml**应更新文件，以便`<uses-sdk>`节点集`android:minSdkVersion`属性设为 17，和`<application>`节点集`android:supportsRtl`属性设为`true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

通过更改设备/仿真程序为使用从右到左的语言，或启用，然后可以测试从右到左本地化**强制 RTL 布局方向**中**设置 > 开发人员选项**。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

应该按指定的所需的语言资源`<Resources>`节点**Package.appxmanifest**文件。 下面的示例演示已加入的阿拉伯语`<Resources>`节点：

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

此外，UWP 需要在.NET 标准库中显式定义应用程序的默认区域性。 这可以通过设置来实现`NeutralResourcesLanguage`属性中`AssemblyInfo.cs`，或在另一个类的默认区域性：

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

更改为相应从右向左的区域设置的语言和设备上的区域，然后可以测试从右到左本地化。

## <a name="limitations"></a>限制

Xamarin.Forms 右到左本地化目前具有许多限制：

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 按钮位置工具栏项位置，并且过渡动画控制由设备区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 轻扫方向不能翻转。
- [`Image`](xref:Xamarin.Forms.Image) 可视内容不能翻转。
- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) 和[ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet/p/System.String/System.String/System.String/System.String[]/)方向控制由设备区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`WebView`](xref:Xamarin.Forms.WebView) 内容不遵从[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- A`TextDirection`属性需要添加，以控制文本对齐方式。

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) 由设备区域设置时，控制方向而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) 文本对齐方式由设备的区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 不会反转手势和对齐方式。

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) 由设备区域设置时，控制方向而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 放置控制由设备区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) 文本对齐方式由设备的区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性不由继承[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)子级。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 文本对齐方式由设备的区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>从右至左语言支持使用 Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 右到左支持，通过[Xamarin 大学](https://university.xamarin.com/)**

## <a name="related-links"></a>相关的链接

- [TodoLocalizedRTL 示例应用程序](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
