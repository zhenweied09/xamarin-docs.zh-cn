---
title: tvOS 10 简介
description: 本文介绍的所有新增和更改 Api 和 tvOS 10 中可用的功能为 Xamarin.tvOS 开发人员。
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 642a851cbfc0450ee8f5f5c4d798c40778e3d3dd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-tvos-10"></a>tvOS 10 简介

_本文介绍的所有新增和更改 Api 和 tvOS 10 中可用的功能为 Xamarin.tvOS 开发人员。_

与新 tvOS 10 SDK Apple 包含新 Api 和服务，使开发人员创建的应用程序和功能的新类别。 

有关 tvOS 10 的详细信息，请参阅 Apple 的[tvOS + 应用](https://developer.apple.com/tvos/)文档。

## <a name="whats-new-in-tvos-10"></a>什么是新建 tvOS 10

Apple 具有 tvOS 10 以及对现有功能，包括许多增强功能中添加几个新的 Api 和服务：

## <a name="new-user-interface-styles"></a>新的用户界面样式

tvOS 10 现在支持深色和浅色用户界面主题，所有生成在 UIKit 控件将自动调整以适应，根据用户的首选项。

当创建和实现新的自定义 UI 控件，开发人员应使用[UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection)类，以适应用户所选主题。

有关详细信息，请参阅我们[新用户界面样式](~/ios/tvos/platform/user-interface-styles.md)文档。

## <a name="security-and-privacy-enhancements"></a>安全和隐私增强功能

Apple 安全和隐私 tvOS 10，可帮助开发人员提高其应用程序的安全性，并确保最终用户的隐私对进行多项增强功能。

因此，watchOS 3 （或更高版本） 上运行的应用必须以静态方式声明其想要通过输入中一个或多个隐私特定键访问特定功能或用户信息其`Info.plist`向为什么应用程序希望获得访问权限的用户说明的文件。

由于 tvOS 10 与 iOS 10 共享这些更改，请参阅我们的 iOS 10[安全和隐私增强功能](~/ios/app-fundamentals/security-privacy.md)指南以获取详细信息。

## <a name="video-subscriber-account"></a>视频的订阅服务器帐户

新对 tvOS 10，该视频订阅服务器帐户框架允许在应用该支持身份验证的流式处理或视频点播向其电缆或附属电视提供程序为最终用户使用单一登录体验进行身份验证。

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>广泛的颜色

tvOS 10 扩展对扩展范围像素格式和整个系统包括框架，例如核心图形、 Core 映像、 金属和 AVFoundation 宽色域颜色空间的支持。 通过提供在整个图形堆栈整个此行为可以进一步简化对具有广泛的颜色显示设备的支持。

此外，`UIKit`已修改的新工作扩展**sRGB**色彩空间，从而更便于混合中而不会显著的性能降低的广泛的颜色色域的颜色。

在处理宽颜色时，Apple 将提供下列最佳方案：

 - `UIColor` 现在使用 sRGB 颜色空间，将不再 clamp 值`0.0`到`1.0`范围。 如果应用程序依赖于以前的夹具行为，它将需要为 tvOS 10 进行修改。
 - 如果该应用程序执行的自定义呈现`UIImages`，使用新[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)类来指定的扩展范围或标准的格式的用法。
 - 当使用低级别的 API，如核心图形或裸机提供图像处理，则应用程序应使用支持 16 位浮点值扩展的范围颜色空间和像素格式。 在必要时，应用程序将需要手动 clamp 颜色组件值。
 - 核心图形、 Core 映像和金属性能着色器都提供两个颜色空间之间进行转换的新方法。

若要了解详细信息，请参阅我们[简介广泛的颜色](~/ios/platform/wide-color.md)指南。

## <a name="newly-available-existing-frameworks"></a>新的可用现有框架

对 iOS （而不 tvOS），可用的多个框架有可供对 tvOS 10 如：

 - ExternalAccessory
 - HomeKit
 - MultipeerConnectivity
 - 照片
 - ReplayKit
 - UserNotification

## <a name="additional-framework-changes"></a>附加的框架的更改

除了的主要 framework 更改和上面列出的添加件，Apple 已作出 tvOS 10 中的许多其他的次要 framework 更改。

若要了解详细信息，请参阅我们[其他 Framework 更改](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>弃用的 API

没有 Api 或框架已否决 tvOS 10。 请参阅 Apple 的[tvOS 10 API 差异](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html)API 修改的文档的完整列表。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [什么是 tvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
