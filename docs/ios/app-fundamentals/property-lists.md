---
title: "使用属性列表"
description: "本文档介绍 Visual Studio for Mac 的图形和高级属性列表 (.plist) 编辑器了用于处理 Info.plist 和 Entitlements.plist。 它阐释设置图标和从 iOS 应用程序的启动映像在 Visual Studio for mac。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 778e70f6817b71e5910aa85425d46261dfe9c803
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-property-lists"></a>使用属性列表

_本文档介绍 Visual Studio for Mac 的图形和高级属性列表 (.plist) 编辑器了用于处理 Info.plist 和 Entitlements.plist。它阐释设置图标和从 iOS 应用程序的启动映像在 Visual Studio for mac。_

适用于 Mac 的 visual Studio 功能，使编辑应用程序属性和功能更轻松的图形.plist 编辑器。 适用于 Mac 的 visual Studio 具有两个.plists-`Info.plist`用于编辑应用程序属性和图标，和`Entitlements.plist`用于管理应用程序功能。 本指南介绍 Info.plists 并提供了概述中处理 Visual Studio for mac。 Entitlements.plist 的信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。

## <a name="infoplist"></a>Info.plist

信息属性列表 ( `Info.plist`) 是一个需要的 iOS 文件，提供有关你的应用程序配置到系统的信息。 适用于 Mac 的 visual Studio 的自定义`Info.plist`受选项卡底部的三个面板左下的编辑器窗口的编辑器功能：

 [![](property-lists-images/tabs.png "在底部的 Info.plist 编辑器选项卡左下的编辑器窗口")](property-lists-images/tabs.png#lightbox)

每个面板控件不同的属性，按如下所述：

-  **应用程序面板**-一个图形界面来设置常见应用程序属性，以及图标和启动映像; 指定地图集成和 backgrounding 模式。
-  **高级面板**-高级面板中，可以指定支持的文档类型，UTIs，和 URL 类型。
-  **源面板**-源面板控件不太常见的属性，以及应用程序的自定义属性。


接下来三节调查的更多详细信息中的每个面板的功能。

## <a name="application-panel"></a>应用程序面板

适用于 Mac 的 visual Studio 功能用于编辑常见的图形界面`Info.plist`应用程序的条目：

1.  应用程序属性
1.  受支持的设备类型
1.  对于每个设备类型的支持的方向
1.  状态栏样式和颜色
1.  图标和启动屏幕
1.  图和后台模式


以下各节中更详细地描述了这些内容。

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>iOS 应用程序目标

本部分包含描述应用程序的重要信息。
**标识符**存储此处必须与输入时，在 iTunes Connect （适用于应用商店应用） 中，还在 iOS 设置门户应用 Id 列表中，开发和分发证书的捆绑标识符。

 [![](property-lists-images/image24.png "iOS 应用程序目标")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>设备部署

 [![](property-lists-images/deployment.png "设备部署")](property-lists-images/deployment.png#lightbox)

设备**部署**信息部分有选择地，具体取决于在所选内容显示**设备**中的下拉列表**应用程序目标**上面一节。 **主界面**下拉列表设置为**MainStoryboard**情节提要驱动应用程序中。 如果用代码完全编写用户界面，则此可以保留为空。

### <a name="supported-device-orientations"></a>受支持的设备方向

 **支持设备方向**控制应用程序如何响应的设备旋转。 它是很常见的 iPhone/iPad 应用仅支持**纵向**，或所有内容，但**倒置**。 通常所有 iPad 应用程序，除非游戏应都支持所有的方向。

### <a name="status-bar-styles"></a>状态栏样式

**状态条样式**部分是用于编辑应用程序的一个图形界面`UIStatusBarStyle`:

 [![](property-lists-images/status.png "状态栏样式")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>图标、 启动映像和 iTunes 图稿

在你的 Info.plist 文件中使用图标、 图像和图片的信息可在[处理映像](~/ios/app-fundamentals/images-icons/index.md)指南。




### <a name="maps-integration-and-background-modes"></a>映射集成和后台模式

`Info.plist`包含特殊的各节来指定地图集成和 backgrounding 模式。 选择你想要支持的选项将向你为你的应用程序中添加所需的属性。

 [![](property-lists-images/maps.png "映射集成")](property-lists-images/maps.png#lightbox)

有关使用图的详细信息，请参阅 Xamarin [iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)指南。

 [![](property-lists-images/bging.png "后台模式")](property-lists-images/bging.png#lightbox)

有关后台模式的详细信息，请参阅 Xamarin [Backgrounding 在 iOS 中](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)指南。

## <a name="advanced-panel"></a>高级的面板

高级面板中控制的文档类型和应用程序支持的 URL 方案。

 [![](property-lists-images/image34.png "高级的面板")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>文档类型

对于支持打开的特定文件类型的应用程序，iOS 提供`CFBundleDocumentTypes`密钥。 如果想要应用程序以支持某些已知的文件类型-例如 Pdf-我们将添加 PDF 值的键。 本部分提供了一种简便方式输入数据将存储在`CFBundleDocumentTypes`中的键`Info.plist`文件。

请参阅上的文档[注册你的应用程序支持的文件类型](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)有关如何配置这些值的详细信息。

## <a name="utis"></a>UTIs

有时应用程序需要支持打开自定义的文件类型。 例如，我们可能想要使用的自定义扩展打开映像文件*.xam*。 若要指定自定义文件类型，我们将创建自定义的实用程序的通用类型标识符-使用`UIExportedTypeDeclarations`密钥。 下面的屏幕截图演示如何创建自定义的实用程序.xam 扩展：

 [![](property-lists-images/uti.png "UTIs 编辑器")](property-lists-images/uti.png#lightbox)

只需为导出的类型 UTIs 指定特定于你的应用，自定义 UTIs*导入类型 UTIs* (`UIImportedTypeDeclarations`密钥) 指定支持但不是由你的应用程序拥有的自定义类型。

有关使用自定义 UTIs 的详细信息，请参阅 Apple 的[注册文件类型你应用程序支持](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1)指南。

## <a name="custom-urls"></a>自定义 Url

（也称为协议） 的 URL 方案名称是 URL 的第一部分。 例如，`http://`和`https://`公共 URL 方案。 你可以创建用于你的应用程序的自定义 URL 方案的选择。 自定义 URL 方案用于通信和其他应用程序使用来回发送数据。 下面的屏幕截图演示如何创建一个新的自定义 URL 方案调用`monkeys://`:

 [![](property-lists-images/url.png "自定义 Url")](property-lists-images/url.png#lightbox)



有关实现自定义 URL 方案的详细信息，请参阅 Apple 的[本指南的实现自定义 URL 方案部分](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>源面板

**源**选项卡`Info.plist`文件允许添加或编辑自定义值。 适用于 Mac 的 visual Studio 提供最常见的属性的列表：

 [![](property-lists-images/image31.png "从下拉列表中添加一个新的属性")](property-lists-images/image31.png#lightbox)

为已知属性适用于 Mac 的 Visual Studio 将有效的值的列表，如下面的屏幕截图所示：

 [![](property-lists-images/image32.png "从已知值列表中选择值")](property-lists-images/image32.png#lightbox)

适用于 Mac 的 visual Studio 还将检测属性类型，如所示：

 [![](property-lists-images/image33.png "可用的属性类型")](property-lists-images/image33.png#lightbox)

查看 Apple 的[应用相关资源](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)可选属性的其他信息的链接。

 <a name="Entitlements" />

## <a name="summary"></a>摘要

这篇文章演示了使用图形和高级.plist 编辑器编辑常见应用程序配置中指定图标和启动映像。 它还引入了`Entitlements.plist`用于添加和管理应用程序功能。


## <a name="related-links"></a>相关链接

- [IDE](https://developer.xamarin.com/recipes/cross-platform/ide)
- [应用相关资源](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [你的应用程序支持的注册文件类型](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [实现自定义 URL 方案](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [有关资产目录](https://developer.apple.com/library/ioshttps://developer.xamarin.com/recipes/xcode_help-image_catalog-1.0/Recipe.html)
