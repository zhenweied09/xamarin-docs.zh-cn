---
title: 布局选项
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 8f197bbffeabb708769c48f0130aa27a86b14386
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="layout-options"></a>布局选项

有两个不同的机制，用于控制布局视图进行调整大小或旋转时：

-  **自动调整大小**– 自动调整大小的设计器中的检查器使您能够设置`AutoresizingMask`属性。 这会使控件定位到其容器的边缘和/或修复它们的大小。 自动调整大小适用于 iOS 的所有版本。 此进行了更详细地描述
-  **自动布局**– 允许对 UI 控件的关系细化的控制的 iOS6 中引入的功能。 它还允许相对于其他元素的元素的位置的设计图面上的控件。 本主题更详细地介绍了[使用 Xamarin iOS 设计器的自动数据布局](~/ios/user-interface/designer/designer-auto-layout.md)指南。


## <a name="autosizing"></a>自动调整大小

当用户调整大小时的窗口，如将设备旋转时和方向更改，系统将自动调整根据其自动调整大小规则该窗口中的视图。 可以设置这些规则在 C# 中使用`AutoresizingMask`属性`UIView`或**属性填充**的 ios 设计器中，如下所示：

 [![](layout-options-images/image41.png "Visual Studio for Mac 设计器")](layout-options-images/image41.png#lightbox)

选择一个控件时，这将允许你手动指定的位置和尺寸控件，以及选择**自动调整大小**行为。 如下面的屏幕截图中所示，我们可以使用 springs 和 struts 调整控件中为它的父定义所选的视图的关系：

 [![](layout-options-images/image42.png "Visual Studio for Mac 设计器")](layout-options-images/image42.png#lightbox)

调整*spring*将导致要调整大小的视图基于的宽度或高度的其父视图。 调整*strut*将使视图保持常量本身和其父视图，请在该特定的边缘之间的距离。

此外可以在代码中设置这些设置：

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


若要测试的自动调整大小设置，启用不同**支持设备方向**中项目的选项：

 [![](layout-options-images/image43a.png "自动调整大小设置")](layout-options-images/image43a.png#lightbox)

后面的代码中，我们可以使用下面的代码，这会导致水平调整大小的两个文本框控件：

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


我们还可以调整使用设计器的控件。 选择 struts，如下面表现出将导致要保持而不会被剪裁关闭视图底部的右对齐的图像：

 [![](layout-options-images/autoresize.png "Autorotation")](layout-options-images/autoresize.png#lightbox)

这些屏幕截图显示如何控件调整大小或位置本身转动屏幕时：

 [![](layout-options-images/image44a.png "Autorotation")](layout-options-images/image44a.png#lightbox)

请注意，文本视图和文本字段同时拉伸以使保持不变的保留，并且由于右键边距，`FlexibleWidth`设置。 映像包含的边缘和左边距灵活，这意味着它保留底部和右边距 – 转动屏幕时视图中保留该映像。 复杂布局通常需要每个可见控件上这些设置使用户界面保持一致并防止控件重叠时视图的边界更改 （由于旋转或其他调整大小的事件） 的组合。





## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
