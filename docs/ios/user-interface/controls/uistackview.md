---
title: 堆栈视图
description: 本文介绍如何使用 Xamarin.iOS 应用程序中的新 UIStackView 控件管理一组的子视图中水平或垂直排列的堆栈。
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: aec1c4ceb562f528d6bcef7e4de0f2708952d2a5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="stack-view"></a>堆栈视图

_本文介绍如何使用 Xamarin.iOS 应用程序中的新 UIStackView 控件管理一组的子视图中水平或垂直排列的堆栈。_

> [!IMPORTANT]
> 请注意，尽管 StackView 支持 iOS 设计器中，你可能会遇到可用性 bug 时使用稳定的通道。 切换 Beta 或 Alpha 通道应当能缓解此问题。 我们已决定提供本演练中，使用 Xcode，直到在稳定的通道中实现所需的修补程序。

堆栈视图控件 (`UIStackView`) 利用动态响应 iOS 设备的方向和屏幕大小自动布局和大小类来管理水平或垂直，某些子视图的堆栈的电源。

附加到堆栈视图的所有子视图的布局受基于如轴、 分发、 对齐和间距的开发人员定义属性：

[![](uistackview-images/stacked01.png "堆栈视图布局关系图")](uistackview-images/stacked01.png#lightbox)

使用时`UIStackView`在 Xamarin.iOS 应用中，开发人员可以定义在子视图是在情节提要设计器中，iOS 中或通过添加和删除在 C# 代码中的子视图。

本文档由两部分组成： 可帮助的实现你的第一个堆栈查看，然后一些有关它的工作原理的更多技术细节的快速入门。

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView，也可由[Xamarin 大学](https://university.xamarin.com/)**

## <a name="uistackview-quickstart"></a>UIStackView 快速入门

作为的快速介绍`UIStackView`控件，我们将要创建一个简单接口，允许用户输入从 1 到 5 的级别。 我们将使用两个堆栈视图： 一个用于排列垂直在设备的屏幕，另一个用于在屏幕水平排列 1-5 分级图标上的接口。

### <a name="define-the-ui"></a>定义用户界面

启动新的 Xamarin.iOS 项目和编辑**Main.storyboard**在 Xcode 的接口生成器中的文件。 首先，将单个**垂直堆栈视图**上**视图控制器**:

[![](uistackview-images/quick01.png "将视图控制器中单个垂直堆栈视图拖动")](uistackview-images/quick01.png#lightbox)

在**属性检查器**，设置以下选项：

[![](uistackview-images/quick02.png "设置堆栈视图选项")](uistackview-images/quick02.png#lightbox)

其中：

- **轴**– 确定是否堆栈视图排列子视图或者**水平**或**垂直**。
- **对齐**– 控制在子视图在堆栈视图内的对齐方式。
- **分发**– 控制在子视图堆栈视图中的大小如何。
- **间距**– 控制每个堆栈视图中的子视图之间的最小空间。
- **基线相对**– 如果选中，将从其基线派生的每个子视图的垂直间距。
- **布局边距相对**– 相对于标准布局边距放置在子视图。

当使用堆栈视图，你可以将**对齐**作为**X**和**Y**子视图的位置和**分发**为**高度**和**宽度**。

> [!IMPORTANT]
> `UIStackView` 旨在作为非呈现容器视图并在这种情况下，不绘制到类似的其他子类画布`UIView`。 因此设置属性如`BackgroundColor`或重写`DrawRect`将具有任何视觉效果。

继续到布局加上标签、 ImageView、 两个按钮和水平堆栈视图，使它类似于以下应用程序的接口：

[![](uistackview-images/quick03.png "布局堆栈视图用户界面")](uistackview-images/quick03.png#lightbox)

使用以下选项配置水平堆栈视图：

[![](uistackview-images/quick04.png "配置水平堆栈视图选项")](uistackview-images/quick04.png#lightbox)

由于我们不希望分级扩展中的图标表示每个"点"时它被添加到水平的堆栈视图中，我们已设置**对齐**到**Center**和**分发**到**同样填充**。

最后，连接以下**Outlet**和**操作**:

[![](uistackview-images/quick05.png "堆栈视图插座和操作")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>填充从代码 UIStackView

返回到 Visual Studio 针对 Mac 和编辑**ViewController.cs**文件并添加以下代码：

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

### <a name="testing-the-ui"></a>测试 UI

与所有必需的 UI 元素和就地的代码，现在可以运行并测试该接口。 时显示的用户界面，所有垂直堆栈视图中的元素将是均等间距分布从上到下。

当用户点击**提升评级**按钮，另一个"星型"添加到屏幕 （最多 5）：

[![](uistackview-images/intro01.png "运行示例应用程序")](uistackview-images/intro01.png#lightbox)

"星号"将自动居中和均等地分发的水平堆栈视图中。 当用户点击**降低评级**按钮，"星型"则会删除 （直到无处于）。

## <a name="stack-view-details"></a>堆栈查看详细信息

现在，我们已经大致了解什么`UIStackView`控件的本质和它的工作原理，让我们深入了解一下它的一些功能并详细信息。

### <a name="auto-layout-and-size-classes"></a>自动布局和大小类

正如我们所看到上面，子视图添加到时堆栈视图其布局由使用自动布局和大小类位置和大小排列的视图该堆栈视图完全控制。

堆栈视图将_pin_到其集合中的第一个和最后一个子视图**顶部**和**底部**垂直堆栈视图的边缘或**左**和**右**水平堆栈视图的边缘。 如果你设置`LayoutMarginsRelativeArrangement`属性`true`，则该视图会固定到而不是边缘的相关边距的间距。

堆栈视图使用子视图的`IntrinsicContentSize`属性时计算沿定义的子视图大小`Axis`(除`FillEqually Distribution`)。 `FillEqually Distribution`调整大小的所有子视图，以便它们的大小，因此填充堆栈视图沿`Axis`。

除`Fill Alignment`，堆栈视图使用子视图的`IntrinsicContentSize`属性计算视图的大小时垂直于给定`Axis`。 有关`Fill Alignment`，调整大小的所有子视图，以便它们填充堆栈视图垂直于给定`Axis`。

### <a name="positioning-and-sizing-the-stack-view"></a>位置和大小堆栈视图

堆栈视图具有完全控制权的任何子视图布局时 (如根据属性`Axis`和`Distribution`)，你仍需要定位堆栈视图 (`UIStackView`) 在其父视图中使用自动布局和大小类。

通常，这意味着固定的堆栈视图以进行扩展和收缩，从而定义其位置的至少两个边缘。 如果没有任何其他约束，堆栈视图将自动调整大小以适应所有其子视图，如下所示：

 - 沿大小其`Axis`将所有子视图大小以及任何已定义每个子视图之间的空间的总和。
 - 如果`LayoutMarginsRelativeArrangement`属性是`true`，堆栈视图大小还将包括为边距留出空间。
 - 与垂直大小`Axis`将设置为集合中最大的子视图。

此外，可以指定约束堆栈视图**高度**和**宽度**。 在这种情况下，在子视图将布局 （大小） 来填充指定的堆栈视图所确定的那样的空间`Distribution`和`Alignment`属性。

如果`BaselineRelativeArrangement`属性是`true`，在子视图将布局基于第一个或最后一个子视图的基线，而不是使用**顶部**，**底部**或 **Center*-  **Y**位置。 这些计算堆栈视图的内容，如下所示：

 - 垂直堆栈视图将返回第一个基线第一个子视图和最后的最后一个。 如果任何一种这些子视图本身堆栈视图，则将使用其第一个或最后一个基线。
 - 第一个和最后一个基线，水平堆栈视图将使用其最高的子视图。 如果最高的视图也是堆栈视图，它将作为基线来使用最高子视图。

> [!IMPORTANT]
> 基线对齐无效拉伸或压缩子视图大小基线将计算到错误的位置。 对于基线对齐方式，请确保子视图的**高度**匹配内部函数的内容视图的**高度**。

### <a name="common-stack-view-uses"></a>堆栈视图的常见

有几种很好地配合堆栈视图控件的布局类型。 根据 Apple，下面是几种更常见的用法：

- **定义轴大小沿**– 通过固定沿堆栈视图的两个边缘`Axis`，另一个相邻的边缘，若要设置的位置，视图将会增长沿轴以适应其子视图所定义的空间堆栈。
 -  **定义子视图的位置**– 这两个维度，以适合包含子视图通过固定到该堆栈视图与父视图的相邻边缘，堆栈视图将会增长。
- **定义的大小和位置堆栈的**– 通过固定到父视图堆栈视图的所有四个边缘，堆栈视图排列基于堆栈视图中定义的空间的间距。
 -  **定义大小垂直轴**– 通过固定堆栈视图的这两个边缘垂直`Axis`，另一个要设置的位置，视图将会增长到轴以适应所定义的空间垂直堆栈的轴沿边缘其子视图。

### <a name="managing-the-appearance"></a>管理外观

`UIStackView`旨在作为非呈现容器视图并在这种情况下，不绘制到类似的其他子类画布`UIView`。 设置属性如`BackgroundColor`或重写`DrawRect`将具有任何视觉效果。

有几个属性用于控制如何堆栈视图时，将排列它的子集合：

- **轴**– 确定是否堆栈视图排列子视图或者**水平**或**垂直**。
- **对齐**– 控制在子视图在堆栈视图内的对齐方式。
- **分发**– 控制在子视图堆栈视图中的大小如何。
- **间距**– 控制每个堆栈视图中的子视图之间的最小空间。
- **基线相对**– 如果`true`，将从其基线派生的每个子视图的垂直间距。
- **布局边距相对**– 相对于标准布局边距放置在子视图。

通常将使用堆栈视图可以排列少量的子视图。 可以通过嵌套在每个其他的一个或多个堆栈视图创建更复杂的用户界面 (正如我们做[UIStackView 快速入门](#UIStackView-Quickstart)上面)。

你可以借此进一步微调 Ui 外观通过将其他约束添加到 （例如到控件的高度或宽度） 的间距。 但是，应格外小心无法包括到那些本身引起堆栈视图的约束冲突。

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>维护方式排列视图以及子视图一致性

堆栈视图将确保其`ArrangedSubviews`属性始终是的子集其`Subviews`属性使用以下规则：

 - 如果子视图添加到`ArrangedSubviews`集合，它将自动添加到`Subviews`集合 （除非它已是该集合的一部分）。
 - 如果从删除子视图`Subviews`（从显示中删除） 的集合，它也会从删除`ArrangedSubviews`集合。
 - 删除从子视图`ArrangedSubviews`集合不会删除从`Subviews`集合。 因此，它将不再布局堆栈视图中，但仍将在屏幕上可见。

`ArrangedSubviews`集合始终是的子集`Subview`集合，但是每个集合中的单个间距的顺序是分开的并且由以下控制：

 - 在子视图中的顺序`ArrangedSubviews`集合确定其在堆栈中的显示顺序。
 - 在子视图中的顺序`Subview`回收后至前视图中确定其 Z 顺序 （或分层）。

### <a name="dynamically-changing-content"></a>动态地更改内容

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

## <a name="summary"></a>总结

本文已覆盖新`UIStackView`控制 （适用于 iOS 9) 来管理一套 Xamarin.iOS 应用程序中的任一水平或垂直排列堆栈中的子视图。
它与使用堆栈视图来创建 UI 的一个简单的示例开始，便完成了更详细地了解在堆栈视图及其属性和功能。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [为开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [什么是在 iOS 9.0 新增](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [UIStackView 引用](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [引入 UIStackView （视频）](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
