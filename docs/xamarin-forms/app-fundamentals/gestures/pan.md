---
title: 添加平移手势识别器
description: 本文介绍如何使用平移手势到水平和垂直方向平移图像，以便将图像尺寸小于视区中显示时可以查看所有映像内容。
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 59e9f4c61bda86faa5a55d70ef91411adb14da6d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2018
ms.locfileid: "38996801"
---
# <a name="adding-a-pan-gesture-recognizer"></a>添加平移手势识别器

_平移手势用于检测的手指在屏幕上四处移动，并将该移动应用到的内容，并且通过实现`PanGestureRecognizer`类。平移手势的常见方案是水平和垂直方向平移图像，以便将图像尺寸小于视区中显示时可以查看的所有映像内容。这通过移动中位于视区的映像来实现，本文中所示。_

若要使可移动与平移手势的用户界面元素，创建[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)实例，则处理[ `PanUpdated` ](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated)事件，并添加到新的笔势识别器[`GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers)上的用户界面元素的集合。 下面的代码示例演示`PanGestureRecognizer`附加到[ `Image` ](xref:Xamarin.Forms.Image)元素：

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

这也可以在 XAML，如下面的代码示例中所示：

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

有关代码`OnPanUpdated`事件处理程序然后添加到代码隐藏文件：

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> 需要在 Android 上的正确平移[Xamarin.Forms 2.1.0-pre1 NuGet 包](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1)最小值。

## <a name="creating-a-pan-container"></a>创建平移容器

本部分包含的通用帮助器类执行自由格式平移，这通常适用于在映像或映射中导航。 处理平移手势来执行此操作需要某些数学运算来转换用户界面。 此数学用于仅在预包装的用户界面元素的边界内平移。 以下代码示例演示 `PanContainer` 类：

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

此类可以封装用户界面元素，以便手势将平移预包装的用户界面元素。 以下 XAML 代码示例所示`PanContainer`包装[ `Image` ](xref:Xamarin.Forms.Image)元素：

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

下面的代码示例演示如何`PanContainer`包装[ `Image` ](xref:Xamarin.Forms.Image) C# 页面中的元素：

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

在这两个示例中， [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)并[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)属性设置为要显示的图像的宽度和高度值。

当[ `Image` ](xref:Xamarin.Forms.Image)元素收到平移手势时，将素数所显示的图像。 由执行平移`PanContainer.OnPanUpdated`方法，在下面的代码示例所示：

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

此方法更新的预包装的用户界面元素，基于用户的平移手势可查看的内容。 这通过使用的值来实现[ `TotalX` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX)并[ `TotalY` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY)属性的[ `PanUpdatedEventArgs` ](xref:Xamarin.Forms.PanUpdatedEventArgs)实例来计算方向和平移的距离。 `App.ScreenWidth`和`App.ScreenHeight`属性提供的高度和宽度的视区，并通过各自的特定于平台的项目设置为屏幕宽度和设备的屏幕高度值。 通过设置然后素数预包装的用户元素及其[ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)和[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)计算出的值的属性。

当平移中未占据整个屏幕的元素的内容，可以从该元素的获取的高度和宽度的视区[ `Height` ](xref:Xamarin.Forms.VisualElement.Height)并[ `Width` ](xref:Xamarin.Forms.VisualElement.Width)属性。

> [!NOTE]
> 显示高分辨率图像可以极大地提高应用程序的内存占用量。 因此，它们应仅创建所需和应应用不再需要它们时，就立即释放时。 有关详细信息，请参阅[优化图像资源](~/xamarin-forms/deploy-test/performance.md#optimizeimages)。

## <a name="related-links"></a>相关链接

- [PanGesture （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
