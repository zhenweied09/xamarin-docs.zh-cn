---
title: ScrollView
description: "使用 ScrollView 呈现无法适合只在一个屏幕并释放空间用于键盘的内容的布局。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2016
ms.openlocfilehash: c305075d37a25bfe828f16d4e69955437a591f9a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="scrollview"></a>ScrollView

[`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) 包含布局和为用户提供了滚动屏幕外。 `ScrollView` 此外用于允许视图以显示键盘时自动移动到屏幕的可见部分。

[ ![](scroll-view-images/layouts-sml.png "Xamarin.Forms 布局")](scroll-view-images/layouts.png "Xamarin.Forms 布局")

本文介绍：

- **[目的](#Purpose)** &ndash;的目的`ScrollView`以及使用时。
- **[使用情况](#Usage)** &ndash;如何使用`ScrollView`在实践中。
- **[属性](#Properties)** &ndash;可读取和修改的公共属性。
- **[方法](#Methods)** &ndash;可以调用以进行滚动视图的公共方法。
- **[事件](#Events)** &ndash;可以用于侦听的视图状态的更改的事件。

## <a name="purpose"></a>目标

`ScrollView` 可以用于确保也在较小的手机上，显示较大的视图。 例如，适用于 iPhone 6s 的布局可能 4s 在 iPhone 上被剪切。 使用`ScrollView`将允许要较小屏幕上显示的布局的剪切的部分。

## <a name="usage"></a>用法

> [!NOTE]
> **请注意**: `ScrollView`s 不应进行嵌套。 此外， `ScrollView`s 应嵌套深度不能与 like 提供滚动，其他控件`ListView`和`WebView`。

`ScrollView` 公开`Content`属性可以设置为单一视图或布局。 请看以下示例的一种布局有非常大的字数后, 跟`Entry`:

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

在 C# 中：

```csharp
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,   HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

用户向下滚动，唯一之前`BoxView`可见：

![](scroll-view-images/scroll-start.png "在 ScrollView 字数")

请注意，当用户开始输入中的文本`Entry`，视图滚动以使其在屏幕上可见：

![](scroll-view-images/scroll-end.png "中 ScrollView 条目")

## <a name="properties"></a>属性

ScrollView 具有以下属性：

- **内容**&ndash;获取或设置要在中显示的视图`ScrollView`。
- **[ContentSize](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/)**  &ndash;只读的获取内容，它具有宽度和高度组件的大小。 这是一个可绑定属性
- **[方向](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/)** &ndash;这是[ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/)，这是一个枚举，可以将设置为`Horizontal`， `Vertical`，或`Both`。
- **ScrollX** &ndash;只读、 获取 X 维度中的当前滚动位置。
- **ScrollY** &ndash;只读的获取 Y 维度中的当前滚动位置。

## <a name="methods"></a>方法

`ScrollView` 提供`ScrollToAsync`方法，可用于滚动视图使用坐标或通过指定应设为可见的特定视图。

当使用坐标，指定`x`和`y`坐标，以及一个布尔值，该值指示是否滚动应进行动画处理：

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

滚动到特定元素时`ScrollToPosition`枚举指定元素在视图中显示其中：

- **Center** &ndash;滚动到视图的可见部分中心的元素。
- **结束**&ndash;滚动到视图的可见部分末尾的元素。
- **MakeVisible** &ndash;滚动元素，以便在视图内可见。
- **启动**&ndash;滚动到视图的可见部分的起始位置的元素。

`IsAnimated`属性指定如何将滚动视图。 当设置为 true，平滑动画是将使用的而不是立即将内容移动到视图。

## <a name="events"></a>事件

`ScrollView` 公开一个事件， `Scrolled`。 `Scrolled` 查看完滚动时引发。 事件处理程序`Scrolled`采用`ScrolledEventArgs`，它具有`ScrollX`和`ScrollY`属性。 以下内容演示如何使用的当前滚动位置更新标签`ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

请注意，滚动位置可以是负数，由于弹跳效果时滚动列表的末尾。


## <a name="related-links"></a>相关链接

- [布局 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 示例 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
