---
title: 使用堆积视图
description: 本文介绍如何设计和在 Xamarin.tvOS 应用内部使用堆积视图。
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: a6300e4da47022199c0503e6be63b0c90f15654d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-stacked-view"></a>使用堆积视图

_本文介绍如何设计和在 Xamarin.tvOS 应用内部使用堆积视图。_


堆栈视图控件 (`UIStackView`) 利用动态响应的内容更改和 Apple TV 设备的屏幕大小自动布局和大小类来管理水平或垂直，某些子视图的堆栈的电源。

附加到堆栈视图的所有子视图的布局受基于如轴、 分发、 对齐和间距的开发人员定义属性：

[![](stacked-views-images/stacked01.png "子视图布局图示")](stacked-views-images/stacked01.png#lightbox)

使用时`UIStackView`在 Xamarin.tvOS 应用中，开发人员可以定义在子视图是在情节提要设计器中，iOS 中或通过添加和删除在 C# 代码中的子视图。

## <a name="about-stacked-view-controls"></a>有关堆积的视图控件 

`UIStackView`旨在作为非呈现容器视图并在这种情况下，不绘制到类似的其他子类画布`UIView`。 设置属性如`BackgroundColor`或重写`DrawRect`将具有任何视觉效果。

有几个属性用于控制如何堆栈视图时，将排列它的子集合：

- **轴**– 确定是否堆栈视图排列子视图或者**水平**或**垂直**。
- **对齐**– 控制在子视图在堆栈视图内的对齐方式。
- **分发**– 控制在子视图堆栈视图中的大小如何。
- **间距**– 控制每个堆栈视图中的子视图之间的最小空间。
- **基线相对**– 如果`true`，将从其基线派生的每个子视图的垂直间距。
- **布局边距相对**– 相对于标准布局边距放置在子视图。

通常将使用堆栈视图可以排列少量的子视图。 可以通过嵌套在每个其他的一个或多个堆栈视图创建更复杂的用户界面。

你可以借此进一步微调 Ui 外观通过将其他约束添加到 （例如到控件的高度或宽度） 的间距。 但是，应格外小心无法包括到那些本身引起堆栈视图的约束冲突。

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>自动布局和大小类

当子视图添加到堆栈视图由使用自动布局和大小类位置和大小排列的视图该堆栈视图完全控制其布局。

堆栈视图将_pin_到其集合中的第一个和最后一个子视图**顶部**和**底部**垂直堆栈视图的边缘或**左**和**右**水平堆栈视图的边缘。 如果你设置`LayoutMarginsRelativeArrangement`属性`true`，则该视图会固定到而不是边缘的相关边距的间距。

堆栈视图使用子视图的`IntrinsicContentSize`属性时计算沿定义的子视图大小`Axis`(除`FillEqually Distribution`)。 `FillEqually Distribution`调整大小的所有子视图，以便它们的大小，因此填充堆栈视图沿`Axis`。

除`Fill Alignment`，堆栈视图使用子视图的`IntrinsicContentSize`属性计算视图的大小时垂直于给定`Axis`。 有关`Fill Alignment`，调整大小的所有子视图，以便它们填充堆栈视图垂直于给定`Axis`。

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>位置和大小堆栈视图

堆栈视图具有完全控制权的任何子视图布局时 (如根据属性`Axis`和`Distribution`)，你仍需要定位堆栈视图 (`UIStackView`) 在其父视图中使用自动布局和大小类。

通常，这意味着固定的堆栈视图以进行扩展和收缩，从而定义其位置的至少两个边缘。 如果没有任何其他约束，堆栈视图将自动调整大小以适应所有其子视图，如下所示：

* 沿大小其`Axis`将所有子视图大小以及任何已定义每个子视图之间的空间的总和。
* 如果`LayoutMarginsRelativeArrangement`属性是`true`，堆栈视图大小还将包括为边距留出空间。
* 与垂直大小`Axis`将设置为集合中最大的子视图。

此外，可以指定约束堆栈视图**高度**和**宽度**。 在这种情况下，在子视图将布局 （大小） 来填充指定的堆栈视图所确定的那样的空间`Distribution`和`Alignment`属性。

如果`BaselineRelativeArrangement`属性是`true`，在子视图将布局基于第一个或最后一个子视图的基线，而不是使用**顶部**，**底部**或 **Center*-  **Y**位置。 这些计算堆栈视图的内容，如下所示：

* 垂直堆栈视图将返回第一个基线第一个子视图和最后的最后一个。 如果任何一种这些子视图本身堆栈视图，则将使用其第一个或最后一个基线。
* 第一个和最后一个基线，水平堆栈视图将使用其最高的子视图。 如果最高的视图也是堆栈视图，它将作为基线来使用最高子视图。

> [!IMPORTANT]
> 基线对齐无效拉伸或压缩子视图大小基线将计算到错误的位置。 对于基线对齐方式，请确保子视图的**高度**匹配内部函数的内容视图的**高度**。




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>堆栈视图的常见

有几种很好地配合堆栈视图控件的布局类型。 根据 Apple，下面是几种更常见的用法：

- **定义轴大小沿**– 通过固定沿堆栈视图的两个边缘`Axis`，另一个相邻的边缘，若要设置的位置，视图将会增长沿轴以适应其子视图所定义的空间堆栈。
*  **定义子视图的位置**– 这两个维度，以适合包含子视图通过固定到该堆栈视图与父视图的相邻边缘，堆栈视图将会增长。
- **定义的大小和位置堆栈的**– 通过固定到父视图堆栈视图的所有四个边缘，堆栈视图排列基于堆栈视图中定义的空间的间距。
*  **定义大小垂直轴**– 通过固定堆栈视图的这两个边缘垂直`Axis`，另一个要设置的位置，视图将会增长到轴以适应所定义的空间垂直堆栈的轴沿边缘其子视图。

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>堆栈视图和情节提要

使用 Xamarin.tvOS 应用中的堆栈视图的最简单方法是将它们添加到使用 iOS 设计器的应用程序的 UI。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**解决方案 Pad**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 设计你想要将添加到堆栈视图的各个元素的布局： 

    [![](stacked-views-images/layout01.png "元素的布局示例")](stacked-views-images/layout01.png#lightbox)
1. 将任何所需的约束添加到的元素，以确保它们可正确缩放。 该元素已添加到堆栈视图后，此步骤非常重要。
1. 进行所需的数目的副本 （在此情况下需要四个）： 

    [![](stacked-views-images/layout02.png "所需的数目的副本")](stacked-views-images/layout02.png#lightbox)
1. 拖动**堆栈视图**从**工具箱**并将其放在视图上： 

    [![](stacked-views-images/layout03.png "堆栈视图")](stacked-views-images/layout03.png#lightbox)
1. 堆栈视图中，选择在**小组件选项卡**的**属性填充**选择**填充**为**对齐**，**填充同样**为**分发**并输入`25`为**间距**: 

    [![](stacked-views-images/layout04.png "小组件选项卡")](stacked-views-images/layout04.png#lightbox)
1. 定位堆栈视图屏幕位置你希望它，然后添加约束，以将其保存在所需的位置上。
1. 选择的单个元素，并将其拖到堆栈视图： 

    [![](stacked-views-images/layout05.png "堆栈视图中的各个元素")](stacked-views-images/layout05.png#lightbox)
1. 将调整布局并将根据上设置的属性的堆栈视图中排列元素。
1. 分配**名称**中**小组件选项卡**的**属性资源管理器**用于 UI 控件在 C# 代码。
1. 保存更改。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 设计你想要将添加到堆栈视图的各个元素的布局： 

    [![](stacked-views-images/layout01.png "元素布局示例")](stacked-views-images/layout01.png#lightbox)
1. 将任何所需的约束添加到的元素，以确保它们可正确缩放。 该元素已添加到堆栈视图后，此步骤非常重要。
1. 进行所需的数目的副本 （在此情况下需要四个）： 

    [![](stacked-views-images/layout02.png "所需的数目的副本")](stacked-views-images/layout02.png#lightbox)
1. 拖动**堆栈视图**从**工具箱**并将其放在视图上： 

    [![](stacked-views-images/layout03-vs.png "堆栈视图")](stacked-views-images/layout03-vs.png#lightbox)
1. 堆栈视图中，选择在**小组件选项卡**的**属性资源管理器**选择**填充**为**对齐**，**填充同样**为**分发**并输入`25`为**间距**: 

    [![](stacked-views-images/layout04-vs.png "小组件选项卡")](stacked-views-images/layout04-vs.png#lightbox)
1. 定位堆栈视图屏幕位置你希望它，然后添加约束，以将其保存在所需的位置上。
1. 选择的单个元素，并将其拖到堆栈视图： 

    [![](stacked-views-images/layout05-vs.png "堆栈视图中的各个元素")](stacked-views-images/layout05-vs.png#lightbox)
1. 将调整布局并将根据上设置的属性的堆栈视图中排列元素。
1. 分配**名称**中**小组件选项卡**的**属性资源管理器**用于 UI 控件在 C# 代码。
1. 保存更改。

-----

> [!IMPORTANT]
> 虽然可能如分配操作，但`TouchUpInside`到 UI 元素 (如`UIButton`) 中的 iOS 设计器创建事件处理程序时，它将永远不会调用因为 Apple TV 没有触摸屏幕或支持触控事件。 你应始终使用默认值`Action Type`针对 tvOS 用户界面元素创建操作时。

有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

对于本示例中，我们已公开 Outlet，段控件的操作，并为每个"播放器卡"上的电源插座。 在代码中，我们隐藏和显示基于当前的段的播放器。 例如：

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

当应用运行时，四个元素将均等地分发在我们堆栈视图中：

[![](stacked-views-images/layout06.png "当应用运行时，四个元素将均等地分发在我们堆栈视图中")](stacked-views-images/layout06.png#lightbox)

如果玩家人数会降低，未使用的视图隐藏的堆栈视图调整布局，以适合：

[![](stacked-views-images/layout07.png "如果玩家人数会降低，未使用的视图隐藏的堆栈视图调整布局，以适合")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>填充从代码的堆栈视图

除了完全 iOS 设计器中定义的内容和堆栈视图的布局，你可以创建和从 C# 代码中动态移除。

执行下面的示例中使用堆栈视图来处理评审 (1 到 5) 中的"星号":

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

让我们看看此代码的几个部分中详细信息。 首先，我们使用`if`语句以检查不能超过五个"星号"或小于零。

若要添加新的"星型"我们加载其图像并设置其**内容模式**到**缩放方面适合**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

这可防止"星号"图标添加到堆栈视图时正在失真。

接下来，我们将新的"星号"图标添加到堆栈视图的集合的子视图：

```csharp
RatingView.AddArrangedSubview(icon);
```

你会注意到我们添加`UIImageView`到`UIStackView`的`ArrangedSubviews`属性并不适用于`SubView`。 必须将你想要控制其布局的堆栈视图任何视图添加到`ArrangedSubviews`属性。

若要从堆栈视图中删除子视图，首先我们获取子视图删除：

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

然后，我们需要将其从同时删除`ArrangedSubviews`集合和超级视图：

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

只需从删除子视图`ArrangedSubviews`集合采用出堆栈视图的控件，但不会从屏幕删除它。

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>动态地更改内容

添加、 删除或隐藏子视图时，堆栈视图将自动调整在子视图的布局。 如果调整堆栈视图的任何属性，也进行调整的布局 (如其`Axis`)。

可以通过例如将它们放置在动画块中，进行动画处理的布局更改：

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

可以使用情节提要中的大小类指定许多堆栈视图的属性。 这些属性将自动进行动画处理是响应的大小或方向更改。

<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖设计和在 Xamarin.tvOS 应用内部使用堆积视图。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
