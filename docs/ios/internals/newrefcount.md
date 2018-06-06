---
title: 新引用计数在 Xamarin.iOS 的系统
description: 本文档介绍了 Xamarin 的增强的引用计数系统，默认情况下在所有的 Xamarin.iOS 应用程序中启用。
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f2e40ca1fdd4a02d62e45004b75f3abefda781a5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786247"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>新引用计数在 Xamarin.iOS 的系统

Xamarin.iOS 9.2.1 引入的增强的引用，默认情况下计数对所有应用程序的系统。 它可以用于消除许多很难进行跟踪和解决在早期版本的 Xamarin.iOS 的内存问题。

## <a name="enabling-the-new-reference-counting-system"></a>启用新的引用计数系统

从 Xamarin 9.2.1 开始新的 ref 计数系统已经启用了**所有**默认情况下的应用程序。

如果你正在开发的现有应用程序，你可以检查以确保将.csproj 文件的所有匹配项`MtouchUseRefCounting`设置为`true`、 like 下面：

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

如果设置为`false`你的应用程序将不使用新的工具。

### <a name="using-older-versions-of-xamarin"></a>使用旧版本的 Xamarin

Xamarin.iOS 7.2.1 和上面功能增强我们新引用计数系统的预览。

**经典 API:**

若要启用此新引用计数系统，请检查**使用引用计数扩展**复选框位于**高级**项目的选项卡**iOS 生成选项**如下所示： 

[![](newrefcount-images/image1.png "启用新的引用计数系统")](newrefcount-images/image1.png#lightbox)

请注意，这些选项中，已删除较新版本的 Visual Studio for mac。

 **[统一的 API:](~/cross-platform/macios/unified/index.md)**

 新的引用计数扩展需要统一 API，因此应该启用默认情况下。 较旧版本的 IDE 可能没有自动检查此值，你可能需要勾选由它自己。

    
> [!IMPORTANT]
> 此功能的早期版本已被解决由于 MonoTouch 5.2，但却仅可用于**sgen**作为实验预览。 此新的增强版本现在也是可用于**Boehm**垃圾回收器。


从历史上看发生了两种类型的对象由 Xamarin.iOS： 那些通过让额外的内存中状态通常是只是本机对象 （对等对象） 和那些扩展或合并新功能 （派生对象） 的周围的包装器。 以前为可能，我们无法增加具有状态的对等对象 （例如通过添加一个 C# 事件处理程序），但我们让转未引用，然后收集的对象。 这可能导致崩溃更高版本上 (例如如果 Objective C 运行时返回到调用的托管对象)。

新的系统会自动升级到当它们在存储的任何额外信息时由运行时托管的对象的对等对象。

这解决了各种崩溃发生的情况下，例如这个：

```csharp
class MyTableSource : UITableViewSource {
   public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath) {
        var cell = tableView.DequeueReusableCell ("myId");
        if (cell != null)
                return cell;

        cell = new UITableViewCell (UITableViewCellStyle.Default, "myId");
        var txt = new UITextField ();
        txt.TouchDown += delegate { Console.WriteLine ("...."); };
        cell.ContentView.AddSubview (txt);
        return cell;
   }
}
```

此代码将不带引用计数扩展名崩溃因为`cell`变为可回收，，因此其`TouchDown`委托，这会将它们转换为无关联的指针。

引用计数扩展确保托管的对象保持处于活动状态，并防止其集合，提供的本机对象保留由本机代码。

新的系统也无需*大多数*私有备份在绑定-这是使实例保持活动状态的默认方法中使用的字段。 托管的链接器足够智能，可删除所有那些*不需要*字段从应用程序使用新的引用计数扩展。

这意味着每个托管的对象实例使用比之前更内存。 它还解决了其中一些支持字段将保留不由 Objective C 运行时，使得很难回收某些内存不再需要的引用的相关的问题。
