---
title: 添加点击手势笔势识别器
description: 点击动作用于 tap 检测，并与 TapGestureRecognizer 类实现。
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: c015ce4b24a1e00b4369a8e98d1381b570557a9c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="adding-a-tap-gesture-gesture-recognizer"></a>添加点击手势笔势识别器

_点击动作用于 tap 检测，并与 TapGestureRecognizer 类实现。_

## <a name="overview"></a>概述

若要使用户界面元素与点击动作可单击，创建[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)实例时，处理[ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/)事件并将添加到新的笔势识别器[`GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/)上的用户界面元素的集合。 下面的代码示例演示`TapGestureRecognizer`附加到[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)元素：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

默认情况下该映像将响应单个分流。 设置[ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/)属性要等待的时间双击 （或点击如果所需的操作）。

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

当[ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/)设置高于 1，事件处理程序将仅执行当次点击发生在一段时间 （此期间不可配置） 内。 如果第二个 （或后续） 点击操作不会发生在该时间段内有效地对它们将被忽略，并点击计数重新启动。

<a name="Using_Xaml" />

## <a name="using-xaml"></a>使用 Xaml

可以将笔势识别器添加到使用附加的属性的 Xaml 中的控件。 若要添加的语法[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)的图像如下所示 (在这种情况下定义*双击*事件):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

事件处理程序 （示例） 中的代码增加了一个计数器和映像从颜色更改为黑色&amp;白色。

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

通常使用 Mvvm 模式的应用程序使用`ICommand`而不是直接方式设置事件处理程序。 [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)可以轻松地支持`ICommand`通过在代码中设置绑定：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

或使用 Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

在此示例中找不到此视图模型的完整代码。 相关`Command`实现详细信息如下所示：

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

点击动作用于 tap 检测并实现与[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)类。 可指定的点击数来识别双击 (或三次点击，或多个点击) 行为。


## <a name="related-links"></a>相关链接

- [TapGesture （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [TapGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)
