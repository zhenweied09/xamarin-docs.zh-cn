---
title: "使用屏幕大小"
ms.topic: article
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 81e0a9c8d65e354704f01ce70b8eb0b1b67f993f
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="working-with-screen-sizes"></a>使用屏幕大小

Apple Watch 现已推出两种屏幕大小：

- **38mm**
  - 136 x 170 逻辑像素为单位 （272 x 340 物理像素为单位）

- **42mm**
  - 156 x 195 逻辑像素 （312 x 390 物理像素为单位）。

你应考虑到在设计和测试你的应用程序时的屏幕的大小。

## <a name="watchos-interface-designer"></a>watchOS 接口设计器

默认情况下将显示 Visual Studio for Mac 设计器观看接口控制器在**Any Apple Watch**。

![](screen-sizes-images/screen-any-sml.png "在任何 Apple Watch 设计器显示监视接口控制器")

使用大小菜单可以编辑和预览将情节提要在任一可用的屏幕大小： **38 mm**或**42 mm**:

![](screen-sizes-images/screen-menu-sml.png "选择 38 mm 或 42 mm 的大小")

更大的屏幕大小有时将呈现将截断/隐藏较小屏幕上的内容。
请务必在这两个大小上进行测试。


### <a name="interface-design"></a>接口设计

你的应用程序应显示在屏幕上，而不考虑大小，相同的内容和应展开或折叠与相应的元素。 在 Visual Studio for Mac 设计器中，在属性检查器中，应使用**相对于容器**或**大小以适应内容**优先于固定大小。

![](screen-sizes-images/sizeattributepanel-sml.png "相对于容器或为适合内容的大小，请优先使用固定的大小")

由于黑色挡板括在监视屏幕，不建议提供围绕你的接口的填充。 使元素靠屏幕边缘，并让窗体应用程序周围的自然边框挡板。


## <a name="watchos-simulator"></a>watchOS 模拟器

当模拟器上测试你可以轻松地切换之间使用的两个屏幕大小**硬件 > 设备**菜单。

![](screen-sizes-images/simulator.png "模拟器可以使用硬件设备菜单的两个屏幕大小之间进行切换")


## <a name="image-resources"></a>图像资源

如果单个资产看起来不很好地不同的大小，则应使用多个图像资产。 图像资产目录允许单独的位图，以指定每个大小：

![](screen-sizes-images/images-xcassets.png "图像资产目录编辑器")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

或者，使用代码来确定的屏幕大小和完全加载不同的映像：

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

阅读有关使用的详细信息[图像控件](~/ios/watchos/user-interface/image.md)。



## <a name="related-links"></a>相关链接

- [watchOS 3 简介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
