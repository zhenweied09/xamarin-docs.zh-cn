---
title: 项目引用
description: IOS 应用程序、 watch 应用和监视扩展之间的关系的说明。
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: f3573e8b578ca567ea9d7360eb132aead4c24f37
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="project-references"></a>项目引用

_IOS 应用程序、 watch 应用和监视扩展之间的关系的说明。_

WatchOS 解决方案中的三个项目是*自动配置*来互相引用 watchOS 3 应用构建和正确捆绑在一起以特定方式。 这些项目引用和捆绑标识符设置下面介绍的引用。

## <a name="project-references"></a>项目引用

通过双击每个项目的引用节点来查看引用：

- **iPhone 应用**引用**Watch 应用**

![](project-references-images/catalog-reference1.png "iPhone 应用程序引用 Watch 应用")

- **观看应用**引用**监视应用程序扩展**

![](project-references-images/catalog-reference2.png "iPhone 应用程序引用 Watch 应用")


 - **监视应用程序扩展**不引用其他项目任一

![](project-references-images/catalog-reference3.png "监视应用程序扩展不引用其他项目")



## <a name="bundle-identifiers"></a>捆绑标识符

你还需要确保你**捆绑标识符**正确无误。
所有三个项目都应具有*相同*标识符前缀，使用具有预定义的扩展的两个监视项目`watchkitextension`和`watchkitapp`、，如下所示 (有关**WatchKitCatalog**示例）：

 - Xamarin.iOS 统一项目- `com.xamarin.WatchKitCatalog`

 - WatchKit 扩展项目- `com.xamarin.WatchKitCatalog.watchkitextension`

 - 监视应用程序项目的 `com.xamarin.WatchKitCatalog.watchkitapp`

此外请确保这些**Info.plist**设置是否正确：

 - 监视应用程序项目的`WKCompanionAppBundleIdentifier`匹配父/容器应用的捆绑 ID (ie。 在 iPhone 运行的一个);

 - 监视工具包扩展项目的**WKApp 捆绑 ID**匹配 Watch 应用项目的捆绑包 id。

你可以通过双击编辑标识符**Info.plist**每个项目中的文件。

此屏幕截图**监视扩展**显示的 Info.plist 文件**Watch 应用**标识符以及：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
![](project-references-images/infoplist-extension.png "此屏幕截图中为监视扩展的 Info.plist 文件")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
![](project-references-images/infoplist-extension-vs.png "此屏幕截图中为监视扩展的 Info.plist 文件")

-----

此屏幕截图**Watch 应用**Info.plist 文件。
当前**监视 OS**版本是 8.2，因此**部署目标**Watch 应用应**8.2**。 请注意，如果您有安装 Xcode 6.3，此值可能设置为 8.3-应更改 8.2。

![](project-references-images/infoplist-watchapp.png "监视 Info.plist 文件")

监视应用程序的部署目标可以是不同于监视扩展和 iOS 应用程序。

