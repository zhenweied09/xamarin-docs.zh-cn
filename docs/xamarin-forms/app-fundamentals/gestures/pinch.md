---
title: 添加捏合手势识别器
description: 捏合手势用于执行交互式缩放，并与 PinchGestureRecognizer 类实现。 捏合手势的一个常见方案是映像的执行交互式缩放比例为捏合位置。 这通过缩放内容的视区中，来实现，这篇文章中所示。
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: f8f81418cf5284b288f29fd2363b6b8f2d9d5fdd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="adding-a-pinch-gesture-recognizer"></a>添加捏合手势识别器

_捏合手势用于执行交互式缩放，并与 PinchGestureRecognizer 类实现。捏合手势的一个常见方案是映像的执行交互式缩放比例为捏合位置。这通过缩放内容的视区中，来实现，这篇文章中所示。_

## <a name="overview"></a>概述

若要使用户界面元素与捏合手势可缩放，创建[ `PinchGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/)实例时，处理[ `PinchUpdated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.PinchGestureRecognizer.PinchUpdated/)事件，并添加到新的笔势识别器[`GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/)上的用户界面元素的集合。 下面的代码示例演示`PinchGestureRecognizer`附加到[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)元素：

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

这也可以实现在 XAML 中，如下面的代码示例中所示：

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

代码`OnPinchUpdated`随后将事件处理程序添加到代码隐藏文件：

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>创建 PinchToZoom 容器

处理捏合手势以执行缩放操作需要某些数学运算来转换用户界面。 本部分包含一个通用帮助器类以执行数学计算，可用于以交互方式缩放任何用户界面元素。 以下代码示例演示 `PinchToZoomContainer` 类：

```csharp
public class PinchToZoomContainer : ContentView
{
  ...

  public PinchToZoomContainer ()
  {
    var pinchGesture = new PinchGestureRecognizer ();
    pinchGesture.PinchUpdated += OnPinchUpdated;
    GestureRecognizers.Add (pinchGesture);
  }

  void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
  {
    ...
  }
}
```

此类可以封装用户界面元素周围，以便捏合手势将缩放已包装的用户界面元素。 下面的 XAML 代码示例演示`PinchToZoomContainer`包装[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PinchGesture;assembly=PinchGesture"
             x:Class="PinchGesture.HomePage">
    <ContentPage.Content>
        <Grid Padding="20">
            <local:PinchToZoomContainer>
                <local:PinchToZoomContainer.Content>
                    <Image Source="waterfront.jpg" />
                </local:PinchToZoomContainer.Content>
            </local:PinchToZoomContainer>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

下面的代码示例演示如何`PinchToZoomContainer`包装[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) C# 页面中的元素：

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new Grid {
      Padding = new Thickness (20),
      Children = {
        new PinchToZoomContainer {
          Content = new Image { Source = ImageSource.FromFile ("waterfront.jpg") }
        }
      }
    };
  }
}
```

当[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)元素收到捏合手势时，显示的图像将被放大加入或退出。由执行缩放`PinchZoomContainer.OnPinchUpdated`方法，下面的代码示例中所示：

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  if (e.Status == GestureStatus.Started) {
    // Store the current scale factor applied to the wrapped user interface element,
    // and zero the components for the center point of the translate transform.
    startScale = Content.Scale;
    Content.AnchorX = 0;
    Content.AnchorY = 0;
  }
  if (e.Status == GestureStatus.Running) {
    // Calculate the scale factor to be applied.
    currentScale += (e.Scale - 1) * startScale;
    currentScale = Math.Max (1, currentScale);

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the X pixel coordinate.
    double renderedX = Content.X + xOffset;
    double deltaX = renderedX / Width;
    double deltaWidth = Width / (Content.Width * startScale);
    double originX = (e.ScaleOrigin.X - deltaX) * deltaWidth;

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the Y pixel coordinate.
    double renderedY = Content.Y + yOffset;
    double deltaY = renderedY / Height;
    double deltaHeight = Height / (Content.Height * startScale);
    double originY = (e.ScaleOrigin.Y - deltaY) * deltaHeight;

    // Calculate the transformed element pixel coordinates.
    double targetX = xOffset - (originX * Content.Width) * (currentScale - startScale);
    double targetY = yOffset - (originY * Content.Height) * (currentScale - startScale);

    // Apply translation based on the change in origin.
    Content.TranslationX = targetX.Clamp (-Content.Width * (currentScale - 1), 0);
    Content.TranslationY = targetY.Clamp (-Content.Height * (currentScale - 1), 0);

    // Apply scale factor.
    Content.Scale = currentScale;
  }
  if (e.Status == GestureStatus.Completed) {
    // Store the translation delta's of the wrapped user interface element.
    xOffset = Content.TranslationX;
    yOffset = Content.TranslationY;
  }
}
```

此方法将更新基于用户的捏合手势已包装的用户界面元素的缩放的级别。 这通过使用的值来实现[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale/)， [ `ScaleOrigin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin/)和[ `Status` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.Status/)属性[ `PinchGestureUpdatedEventArgs`](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureUpdatedEventArgs/)要计算的比例因子捏合手势原点应用实例。 已包装的用户元素然后放大捏合手势原点通过设置其[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)， [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)，和[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)为计算的值的属性。

## <a name="summary"></a>总结

捏合手势用于执行交互式缩放并实现与[ `PinchGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/)类。


## <a name="related-links"></a>相关链接

- [PinchGesture （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [PinchGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/)
