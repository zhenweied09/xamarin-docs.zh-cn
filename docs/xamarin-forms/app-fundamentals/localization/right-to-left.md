---
title: 从右到左本地化
description: 从右到左本地化将对从右到左的流方向的支持添加到 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 67b0d90290b18c7a5b55c5e3496b54970a8cfc38
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617600"
---
# <a name="right-to-left-localization"></a>从右到左本地化

_从右到左本地化将对从右到左的流方向的支持添加到 Xamarin.Forms 应用程序。_

> [!NOTE]
> 从右到左本地化需要 iOS 9 或更高版本，以及 API 17 或更高版本在 Android 上使用。

流方向是密切关注扫描的页上的 UI 元素的方向。 某些语言，如阿拉伯语和希伯来语，需要从右到左流动方向进行布局的 UI 元素。 这可以通过设置来实现[ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。 此属性获取或设置在任何的控制其布局，并应将设置为其中一个父元素的 UI 元素流中的方向[ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection)枚举值：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

设置[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性设置为[ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft)元素通常设置的对齐方式向右、 向右到左的阅读顺序和控件的布局从流从右到左：

[![在阿拉伯语从右到左流动方向与 TodoItemPage](rtl-images/TodoItemPage-Arabic.png "TodoItemPage 在阿拉伯语从右到左流方向")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage 在阿拉伯语从右到左流方向")

> [!TIP]
> 您应该只设置[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)初始布局的属性。 更改此值在运行时将导致一个成本高昂的布局过程，将会影响性能。

默认值[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)无父元素的属性值是[ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight)，而默认`FlowDirection`使用的父元素为[ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). 因此，一个元素继承`FlowDirection`从可视化树中，并且任何元素中其父级的属性值可以重写它从其父级中获取的值。

> [!TIP]
> 在本地化右到左的语言的应用时，设置[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)页或根布局上的属性。 这将导致所有页上或根布局，以与流方向做出适当的响应中包含的元素。

## <a name="respecting-device-flow-direction"></a>并遵循设备的流方向

并遵循设备的流方向基于所选的语言和区域是一个显式的开发人员的选择，而且不会自动发生。 它可以通过设置来实现[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)页上或根布局属性为`static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection)值：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

页上或根布局的所有子元素将默认情况下都继承[ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection)值。

## <a name="platform-setup"></a>平台安装程序

启用从右到左的区域设置需要特定平台安装程序。

### <a name="ios"></a>iOS

所需的从右向左的区域设置应作为受支持的语言添加到的数组项`CFBundleLocalizations`中的键**Info.plist**。 下面的示例演示具有已添加到数组中的阿拉伯语`CFBundleLocalizations`密钥：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Info.plist 支持语言](rtl-images/ios-locales.png "Info.plist 支持的语言")

有关详细信息，请参阅[在 iOS 中的本地化基础知识](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios)。

然后可以通过更改为从右向左的区域设置中指定的语言和设备/模拟器上的区域进行测试从右到左本地化**Info.plist**。

> [!WARNING]
> 请注意，当更改语言和区域为从右向左的区域设置在 iOS 上，任何[ `DatePicker` ](xref:Xamarin.Forms.DatePicker)视图将引发异常，如果不包括所需的区域设置的资源。 例如，当在阿拉伯语已测试的应用程序`DatePicker`，确保**mideast**中选择**国际化**一部分**iOS 生成**窗格。

### <a name="android"></a>Android

应用程序的**AndroidManifest.xml**应更新文件，以便`<uses-sdk>`的节点集`android:minSdkVersion`属性为 17，并`<application>`的节点集`android:supportsRtl`属性为`true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

通过更改设备/模拟器以使用从右到左语言中，或者通过启用，然后可以测试从右向左的本地化**Force RTL 布局方向**中**设置 > 开发人员选项**。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

应在中指定所需的语言资源`<Resources>`的节点**Package.appxmanifest**文件。 下面的示例显示了已加入的阿拉伯语`<Resources>`节点：

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

此外，UWP 需要.NET Standard 库中显式定义应用程序的默认区域性。 这可以通过设置来实现`NeutralResourcesLanguage`属性中`AssemblyInfo.cs`，或在另一个类中，为默认区域性：

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

更改为相应从右向左的区域设置的语言和设备上的区域，然后可以测试从右向左的本地化。

## <a name="limitations"></a>限制

Xamarin.Forms 从右到左本地化当前具有许多限制：

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 按钮位置工具栏项的位置，并转换动画受设备区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 轻扫方向不能翻转。
- [`Image`](xref:Xamarin.Forms.Image) 可视内容不能翻转。
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 并[ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))方向受设备区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`WebView`](xref:Xamarin.Forms.WebView) 内容不遵从[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- 一个`TextDirection`属性需要要添加，用于控制文本对齐方式。

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) 由设备的区域设置，控制方向而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) 文本对齐方式受设备区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 不会逆转手势和对齐方式。

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) 由设备的区域设置，控制方向而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 放置受设备区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) 文本对齐方式受设备区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 属性不会继承[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)子级。
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 文本对齐方式受设备区域设置，而不是[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)属性。

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>从右至左的语言支持使用 Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Xamarin.Forms 3.0 右到左支持，通过[Xamarin 学院课程](https://university.xamarin.com/)**

## <a name="related-links"></a>相关链接

- [TodoLocalizedRTL 示例应用](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
