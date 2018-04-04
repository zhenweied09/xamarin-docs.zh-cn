---
title: 集合视图
description: 集合视图允许使用任意布局显示内容。 它们允许轻松地创建同时还支持自定义布局的现成的类似网格布局。
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 75ad331a265c14892f101b1aa7956d2cde3beec8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="collection-views"></a>集合视图

_集合视图允许使用任意布局显示内容。它们允许轻松地创建同时还支持自定义布局的现成的类似网格布局。_

中可用的集合视图`UICollectionView`类中，是在 iOS 6 中新引入使用布局在屏幕上提供多个项的概念。 向提供数据的模式`UICollectionView`创建项目并相同委派和数据源模式通常用于 iOS 开发这些项按照与之交互。

但是，集合视图会使用独立于布局子系统`UICollectionView`本身。 因此，只需提供一个不同的布局可以轻松更改表示法的集合视图。

iOS 提供一个名为的布局类`UICollectionViewFlowLayout`允许基于行的布局如网格创建与任何额外工作。 此外，自定义布局可以还创建允许你所想象的任何演示文稿。

## <a name="uicollectionview-basics"></a>UICollectionView 基础知识

`UICollectionView`类由三个不同项组成：

-  **单元格**– 数据驱动的每个项的视图
-  **补充视图**– 与节关联的数据驱动的视图。
-  **修饰视图**– 非数据驱动的视图创建布局

## <a name="cells"></a>单元格

单元格是表示数据集，其中要呈现的集合视图中的单个项的对象。 是的一个实例，每个单元格`UICollectionViewCell`类，该类由三个不同的视图下, 图中所示：

 [![](uicollectionview-images/01-uicollectionviewcell.png "每个单元格组成三个不同的视图，如下所示")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

`UICollectionViewCell`类为每个这些视图具有以下属性：

-   `ContentView` – 此视图包含该单元格显示的内容。 它将呈现在最顶层的 z 顺序在屏幕上。
-   `SelectedBackgroundView` – 对所选内容的支持内置了单元格。 此视图用于以可视方式表示单元格已选中。 呈现的正下方`ContentView`时选择了单元格。
-   `BackgroundView` – 单元格还可以显示背景，这提供的`BackgroundView`。 此视图呈现下`SelectedBackgroundView`。


通过设置`ContentView`使其小于`BackgroundView`和`SelectedBackgroundView`、`BackgroundView`可用于以可视方式帧内容，while`SelectedBackgroundView`时，将显示选择了单元格，如下所示：

 [![](uicollectionview-images/02-cells.png "不同的单元格元素")](uicollectionview-images/02-cells.png#lightbox)

上面的屏幕截图中的单元格创建通过继承`UICollectionViewCell`和设置`ContentView`，`SelectedBackgroundView`和`BackgroundView`属性，分别，如下面的代码中所示：

```csharp
public class AnimalCell : UICollectionViewCell
{
        UIImageView imageView;

        [Export ("initWithFrame:")]
        public AnimalCell (CGRect frame) : base (frame)
        {
            BackgroundView = new UIView{BackgroundColor = UIColor.Orange};

            SelectedBackgroundView = new UIView{BackgroundColor = UIColor.Green};

            ContentView.Layer.BorderColor = UIColor.LightGray.CGColor;
            ContentView.Layer.BorderWidth = 2.0f;
            ContentView.BackgroundColor = UIColor.White;
            ContentView.Transform = CGAffineTransform.MakeScale (0.8f, 0.8f);

            imageView = new UIImageView (UIImage.FromBundle ("placeholder.png"));
            imageView.Center = ContentView.Center;
            imageView.Transform = CGAffineTransform.MakeScale (0.7f, 0.7f);

            ContentView.AddSubview (imageView);
        }

        public UIImage Image {
            set {
                imageView.Image = value;
            }
        }
}
```

 <a name="Supplementary_Views" />


## <a name="supplementary-views"></a>补充视图

补充视图是显示的每个部分与关联的信息的视图`UICollectionView`。 类似于单元格，补充视图是数据驱动。 在单元格显示来自数据源的项数据，补充的视图提供部分数据，如中书架本书类别或的音乐库中的音乐风格。

例如，补充视图无法使用提供的标头，特定的部分中下, 图中所示：

 [![](uicollectionview-images/02a-supplementary-view.png "补充视图用于呈现的标头，特定的部分中，如下所示")](uicollectionview-images/02a-supplementary-view.png#lightbox)

若要使用补充的视图，它首先需要在注册`ViewDidLoad`方法：

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

然后，视图需要通过使用返回`GetViewForSupplementaryElement`、 通过使用创建`DequeueReusableSupplementaryView`，并继承自`UICollectionReusableView`。 下面的代码段将生成 SupplementaryView 上面的屏幕截图中所示：

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

补充视图是比只页眉和页脚更通用的。
它们可放置集合视图中的任何位置，并且可以由任何视图，使其外观完全可自定义。

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>修饰视图

修饰视图是纯粹 visual 视图，可以显示在`UICollectionView`。 与不同的单元格和补充视图，它们不是数据驱动。 它们始终会在布局的子类创建，并且随后可以更改作为内容的布局。 例如，无法使用修饰视图来提供中的内容滚动的后台视图`UICollectionView`，如下所示：

 [![](uicollectionview-images/02c-decoration-view.png "包含一个红色背景修饰视图")](uicollectionview-images/02c-decoration-view.png#lightbox)

 以下代码段更改为样本中的红色背景`CircleLayout`类：

 ```csharp
 public class MyDecorationView : UICollectionReusableView
    {
        [Export ("initWithFrame:")]
        public MyDecorationView (CGRect frame) : base (frame)
        {
            BackgroundColor = UIColor.Red;
        }
    }
 ```


## <a name="data-source"></a>“数据源”

与其他部分 iOS，如`UITableView`和`MKMapView`，`UICollectionView`获取从其数据*数据源*，这会在通过 Xamarin.iOS 中公开**`UICollectionViewDataSource`**类。 此类负责提供到内容`UICollectionView`如：

-  **单元格**– 从返回`GetCell`方法。
-  **补充视图**– 从返回`GetViewForSupplementaryElement`方法。
-  **节数**– 从返回`NumberOfSections`方法。 默认值为 1，如果未实现。
-  **每部分的项目数量**– 从返回`GetItemsCount`方法。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
为方便起见，`UICollectionViewController`类是可用。这将自动配置为将这两个委托，这将在下一部分中进行讨论，和数据源为其`UICollectionView`视图。

与`UITableView`、`UICollectionView`类将仅调用其数据源才能获取屏幕上显示的项的单元格。
滚动到屏幕之外的单元格都将置于对队列的重复使用，如以下图像所示：

 [![](uicollectionview-images/03-cell-reuse.png "滚动到屏幕之外的单元格都将置于到队列，重复使用某个如下所示")](uicollectionview-images/03-cell-reuse.png#lightbox)

单元格重用已得到简化了`UICollectionView`和`UITableView`。 不再需要创建直接在数据源中的单元格，如果单元格注册与系统不重复使用队列中出现。 如果进行调用以取消排队重用队列中的单元格时，单元格不可用，iOS 将创建它自动基于类型或已注册的 nib。
相同的技术也是适用于补充的视图。

例如，考虑以下代码以注册`AnimalCell`类：

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

当`UICollectionView`需要单元格，因为其项是在屏幕上，`UICollectionView`调用其数据源的`GetCell`方法。 这与 UITableView 的工作方式类似，此方法是负责配置通过将备份数据的单元格`AnimalCell`类在这种情况下。

下面的代码演示如何实现`GetCell`返回`AnimalCell`实例：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

调用`DequeReusableCell`是该单元格将或者取消排队等候，从重用队列，则未在队列中，创建根据注册的调用中的类型提供一个单元格时`CollectionView.RegisterClassForCell`。

在这种情况下，通过注册`AnimalCell`类，iOS 会创建一个新`AnimalCell`内部并返回它时进行调用以取消排队单元格，其后为其配置包含在动物类，并且返回到要显示的图像`UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>委托

`UICollectionView`类使用类型的委托`UICollectionViewDelegate`以支持与中的内容交互`UICollectionView`。 这样就可以控制的：

-  **单元格选择**– 确定是否选定单元格。
-  **单元格高亮**– 确定当前接触单元格。
-  **单元格菜单**– 响应长按笔势中单元格显示的菜单。


与数据源，`UICollectionViewController`配置默认情况下为的委托`UICollectionView`。

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>单元格突出显示

当上一个单元格按下，单元格转换为突出显示状态，直到未选择用户将其手指提起从单元格。 这实际上选择之前允许的单元格的外观临时更改。 在选择，该单元格时`SelectedBackgroundView`显示。 下图显示突出显示的状态，只需选择发生之前：

 [![](uicollectionview-images/04-cell-highlight.png "此图显示突出显示的状态之前所选内容时发生")](uicollectionview-images/04-cell-highlight.png#lightbox)

若要实现突出显示，`ItemHighlighted`和`ItemUnhighlighted`方法`UICollectionViewDelegate`可用。 例如，下面的代码将应用的黄色背景`ContentView`当突出显示该单元格时，和白色背景时未选中此选项，如在上图中所示：

```csharp
public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.Yellow;
}

public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.White;
}
```

 <a name="Disabling_Selection" />


#### <a name="disabling-selection"></a>禁用所选内容

在默认情况下启用选择`UICollectionView`。 若要禁用所选内容，重写`ShouldHighlightItem`并返回 false，如下所示：

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

当禁用突出显示时，选择一个单元格的过程已禁用。 此外，还有`ShouldSelectItem`方法直接控制选择，虽然如果`ShouldHighlightItem`实现，并返回 false，`ShouldSelectItem`不调用。

 `ShouldSelectItem` 允许选择要打开逐个项的项的方式，打开或关闭时`ShouldHighlightItem`未实现。 它还允许突出显示没有任何选择，如果`ShouldHighlightItem`实现，并返回 true，时`ShouldSelectItem`返回 false。

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>单元格菜单

中的每个单元格`UICollectionView`能够显示一个菜单，使剪切、 复制和粘贴 （可选） 必须支持。 若要创建单元格上的编辑菜单：

1.  重写`ShouldShowMenu`并返回 true，如果该项目应显示菜单。
1.  重写`CanPerformAction`并返回 true 的每个项可以执行执行操作，这将是任何剪切、 复制或粘贴。
1.  重写`PerformAction`若要执行编辑，将复制的粘贴操作。


长按单元格时，下面的屏幕截图显示菜单：

 [![](uicollectionview-images/04a-menu.png "长按单元格时，此屏幕截图中显示菜单")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>布局

`UICollectionView` 支持允许的所有元素，单元格、 增补视图和修饰视图来管理独立于定位的布局系统`UICollectionView`本身。
使用该布局系统，应用程序可以支持如类似网格的那个我们已了解在本文中，以及提供自定义布局的布局。

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>布局基础知识

在中的布局`UICollectionView`继承自的类中定义`UICollectionViewLayout`。 布局实现负责创建中的所有项的布局属性`UICollectionView`。 有两种方法来创建布局：

-  使用内置`UICollectionViewFlowLayout`。
-  通过继承提供自定义布局`UICollectionViewLayout`。


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>流布局

`UICollectionViewFlowLayout`类提供，适用于排列在网格中的单元格的内容，如我们所见的基于行的布局。

若要使用流布局：

-  创建的实例`UICollectionViewFlowLayout`:


```csharp
var layout = new UICollectionViewFlowLayout ();
```

-  将此实例传递给构造函数的`UICollectionView`:


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

这是在网格中的布局内容需要的。 此外，方向发生更改时，`UICollectionViewFlowLayout`处理重新排列内容相应地，如下所示：

 [![](uicollectionview-images/05-layout-orientation.png "方向更改的示例")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>部分内嵌

若要提供围绕一些空间`UIContentView`，布局具有`SectionInset`类型的属性`UIEdgeInsets`。 例如，下面的代码提供的每个区域的周围 50 像素缓冲区`UIContentView`时由放置`UICollectionViewFlowLayout`:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

这会导致间距周围的部分如下所示：

 [![](uicollectionview-images/06-sectioninset.png "如下所示，在该节周围间距")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>子类化 UICollectionViewFlowLayout

在版本到使用`UICollectionViewFlowLayout`直接，它可以还子类化来进一步自定义沿行的内容的布局。 例如，这可以用于创建不支持单元格换到网格中，但改为使用水平滚动效果，创建单个行的布局，如下所示：

 [![](uicollectionview-images/07-line-layout.png "使用水平滚动影响单个行")](uicollectionview-images/07-line-layout.png#lightbox)

若要实现这通过子类化`UICollectionViewFlowLayout`需要：

-  正在初始化将应用于该版式本身的任何布局属性或构造函数中的布局中的所有项。
-  重写`ShouldInvalidateLayoutForBoundsChange`，使其返回 true，当边界`UICollectionView`将重新计算单元格的布局的更改。 使用此转换应用于靠近单元格将应用在滚动期间，在这种情况下确保代码。
-  重写`TargetContentOffset`以便靠近单元格的中心对齐`UICollectionView`作为滚动停止。
-  重写`LayoutAttributesForElementsInRect`返回的数组`UICollectionViewLayoutAttributes`。 每个`UICollectionViewLayoutAttribute`包含有关如何布局特定项，其中包括属性，例如其`Center`， `Size` ，`ZIndex`和`Transform3D`。


下面的代码演示此类实现：

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;
using CoreGraphics;
using CoreAnimation;

namespace SimpleCollectionView
{
    public class LineLayout : UICollectionViewFlowLayout
    {
        public const float ITEM_SIZE = 200.0f;
        public const int ACTIVE_DISTANCE = 200;
        public const float ZOOM_FACTOR = 0.3f;

        public LineLayout ()
        {
            ItemSize = new CGSize (ITEM_SIZE, ITEM_SIZE);
            ScrollDirection = UICollectionViewScrollDirection.Horizontal;
            SectionInset = new UIEdgeInsets (400,0,400,0);
            MinimumLineSpacing = 50.0f;
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            return true;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var array = base.LayoutAttributesForElementsInRect (rect);
            var visibleRect = new CGRect (CollectionView.ContentOffset, CollectionView.Bounds.Size);

            foreach (var attributes in array) {
                if (attributes.Frame.IntersectsWith (rect)) {
                    float distance = (float)(visibleRect.GetMidX () - attributes.Center.X);
                    float normalizedDistance = distance / ACTIVE_DISTANCE;
                    if (Math.Abs (distance) < ACTIVE_DISTANCE) {
                        float zoom = 1 + ZOOM_FACTOR * (1 - Math.Abs (normalizedDistance));
                        attributes.Transform3D = CATransform3D.MakeScale (zoom, zoom, 1.0f);
                        attributes.ZIndex = 1;
                    }
                }
            }
            return array;
        }

        public override CGPoint TargetContentOffset (CGPoint proposedContentOffset, CGPoint scrollingVelocity)
        {
            float offSetAdjustment = float.MaxValue;
            float horizontalCenter = (float)(proposedContentOffset.X + (this.CollectionView.Bounds.Size.Width / 2.0));
            CGRect targetRect = new CGRect (proposedContentOffset.X, 0.0f, this.CollectionView.Bounds.Size.Width, this.CollectionView.Bounds.Size.Height);
            var array = base.LayoutAttributesForElementsInRect (targetRect);
            foreach (var layoutAttributes in array) {
                float itemHorizontalCenter = (float)layoutAttributes.Center.X;
                if (Math.Abs (itemHorizontalCenter - horizontalCenter) < Math.Abs (offSetAdjustment)) {
                    offSetAdjustment = itemHorizontalCenter - horizontalCenter;
                }
            }
            return new CGPoint (proposedContentOffset.X + offSetAdjustment, proposedContentOffset.Y);
        }

    }
}
```

 <a name="Custom_Layout" />


### <a name="custom-layout"></a>自定义布局

除了使用`UICollectionViewFlowLayout`，布局可以还完全自定义通过直接从继承`UICollectionViewLayout`。

若要重写的主要方法是：

-   `PrepareLayout` – 用于执行将在布局流程中使用的初始几何计算。
-   `CollectionViewContentSize` – 返回用来显示内容的区域的大小。
-   `LayoutAttributesForElementsInRect` – 当 UICollectionViewFlowLayout 示例中前面所示，此方法用于将信息提供给`UICollectionView`如何布局有关每个项。 但是，与不同`UICollectionViewFlowLayout`，在创建自定义布局，但你选择，您可以定位项。


例如，相同的内容无法循环布局中看到如下所示：

 [![](uicollectionview-images/08-circle-layout.png "循环的自定义如下所示的布局")](uicollectionview-images/08-circle-layout.png#lightbox)

有关布局的强大件事就是从类似网格布局中，更改为水平滚动布局中，和随后到此循环布局需要只允许布局类提供给`UICollectionView`更改。 在中为 nothing `UICollectionView`，其委托或数据在所有源代码更改。


## <a name="changes-in-ios-9"></a>在 iOS 9 中的更改


在 iOS 9，集合视图中 (`UICollectionView`) 现在支持将通过添加新的默认笔势识别器和几个新的支持方法在初始状态下的项重新排序。

使用这些新方法，你可以轻松地实现拖动集合视图中重新排序，并可以选择自定义期间重新排序过程的任何阶段的项外观。

[![](uicollectionview-images/intro01.png "重新排序过程的示例")](uicollectionview-images/intro01.png#lightbox)

在本文中，我们来看看在 Xamarin.iOS 应用程序以及一些其他 iOS 9 对集合视图控件所做的更改中实现拖到重新排序：

- [项变得简单重新排序](#Easy-Reordering-of-Items)
    - [重新排序的简单示例](#Simple-Reordering-Example)
    - [使用自定义笔势识别器](#Using-a-Custom-Gesture-Recognizer)
    - [自定义布局和重新排序](#Custom-Layouts-and-Reording)
- [集合视图更改](#Collection-View-Changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>项重新排序

如上面所述，在 iOS 9 中的集合视图的最重大更改之一是现成的轻松拖到重新排序功能添加。

在 iOS 9 中，添加到集合视图重新排序的最快方法是使用`UICollectionViewController`。
集合视图控制器现在具有`InstallsStandardGestureForInteractiveMovement`属性，它将添加一个标准*笔势识别器*支持拖动对集合中的项重新排序。
由于默认值是`true`，只需实现`MoveItem`方法`UICollectionViewDataSource`类，以支持拖到重新排序。 例如：

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>重新排序的简单示例

作为一个快速示例，启动新的 Xamarin.iOS 项目并编辑**Main.storyboard**文件。 拖动`UICollectionViewController`拖到设计图面：

[![](uicollectionview-images/quick01.png "添加 UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

选择 （它可能是最简单的方法文档大纲中执行此操作） 的集合视图。 在属性 Pad 布局选项卡中，设置以下大小，如下面的屏幕截图中所示：

- **单元格大小**： 宽度 – 60 |高度 – 60
- **标头大小**： 宽度 – 0 |高度 – 0
- **页脚大小**： 宽度 – 0 |高度 – 0
- **最小间距**： 单元格 – 8 |行 – 8
- **部分内边距**： 顶部 – 16 |底部 – 16 |左 – 16 |右 – 16

[![](uicollectionview-images/quick04.png "设置集合视图大小")](uicollectionview-images/quick04.png#lightbox)

接下来，编辑单元格的默认值：
    - 更改为蓝色其背景色
    - 添加一个标签来充当单元格的标题
    - 将重用标识符设置为**单元格**

[![](uicollectionview-images/quick02.png "编辑默认单元格")](uicollectionview-images/quick02.png#lightbox)

添加约束以保留的单元格内居中，因为它在改变大小的标签：

在**属性填充**为_CollectionViewCell_并设置**类**到`TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "将类设置为 TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

设置**集合可重用视图**到`Cell`:

[![](uicollectionview-images/quick06.png "设置为单元格的集合可重用视图")](uicollectionview-images/quick06.png#lightbox)

最后，选择标签并将其命名`TextLabel`:

[![](uicollectionview-images/quick07.png "名称标签 TextLabel")](uicollectionview-images/quick07.png#lightbox)

编辑`TextCollectionViewCell`类，并添加以下属性。:

```csharp
using System;
using Foundation;
using UIKit;

namespace CollectionView
{
    public partial class TextCollectionViewCell : UICollectionViewCell
    {
        #region Computed Properties
        public string Title {
            get { return TextLabel.Text; }
            set { TextLabel.Text = value; }
        }
        #endregion

        #region Constructors
        public TextCollectionViewCell (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

此处`Text`标签的属性公开为该单元格的标题，以便可以从代码中设置。

向项目添加新的 C# 类和调用它`WaterfallCollectionSource`。 编辑文件，并使其看起来如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionSource : UICollectionViewDataSource
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        public List<int> Numbers { get; set; } = new List<int> ();
        #endregion

        #region Constructors
        public WaterfallCollectionSource (WaterfallCollectionView collectionView)
        {
            // Initialize
            CollectionView = collectionView;

            // Init numbers collection
            for (int n = 0; n < 100; ++n) {
                Numbers.Add (n);
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView) {
            // We only have one section
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section) {
            // Return the number of items
            return Numbers.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a reusable cell and set {~~it's~>its~~} title from the item
            var cell = collectionView.DequeueReusableCell ("Cell", indexPath) as TextCollectionViewCell;
            cell.Title = Numbers [(int)indexPath.Item].ToString();

            return cell;
        }

        public override bool CanMoveItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // We can always move items
            return true;
        }

        public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
        {
            // Reorder our list of items
            var item = Numbers [(int)sourceIndexPath.Item];
            Numbers.RemoveAt ((int)sourceIndexPath.Item);
            Numbers.Insert ((int)destinationIndexPath.Item, item);
        }
        #endregion
    }
}
```

此类将是我们集合视图的数据源，并提供在集合中每个单元格的信息。
请注意，`MoveItem`实现方法以允许为集合中的项拖动重新排序。

向项目中添加另一个新的 C# 类和调用它`WaterfallCollectionDelegate`。 编辑此文件并使其看起来如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
    public class WaterfallCollectionDelegate : UICollectionViewDelegate
    {
        #region Computed Properties
        public WaterfallCollectionView CollectionView { get; set;}
        #endregion

        #region Constructors
        public WaterfallCollectionDelegate (WaterfallCollectionView collectionView)
        {

            // Initialize
            CollectionView = collectionView;

        }
        #endregion

        #region Overrides Methods
        public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath) {
            // Always allow for highlighting
            return true;
        }

        public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and change to green background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(183,208,57);
        }

        public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get cell and return to blue background
            var cell = collectionView.CellForItem(indexPath);
            cell.ContentView.BackgroundColor = UIColor.FromRGB(164,205,255);
        }
        #endregion
    }
}
```

这将充当我们集合视图的委托。 方法已被重写，以突出显示单元格，当用户与之交互集合视图中。

向项目添加一个最后一个 C# 类和调用它`WaterfallCollectionView`。 编辑此文件并使其看起来如下所示：

```csharp
using System;
using UIKit;
using System.Collections.Generic;
using Foundation;

namespace CollectionView
{
    [Register("WaterfallCollectionView")]
    public class WaterfallCollectionView : UICollectionView
    {

        #region Constructors
        public WaterfallCollectionView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Initialize
            DataSource = new WaterfallCollectionSource(this);
            Delegate = new WaterfallCollectionDelegate(this);

        }
        #endregion
    }
}
```

请注意，`DataSource`和`Delegate`上面创建时从其情节提要构造集合视图设置 (或**.xib**文件)。

编辑**Main.storyboard**再次文件并选择集合视图和切换到**属性**。 设置**类**到自定义`WaterfallCollectionView`我们上面定义的类：

保存到 UI 所做的更改并运行应用程序。
如果用户从列表中选择一个项，并将其拖动到新位置，因为它们开移动的项其他项将自动动画。
当用户将项放置到新位置时，它将只讨论到该位置。 例如：

[![](uicollectionview-images/intro01.png "下面举例说明将项拖动到新位置")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>使用自定义笔势识别器

在您无法使用的情况下`UICollectionViewController`，并且必须使用正则表达式`UIViewController`，或者如果你想要使更好地控制拖放笔势，你可以创建你自己的自定义笔势识别器并将其添加到集合视图中，加载视图时。 例如：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Create a custom gesture recognizer
    var longPressGesture = new UILongPressGestureRecognizer ((gesture) => {

        // Take action based on state
        switch(gesture.State) {
        case UIGestureRecognizerState.Began:
            var selectedIndexPath = CollectionView.IndexPathForItemAtPoint(gesture.LocationInView(View));
            if (selectedIndexPath !=null) {
                CollectionView.BeginInteractiveMovementForItem(selectedIndexPath);
            }
            break;
        case UIGestureRecognizerState.Changed:
            CollectionView.UpdateInteractiveMovementTargetPosition(gesture.LocationInView(View));
            break;
        case UIGestureRecognizerState.Ended:
            CollectionView.EndInteractiveMovement();
            break;
        default:
            CollectionView.CancelInteractiveMovement();
            break;
        }

    });

    // Add the custom recognizer to the collection view
    CollectionView.AddGestureRecognizer(longPressGesture);
}
```

此处我们将使用几种新方法添加到集合视图来实现和控制拖动操作：

 - `BeginInteractiveMovementForItem` -将标记移动操作开始。
 - `UpdateInteractiveMovementTargetPosition` -发送对其进行更新该项的位置。
 - `EndInteractiveMovement` -将标记的末尾将项移动。
 - `CancelInteractiveMovement` -将标记用户取消移动操作。

应用程序运行时，将工作拖动操作，默认值拖动手势识别器附带集合视图时一样。

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>自定义布局和重新排序

在 iOS 9 中，几个新方法已添加用于在集合视图中的拖到重新排序和自定义布局。 若要浏览此功能，让我们向集合添加自定义布局。

首先，添加一个新 C# 类调用`WaterfallCollectionLayout`到项目。 对其进行编辑，并使其如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;
using CoreGraphics;

namespace CollectionView
{
    [Register("WaterfallCollectionLayout")]
    public class WaterfallCollectionLayout : UICollectionViewLayout
    {
        #region Private Variables
        private int columnCount = 2;
        private nfloat minimumColumnSpacing = 10;
        private nfloat minimumInterItemSpacing = 10;
        private nfloat headerHeight = 0.0f;
        private nfloat footerHeight = 0.0f;
        private UIEdgeInsets sectionInset = new UIEdgeInsets(0, 0, 0, 0);
        private WaterfallCollectionRenderDirection itemRenderDirection = WaterfallCollectionRenderDirection.ShortestFirst;
        private Dictionary<nint,UICollectionViewLayoutAttributes> headersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private Dictionary<nint,UICollectionViewLayoutAttributes> footersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
        private List<CGRect> unionRects = new List<CGRect>();
        private List<nfloat> columnHeights = new List<nfloat>();
        private List<UICollectionViewLayoutAttributes> allItemAttributes = new List<UICollectionViewLayoutAttributes>();
        private List<List<UICollectionViewLayoutAttributes>> sectionItemAttributes = new List<List<UICollectionViewLayoutAttributes>>();
        private nfloat unionSize = 20;
        #endregion

        #region Computed Properties
        [Export("ColumnCount")]
        public int ColumnCount {
            get { return columnCount; }
            set {
                WillChangeValue ("ColumnCount");
                columnCount = value;
                DidChangeValue ("ColumnCount");

                InvalidateLayout ();
            }
        }

        [Export("MinimumColumnSpacing")]
        public nfloat MinimumColumnSpacing {
            get { return minimumColumnSpacing; }
            set {
                WillChangeValue ("MinimumColumnSpacing");
                minimumColumnSpacing = value;
                DidChangeValue ("MinimumColumnSpacing");

                InvalidateLayout ();
            }
        }

        [Export("MinimumInterItemSpacing")]
        public nfloat MinimumInterItemSpacing {
            get { return minimumInterItemSpacing; }
            set {
                WillChangeValue ("MinimumInterItemSpacing");
                minimumInterItemSpacing = value;
                DidChangeValue ("MinimumInterItemSpacing");

                InvalidateLayout ();
            }
        }

        [Export("HeaderHeight")]
        public nfloat HeaderHeight {
            get { return headerHeight; }
            set {
                WillChangeValue ("HeaderHeight");
                headerHeight = value;
                DidChangeValue ("HeaderHeight");

                InvalidateLayout ();
            }
        }

        [Export("FooterHeight")]
        public nfloat FooterHeight {
            get { return footerHeight; }
            set {
                WillChangeValue ("FooterHeight");
                footerHeight = value;
                DidChangeValue ("FooterHeight");

                InvalidateLayout ();
            }
        }

        [Export("SectionInset")]
        public UIEdgeInsets SectionInset {
            get { return sectionInset; }
            set {
                WillChangeValue ("SectionInset");
                sectionInset = value;
                DidChangeValue ("SectionInset");

                InvalidateLayout ();
            }
        }

        [Export("ItemRenderDirection")]
        public WaterfallCollectionRenderDirection ItemRenderDirection {
            get { return itemRenderDirection; }
            set {
                WillChangeValue ("ItemRenderDirection");
                itemRenderDirection = value;
                DidChangeValue ("ItemRenderDirection");

                InvalidateLayout ();
            }
        }
        #endregion

        #region Constructors
        public WaterfallCollectionLayout ()
        {
        }

        public WaterfallCollectionLayout(NSCoder coder) : base(coder) {

        }
        #endregion

        #region Public Methods
        public nfloat ItemWidthInSectionAtIndex(int section) {

            var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
            return (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);
        }
        #endregion

        #region Override Methods
        public override void PrepareLayout ()
        {
            base.PrepareLayout ();

            // Get the number of sections
            var numberofSections = CollectionView.NumberOfSections();
            if (numberofSections == 0)
                return;

            // Reset collections
            headersAttributes.Clear ();
            footersAttributes.Clear ();
            unionRects.Clear ();
            columnHeights.Clear ();
            allItemAttributes.Clear ();
            sectionItemAttributes.Clear ();

            // Initialize column heights
            for (int n = 0; n < ColumnCount; n++) {
                columnHeights.Add ((nfloat)0);
            }

            // Process all sections
            nfloat top = 0.0f;
            var attributes = new UICollectionViewLayoutAttributes ();
            var columnIndex = 0;
            for (nint section = 0; section < numberofSections; ++section) {
                // Calculate section specific metrics
                var minimumInterItemSpacing = (MinimumInterItemSpacingForSection == null) ? MinimumColumnSpacing :
                    MinimumInterItemSpacingForSection (CollectionView, this, section);

                // Calculate widths
                var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
                var itemWidth = (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);

                // Calculate section header
                var heightHeader = (HeightForHeader == null) ? HeaderHeight :
                    HeightForHeader (CollectionView, this, section);

                if (heightHeader > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Header, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, heightHeader);
                    headersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);

                    top = attributes.Frame.GetMaxY ();
                }

                top += SectionInset.Top;
                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }

                // Calculate Section Items
                var itemCount = CollectionView.NumberOfItemsInSection(section);
                List<UICollectionViewLayoutAttributes> itemAttributes = new List<UICollectionViewLayoutAttributes> ();

                for (nint n = 0; n < itemCount; n++) {
                    var indexPath = NSIndexPath.FromRowSection (n, section);
                    columnIndex = NextColumnIndexForItem (n);
                    var xOffset = SectionInset.Left + (itemWidth + MinimumColumnSpacing) * (nfloat)columnIndex;
                    var yOffset = columnHeights [columnIndex];
                    var itemSize = (SizeForItem == null) ? new CGSize (0, 0) : SizeForItem (CollectionView, this, indexPath);
                    nfloat itemHeight = 0.0f;

                    if (itemSize.Height > 0.0f && itemSize.Width > 0.0f) {
                        itemHeight = (nfloat)Math.Floor (itemSize.Height * itemWidth / itemSize.Width);
                    }

                    attributes = UICollectionViewLayoutAttributes.CreateForCell (indexPath);
                    attributes.Frame = new CGRect (xOffset, yOffset, itemWidth, itemHeight);
                    itemAttributes.Add (attributes);
                    allItemAttributes.Add (attributes);
                    columnHeights [columnIndex] = attributes.Frame.GetMaxY () + MinimumInterItemSpacing;
                }
                sectionItemAttributes.Add (itemAttributes);

                // Calculate Section Footer
                nfloat footerHeight = 0.0f;
                columnIndex = LongestColumnIndex();
                top = columnHeights [columnIndex] - MinimumInterItemSpacing + SectionInset.Bottom;
                footerHeight = (HeightForFooter == null) ? FooterHeight : HeightForFooter(CollectionView, this, section);

                if (footerHeight > 0) {
                    attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Footer, NSIndexPath.FromRowSection (0, section));
                    attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, footerHeight);
                    footersAttributes.Add (section, attributes);
                    allItemAttributes.Add (attributes);
                    top = attributes.Frame.GetMaxY ();
                }

                for (int n = 0; n < ColumnCount; n++) {
                    columnHeights [n] = top;
                }
            }

            var i =0;
            var attrs = allItemAttributes.Count;
            while(i < attrs) {
                var rect1 = allItemAttributes [i].Frame;
                i = (int)Math.Min (i + unionSize, attrs) - 1;
                var rect2 = allItemAttributes [i].Frame;
                unionRects.Add (CGRect.Union (rect1, rect2));
                i++;
            }

        }

        public override CGSize CollectionViewContentSize {
            get {
                if (CollectionView.NumberOfSections () == 0) {
                    return new CGSize (0, 0);
                }

                var contentSize = CollectionView.Bounds.Size;
                contentSize.Height = columnHeights [0];
                return contentSize;
            }
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForItem (NSIndexPath indexPath)
        {
            if (indexPath.Section >= sectionItemAttributes.Count) {
                return null;
            }

            if (indexPath.Item >= sectionItemAttributes [indexPath.Section].Count) {
                return null;
            }

            var list = sectionItemAttributes [indexPath.Section];
            return list [(int)indexPath.Item];
        }

        public override UICollectionViewLayoutAttributes LayoutAttributesForSupplementaryView (NSString kind, NSIndexPath indexPath)
        {
            var attributes = new UICollectionViewLayoutAttributes ();

            switch (kind) {
            case "header":
                attributes = headersAttributes [indexPath.Section];
                break;
            case "footer":
                attributes = footersAttributes [indexPath.Section];
                break;
            }

            return attributes;
        }

        public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
        {
            var begin = 0;
            var end = unionRects.Count;
            List<UICollectionViewLayoutAttributes> attrs = new List<UICollectionViewLayoutAttributes> ();


            for (int i = 0; i < end; i++) {
                if (rect.IntersectsWith(unionRects[i])) {
                    begin = i * (int)unionSize;
                }
            }

            for (int i = end - 1; i >= 0; i--) {
                if (rect.IntersectsWith (unionRects [i])) {
                    end = (int)Math.Min ((i + 1) * (int)unionSize, allItemAttributes.Count);
                    break;
                }
            }

            for (int i = begin; i < end; i++) {
                var attr = allItemAttributes [i];
                if (rect.IntersectsWith (attr.Frame)) {
                    attrs.Add (attr);
                }
            }

            return attrs.ToArray();
        }

        public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
        {
            var oldBounds = CollectionView.Bounds;
            return (newBounds.Width != oldBounds.Width);
        }
        #endregion

        #region Private Methods
        private int ShortestColumnIndex() {
            var index = 0;
            var shortestHeight = nfloat.MaxValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height < shortestHeight) {
                    shortestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int LongestColumnIndex() {
            var index = 0;
            var longestHeight = nfloat.MinValue;
            var n = 0;

            // Scan each column for the shortest height
            foreach (nfloat height in columnHeights) {
                if (height > longestHeight) {
                    longestHeight = height;
                    index = n;
                }
                ++n;
            }

            return index;
        }

        private int NextColumnIndexForItem(nint item) {
            var index = 0;

            switch (ItemRenderDirection) {
            case WaterfallCollectionRenderDirection.ShortestFirst:
                index = ShortestColumnIndex ();
                break;
            case WaterfallCollectionRenderDirection.LeftToRight:
                index = ColumnCount;
                break;
            case WaterfallCollectionRenderDirection.RightToLeft:
                index = (ColumnCount - 1) - ((int)item / ColumnCount);
                break;
            }

            return index;
        }
        #endregion

        #region Events
        public delegate CGSize WaterfallCollectionSizeDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, NSIndexPath indexPath);
        public delegate nfloat WaterfallCollectionFloatDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);
        public delegate UIEdgeInsets WaterfallCollectionEdgeInsetsDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);

        public event WaterfallCollectionSizeDelegate SizeForItem;
        public event WaterfallCollectionFloatDelegate HeightForHeader;
        public event WaterfallCollectionFloatDelegate HeightForFooter;
        public event WaterfallCollectionEdgeInsetsDelegate InsetForSection;
        public event WaterfallCollectionFloatDelegate MinimumInterItemSpacingForSection;
        #endregion
    }
}
```

这可以是使用类以提供自定义的两个列、 瀑布图类型到集合视图的布局。
该代码使用键值对的编码 (通过`WillChangeValue`和`DidChangeValue`方法) 提供此类中我们计算属性的数据绑定。

接下来，编辑`WaterfallCollectionSource`并进行以下更改和添加：

```csharp
private Random rnd = new Random();
...

public List<nfloat> Heights { get; set; } = new List<nfloat> ();
...

public WaterfallCollectionSource (WaterfallCollectionView collectionView)
{
    // Initialize
    CollectionView = collectionView;

    // Init numbers collection
    for (int n = 0; n < 100; ++n) {
        Numbers.Add (n);
        Heights.Add (rnd.Next (0, 100) + 40.0f);
    }
}
```

这将为每个项将显示在列表中创建一个随机的高度。

接下来，编辑`WaterfallCollectionView`类并添加以下帮助器属性：

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

这将使更轻松地在我们的数据源 （和项高度） 获得从自定义布局。

最后，编辑视图控制器，并添加以下代码：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    var waterfallLayout = new WaterfallCollectionLayout ();

    // Wireup events
    waterfallLayout.SizeForItem += (collectionView, layout, indexPath) => {
        var collection = collectionView as WaterfallCollectionView;
        return new CGSize((View.Bounds.Width-40)/3,collection.Source.Heights[(int)indexPath.Item]);
    };

    // Attach the custom layout to the collection
    CollectionView.SetCollectionViewLayout(waterfallLayout, false);
}
```

这将创建我们自定义布局的实例，设置要提供大小信息的每个项的事件并将新的布局附加到我们集合视图。

如果我们试运行 Xamarin.iOS 应用程序时，集合视图现在外观如下所示：

[![](uicollectionview-images/custom01.png "集合视图将现在如下所示")](uicollectionview-images/custom01.png#lightbox)

我们可以仍拖-到-对项目重新排序为之前，但现在是调整大小以适应其新位置时将丢弃这些值将更改的项。

## <a name="collection-view-changes"></a>集合视图更改

在以下部分中，我们将详细的介绍了的 iOS 9 到集合视图中的每个类所做的更改。

### <a name="uicollectionview"></a>UICollectionView

进行以下更改或添加项`UICollectionView`ios 9 的类：

 - `BeginInteractiveMovementForItem` – 标记拖动操作开始。
 - `CancelInteractiveMovement` – 告知集合用户已取消拖动操作的视图。
 - `EndInteractiveMovement` – 告知集合用户完成拖动操作的视图。
 - `GetIndexPathsForVisibleSupplementaryElements` – 返回`indexPath`的页眉或页脚集合视图部分中的。
 - `GetSupplementaryView` – 返回给定的页眉或页脚。
 - `GetVisibleSupplementaryViews` – 返回所有可见的页眉和页脚的列表。
 - `UpdateInteractiveMovementTargetPosition` – 告知集合视图，用户已在移动，或者在移动，在拖动操作过程中的项。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

进行以下更改或添加项`UICollectionViewController`在 iOS 9 中的类：

 - `InstallsStandardGestureForInteractiveMovement` -如果`true`将使用新笔势识别器自动支持拖到重新排序。
 - `CanMoveItem` – 如果给定的项可以拖动重新排序，则通知集合视图。
 - `GetTargetContentOffset` – 用于获取给定的集合视图项的偏移量。
 - `GetTargetIndexPathForMove` -获取`indexPath`拖动操作的给定项目。
 - `MoveItem` – 在列表中移动给定项的顺序。


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

进行以下更改或添加项`UICollectionViewDataSource`在 iOS 9 中的类：

 - `CanMoveItem` – 如果给定的项可以拖动重新排序，则通知集合视图。
 - `MoveItem` – 在列表中移动给定项的顺序。

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

进行以下更改或添加项`UICollectionViewDelegate`在 iOS 9 中的类：

 - `GetTargetContentOffset` – 用于获取给定的集合视图项的偏移量。
 - `GetTargetIndexPathForMove` -获取`indexPath`拖动操作的给定项目。

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

进行以下更改或添加项`UICollectionViewFlowLayout`在 iOS 9 中的类：

 - `SectionFootersPinToVisibleBounds` – 软木于可见集合视图边界部分页脚。
 - `SectionHeadersPinToVisibleBounds` – 软木于可见集合视图边界节标头。

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

进行以下更改或添加项`UICollectionViewLayout`在 iOS 9 中的类：

 - `GetInvalidationContextForEndingInteractiveMovementOfItems` – 在用户完成拖动或取消它时，则返回在拖动操作的末尾处的失效上下文。
 - `GetInvalidationContextForInteractivelyMovingItems` – 返回在拖动操作开始处的失效上下文。
 - `GetLayoutAttributesForInteractivelyMovingItem` -获取的布局属性给定项拖动项时。
 - `GetTargetIndexPathForInteractivelyMovingItem` – 返回`indexPath`拖动项时在给定时间的项。

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

进行以下更改或添加项`UICollectionViewLayoutAttributes`在 iOS 9 中的类：

 - `CollisionBoundingPath` – 在拖动操作期间返回两个项目的冲突的路径。
 - `CollisionBoundsType` – 返回冲突的类型 (作为`UIDynamicItemCollisionBoundsType`) 在拖动操作期间发生这种。

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

进行以下更改或添加项`UICollectionViewLayoutInvalidationContext`在 iOS 9 中的类：

 - `InteractiveMovementTarget` – 返回拖动操作的目标项。
 - `PreviousIndexPathsForInteractivelyMovingItems` – 返回`indexPaths`的其他项涉及拖动以重新排序操作。
 - `TargetIndexPathsForInteractivelyMovingItems` – 返回`indexPaths`的将拖到重新排序操作后对其重新排序的项。

### <a name="uicollectionviewsource"></a>UICollectionViewSource

进行以下更改或添加项`UICollectionViewSource`在 iOS 9 中的类：

 - `CanMoveItem` – 如果给定的项可以拖动重新排序，则通知集合视图。
 - `GetTargetContentOffset` – 返回通过拖动重新排序操作将被移动的项的偏移量。
 - `GetTargetIndexPathForMove` – 返回`indexPath`将拖到重新排序操作期间移动的项。
 - `MoveItem` – 在列表中移动给定项的顺序。

## <a name="summary"></a>总结

本文已涉及到在 iOS 9 中的集合视图的更改，并描述如何在 Xamarin.iOS 中实现它们。
它涵盖实现简单拖到重新排序操作中的集合视图中;自定义笔势识别器使用重新拖到排序;以及字段拖到重新排序如何影响自定义集合视图布局。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [集合视图示例](https://developer.xamarin.com/samples/monotouch/ios9/CollectionView/)
- [SimpleCollectionView （示例）](https://developer.xamarin.com/samples/SimpleCollectionView/)
- [事件、协议和委托](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [使用表和单元格](~/ios/user-interface/controls/tables/index.md)
