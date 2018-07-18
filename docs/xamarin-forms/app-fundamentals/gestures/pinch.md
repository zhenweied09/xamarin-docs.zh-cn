---
title: 添加捏合手势识别器
description: 此文章介绍了如何使用收缩手势来执行交互在 pinch 位置的图像的缩放。
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 37befdcd4ccbcd49e3cebda92d55ae6f70da2ad6
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998693"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>添加捏合手势识别器

_捏合手势用于执行交互式缩放，并使用 PinchGestureRecognizer 类实现。捏合手势的常见方案是图像的执行交互式 pinch 位置处的缩放。这通过缩放内容的视区，来实现，本文中所示。_

## <a name="overview"></a>概述

若要使用户界面元素可使用收缩手势进行缩放，创建[ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer)实例，则处理[ `PinchUpdated` ](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated)事件，并添加到新的笔势识别器[`GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers)上的用户界面元素的集合。 下面的代码示例演示`PinchGestureRecognizer`附加到[ `Image` ](xref:Xamarin.Forms.Image)元素：

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

这也可以在 XAML，如下面的代码示例中所示：

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

有关代码`OnPinchUpdated`事件处理程序然后添加到代码隐藏文件：

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>正在创建 PinchToZoom 容器

处理捏合手势进行缩放操作需要某些数学运算来转换用户界面。 本部分包含执行数学计算，可用于以交互方式缩放任何用户界面元素的通用帮助器类。 以下代码示例演示 `PinchToZoomContainer` 类：

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

此类可以封装用户界面元素，以便收缩手势将缩放预包装的用户界面元素。 以下 XAML 代码示例所示`PinchToZoomContainer`包装[ `Image` ](xref:Xamarin.Forms.Image)元素：

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

下面的代码示例演示如何`PinchToZoomContainer`包装[ `Image` ](xref:Xamarin.Forms.Image) C# 页面中的元素：

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

当[ `Image` ](xref:Xamarin.Forms.Image)元素收到收缩手势时，显示的图像将进行放大加入或退出。由执行缩放`PinchZoomContainer.OnPinchUpdated`方法，在下面的代码示例所示：

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

此方法更新基于用户的收缩手势的预包装的用户界面元素的缩放级别。 这通过使用的值来实现[ `Scale` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale)， [ `ScaleOrigin` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin)并[ `Status` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status)属性[ `PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs)实例来计算 pinch 手势的原始应用的缩放因子。 预包装的用户元素然后放大捏合手势原点通过设置其[ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)， [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)，以及[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)计算出的值的属性。

## <a name="summary"></a>总结

捏合手势用于执行交互式缩放，并且通过实现[ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer)类。


## <a name="related-links"></a>相关链接

- [PinchGesture （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
