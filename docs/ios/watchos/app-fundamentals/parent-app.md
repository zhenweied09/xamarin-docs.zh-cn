---
title: 使用 watchOS 中 Xamarin 父应用程序
description: 本文档介绍如何使用 Xamarin 的 watchOS 父应用程序。 它讨论 WatchKit 应用扩展、 iOS 应用、 共享的存储，以及的详细信息。
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 3af2cce0d84e3934eeb89917990f111d29aadef1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790687"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>使用 watchOS 中 Xamarin 父应用程序

> [!IMPORTANT]
> 访问父应用程序仅使用下面的示例可用于 watchOS 1 监视应用。


有不同的方式，监视应用程序和它与捆绑在一起的 iOS 应用之间进行通信：

- 监视扩展可以[调用的方法](#code)针对的父应用程序在 iPhone 上在后台运行。

- 监视扩展可以[共享存储位置](#storage)父 iPhone 应用的。

- Handoff 用于将数据从快速或通知传递到 Watch 应用，将用户发送到应用程序中的特定接口控制器。

父应用程序有时也称为容器应用程序。


<a name="code" />

## <a name="run-code"></a>运行代码

监视扩展和父 iPhone 应用之间进行通信进行了演示[GpsWatch 示例](https://developer.xamarin.com/samples/GpsWatch)。
监视扩展可以请求对父 iOS 应用执行某种处理其代表使用`OpenParentApplication`方法。

为长时间运行的任务 （包括网络请求）-仅父 iOS 应用程序可以充分利用后台处理完成这些任务，并将检索到的数据保存在可访问的监视扩展到的位置，这是特别有用。



### <a name="watch-kit-app-extension"></a>监视工具包应用扩展

调用`WKInterfaceController.OpenParentApplication`监视应用程序扩展中。 它将返回`bool`，该值指示是否已发送方法请求，不论成功与否。 检查`error`在响应中的参数`Action`以确定是否有任何过程中发生在 iPhone 应用中运行的方法。

```csharp
WKInterfaceController.OpenParentApplication (new NSDictionary (), (replyInfo, error) => {
    if(error != null) {
        Console.WriteLine (error);
        return;
    }
    Console.WriteLine ("parent app responded");
    // do something with replyInfo[] dictionary
});
```


### <a name="ios-app"></a>iOS 应用程序

从监视应用程序扩展的所有调用都路由通过 iPhone 应用`HandleWatchKitExtensionRequest`方法。
如果您正在监视应用程序中进行不同的请求，则此方法将需要查询`userInfo`字典来确定如何处理该请求。


```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    // ... other AppDelegate methods
    public override void HandleWatchKitExtensionRequest
        (UIApplication application, NSDictionary userInfo, Action<NSDictionary> reply)
    {
        var status = 2;
        // do something in the background, and respond
        reply (new NSDictionary (
            "count", NSNumber.FromInt32 ((int)status),
            "value2", new NSString("some-info")
            ));
    }
}
```


<a name="storage" />

## <a name="shared-storage"></a>共享的存储

如果你配置[应用程序组](~/ios/watchos/app-fundamentals/app-groups.md)则 iOS 8 扩展 （包括监视扩展） 可以与父应用程序共享数据。

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>使用 NSUserDefaults

可以监视应用程序扩展和父 iPhone 应用中编写以下代码，以便它们可以引用一组通用的`NSUserDefaults`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files" />

### <a name="files"></a>文件

IOS 应用和监视扩展还可以共享文件使用的公共文件路径。

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

注意： 如果该路径是`null`然后检查[应用程序组配置](~/ios/watchos/app-fundamentals/app-groups.md)以确保预配配置文件是否已正确配置且已在开发计算机上下载/安装。

有关详细信息，请参阅[应用组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文档。

## <a name="wormholesharp"></a>WormHoleSharp

WatchOS 1 的受欢迎的开源机制 (基于[MMWormHole](https://github.com/mutualmobile/MMWormhole)) 父应用程序和监视应用程序之间传递数据或命令。

你可以配置旋涡式星体使用如下中将 iOS 应用程序的应用程序组，并观看扩展：

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

下载的 C# 版本[WormHoleSharp](https://github.com/Clancey/WormHoleSharp)。



## <a name="related-links"></a>相关链接

- [GpsWatch （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WormHoleSharp （示例）](https://github.com/Clancey/WormHoleSharp)
- [Apple 的 WKInterfaceController 引用](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 的与应用程序包含共享数据](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
