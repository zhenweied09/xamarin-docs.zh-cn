---
title: 对于平板电脑和桌面应用程序的布局
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: bcd277145de13a95a0b19aa4945b02078af52978
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="layout-for-tablet-and-desktop-apps"></a>对于平板电脑和桌面应用程序的布局

Xamarin.Forms 支持在受支持的平台上可用的所有设备类型，因此除了手机，应用还可以在上运行：

* Ipad，
* Android 平板电脑
* Windows 平板电脑和台式计算机 （运行 Windows 10）。

此页简要讨论：

* 支持[设备类型](#Device_Types)，和
* 如何[优化](#optimize)平板电脑与手机的布局。

<a name="Device_Types" />

## <a name="device-types"></a>设备类型

更大的屏幕设备是可用于所有 Xamarin.Forms 所支持的平台。

### <a name="ipads-ios"></a>Ipad (iOS)

Xamarin.Forms 模板自动包括 iPad 支持通过配置**Info.plist > 设备**将设置为**通用**（这意味着支持 iPhone 和 iPad）。

若要提供愉快启动体验，并确保在所有设备上使用完整的屏幕分辨率，你应确保[iPad 特定的启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)（使用情节提要） 提供。 这可确保在 iPad 迷你、 iPad 和 iPad Pro 设备上正确呈现应用程序。

在 iOS 9 之前所有的应用占用整个屏幕在设备上，但现在可以执行某些 Ipad[拆分屏幕多任务](~/ios/platform/multitasking.md)。
这意味着你的应用程序可能会花费只包含屏幕的屏幕或整个屏幕的宽度的 50%的精简列。

[![](tablet-images/ipad-sml.png "iPad 拆分屏幕示例")](tablet-images/ipad.png#lightbox "iPad 拆分屏幕示例")

拆分屏幕功能意味着应设计你的应用能很好地配合降至 320 像素宽，或高达 1366年像素宽。

### <a name="android-tablets"></a>Android 平板电脑

Android 生态系统具有大量的支持的屏幕大小，从最大的平板电脑多小手机。 Xamarin.Forms 可以支持所有的屏幕大小，但作为与其他平台你可能需要调整为更大的设备用户界面。

当支持许多不同的屏幕分辨率，你可以提供不同的大小，以优化用户体验中的本机映像资源。
查看[Android 资源](~/android/app-fundamentals/resources-in-android/index.md)文档 (和尤其[为不同的屏幕大小创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) 有关如何构建的文件夹和 Android 应用中的文件名的详细信息若要优化的映像资源包含在你的应用程序的项目。

### <a name="windows-tablets-and-desktops"></a>Windows 平板电脑和桌面

若要支持平板电脑和运行 Windows 的台式计算机，你将需要使用[Windows UWP 支持](~/xamarin-forms/platform/windows/installation/index.md)，哪些生成 Windows 10 运行的通用应用。

Windows 平板电脑和台式计算机上运行的应用可调整到任意维度此外到正在运行全屏幕。

[![](tablet-images/splitscreen-sml.png "Windows 拆分屏幕示例")](tablet-images/splitscreen.png#lightbox "Windows 拆分屏幕示例")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>针对平板电脑和桌面进行优化

你可以调整具体取决于你 Xamarin.Forms 用户界面是否手机或平板电脑/桌面设备正被使用。 这意味着你可以优化用于大屏幕设备，例如平板电脑和台式计算机的用户体验。


### <a name="deviceidiom"></a>Device.Idiom

你可以使用[ `Device` ](~/xamarin-forms/platform/device.md)类来更改你的应用或用户界面的行为。 使用`Device.Idiom`可以的枚举

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

若要对单个页面布局进行重大更改，或甚至可以生成更大的屏幕上的完全不同页面，可以扩展此方法。

### <a name="leveraging-masterdetailpage"></a>利用 MasterDetailPage

[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)非常适合于较大的屏幕，尤其是在其中使用它的 iPad 上[ `UISplitViewController` ](https://developer.xamarin.com/api/type/UIKit.UISplitViewController/)提供本机 iOS 体验。

查看[此 Xamarin 博客文章](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)若要查看如何适应你的用户界面，以便手机使用一种布局以及较大的屏幕可以使用另一个 (与`MasterDetailPage`)。



## <a name="related-links"></a>相关链接

- [Xamarin 博客](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [MyShoppe 示例](https://github.com/jamesmontemagno/myshoppe)
