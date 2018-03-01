---
title: "使用屏幕大小"
ms.topic: article
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: 2bb42a862ae8d9fb4c94a044542700dbc324f35b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-screen-sizes"></a>使用屏幕大小

矩形或圆角显示，也可以是不同的大小，可将 android 磨损设备。

![矩形的圆磨损的屏幕快照显示](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>标识屏幕类型

磨损支持库提供帮助你的某些控件检测和适应不同的屏幕形状，如`WatchViewStub`和`BoxInsetLayout`。

请注意，某些其他支持库控件 (如`GridViewPager`)*自动*检测屏幕形状本身并不应按如下所述的控件的子级添加。

### <a name="watchviewstub"></a>WatchViewStub

请参阅[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)示例以了解如何检测屏幕类型并显示每种类型的不同布局。

主布局文件包含`android.support.wearable.view.WatchViewStub`引用不同的布局矩形和 round 界面使用`app:rectLayout`和`app:roundLayout`属性：

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

解决方案包含每个样式，它将在运行时选择不同的布局：

![资源/布局下方显示的文件](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

而不是生成每个屏幕类型的不同布局，你还可以创建适合于矩形或圆屏幕的单一视图。

这[Google 示例](https://developer.android.com/training/wearables/ui/layouts.html#same-layout)演示如何使用`BoxInsetLayout`在矩形和 round 屏幕上使用相同的布局。


## <a name="wear-ui-designer"></a>带用户界面设计器

Xamarin Android 设计器支持矩形和 round 屏幕：

![在 Xamarin Android 设计器中选择 Android 磨损正方形屏幕](screen-sizes-images/design-screen-type.png)

矩形样式中的设计图面如下所示：

![矩形样式中的设计图面](screen-sizes-images/design-rect.png) 

Round 样式中的设计图面如下所示：

![设计图面中舍入的样式](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>带模拟器

**Google 仿真程序管理器**包含设备定义为同时屏幕类型。 你可以创建矩形和 round 仿真程序测试你的应用程序。

![带 Google 仿真程序管理器中显示的设备定义](screen-sizes-images/emulator-devices.png)

仿真程序将呈现矩形屏幕如下：

![仿真程序呈现的矩形的屏幕](screen-sizes-images/recipe-2.png) 

舍入屏幕，它会呈现如下：

![仿真程序呈现的舍入屏幕](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>视频

[全屏应用的 Android 损耗](https://www.youtube.com/watch?v=naf_WbtFAlY)从[developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw)。

