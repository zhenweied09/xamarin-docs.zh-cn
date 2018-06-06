---
title: watchOS Xamarin 中的菜单控件 (强制 Touch)
description: 本文档介绍如何在 Xamarin 中使用 watchOS 强制触摸手势。 还会讨论如何响应强制触摸屏输入，如何添加一个菜单，并更改菜单项。
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4b973b925b99189416087224644c376864c56871
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791340"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>watchOS Xamarin 中的菜单控件 (强制 Touch)

监视工具包提供触发菜单当在监视应用程序屏幕上执行强制触摸手势。

![](menu-images/menu.png "显示一个菜单的 Apple Watch")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>响应强制触摸

如果`Menu`实现了对于接口控制器，当用户执行强制 Touch 将显示菜单。 如果尚未实施任何菜单，屏幕简要动画处理的任何其他操作时发生。

强制收尾工作不与任何屏幕; 上的特定元素相关联只有一个菜单可以附加到接口控制器并且其将出现而不考虑强制 Touch 按屏幕上出现的位置。

一个和第四个菜单之间可以显示选项。


## <a name="adding-a-menu"></a>添加菜单

A`Menu`必须添加到`InterfaceController`在设计时情节提要。 菜单控件拖动到接口控制器上时没有情节提要 preview 上的无可视指示但**菜单**出现在**文档大纲**填充：

![](menu-images/menu-action.png "在设计时编辑菜单")

最多四个菜单项可以添加到菜单控件。 它们可以在中配置**属性**填充。 可以设置以下属性：

- 标题，并
- 自定义映像，或
- 系统映像： 接受，请添加、 块、 拒绝、 信息，可能是，更多，静音、 暂停、 播放、 重复，恢复、 共享、 随机排布、 发言人、 垃圾桶。

创建`Action`通过选择**事件**部分**属性**板和键入的操作方法的名称。 分部方法将创建在代码中，可以实现在接口控制器类中，如下：

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>自定义映像

类似于在 iOS 中的选项卡图像，菜单项图像需要使用 alpha 通道，以允许背景以显示通过的不透明模式。

你应添加用于为获得最佳性能监视应用程序项目 （不是监视应用程序扩展项目） 的菜单的图像。


## <a name="changing-the-menu-items"></a>更改菜单项

<!--
### Design Time Items

Menu items added the the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>在运行时添加

不会导致`Menu`要添加到接口控制器在运行时，尽管的集合`MenuItem`s*可以*以编程方式更改。
使用`AddMenuItem`方法如下所示：

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

Xamarin.iOS 监视工具包 API 当前需要`selector`为`AdMenuItem`方法，应进行如下声明：

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>删除在运行时

`ClearAllMenuItems`可以调用方法以删除所有*以编程方式添加*菜单项。

不能清除在情节提要中配置的菜单项。



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的菜单文档](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
