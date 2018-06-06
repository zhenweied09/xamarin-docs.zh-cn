---
title: 在 iOS 11 中的视觉对象设计更新
description: 本文档介绍 iOS 11 中引入的更新的可视设计。 它讨论到导航栏、 搜索控制器、 边距、 全屏幕内容和表视图的更改。
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 5bc29e021e30910c7fcc4c3b451d0a4dfe187d05
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787846"
---
# <a name="visual-design-updates-in-ios-11"></a>在 iOS 11 中的视觉对象设计更新

在 iOS 11 中，Apple 引入了新的可视更改，包括更新的导航栏、 搜索栏和表视图。 除了已得到改进，允许通过边距和全屏内容进行更大的灵活性。 本指南中介绍了这些更改。

## <a name="uikit"></a>UIKit

UIKit 条在 iOS 11，以使它们更易于访问的最终用户中适用。

一个此类更改是当用户 long 类型的值的按可实现条上时显示新 HUD 显示项。 若要启用此功能，设置`largeContentSizeImage`属性`UIBarItem`并添加更大的映像通过[资产目录](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>导航栏
iOS 11 引入了新功能，以使导航栏标题易于阅读。 应用程序可以通过分配显示此更大的标题`PrefersLargeTitles`属性为 true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

你的应用程序中的设置更大标题使_所有_在你的应用程序之间的导航栏标题显示更大，如下面的屏幕截图中所示：

![大型导航标题](visual-design-images/image7.png)

若要控制在导航栏上显示大标题时，设置`LargeTitleDisplayMode`导航部件上的`Always`， `Never`，或`Automatic`。

### <a name="search-controller"></a>搜索控制器

iOS 11 变得更轻松地搜索控制器将直接添加到导航栏。 一旦你已创建的搜索控制器，则将其添加到与你导航栏`SearchController`属性：

```csharp
NavigationItem.SearchController = searchController;
```

[![大型导航标题与搜索栏](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

根据你的应用程序的功能，你可能或可能不希望在用户滚动浏览列表时隐藏搜索栏。 你可以对此进行调整使用`HidesSearchBarWhenScrolling`属性。

## <a name="margins"></a>边距

Apple 已创建一个新属性 – `directionalLayoutMargins` – 可用于设置视图和子视图之间的空间。 使用`directionalLayoutMargins`与`leading`或`trailing`内边距。 无论系统是从左到右还是从右到左的语言，由 iOS 适当地设置应用程序中的间距。

在 iOS 10 中以及在之前，所有视图都具有它们将对齐的最小的边距大小。 iOS 11 引入了重写，使用的选项`ViewRespectsSystemMinimumLayoutMargins`。 例如，将此属性设置为 false，可调整你边缘内的边距为零：

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![与零个内嵌在 ios 11 中的图像显示边距](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>全屏显示内容

iOS 7[引入](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen)`topLayoutGuide`和`bottomLayoutGuide`作为一种限制您的视图，以便它们不由 UIKit 隐藏的在你的屏幕的可见区域。 鉴于 iOS 11 中已弃用这些_安全区域_。

安全区域是思考一下你的应用程序以及如何约束将添加的视图超级视图之间的可见空间的新方法。 例如，考虑下面的图像：

[![安全区域 vs 顶部和底部布局指南](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

以前，如果你添加一个视图，希望它在上面的绿色区域中均可见，你将对其进行约束到_底部_的`TopLayoutGuide`和_顶部_的`BottomLayoutGuide`。 在 iOS 11 中，你将改为对其进行约束到_顶部_和_底部_的安全区域。 下面是一个示例：

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>表视图

UITableView iOS 11 中已大量的小，但却非常重要，更改。

默认情况下，页眉、 页脚和单元格将立即自动调整大小基于其内容。 若要选择退出此自动调整大小行为集`EstimatedRowHeight`， `EstimatedSectionHeaderHeight`，或`EstimatedSectionFooterHeight`为零。

但是，在某些情况下 (例如，当添加 UITableViewController iOS 设计器中或在接口生成器中使用现有 Storboards 时) 可能需要手动启用自动调整大小的单元格。 若要执行此操作，请确保具有以下属性上设置的表视图单元格、 页眉和页脚分别：

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 已扩展行操作的功能。 `UISwipeActionsConfiguration` 引入了可以定义一组用户刷在任一方向表视图中某一行时应发生的操作。 此行为是类似于本机将 Mail.app。 有关详细信息，请参阅[行操作](~/ios/user-interface/controls/tables/row-action.md)指南。

表视图都支持拖放在 iOS 11。 有关详细信息，请参阅[拖放式](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview)指南。


## <a name="related-links"></a>相关链接

- [什么是新建 iOS 11 (Apple)](https://developer.apple.com/ios/)
- [更新的应用商店产品页 (Apple)](https://developer.apple.com/app-store/product-page/)
- [更新你的应用程序适用于 iOS 11 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
