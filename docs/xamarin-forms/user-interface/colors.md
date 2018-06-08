---
title: 颜色
description: Xamarin.Forms 提供了灵活的跨平台颜色类别。
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: 7a304790213bcebe50a3f39295b5b1d1fb052879
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848338"
---
# <a name="colors"></a>颜色

_Xamarin.Forms 提供了灵活的跨平台颜色类别。_

本文介绍的各种方法`Color`类可以使用 Xamarin.Forms 中。

`Color`类提供了许多方法以生成的颜色实例

-  **名为颜色**的一套常见已命名的颜色，包括`Red`， `Green`，和`Blue`。
-  **FromHex** -字符串值类似于在 HTML 中，例如"00FF00"所使用的语法。 字母是可根据需要指定为字符 ("CC00FF00") 的第一对。
-  **FromHsla** -色调、 饱和度和亮度`double`值，其中可选 alpha 值 (0.0 1.0)。
-  **FromRgb** -红色、 绿色和蓝色`int`值 (0-255)。
-  **FromRgba** -红、 绿、 蓝方和字母`int`值 (0-255)。
-  **FromUint** -设置单个`double`值表示**argb**。

下面是一些示例颜色，分配给`BackgroundColor`的一些标签使用不同变体允许的语法：

```csharp
var red    = new Label { Text = "Red",   BackgroundColor = Color.Red };
var orange = new Label { Text = "Orange",BackgroundColor = Color.FromHex("FF6A00") };
var yellow = new Label { Text = "Yellow",BackgroundColor = Color.FromHsla(0.167, 1.0, 0.5, 1.0) };
var green  = new Label { Text = "Green", BackgroundColor = Color.FromRgb (38, 127, 0) };
var blue   = new Label { Text = "Blue",  BackgroundColor = Color.FromRgba(0, 38, 255, 255) };
var indigo = new Label { Text = "Indigo",BackgroundColor = Color.FromRgb (0, 72, 255) };
var violet = new Label { Text = "Violet",BackgroundColor = Color.FromHsla(0.82, 1, 0.25, 1) };

var transparent = new Label { Text = "Transparent",BackgroundColor = Color.Transparent };
var @default = new Label    { Text = "Default",    BackgroundColor = Color.Default };
var accent = new Label      { Text = "Accent",     BackgroundColor = Color.Accent };
```

这些颜色显示在下面每个平台。 请注意的最终颜色- `Accent` -为 iOS 和 Android; blue-ish 颜色由 Xamarin.Forms 定义此值。

 [![颜色演示](colors-images/colors-sml.png "颜色演示")](colors-images/colors.png#lightbox "颜色演示")

## <a name="colordefault"></a>Color.Default

使用`Default`设置 （或重置） 颜色值设为平台默认值 （了解，这表示不同的基础颜色，在每个属性对于每个平台上）。

开发人员可以使用此值设置`Color`属性但应**不**查询它的组件 RGB 值 （它们在所有设置为-1） 此实例。

## <a name="colortransparent"></a>Color.Transparent

设置要清除的颜色。

## <a name="coloraccent"></a>Color.Accent

在 iOS 和 Android 此实例设置为一种对比颜色在默认背景上可见，但又不是默认文本颜色相同。

## <a name="additional-methods"></a>其他方法

`Color` 实例包括可以用于创建新的颜色的其他方法：

-  **AddLuminosity** -通过提供增量修改亮度返回一种新颜色。
-  **WithHue** -返回一种新颜色、 色调替换提供的值。
-  **WithLuminosity** -返回一种新颜色，亮度替换提供的值。
-  **WithSaturation** -返回一种新颜色，饱和度替换提供的值。
-  **MultiplyAlpha** -通过修改 alpha，乘以所提供的 alpha 值返回一种新颜色。

## <a name="implicit-conversions"></a>隐式转换

之间的隐式转换`Xamarin.Forms.Color`和`System.Drawing.Color`类型都可执行：

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

此代码段使用`Device.RuntimePlatform`属性有选择地设置的颜色`ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>使用从 XAML

颜色也可以轻松地引用中使用的定义的颜色名称或此处显示的十六进制表示的 XAML 中：

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

## <a name="summary"></a>总结

Xamarin.Forms`Color`类用于创建平台感知颜色的引用。 它可在共享的代码和 XAML。


## <a name="related-links"></a>相关链接

- [ColorsSample](https://developer.xamarin.com/samples/WorkingWithColors)
- [可绑定选取器 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
