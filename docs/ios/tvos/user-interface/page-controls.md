---
title: "使用页控件"
description: "本文介绍如何设计和使用在 Xamarin.tvOS 应用内的页控件。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b1b53fefdd72c36bdffd3c5ade0b8d86da225b14
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-page-control"></a>使用页控件

_本文介绍如何设计和使用在 Xamarin.tvOS 应用内的页控件。_

有时你可能需要 Xamarin.tvOS 应用程序中显示一系列的网页或图像。 页面控件旨在清楚地显示用户位于超出最大页数的页。 页控件将显示一系列针对在深色，oval 调整背景的点。 当前页将显示实心的点，其他所有页都显示为空心点。 如果有过多，无法放入其背景区域的页控件将剪切的外部的大多数点。

[![](page-controls-images/page01.png "示例页控件")](page-controls-images/page01.png#lightbox)

设计为向只对用户提供反馈的非交互式元素中的页控件。 你将需要添加其他控件以更改 （如手势或按钮） 的当前页码。

在使用页控件时，Apple 将出现以下建议：

- **在仅完整集合上的使用**-页面控件适合在全屏显示环境来显示单个集合中存在的多个页面中。
- **限制页数**-页面控件适合进行十 （10） 或更少的页，最大为二十 （20） 页。 对于超过 20 个页，请考虑使用[集合视图](~/ios/tvos/user-interface/collection-views.md)和显示在网格中的页。

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>页面控件和情节提要

Xamarin.tvOS 应用中使用的页面控件的最简单方法是将它们添加到使用 iOS 设计器的应用程序的 UI。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

    
1. 在**解决方案 Pad**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**页控件**从**工具箱**并将其放在视图上： 

    [![](page-controls-images/page02.png "页面控件")](page-controls-images/page02.png#lightbox)
1. 在**小组件选项卡**的**属性填充**，你可以调整页控件的几个属性，如其**当前页**和**# 页**: 

    [![](page-controls-images/page03.png "小组件选项卡")](page-controls-images/page03.png#lightbox)
1. 接下来，将控件或笔势添加到视图以向后移动和向前移动页的集合。
1. 最后，将分配**名称**到控件，以便可以在 C# 代码中响应它们。 例如： 

    [![](page-controls-images/page04.png "命名该控件")](page-controls-images/page04.png#lightbox)
1. 保存更改。
    

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

    
1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**页控件**从**工具箱**并将其放在视图上： 

    [![](page-controls-images/page02-vs.png "页面控件")](page-controls-images/page02-vs.png#lightbox)
1. 在**小组件选项卡**的**属性资源管理器**，你可以调整页控件的几个属性，如其**当前页**和**# 页**: 

    [![](page-controls-images/page03-vs.png "小组件选项卡")](page-controls-images/page03-vs.png#lightbox)
1. 接下来，将控件或笔势添加到视图以向后移动和向前移动页的集合。
1. 最后，将分配**名称**到控件，以便可以在 C# 代码中响应它们。 例如： 

    [![](page-controls-images/page04-vs.png "命名该控件")](page-controls-images/page04-vs.png#lightbox)
1. 保存更改。
    

-----

> [!IMPORTANT]
> 尽管可以将事件分配如`TouchUpInside`到 UI 元素 （例如 UIButton) iOS 设计器中，它将永远不会调用因为 Apple TV 没有触摸屏幕或支持触控事件。 应始终使用`Primary Action`事件时创建用户界面元素的对 tvOS 的事件处理程序。




编辑视图控制器 (示例`ViewController.cs`) 文件，并添加代码来处理正在更改的页面。 例如：

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

让我们仔细看一下页面控件的两个属性。 首先，若要指定最大页数，使用以下方法：

```csharp
PageView.Pages = 6;
```

若要更改当前页码，使用以下代码：

```csharp
PageView.CurrentPage = PageNumber;
```

`CurrentPage`属性为零 (0) 工作，因此第一页将为零，最后将其中一个最大页数减。

有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖设计和使用在 Xamarin.tvOS 应用内的页控件。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
