---
title: 检查实时应用程序
description: 本文档介绍如何使用 Xamarin 检查器检查应用程序。 它还介绍了 Xamarin 检查器工具的限制。
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: topgenorth
ms.author: toopge
ms.date: 06/19/2018
ms.openlocfilehash: 67cc6b42901521226322d964514f19b4b639148b
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268818"
---
# <a name="inspecting-live-applications"></a>检查实时应用程序

实时应用程序检查是适用于企业的客户。

1. 打开任何[支持应用程序项目](~/tools/inspector/install.md#supported-platforms)Visual Studio 中用于 Mac 或 Visual Studio。
1. 在调试模式下运行你的应用。
1. 单击**检查**IDE 工具栏中的按钮 (在 Visual Studio 中，**检查当前的应用。 ...** 菜单项也会提供**工具**或**调试**菜单)。

[![](inspect-images/mac-heres-the-button.png "单击 IDE 工具栏中的检查按钮")](inspect-images/mac-heres-the-button.png#lightbox)

将打开一个新的 Xamarin 检查器客户端窗口，使用全新的 REPL 提示符。

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "将打开一个新的 Xamarin 检查器客户端窗口，使用全新的 REPL 提示符")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

此窗口出现后，必须交互式 C# 提示可用于执行和 C# 语句和表达式进行求。 这独特之处在于，在目标进程的上下文中计算代码。 在这种情况下，我们展示根据显示的 iOS 应用程序运行的代码。

对应用程序的状态进行任何更改的实际发生目标进程，因此，你可以使用 C#，以更改该应用程序实时，也可以检查实时应用程序的状态。

例如，在 iOS 上，我们可能需要找到我们 UIApplication 委托类，该类是我们 （其中我们存储大量的应用程序状态） 的主要驱动程序：

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

（请注意每个提交发生在多行的编辑器中。 `Shift + Enter` 将创建一个新行，和`Cmd + Enter`(`Ctrl + Enter` Windows 上) 将提交评估的代码。 `Enter` 会自动将提交时是安全的。）

更方便的方法可用于访问你的应用程序的可视元素是使用"检查"按钮。 按此操作时，你可以通过单击你的应用程序选择 UI 元素。 变量`selectedView`将分配为指向在屏幕上的实际元素。 在上面的屏幕截图，你可以看到如何我们访问，然后编辑`selectedView.BarTintColor`上`UISearchBar`我们已选中。

实时可视化树也是非常有用的。 它表示你查看层次结构的当前快照。 你可以选择要设置行`selectedView`REPL 中并查看该视图的属性值。 在 Mac 上，你可以与的分层视图的三维分离型可视化效果进行交互。 在 Windows 上，可以直观地编辑视图的属性值。

## <a name="known-limitations"></a>已知的限制

 - 在主显示器上仅支持视图选择。
 - 属性网格编辑不可用于 Mac，并且在 Windows 上限制为几种数据类型。 用于 REPL 更强大的编辑。
 - 只要安装检查器外接程序/扩展插件并且在 IDE 中启用，我们会将代码注入到你的应用程序每次在调试模式下启动。 如果你注意到你的应用程序中的任何奇怪的行为，请尝试禁用或卸载该检查器外接程序/扩展，重新启动 IDE 中，并且要重新检查。 和请[报告 bug](~/tools/inspector/install.md#reporting-bugs)让我们知道 ！
 - 如果检查 UI 元素后，即可在仍更改，请[告诉我们](~/tools/inspector/install.md#reporting-bugs)，因为这可能表明 bug。

