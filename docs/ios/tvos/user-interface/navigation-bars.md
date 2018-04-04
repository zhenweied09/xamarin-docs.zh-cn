---
title: 使用导航控制器
description: 本文介绍如何设计和使用在 Xamarin.tvOS 应用内的导航栏。
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 8a9a1c852137a2bcc0d46615e69eef0a245a9768
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-navigation-controllers"></a>使用导航控制器

_本文介绍如何设计和使用在 Xamarin.tvOS 应用内的导航栏。_

导航栏可以添加到要显示的标题和可选的导航栏按钮的视图的顶部。 它们通常用于当用户导航从主页上，如表视图、 集合或菜单与子视图显示选定项的详细信息。

[![](navigation-bars-images/navbar01.png "示例导航栏")](navigation-bars-images/navbar01.png#lightbox)

添加到的标题 （即会显示在中心），在导航栏可以包含一个或多个导航栏按钮 (`UIBarButtonItem`) 在左侧和右侧栏。

> [!IMPORTANT]
> 导航栏是默认情况下完全透明的。 应格外小心以确保导航栏的内容对其下的内容保持可读。 例如，在表视图或集合中的内容滚动时它的下方。




<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>导航栏和情节提要

使用 Xamarin.tvOS 应用中的导航栏的最简单方法是将它们添加到使用 iOS 设计器的应用程序的 UI。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


1. 在**解决方案 Pad**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**导航栏**从**工具箱**并将其放在屏幕顶部的视图： 

    [![](navigation-bars-images/navbar02.png "导航栏")](navigation-bars-images/navbar02.png#lightbox)
1. 双击**导航栏**选择到**导航项**。 在**小组件**选项卡**属性填充**，你可以设置**标题**: 

    [![](navigation-bars-images/navbar03.png "设置标题")](navigation-bars-images/navbar03.png#lightbox)
1. 接下来，您可以在其中添加一个或多个**栏按钮项**到两端的栏： 

    [![](navigation-bars-images/navbar04.png "一个工具栏按钮项")](navigation-bars-images/navbar04.png#lightbox)
1. 最后，网络向上**栏按钮项**于中的操作**事件**选项卡**属性资源管理器**: 

    [![](navigation-bars-images/navbar05.png "条形按钮项操作")](navigation-bars-images/navbar05.png#lightbox)
1. 保存更改。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**导航栏**从**工具箱**并将其放在屏幕顶部的视图： 

    [![](navigation-bars-images/navbar02-vs.png "导航栏")](navigation-bars-images/navbar02-vs.png#lightbox)
1. 双击**导航栏**选择到**导航项**。 在**小组件**选项卡**属性资源管理器**，你可以设置**标题**: 

    [![](navigation-bars-images/navbar03-vs.png "设置标题")](navigation-bars-images/navbar03-vs.png#lightbox)
1. 接下来，您可以在其中添加一个或多个**栏按钮项**到两端的栏： 

    [![](navigation-bars-images/navbar04-vs.png "条形按钮项")](navigation-bars-images/navbar04-vs.png#lightbox)
1. 最后，网络向上**栏按钮项**于中的操作**事件**选项卡**属性资源管理器**: 

    [![](navigation-bars-images/navbar05-vs.png "条形按钮项操作")](navigation-bars-images/navbar05-vs.png#lightbox)
1. 保存更改。


-----

> [!IMPORTANT]
> 尽管可以将事件分配如`TouchUpInside`到 UI 元素 （例如 UIButton) iOS 设计器中，它将永远不会调用因为 Apple TV 没有触摸屏幕或支持触控事件。 应始终使用`Primary Action`事件时创建用户界面元素的对 tvOS 的事件处理程序。




下面的代码在三个不同 BarButtonItems 上提供的事件处理程序的示例： `ShowFirstHotel`， `ShowSecondHotel`，和`ShowThirdHotel`。 单击每个项时，背景图像`HotelImage`更改。 这在视图控制器中编辑 (示例`ViewController.cs`) 文件：

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

只要按钮的`Enabled`属性是`true`和不受另一个控件或视图，则它可进行使用 Siri 远程处于焦点项。

有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖设计和使用在 Xamarin.tvOS 应用内的导航栏。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
