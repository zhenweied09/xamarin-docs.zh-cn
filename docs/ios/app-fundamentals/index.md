---
title: Xamarin.iOS 应用程序基础知识
description: 此文档链接到各种指南描述概念认证 Xamarin.iOS 开发，例如应用程序传输安全的基础 backgrounding 事件，和线程处理。
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/21/2017
ms.openlocfilehash: cdace50d851b2c99f9241b869f248e58d5b93377
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784492"
---
# <a name="xamarinios-application-fundamentals"></a>Xamarin.iOS 应用程序基础知识

本部分提供的指南，在某些更常见的操作任务或开发人员需要时应注意的开发 Xamarin.iOS (以前称为 MonoTouch) 应用程序的概念。

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[应用传输安全性](~/ios/app-fundamentals/ats.md)

本文将介绍应用程序传输安全性 iOS 9 应用程序和这意味着你的 Xamarin.iOS 项目强制实施的安全更改，它将覆盖 ATS 配置选项，以及它将介绍如何选择退出的 ATS，如果需要。 因为默认情况下启用了 ATS，任何不安全的 internet 连接将引发 iOS 9 应用中的异常，（除非已显式允许它）。


## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[后台处理](~/ios/app-fundamentals/backgrounding/index.md)

后台处理或 backgrounding 是让应用程序在前台运行另一个应用程序时在后台执行任务的过程。 该指南可作为后台处理 iOS 中的简介。


## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[事件、 协议和委托](~/ios/app-fundamentals/delegates-protocols-and-events.md)

本文提供了用于接收回调并填充数据的用户界面控件的密钥 iOS 技术。 这些技术是事件、 协议和委托;本文说明了其中每个是以及如何从 C# 使用每个。 它演示了 Xamarin.iOS 如何使用 iOS 控件来公开熟悉.NET 事件，以及如何 Xamarin.iOS 提供对等协议和委托 Objective C 概念的支持 （OBJECTIVE-C 的委托不应与 C# 委托相混淆）。 本文还提供了展示如何协议可兼用于为基础的 OBJECTIVE-C 的委托和非委托方案中的示例。

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[线程处理](~/ios/app-fundamentals/threading.md)

本文讨论在 Xamarin.iOS 应用程序中，线程处理，并介绍有点的.NET 线程池、 响应的应用程序和垃圾回收。&nbsp;

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[使用图像](~/ios/app-fundamentals/images-icons/index.md)

本文介绍如何在 Xamarin.iOS，（例如图标，加载图像，等等） 的应用程序支持图像和应用程序 （例如映像应用于这些控件） 中的图像中使用映像。 它还介绍了如何使用适用于 Mac 的 Visual Studio 将映像，以及如何与代码中的映像进行交互。

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[使用属性列表](~/ios/app-fundamentals/index.md)

本文档介绍 Visual Studio for Mac 的图形和高级属性列表 (.plist) 编辑器了用于处理 Info.plist 和 Entitlements.plist。 它阐释设置图标和启动映像对于 iOS 应用程序，并演示从指定的应用程序功能 （授权） 在 Visual Studio for mac。

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[使用文件系统](~/ios/app-fundamentals/file-system.md)

Xamarin.iOS 可以使用相同的 System.IO 类以便与文件和目录中你将在任意.NET 应用程序的 iOS。 但是，尽管熟悉类和方法中，iOS 上的文件可以创建或访问实现一些限制，并且还提供特殊功能，对于某些目录。 本文概述了这些限制和功能，并演示如何在 Xamarin.iOS 应用程序中的文件访问工作原理。

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[在代码中创建 iOS 应用程序](~/ios/app-fundamentals/ios-code-only.md)

本文介绍如何创建 iOS 应用程序完全用代码使用 Visual Studio 和 Visual Studio for mac。 本文介绍了如何从空项目模板开始，通过从 UIKit 创建视图层次结构，在控制器中生成应用程序屏幕。 然后介绍了如何创建可在控制器中加载的自定义视图。

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[使用用户默认值](~/ios/app-fundamentals/user-defaults.md)

`NSUserDefaults`类提供一种 iOS 应用和扩展，以编程方式与整个系统的默认系统交互。 通过使用默认系统，用户可以配置应用的行为或样式以满足他们的首选项 （基于应用程序的设计）。 例如，若要提供皇室度量值的度量值的 vs 中的数据或选择一个给定的用户界面主题。

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[触控](~/ios/app-fundamentals/touch/index.md)

许多现有设备上的触摸屏允许用户快速、 高效地交互与设备自然和直观的方式。 这种交互并不只限于简单触摸检测 – 可以使用手势以及。 例如，捏合缩放手势是很常见的示例 – 挤压用户可以放大或缩小的两个手指屏幕的一部分。本指南将检查触控和手势在 iOS 中。

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[使用安全和隐私](~/ios/app-fundamentals/security-privacy.md)

Apple 已对安全和隐私 iOS 10 （和更高版本） 中存在多项的增强，可帮助开发人员提高其应用程序的安全性，并确保最终用户的隐私。 本文将介绍在 Xamarin.iOS 应用程序中实现这些功能。

##  <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[本地化](~/ios/app-fundamentals/localization/index.md)

此指南介绍了如何添加到 Xamarin.iOS 应用程序以支持国际化的编码。
