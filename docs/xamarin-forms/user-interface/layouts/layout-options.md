---
title: LayoutOptions
description: "Xamarin.Forms 中的每个视图具有类型 LayoutOptions HorizontalOptions 和 VerticalOptions 的属性。 此文章介绍了每个 LayoutOptions 值对的对齐方式和扩展视图的影响。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: 978985c4e9803fad33760e4b40ab73d57f3ec420
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="layoutoptions"></a>LayoutOptions

_Xamarin.Forms 中的每个视图具有类型 LayoutOptions HorizontalOptions 和 VerticalOptions 的属性。此文章介绍了每个 LayoutOptions 值对的对齐方式和扩展视图的影响。_

## <a name="overview"></a>概述

[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)结构封装两个布局首选项：

- **对齐**– 视图的首选对齐方式，确定其位置和其父布局中的大小。
- **扩展**– 仅使用[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)，和是否可用，该值指示视图是否应使用额外的空间。

这些布局首选项可应用于[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)，相对于其父级，通过设置[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)或[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性`View`到从公共的字段之一[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)结构。 公共字段如下所示：

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

`Start`， `Center`， `End`，和`Fill`字段用于定义父布局中的视图的对齐方式：

- 对于水平对齐方式， [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)位置[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)上的左下方的父布局中，和垂直对齐方式，它定位`View`顶部父布局。
- 对于水平和垂直对齐方式， [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)水平或垂直居中对齐[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)。
- 对于水平对齐方式， [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)位置[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)上的右端的父布局中，对于垂直对齐方式，它定位`View`底部父布局中。
- 对于水平对齐方式， [ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)确保[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)填充宽度的父布局中，和垂直对齐方式，它确保`View`填充父布局的高度。

`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，和`FillAndExpand`值用于定义对齐方式的首选项，并是否视图将占用更多空间，如果父项中可用[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。

> [!NOTE]
> 视图的默认值[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性是[ `LayoutOptions.Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)。

<a name="alignment" />

## <a name="alignment"></a>对齐方式

对齐方式控制当父布局包含未使用的空间时视图中其父布局的排列方式 （即父布局是大于所有子级的组合大小）。

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)仅遵循`Start`， `Center`， `End`，和`Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)相反方向的子视图上的字段到`StackLayout`方向。 因此，子视图在垂直方向`StackLayout`可以设置其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)属性之一`Start`， `Center`， `End`，或`Fill`字段。 同样，子视图在水平方向`StackLayout`可以设置其[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性之一`Start`， `Center`， `End`，或`Fill`字段。

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)不遵从`Start`， `Center`， `End`，和`Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)方向与相同的子视图上的字段`StackLayout`方向。 因此，垂直方向`StackLayout`忽略`Start`， `Center`， `End`，或`Fill`字段如果对设置了[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)子视图的属性。 同样，水平方向`StackLayout`忽略`Start`， `Center`， `End`，或`Fill`字段如果对设置了[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)子视图的属性。

> [!NOTE]
> [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) 通常，替代大小使用指定的请求[ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/)和[ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)属性。

下面的 XAML 代码示例演示垂直方向[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)其中每个子[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)设置其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)属性中的四个对齐字段之一[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)结构：

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

等效的 C# 代码所示：

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

该代码会导致以下屏幕截图中所示的布局：

[![](layout-options-images/alignment.png "对齐布局选项")](layout-options-images/alignment-large.png "对齐布局选项")

<a name="expansion" />

## <a name="expansion"></a>扩展

扩展控制是否视图将占用更多空间，是否在内可用[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。 如果`StackLayout`包含未使用的空间 (即，`StackLayout`大于的所有子级的组合大小)，未使用的空间同样由进行设置，请求扩展的所有子视图共享其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)或[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性设置为[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)使用字段`AndExpand`后缀。 请注意，在中的所有空间`StackLayout`是使用，扩展选项不起作用。

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)只能扩展中其取向方向的子视图。 因此，垂直方向`StackLayout`可以展开设置的子视图其[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性之一`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，或`FillAndExpand`字段，如果`StackLayout`包含未使用的空间。 同样，水平方向`StackLayout`可以展开设置的子视图其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)属性之一`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，或`FillAndExpand`字段，如果`StackLayout`包含未使用的空间。

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)不能展开其方向相反方向的子视图。 因此，在垂直方向`StackLayout`，则设置[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)到的子视图属性[ `StartAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)已将属性设置为相同的效果[`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/).

> [!NOTE]
> 请注意，启用扩展不更改视图的大小除非它使用[ `LayoutOptions.FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)。

下面的 XAML 代码示例演示垂直方向[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)其中每个子[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)设置其[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性中的 4 个扩展字段之一[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)结构：

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

等效的 C# 代码所示：

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

该代码会导致以下屏幕截图中所示的布局：

[![](layout-options-images/expansion.png "扩展布局选项")](layout-options-images/expansion-large.png "扩展布局选项")

每个[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)所占据的空间中相同的量[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。 但是，仅最终`Label`，设置其[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性[ `FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)具有不同大小。 此外，每个`Label`隔开一个红色的小[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)，从而使空间`Label`占用轻松地查看。

## <a name="summary"></a>摘要

本文所述将每个[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)结构值对的对齐方式和视图中，相对于其父级的扩展。 `Start`， `Center`， `End`，和`Fill`字段用于定义父布局中的视图的对齐方式和`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，和`FillAndExpand`字段用于定义对齐方式的首选项，并确定是否该视图将占用更多空间，是否在内可用[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。



## <a name="related-links"></a>相关链接

- [LayoutOptions （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutoptions/)
- [LayoutOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)
