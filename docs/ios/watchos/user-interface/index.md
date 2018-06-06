---
title: watchOS Xamarin 中的用户界面控件
description: 本文档描述了可用于在 watchOS 用户界面中使用的各种控件。 它提供的标签、 按钮、 交换机、 滑块、 图像、 分隔符，图、 和的详细信息的说明。
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/19/2016
ms.openlocfilehash: b56cfed8f045d824996a004539533b27d66c8cb1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791405"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>watchOS Xamarin 中的用户界面控件

[ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog)示例演示各种 watchOS 控件。 应用程序的情节提要如下所示 （单击来缩放）：

[![](images/storyboard-sml.png "示例 watchOS 布局")](images/storyboard.png#lightbox)

所有控件的编程名称的前面带`WKInterface`（如。 `WKInterfaceLabel`, `WKInterfaceButton`).

|控件|描述|屏幕快照|
|---|---|---|
|Label|使用`SetText`和其他属性，以控制标签控件中文本的外观。 `NSAttributedString` 此外支持。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Button|创建并在情节提要中设置属性。 Ctrl + 拖动添加`Action`以实现单击时的处理。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|开关|使用`SetOn`控制交换机状态。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Slider|可能会出现许多不同的样式。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|图像|使用`myImage.SetImage("MyWatchImage")`以加载图像上监视，或`WKInterfaceDevice.CurrentDevice.AddCachedImage`缓存它们为在手表上重复使用。<br />[图像控件文档](~/ios/watchos/user-interface/image.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separator|使用分隔符来帮助创建具吸引力监视 Ui。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|映射|地图图像静态显示受监视，但你可以控制其外观，包括添加 pin 的许多方面。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|电影和 InlineMove|电影可以保持打开状态在他们自己或内联<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Group|使用组来帮助创建具吸引力监视 Ui。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|表|在 iOS 上的表的简化的版本。 实现`DidSelectRow`以响应用户选择 （或使用 segue）。<br />[表控件文档](~/ios/watchos/user-interface/table.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|设备|`WKInterfaceDevice.CurrentDevice` 包括属性，如`ScreenBounds`， `ScreenScale`，和`PreferredContentSizeCategory`。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[菜单](~/ios/watchos/user-interface/menu.md)|在情节提要中定义的强制按菜单并在代码中实现的每个按钮的操作。<br />[菜单控件 (强制 Touch) 文档](~/ios/watchos/user-interface/menu.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|文本输入|使用`PresentTextInputController`和`WKTextInputMode`枚举。<br />[文本输入文档](~/ios/watchos/user-interface/text-input.md)<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|数字王冠|数字王冠可以用于驱动器选取器，或它的旋转可以在代码中进行跟踪。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|笔势|有四种类型的笔势识别可以添加到一个场景： Tap、 轻扫、 平移和 LongPress。<br />[目录代码](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [监视工具包 API 参考](https://developer.xamarin.com/api/namespace/WatchKit/)
