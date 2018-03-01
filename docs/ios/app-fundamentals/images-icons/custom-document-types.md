---
title: "自定义文档图标"
description: "此文章介绍如何包括和管理一个 Xamarin.iOS 应用程序中的图像资产，用作自定义文档类型的图标。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/23/2017
ms.openlocfilehash: 582fcbacbf1959e05773babb1219817ba319a937
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="custom-document-icons"></a>自定义文档图标

_此文章介绍如何包括和管理一个 Xamarin.iOS 应用程序中的图像资产，用作自定义文档类型的图标。_

如果一个 Xamarin.iOS 应用程序支持加载特定的文档类型，开发人员可以提供在遇到该文档类型，例如当用户按住中的附件时，系统将使用的图标*邮件应用程序*作为此处所示：

 [ ![](custom-document-types-images/17.png "下面举例说明文档类型图标")](custom-document-types-images/17.png)

开发人员可以将文档类型信息添加应用程序中的文件格式是支持的左包括字典条目`CFBundleTypeName`字符串和`LSItemContentTypes`中应用的数组`Info.plist`。 文档类型的图标进入`CFBundleTypeIconFiles`数组。 如果未提供一个文档图标，iOS 将是派生的应用程序图标。
可以为多种大小，针对各种设备分辨率进行了优化提供图标。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要将 Visual Studio 中的这些值分配适用于 Mac，使用**文档类型**部分下**高级**选项卡上`Info.plist`编辑器来添加文档类型，并为其分配图像图标。 例如，下面是显示的 PDF 支持注册屏幕截图：

 [ ![](custom-document-types-images/18.png "在 Info.plist 编辑器上的高级选项卡下的文档类型部分")](custom-document-types-images/18.png)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要分配这些值在 Visual Studio 中的，使用**文档类型**部分下**高级**选项卡上`Info.plist`:

 ![](custom-document-types-images/doc01w.png "打开高级选项卡下的文档类型部分")

单击**添加文档类型**按钮，然后填写必填字段：

![](custom-document-types-images/doc02w.png "添加文档类型窗体")

-----


有关文档类型的详细信息，请参阅 Apple 的[统一类型标识符引用](http://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html)和[文档适用于 iOS 的交互编程主题](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html)。


## <a name="related-links"></a>相关链接

- [使用图像 （示例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和映像创建指南](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
