---
title: "UrhoSharp iOS 和 tvOS 的支持"
description: "iOS 和 tvOS 的特定设置以及 UrhoSharp 的功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 465fed25f360f29ad0b63146add8de939fa8924e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="urhosharp-ios-and-tvos-support"></a>UrhoSharp iOS 和 tvOS 的支持

_iOS 和 tvOS 特定安装程序和功能_

尽管 Urho 是一个可移植类库，并且允许相同的 API，用于跨各种平台的游戏的逻辑，仍需要初始化 Urho，在您平台特定的驱动程序，并在某些情况下，你将想要利用平台特定的功能.

在下面的页面中，假定`MyGame`是的 sublcass`Application`类。

## <a name="ios-and-tvos"></a>iOS 和 tvOS

**支持的体系结构：** armv7，arm64，i386

## <a name="creating-a-project"></a>创建项目

创建 iOS 项目，然后将数据添加到资源目录，并确保所有文件都具有**BundleResource**作为**生成操作**。

![项目设置](ios-images/image-4.png "的数据添加到的资源目录")

## <a name="configuring-and-launching-urho"></a>配置和启动 Urho

添加 using 语句`Urho`和`Urho.iOS`命名空间，并将这段代码用于初始化 Urho，以及启动你的应用程序：

```csharp
new MyGame().Run();
```

请注意，因为 iOS 需要`FinishedLaunching`若要完成，你应队列对的调用`Run()`若要运行该方法完成后，这是一种常见的方法：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    LaunchGame();
    return true;
}

async void LaunchGame()
{
    await Task.Yield();
    new SamplyGame().Run();
}
```

务必禁用 PNG 优化，因为默认 iOS PNG 优化器将生成 Urho 不可以正确当前使用的映像

## <a name="custom-embedding-of-urho"></a>自定义嵌入的 Urho

你可以或者使其 Urho 接管整个应用程序屏幕中，并且若要使用它为你的应用程序的一个组件，可以创建`UrhoSurface`即`UIView`，可以在现有应用程序中嵌入。

这是你需要：

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

因此，这将承载您 Urho 的类，然后将执行操作：

```csharp
new MyGame().Run ();
```

