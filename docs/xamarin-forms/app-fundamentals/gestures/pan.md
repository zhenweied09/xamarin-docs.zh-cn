---
title: 添加平移笔势识别器
description: 平移手势用于检测拖动并与 PanGestureRecognizer 类实现。 平移笔势的一个常见方案是水平和垂直拖动图像，以便它在小于图像大小视区中显示时，可以查看所有的映像包内容。 这通过在视区内，映像移动来实现，这篇文章中所示。
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: ed38f7ace9e11b009aae768cda2d4af0f36c337e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="adding-a-pan-gesture-recognizer"></a>添加平移笔势识别器

_平移手势用于检测拖动并与 PanGestureRecognizer 类实现。平移笔势的一个常见方案是水平和垂直拖动图像，以便它在小于图像大小视区中显示时，可以查看所有的映像包内容。这通过在视区内，映像移动来实现，这篇文章中所示。_

## <a name="overview"></a>概述

若要使用户界面元素与平移笔势可拖动，创建[ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/)实例时，处理[ `PanUpdated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.PanGestureRecognizer.PanUpdated/)事件，并添加到新的笔势识别器[`GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/)上的用户界面元素的集合。 下面的代码示例演示`PanGestureRecognizer`附加到[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)元素：

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

这也可以实现在 XAML 中，如下面的代码示例中所示：

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

代码`OnPanUpdated`随后将事件处理程序添加到代码隐藏文件：

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> 在 Android 上的正确平移需要[Xamarin.Forms 2.1.0-pre1 NuGet 包](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1)最少。

## <a name="creating-a-pan-container"></a>创建平移容器

本部分包含的通用帮助器类执行任意形式平移，其中通常适用于在图像或地图中导航。 处理平移手势以执行拖动操作需要某些数学运算来转换用户界面。 此数学用于拖动仅在已包装的用户界面元素的边界内。 以下代码示例演示 `PanContainer` 类：

```csharp
public class PanContainer : ContentView
{
  double x, y;

  public PanContainer ()
  {
    // Set PanGestureRecognizer.TouchPoints to control the
    // number of touch points needed to pan
    var panGesture = new PanGestureRecognizer ();
    panGesture.PanUpdated += OnPanUpdated;
    GestureRecognizers.Add (panGesture);
  }

  void OnPanUpdated (object sender, PanUpdatedEventArgs e)
  {
    ...
  }
}
```

此类可以环绕的用户界面元素，因此，平移手势将将拖动的已包装的用户界面元素。 下面的 XAML 代码示例演示`PanContainer`包装[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PanGesture"
             x:Class="PanGesture.HomePage">
    <ContentPage.Content>
        <AbsoluteLayout>
            <local:PanContainer>
                <Image Source="MonoMonkey.jpg" WidthRequest="1024" HeightRequest="768" />
            </local:PanContainer>
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

下面的代码示例演示如何`PanContainer`包装[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) C# 页面中的元素：

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new AbsoluteLayout {
      Padding = new Thickness (20),
      Children = {
        new PanContainer {
          Content = new Image {
            Source = ImageSource.FromFile ("MonoMonkey.jpg"),
            WidthRequest = 1024,
            HeightRequest = 768
          }
        }
      }
    };
  }
}
```

在这两个示例中， [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)和[ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/)属性设置为要显示的图像的宽度和高度值。

当[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)元素收到平移手势时，将拖动所显示的图像。 由执行拖动`PanContainer.OnPanUpdated`方法，下面的代码示例中所示：

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  switch (e.StatusType) {
  case GestureStatus.Running:
    // Translate and ensure we don't pan beyond the wrapped user interface element bounds.
    Content.TranslationX =
      Math.Max (Math.Min (0, x + e.TotalX), -Math.Abs (Content.Width - App.ScreenWidth));
    Content.TranslationY =
      Math.Max (Math.Min (0, y + e.TotalY), -Math.Abs (Content.Height - App.ScreenHeight));
    break;

  case GestureStatus.Completed:
    // Store the translation applied during the pan
    x = Content.TranslationX;
    y = Content.TranslationY;
    break;
  }
}
```

此方法将更新的已包装的用户界面元素，根据用户的平移手势可查看的内容。 这通过使用的值来实现[ `TotalX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalX/)和[ `TotalY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalY/)属性[ `PanUpdatedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanUpdatedEventArgs/)实例来计算方向和平移的距离。 `App.ScreenWidth`和`App.ScreenHeight`属性提供的高度和宽度的视区和均由各自的特定于平台的项目设置为的屏幕宽度和的设备的屏幕高度值。 已包装的用户元素然后拖动通过设置其[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)计算的值的属性。

时平移不占用整个屏幕的元素中的内容，可以从该元素获取的高度和宽度的视区[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/)和[ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/)属性。

> [!NOTE]
> 显示高分辨率图像会大大增加应用的内存需求量。 因此，它们应仅创建在应用程序不再需要它们时，就会立即应释放和所需时。 有关详细信息，请参阅[优化图像资源](~/xamarin-forms/deploy-test/performance.md#optimizeimages)。

## <a name="summary"></a>总结

平移笔势用于检测拖动和使用实施[ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/)类。



## <a name="related-links"></a>相关链接

- [PanGesture （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [PanGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/)
