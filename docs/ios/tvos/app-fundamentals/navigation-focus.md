---
title: "使用导航和焦点"
description: "本文介绍如何焦点以及如何使用它为在 Xamarin.tvOS 应用内部处理导航和呈现的概念。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 82151599b92094b816f4763c533ed7746db37920
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-navigation-and-focus"></a>使用导航和焦点

_本文介绍如何焦点以及如何使用它为在 Xamarin.tvOS 应用内部处理导航和呈现的概念。_


本文介绍的概念[焦点](#Focus-and-Selection)以及如何使用它来处理[导航](#Navigation)Xamarin.tvOS 应用程序的用户界面中。 我们将检查内置 tvOS 导航控件如何使用焦点、 高亮和选择提供 Xamarin.tvOS 应用程序的用户界面导航。

[ ![](navigation-focus-images/intro01.png "tvOS 应用用户界面导航")](navigation-focus-images/intro01.png)

接下来，我们来看看如何与使用焦点[视差](#Focus-and-Parallax)和*分层映像*可为当前的导航状态提供视觉线索，向最终用户。

最后，我们将看一下使用[焦点](#Working-with-Focus)，[焦点更新](#Working-with-Focus-Updates)，[焦点指南](#Working-with-Focus-Guides)，[集合中的焦点](#Working-with-Focus-in-Collections)和[启用视差](#Enabling-Parallax)Xamarin.tvOS 应用中的图像视图。

<a name="Navigation" />

## <a name="navigation"></a>导航

Xamarin.tvOS 应用程序的用户将不与它的接口直接作为 ios 其中他们点击设备的屏幕上的映像但间接从跨聊天室使用交互[Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)。 你将需要设计应用程序的用户界面，以便它流动当然，但保留用户深陷于 Apple TV 体验时记住这一点。

成功 tvOS 应用平稳支持应用程序的用途以及它提供而不会调用注意导航本身的数据结构的方式实现导航。 设计你导航，使它而无需占据用户界面或绘制焦点离开内容和应用程序用户体验的外观自然和熟悉程度。

[ ![](navigation-focus-images/nav01.png "TvOS 设置应用程序")](navigation-focus-images/nav01.png)

虽然通常使用 Apple TV，用户将转到堆积的一组屏幕，每个提供一组给定的内容。 反过来，每个新的屏幕可能会导致使用如的标准 UI 控件的内容的一个或多个子屏幕[按钮](~/ios/tvos/user-interface/buttons.md)，[选项卡条](~/ios/tvos/user-interface/tab-bars.md)，表，[集合视图](~/ios/tvos/user-interface/collection-views.md)或[拆分视图](~/ios/tvos/user-interface/split-views.md)。

与数据的每个新屏幕上，用户导航更深入到此堆栈的屏幕。 通过使用**菜单**按钮上使用 Siri 远程，它们可以向后导航整个堆栈，以返回到之前的屏幕或主菜单中。

Apple 提供的建议设计应用程序 tvOS 导航时记住以下：

- **布局你导航到快速进行查找的内容且易于**-最小数目的 tap，你的应用程序中的访问内容的用户需要单击和尽可能刷。 简化你导航和组织屏幕的最小数字的内容。
- **创建流体接口使用 Touch** -确保用户可以之间移动_可获得焦点的项_与使用手势可能的最小数目的最小问题。
- **具有焦点记住设计**-由于用户在同一房间与内容交互，它们需要在与它使用 Siri 远程交互之前将焦点移到用户界面项。 如果它要求他们实现其目标的太多手势，用户将获得沮丧与你的应用。
- **提供向后导航菜单按钮通过**-若要创建的简单且熟悉的体验，允许用户向后使用 Siri 远程导航**菜单**按钮。 按**菜单**按钮应始终返回到之前的屏幕或返回到应用程序的主菜单中。 在应用程序的顶部级别，按**菜单**按钮应返回到 Apple 电视主页屏幕。
- **通常避免显示后退按钮**-因为按**菜单**上使用 Siri 远程按钮屏幕堆栈中向后导航，避免显示额外的控制，它将复制此行为。 此规则的唯一例外是购买屏幕或破坏性操作 （如删除内容） 的屏幕位置**取消**应也显示按钮。
- **显示在单个屏幕上，而不是许多大型集合**-Siri 远程旨在使滚动的内容快速的大型集合并轻松使用手势。 如果你的应用程序处理的可获得焦点的项的大型集合，请考虑将它们放在单个屏幕而不是中断到许多屏幕这需要对用户的一部分的多个导航。
- **为 Navigation 使用标准控件**-再次，若要创建的简单且熟悉的用户体验，只要有可能，请使用内置`UIKit`控件如页面控件、 选项卡条、 分段控件、 表视图、 集合视图和拆分你的应用导航的视图。 用户已熟悉这些元素，因为它们直观地将能够在你的应用程序。
- **倾向于水平内容导航**-由于 Apple TV 的性质，轻扫从左到右上使用 Siri 远程是比更自然高和调低。 设计内容布局为你的应用时，请考虑此选项。

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>焦点和所选内容

在 Apple TV，图像、 按钮或其他 UI 元素被视为可_焦点_时当前导航的目标。

[ ![](navigation-focus-images/focus01.png "焦点和选择示例")](navigation-focus-images/focus01.png)

与不同，在用户与设备的触摸屏上的元素的直接交互其中的 iOS 设备在同一房间使用 Siri 远程与从 tvOS 元素进行交互的用户。 若要显示和处理这种用户交互，Apple TV 使用_焦点_基于模型。

使用手势和按钮按下[Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)，用户将焦点从一个用户界面元素移到另一个。 一旦焦点已移动到需要的元素，用户可单击以选择的内容或激活该元素所表示的操作。

焦点发生变动，作为使用细微动画和 （如对映像视差影响） 的效果清楚地将标识当前具有焦点的用户界面项。

Apple 具有用于处理焦点和选择以下建议：

- **使用内置 UI 控件时动作效果**-通过使用`UIKit`和你的用户界面，焦点模型中的焦点 API 将自动应用的默认运动和视觉效果到你的 UI 元素。 这使得你应用感觉本机和熟悉的 Apple TV 平台的用户，并允许可获得焦点的项之间的流畅和直观移动。
- **将焦点移预期双向**-上 Apple TV 中，几乎每个元素使用间接操作。 例如，用户使用 Siri 远程要将焦点移并系统自动滚动要保持当前具有焦点的项可见的接口。 如果你的应用程序实现这种类型的交互，请确保焦点上移开，按用户的手势的方向。 因此，如果用户轻扫权限 Siri 远程焦点应移动到右侧 （这会导致向左滚动屏幕）。 此规则的一个例外是使用直接操作 （其中向上轻扫移元素） 的全屏项。
- **确保已设定焦点的项是 Obvious** -因为你的用户与你的 UI 元素 £ ¬ 交互，很重要代表当前具有焦点的项。通常这将自动由处理内置`UIKit`元素。 对于自定义控件，使用功能，如项大小或卷影显示焦点。
- **使用到使已设定焦点的项响应视差**-小上使用 Siri 远程, 的循环笔势导致平缓、 实时移动的已设定焦点的项。 这[视差效果](#Focus-and-Parallax)内置于`UIKit`_分层映像_授予连接到已设定焦点的项的意义上的用户。
- **创建的相应大小的焦点项**-具有充足的间距的大型项是更轻松地选择和导航比较小的项。
- **设计用户界面元素到查找良好是已设定焦点或 Unfocused** -通常 Apple TV 通过增加其大小表示已设定焦点的项。 确保你的应用程序的 UI 元素查找适合于任何演示文稿大小和，必要情况下，提供更大大小的元素的资产。
- **表示必须焦点更改**-使用动画可顺利淡入淡出项之间**已设定焦点**和**Unfocused**需要快从正在转换的状态。
- **不会正确显示游标**-用户希望导航应用程序的 UI 使用焦点而不是移动在屏幕的游标。 你的用户界面应始终使用焦点模型以提供一致的用户体验。

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>使用焦点

可能存在你想要创建自定义控件可能会成为可获得焦点的项的时间。 如果因此重写`CanBecomeFocused`属性，并返回`true`，否则返回`false`。 例如:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

在任何时候，你可以使用`Focused`属性`UIKit`控件以查看其是否当前项。 如果`true`UI 项当前具有焦点，否则不。 例如:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

尽管通过代码，你不能直接将焦点移到另一个用户界面元素，可以指定哪些用户界面元素首先获得焦点时屏幕加载通过设置其`PreferredFocusedView`属性`true`。 例如:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>使用焦点更新 

当用户导致焦点从一个用户界面元素移动到另一个 （例如，在响应上使用 Siri 远程笔势）_焦点更新事件_将发送到失去焦点的项和获得焦点的项。

继承的自定义元素`UIView`或`UIViewController`，您可以重写以处理焦点更新事件的多个方法：

- **DidUpdateFocus** -视图获得或失去焦点的任何时间都将调用此方法。
- **ShouldUpdateFocus** -此方法用于定义允许其中移动焦点。

若要请求，焦点引擎移动焦点回`PreferredFocusedView`UI 元素，调用`SetNeedsUpdateFocus`视图控制器的方法。

> [!IMPORTANT]
> **注意：**调用`SetNeedsUpdateFocus`仅当针对调用视图控制器包含当前具有焦点的视图不起作用。




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>使用焦点指南

内置于 tvOS 的焦点引擎是很好地处理落在水平和垂直网格的项之间的动作数。 通常情况下，你需要执行任何操作，多个添加到接口设计和焦点引擎的 UI 元素将自动处理这些元素，而开发人员介入无之间移动。

但是，可能有时间，由于您的用户界面设计、 必需品时 UI 元素不会在水平和垂直网格上和可能无法访问，因为它们是彼此对角线。 这是因为焦点引擎被设计为处理简单向上、 向下，仅限 UI 项之间的左和向右移动。

执行示例的以下 UI 布局：

 [ ![](navigation-focus-images/guide01.png "使用焦点指南示例")](navigation-focus-images/guide01.png)
 
因为**详细信息**按钮不在上水平和垂直网格与**购买**按钮将用户都无法访问。 但是，这可以轻松地使用更正_焦点指南_向焦点引擎提供移动提示。 

焦点指南 (`UIFocusGuide`) 将作为到焦点引擎，从而允许重定向到另一个视图的焦点 Focusable 公开非可见区域的视图。

因此，若要解决上述示例中，下面的代码无法添加到视图控制器创建之间焦点指南**详细信息**和**购买**按钮：

```csharp
public UIFocusGuide FocusGuide = new UIFocusGuide ();
...

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Add Focus Guide to layout
    View.AddLayoutGuide (FocusGuide);

    // Define Focus Guide that will allow the user to move
    // between the More Info and Buy buttons.
    FocusGuide.LeftAnchor.ConstraintEqualTo (BuyButton.LeftAnchor).Active = true;
    FocusGuide.TopAnchor.ConstraintEqualTo (MoreInfoButton.TopAnchor).Active = true;
    FocusGuide.WidthAnchor.ConstraintEqualTo (BuyButton.WidthAnchor).Active = true;
    FocusGuide.HeightAnchor.ConstraintEqualTo (MoreInfoButton.HeightAnchor).Active = true;
}
```

首先，新`UIFocusGuide`被创建并添加到视图的布局指南集合使用`AddLayoutGuide`方法。

接下来，相对于调整焦点参考线的顶部、 左、 宽度和高度锚**详细信息**和**购买**按钮，以将其置于它们之间。 请参阅：

[ ![](navigation-focus-images/guide02.png "示例焦点指南")](navigation-focus-images/guide02.png)

它也是特别注意的正在激活新的约束，因为它们通过设置创建其`Active`属性`true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

新的焦点指南建立并添加到视图，视图控制器的`DidUpdateFocus`可以重写方法，添加以下代码以之间移动**详细信息**和**购买**按钮：

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    base.DidUpdateFocus (context, coordinator);

    // Get next focusable item from context
    var nextFocusableItem = context.NextFocusedView;

    // Anything to process?
    if (nextFocusableItem == null) return;

    // Decide the next focusable item based on the current
    // item with focus
    if (nextFocusableItem == MoreInfoButton) {
        // Move from the More Info to Buy button
        FocusGuide.PreferredFocusedView = BuyButton;
    } else if (nextFocusableItem == BuyButton) {
        // Move from the Buy to the More Info button
        FocusGuide.PreferredFocusedView = MoreInfoButton;
    } else {
        // No valid move
        FocusGuide.PreferredFocusedView = null;
    }
}
```

首先，此代码 get`NextFocusedView`从`UIFocusUpdateContext`已传入的 (`context`)。 此视图是否`null`，然后不需要任何处理并方法退出。

接下来，`nextFocusableItem`计算。 如果它匹配**详细信息**或**购买**按钮，焦点发送到使用焦点指南相反按钮`PreferredFocusedView`属性。 例如:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

事件中均不按钮是焦点移位的源`PreferredFocusedView`清除属性：

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>使用集合中的焦点

在决定是否的单个项可以焦点中时`UICollectionView`或`UITableView`，你将重写方法`UICollectionViewDelegate`或`UITableViewDelegate`分别。 例如:

```csharp
public class CardHandDelegate : UICollectionViewDelegateFlowLayout
{
    ...
    public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
    {
        if (indexPath == null) {
            return false;
        } else {
            var controller = collectionView as CardHandViewController;
            return !controller.Hand [indexPath.Row].IsFaceDown;
        }
    }
}
```

`CanFocusItem`方法返回`true`如果当前项可焦点，否则它将返回`false`。

如果你想`UICollectionView`或`UITableView`要记住和还原焦点至最后一项在失去并重新获得焦点时，设置`RemembersLastFocusedIndexPath`属性`true`。

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>焦点和视差

如上面所述，用户界面元素被视为_焦点_时将其当前的项在导航事件期间。 项进入焦点时，通常略有增加其大小和直观地提升使用阴影。 

如果用户在 Siri 远程数据库上进行速度慢、 循环笔势，已设定焦点的项将 sway 实时响应此移动。 Sway 发生时，照明的光泽适用于其映像进行显示表现的图面。 给定的处于非活动状态之后, 的任何扩展的焦点内容变暗和已设定焦点的项将变得甚至更大。 

这些效果协同工作来提供电视屏幕上的内容和与新床从 Apple TV 交互的用户之间的心理连接。

在 Apple TV 上, 此视差效果用于在整个系统传达的 3D 深度和 dynamics 以在焦点的项的意义。 tvOS 使用透明的一系列[分层映像](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images)它移动，可以动态扩展来创建此效果。

分层的映像所需的 tvOS 应用程序的图标，并支持对动态顶部架内容。 尽管不要求这样做，Apple 强烈建议在任何其他可获得焦点的项，在您的应用程序 UI 中实现分层映像。

### <a name="enabling-parallax"></a>启用视差

`UIImageView`控件 (或任何继承自的控件`UIImageView`) 自动支持视差效果。 默认情况下，此支持被禁用，若要启用它，请使用以下代码：

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

此属性设置为`true`，由用户和焦点中选择此项时，图像视图自动将取得视差的效果。

<a name="Summary" />

## <a name="summary"></a>摘要

本文已覆盖焦点以及如何使用它来处理在 Xamarin.tvOS 应用程序的用户界面中进行导航的概念。 它检查内置 tvOS 导航控件如何使用焦点、 高亮和选择提供导航。 接下来，它看起来了如何焦点可以使用与视差和分层映像，可为当前的导航状态提供视觉线索，向最终用户。 最后，它会检查使用焦点，焦点更新集合和启用视差中的焦点。




## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
