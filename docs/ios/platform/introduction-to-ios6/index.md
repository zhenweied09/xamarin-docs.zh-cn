---
title: IOS 6 简介
description: 此文档链接到指南，用于描述 iOS 6 中引入的功能。 集合视图，PassKit，社交框架中，和所有讨论对 StoreKit 的更改。
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: cf623c7788137106ddbb2c23c69465f205a5a400
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787609"
---
# <a name="introduction-to-ios-6"></a>IOS 6 简介

_iOS 6 包括多种用于开发应用，这 Xamarin.iOS 6 将向 C# 开发人员提供的新技术。_

[ ![](images/ios6-large.jpg "IOS 6 徽标")](images/ios6-large.jpg#lightbox)

使用 iOS 6 和 Xamarin.iOS 6，开发人员现在支配来创建该目标 iPhone 5 的 iOS 应用程序，包括已有丰富的功能。
本文档列出的一些更令人兴奋的新功能所提供和链接到每个主题的文章。 除了它涉及将重要，因为开发人员将移到 iOS 6 和 iPhone 5 新解决方法的几个更改。


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[集合视图简介](~/ios/user-interface/controls/uicollectionview.md)

集合视图允许使用任意布局显示内容。 它们允许轻松地同时支持自定义布局以及创建现成的类似网格布局。 有关详细信息请参阅、[简介集合视图](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md)指南。


## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[PassKit 简介](~/ios/platform/passkit.md)

PassKit 框架允许在应用程序与托管 Passbook 应用中的数字传递进行交互。 有关详细信息请参阅、[传递工具包指南简介](~/ios/platform/passkit.md)。


##  <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[EventKit 简介](~/ios/platform/eventkit.md)

EventKit framework 提供了用于访问日历、 日历事件和提醒日历数据库存储的数据的方法。 访问对日历和日历事件后的可用 iOS 4，但 iOS 6 现在将访问权限提醒数据公开。 有关详细信息，请参阅[我](~/ios/platform/eventkit.md)[到 EventKit ntroduction](~/ios/platform/eventkit.md)指南。


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[社交 Framework 介绍](~/ios/platform/social-framework.md)

社交框架提供一个统一的 API 的用户在中国包括 Twitter 和 Facebook，以及 SinaWeibo 的社交网络进行交互。 有关详细信息请参阅、[社交 Framework 介绍](~/ios/platform/social-framework.md)指南。


##  <a name="changes-to-storekitchanges-to-storekitmd"></a>[对 StoreKit 的更改](changes-to-storekit.md)

Apple 引入了存储工具包中的两项新功能： 购买和下载 iTunes 或从你的应用中的应用商店内容和托管应用内购买你的内容文件 ！。 有关详细信息请参阅、[更改为应用商店工具包](changes-to-storekit.md)指南。


## <a name="other-changes"></a>其他更改


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload 和 ViewDidUnload 弃用

`ViewWillUnload`和`ViewDidUnload`方法`UIViewController`不再在 iOS 6 中调用。 在以前版本的 iOS，这些方法可能具有已应用程序用来分别保存状态之前卸载视图和清理代码。

例如，适用于 Mac 的 Visual Studio 将创建调用的方法`ReleaseDesignerOutlets`、 下面，然后将从调用其显示`ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

但是，在 iOS 6 中，它不再需要调用`ReleaseDesignerOutlets`。   
   
   
   
对于清理代码，iOS 6 应用程序应使用`DidReceiveMemoryWarning`。 但是，代码调用`Dispose`应尽量少使用，并且仅用于内存密集型对象如图所示下面：

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

同样，调用`Dispose`如上所述应该很少需要。 在大多数应用程序应执行的常规是移除事件处理程序。

保存状态的情况下，对于应用程序可以执行在`ViewWillDisappear`和`ViewDidDisappear`而不是`ViewWillUnload`。


### <a name="iphone-5-resolution"></a>iPhone 5 解析

iPhone 5 设备具有 640 x 1136 解析。 针对 iOS 的早期版本的应用程序将 letterboxed 运行时显示在 iPhone 5，如下所示：

 [![](images/01-letterboxed.png "针对 iOS 的早期版本的应用程序会出现 letterboxed iPhone 5 上运行时")](images/01-letterboxed.png#lightbox)

在应用程序会出现的顺序全屏幕上 iPhone 5，只需添加了名为映像`Default-568h@2x.png`具有 640 x 1136 的分辨率。 以下屏幕截图显示运行之后此映像已包含的应用程序：

 [![](images/02-fullscreen.png "此屏幕快照显示运行之后此映像已包含的应用程序")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>子类化 UINavigationBar

在 iOS 6`UINavigationBar`都可以子类化。 这样，其他控件的外观和感觉的`UINavigationBar`。 例如，应用程序可以添加子视图、 进行动画处理这些视图和修改的边界的子类`UINavigationBar`。

下面的代码演示一种子类化`UINavigationBar`，它将`UIImageView`:

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

若要添加子类化`UINavigationBar`到`UINavigationController`，使用`UINavigationController`构造函数采用的一种`UINavigationBar`和`UIToolbar`，如下所示：

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

使用此`UINavigationBar`子类导致图像视图显示如以下屏幕截图中所示：

 [![](images/03-navbar.png "在此屏幕截图中所示显示图像视图中使用此 UINavigationBar 子类结果")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>接口方向

在 iOS 之前 6 的应用程序无法覆盖`ShouldAutorotateToInterfaceOrientation`，返回 true 任何方向为特定控制器支持。 例如，下面的代码将用于支持仅纵向：

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

在 iOS 6`ShouldAutorotateToInterfaceOrientation`已弃用。
而是应用程序可以重写`GetSupportedInterfaceOrientations`在根视图控制器如下所示：

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

在 iPad，这将默认为所有四个方向如果`GetSupportedInterfaceOrientation`未实现。 在 iPhone 和 iPod Touch 上，默认值是除之外的所有方向`PortraitUpsideDown`。
