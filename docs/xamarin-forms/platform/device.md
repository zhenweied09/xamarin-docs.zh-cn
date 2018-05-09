---
title: 设备类
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 1fc3fb17ec97ce9028abbf63cdedbfc5fec12204
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="device-class"></a>设备类

[ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)类包含大量的属性和方法，以帮助开发人员自定义布局和每个平台的功能。

除了方法和属性添加到目标代码在特定硬件类型和大小，`Device`类包括[BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread)方法从与用户界面交互的控件时应使用该方法后台线程。

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>提供特定于平台的值

在 Xamarin.Forms 2.3.4 之前, 的平台运行应用程序无法获取通过检查[ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/)属性和比较到[ `TargetPlatform.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/)， [`TargetPlatform.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/)， [ `TargetPlatform.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/)，和[ `TargetPlatform.Windows` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/)枚举值。 同样，其中一个[ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/)重载无法用于提供对控件的特定于平台的值。

但是，因为 Xamarin.Forms 2.3.4 这些 Api 已弃用并替换。 [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)类现在包含标识平台 – 的公共字符串常量[ `Device.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/)， [ `Device.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/)， [ `Device.WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/) （已弃用） [ `Device.WinRT` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinRT/) （已弃用） [ `Device.UWP` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/)，和[ `Device.macOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.macOS/)。 同样， [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/)重载已被替换为[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)和[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) Api。

在 C# 中，可以通过创建提供特定于平台的值`switch`语句[ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/)属性，并提供`case`所需的平台的语句：

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)和[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/)类提供在 XAML 中相同的功能：

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)类是一个泛型类，因此必须实例化与`x:TypeArguments`匹配的目标类型属性。 在[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/)类， [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/)属性可以接受单个`string`值或以逗号分隔的多个`string`值。

> [!IMPORTANT]
> 提供不正确`Platform`属性中的值`On`类不会导致错误。 相反，代码将执行而无需在应用的特定于平台的值。

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

`Device.Idiom`可用于改变布局或具体取决于设备运行应用程序的功能。 [ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/)枚举包含以下值：

-  **Phone** – iPhone、 iPod touch 和 Android 设备比 600 dip 窄 ^
-  **平板电脑**– iPad，Windows 设备和 Android 设备宽于 600 dip ^
-  **桌面**-仅在中返回[UWP 应用](~/xamarin-forms/platform/windows/installation/index.md)Windows 10 桌面计算机上 (返回`Phone`上移动的 Windows 设备，包括在连续方案)
-  **电视**– Tizen 电视设备
-  **不支持**– 未使用

*^ dip 不一定的物理像素计数*

`Idiom` 是用于构建利用较大的屏幕，此类的布局特别有用：

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

## <a name="deviceflowdirection"></a>Device.FlowDirection

[ `Device.FlowDirection` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.FlowDirection/)值检索[ `FlowDirection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlowDirection/)表示当前正由设备的流方向的枚举值。 流方向是人眼扫描的页上的 UI 元素的方向。 枚举值为：

- [`LeftToRight`](https://developer.xamarin.com/api/field/Xamarin.Forms.FlowDirection.LeftToRight/)
- [`RightToRight`](https://developer.xamarin.com/api/field/Xamarin.Forms.FlowDirection.RightToLeft/)
- [`MatchParent`](https://developer.xamarin.com/api/field/Xamarin.Forms.FlowDirection.MatchParent/)

在 XAML 中， [ `Device.FlowDirection` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.FlowDirection/)来检索值，只需使用`x:Static`标记扩展：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

在 C# 中的等效代码是：

```csharp
this.FlowDirection = Device.FlowDirection;
```

有关流方向的详细信息，请参阅[右到左本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

<a name="Device_Styles" />

## <a name="devicestyles"></a>Device.Styles

[ `Styles`属性](~/xamarin-forms/user-interface/styles/index.md)包含可应用于某些控件的内置样式定义 (如`Label`)`Style`属性。 可用的样式包括：

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

<a name="Device_GetNamedSize" />

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` 可在设置时[ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md)在 C# 代码：

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

<a name="Device_OpenUri" />

## <a name="deviceopenuri"></a>Device.OpenUri

`OpenUri`方法可用于触发基础平台，如在本机浏览器中打开一个 URL 上的操作 (**Safari**在 iOS 上或**Internet**在 Android 上)。

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

[WebView 示例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs)包括使用示例`OpenUri`若要打开的 Url，还触发电话呼叫。

[地图示例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs)还使用`Device.OpenUri`以显示地图和路线信息使用本机**映射**在 iOS 和 Android 的应用。

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

`Device`类还具有`StartTimer`方法，提供了简单的方法来触发时间相关的任务 （包括 Pcl） 的 Xamarin.Forms 通用代码在正常运行。 传递`TimeSpan`以设置间隔并返回`true`需要运行的计时器或`false`当前调用后将其停止。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果与用户界面交互内计时器的代码 (如设置的文本`Label`或显示警报) 内，才应执行`BeginInvokeOnMainThread`表达式 （见下文）。

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

后台线程，如计时器或类似的 web 请求的异步操作完成处理程序中运行的代码应永远不会访问用户界面元素。 任何需要更新的用户界面的后台代码应被包装到[ `BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/)。 这等同于`InvokeOnMainThread`在 iOS 上`RunOnUiThread`在 Android 上，和`Dispatcher.RunAsync`通用 Windows 平台上。

Xamarin.Forms 代码是：

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

请注意使用该方法`async/await`不需要使用`BeginInvokeOnMainThread`如果它们从主 UI 线程运行。

## <a name="summary"></a>总结

Xamarin.Forms`Device`类允许细粒度的控制功能和布局上的每个平台基础-甚至共同点代码 （PCL 或共享项目）。


## <a name="related-links"></a>相关链接

- [设备示例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [样式示例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [设备](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)
