---
title: 添加的点击手势手势识别程序
description: 本文介绍如何使用点击手势进行点击检测在 Xamarin.Forms 应用程序中。 点击检测是通过 TapGestureRecognizer 类实现的。
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: e602ae1f140640d9a895b65d78feab3d0a3b7861
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994849"
---
# <a name="adding-a-tap-gesture-gesture-recognizer"></a>添加的点击手势手势识别程序

_点击手势用于点击检测并使用 TapGestureRecognizer 类实现。_

## <a name="overview"></a>概述

若要使用户界面元素与点击手势可单击，创建[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)实例，则处理[ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped)事件，并添加到新的笔势识别器[`GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers)上的用户界面元素的集合。 下面的代码示例演示`TapGestureRecognizer`附加到[ `Image` ](xref:Xamarin.Forms.Image)元素：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

默认情况下该映像将响应单个分流点。 设置[ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired)属性时要等待的双点击 （或更多分流点，如果需要）。

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

当[ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired)设置高于 1，事件处理程序，才会执行如果在一段时间 （这段不是可配置） 内未发生两次点击。 如果在该时间段内不会发生第二个 （或后续） 两次点击有效地忽略它们并点击计数重新启动。

<a name="Using_Xaml" />

## <a name="using-xaml"></a>使用 Xaml

笔势识别器可以添加到 Xaml 中，使用附加的属性中的控件。 若要添加的语法[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)如下所示的图像 (在这种情况下定义*双击*事件):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

（在此示例中） 的事件处理程序的代码递增计数器，并会将映像从颜色更改为黑色&amp;白色。

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>使用 ICommand

通常使用 Mvvm 模式的应用程序使用`ICommand`而不是直接绑定事件处理程序。 [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)可以轻松地支持`ICommand`通过在代码中设置绑定：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

也可以使用 Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

可以在此示例中找到此视图模型的完整代码。 相关`Command`实现的详细信息如下所示：

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```

## <a name="summary"></a>总结

点击手势用于点击检测和使用实现[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)类。 可以指定的点击数来识别双击 (或三击或多个点击) 行为。


## <a name="related-links"></a>相关链接

- [TapGesture （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
