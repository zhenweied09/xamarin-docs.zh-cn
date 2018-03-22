---
title: "图像控件"
ms.topic: article
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4184d7babc396a6b6179e6876dced34b773474b4
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="image-control"></a>图像控件

watchOS 提供[ `WKInterfaceImage` ](https://developer.xamarin.com/api/type/WatchKit.WKInterfaceImage/)控件来显示的图像和简单动画。 某些控件还可以 （如按钮、 组和接口控制器） 的背景图像。

![](image-images/image-walkway.png "Apple Watch 显示图片") ![ ] (image-images/image-animation.png "Apple Watch 与简单动画")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

使用资产目录映像将映像添加到监视工具包应用程序。
仅 **@2x** 版本是必需的因为所有观看具有 Retina 显示的设备。

![](image-images/asset-universal-sml.png "只有 2 x 版本是必需的因为所有观看具有 Retina 显示的设备")

它是好的做法，以确保图像本身监视显示的正确大小。 *避免*使用不正确地尺寸的图像 （尤其是大型的） 和扩展以将其显示在所监视。

可用于监视工具包大小 （38 mm 和 42 mm） 资产目录图像中为每个显示屏大小指定不同图像。

![](image-images/asset-watch-sml.png "可用于监视工具包大小 38 mm 和 42 mm 资产目录图像中为每个显示屏大小指定不同图像")


## <a name="images-on-the-watch"></a>受监视的映像

若要显示图像的最有效方法是*将它们包括在监视应用程序项目*并将其显示使用`SetImage(string imageName)`方法。

例如， [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/)示例有大量的映像添加到资产目录中监视应用程序项目：

![](image-images/asset-whale-sml.png "WatchKitCatalog 示例有大量的映像添加到资产目录中监视应用程序项目")

这些可以有效地加载并显示在监视使用`SetImage`与字符串名称参数：

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>背景图像

相同的逻辑适用于`SetBackgroundImage (string imageName)`上`Button`， `Group`，和`InterfaceController`类。 通过将图像存储在监视应用程序本身才能够达到最佳性能。


## <a name="images-in-the-watch-extension"></a>监视扩展中的映像

除了监视应用本身中存储的图像加载，可以将扩展捆绑的映像发送到显示监视应用程序 （或你无法从远程位置，下载映像，并显示那些）。

若要从监视扩展中加载图像，请创建`UIImage`实例，然后调用`SetImage`与`UIImage`对象。

例如， [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)示例有了名为映像**Bumblebee**监视扩展项目中：

![](image-images/asset-bumblebee-sml.png "WatchKitCatalog 示例包含的图像监视扩展项目中名为 Bumblebee")

下面的代码将导致：

- 正在加载到内存中，映像和
- 显示在手表上。

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>动画

要进行动画处理一组映像，它们应以相同前缀开头的所有，而且具有数字后缀。

[WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)示例在与监视应用程序项目中具有一系列编号图像**总线**前缀：

![](image-images/asset-bus-animation-sml.png "WatchKitCatalog 示例总线前缀与监视应用程序项目中，有一系列带编号的图像")

若要为动画显示这些映像，首先加载映像使用`SetImage`与前缀名称，然后调用`StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

调用`StopAnimating`图像控件，若要停止动画循环上：

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>附录： 缓存映像 (watchOS 1)

> [!IMPORTANT]
> watchOS 3 应用完全在设备上运行。 以下信息适用于 watchOS 1 应用程序。

如果应用程序重复使用存储在扩展 （或已下载） 的映像，则可以缓存在监视的存储中，以提高性能的后续显示图像。

使用`WKInterfaceDevice`s`AddCachedImage`方法来将映像传输到该监视，然后使用`SetImage`与映像名称参数为字符串以便将其显示：

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

您可以查询在代码中使用映像缓存的内容`WKInterfaceDevice.CurrentDevice.WeakCachedImages`。


### <a name="managing-the-cache"></a>管理缓存

缓存大小大约 20 MB。 在应用程序重新启动后，保留和填满时，你有责任清除出文件使用`RemoveCachedImage`或`RemoveAllCachedImages`方法`WKInterfaceDevice.CurrentDevice`对象。



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的映像文档](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
