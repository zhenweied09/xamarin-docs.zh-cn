---
title: 滑块、 交换机和在 Xamarin.iOS 的分段的控件
description: 本文档讨论幻灯片、 交换机和 Xamarin.iOS，描述如何处理它们，以编程方式和 iOS 设计器中的分段的控件。
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 09a5d9e76c41eba4e16cab041daa67d3a5d8a584
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790024"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>滑块、 交换机和在 Xamarin.iOS 的分段的控件

<a name="Sliders" />

## <a name="sliders"></a>滑块

滑块控件允许针对简单选择的范围内的数字值。 控件的默认值为 0 和 1 之间的值，但可以自定义这些限制。

 [![](slider-switch-segmented-controls-images/image25a.png "滑块")](slider-switch-segmented-controls-images/image25a.png#lightbox)

以下屏幕截图显示设计器中可编辑的属性：

 [![](slider-switch-segmented-controls-images/image26a.png "滑块属性")](slider-switch-segmented-controls-images/image25a.png#lightbox)

你可以在代码中设置这些值，如下所示，包括方式设置处理程序，以显示中的当前所选的值`UILabel`控件：

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

此外可以自定义设置滑块的可视外观

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

自定义的滑块类似如下所示：

 [![](slider-switch-segmented-controls-images/image27a.png "自定义的滑块")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> 目前尚[bug](http://stackoverflow.com/a/19496179)导致`ThumbTint`不呈现在运行时，按预期方式。 你可以添加以下代码行**之前**上面一种解决方法的代码。 [[源](http://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> 你可以使用任何图像，因为它将被重写，但请确保将其放_中_Resources 目录和在你的代码中调用。

<a name="Switch" />

## <a name="switch"></a>开关

iOS 使用`UISwitch`为 boolean 类型的输入，可能由其他平台上的单选按钮。 用户可以通过移动操作该控件*thumb*之间**开/关**位置。

 [![](slider-switch-segmented-controls-images/image28a.png "交换机")](slider-switch-segmented-controls-images/image28a.png#lightbox)

可以在自定义的开关的外观**属性填充**的设计器中，这会使你能够控制的默认状态，**开/关浅色**颜色和**开/关映像**. 下图所示：

 [![](slider-switch-segmented-controls-images/image29a.png "交换机属性")](slider-switch-segmented-controls-images/image29a.png#lightbox)

此外可以在代码中设置的交换机的属性，如下面的代码中会显示具有的默认值的交换机`On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>分段的控件

分段控件是有序的方式，以允许用户与少量的选项进行交互。 水平布和每个段充当一个单独的按钮。 当使用设计器，可以下找到分段控件**工具箱 > 控件**，并应类似于下图：

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "分段的控件")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

一种独特功能的设计器允许为每个段可单独选择设计图面上，如下所示：

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "分段的控件")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

这使属性键盘，以便可用于更精确地控制每个段的属性。 你可以看到在下面的屏幕截图中可编辑的属性：

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "分段的控件")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

应注意的是 iOS7，未弃用分段控件样式，因此，调整此 iOS7 应用程序中的选项将产生任何影响。

## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
- [警报控制器](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)
