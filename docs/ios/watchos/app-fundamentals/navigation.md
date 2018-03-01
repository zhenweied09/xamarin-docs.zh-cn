---
title: "使用导航"
ms.topic: article
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 79277d412b87e6ac44557122fa06d4d5d873fd38
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-navigation"></a>使用导航

最简单导航上的选项可监视是一个简单[模式弹出](#modal)，出现在当前的场景顶部。

存在多场景监视应用是否可用的两个导航范例：

- [分层导航](#Hierarchical_Navigation)
- [基于页面的接口](#Page-Based_Interfaces)

## <a name="modal-interfaces"></a>模式的接口

使用`PresentController`方法以模式方式打开接口控制器。 必须已在中定义的接口控制器**Interface.storyboard**。

```csharp
PresentController ("pageController","some context info");
```

有模式地显示控制器使用整个屏幕 （覆盖以前相同）。 默认情况下，标题设置为**取消**和点击它会消除控制器。

若要以编程方式关闭以模式方式显示控制器，调用`DismissController`。

```csharp
DismissController();
```

模式的屏幕可以是单个场景或使用基于页面的布局。


## <a name="hierarchical-navigation"></a>分层导航

显示例如可以通过导航，类似的方式的堆栈的场景`UINavigationController`适用于 iOS。 可以推送到导航堆栈上和 （以编程方式或通过用户选择） 中弹出场景。

![](navigation-images/hierarchy-1.png "可以在导航堆栈上推送后台") ![ ](navigation-images/hierarchy-2.png "场景可以从导航堆栈中弹出")

与 iOS，左侧边缘滑动导航回父控制器分层导航堆栈中。

这两个[WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog)和[WatchTables](https://developer.xamarin.com/samples/WatchTables)示例包括层次结构导航。

### <a name="pushing-and-popping-in-code"></a>推送和弹出在代码中

观看工具包不需要过度存档的"导航控制器"要创建类似 iOS 执行的操作-只需推送控制器使用`PushController`方法和导航堆栈将自动创建。

```csharp
PushController("secondPageController","some context info");
```

监视的屏幕将包括**回**按钮在左上角，但你可以以编程方式从导航堆栈使用删除场景`PopController`。

```csharp
PopController();
```

因为 ios，它也是无法返回到导航堆栈使用的根`PopToRootController`。

```csharp
PopToRootController();
```

### <a name="using-segues"></a>使用 Segue

Segue 可以定义层次结构导航情节提要中的场景之间创建。 若要获取目标场景，操作系统调用的上下文`GetContextForSegue`初始化新的接口控制器。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```

## <a name="page-based-interfaces"></a>基于页面的接口

基于页面的接口往下轻扫左到右，方式类似于`UIPageViewController`适用于 iOS。 指示器点将显示该屏幕以显示当前显示哪一页的底部。

![](navigation-images/paged-1.png "示例的第一页") ![ ](navigation-images/paged-2.png "示例第二页") ![ ](navigation-images/paged-5.png "示例第五个页")


若要使基于页面的接口监视应用程序的主 UI，使用`ReloadRootControllers`具有接口控制器和上下文的数组：

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

你还可以呈现不是根的基于页面的控制器使用`PresentController`从一个其他后台应用程序中。

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WatchTables （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchTables/)
