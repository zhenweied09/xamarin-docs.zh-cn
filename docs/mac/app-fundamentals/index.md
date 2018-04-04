---
title: 应用程序基础知识
description: 此指南介绍了各种概念了解开发 Xamarin.Mac 应用程序时所需的文档链接。
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/17/2015
ms.openlocfilehash: 807cf0e16cafc00483cecfc578367bc110657672
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="application-fundamentals"></a>应用程序基础知识

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[常见的模式和惯例](~/mac/app-fundamentals/patterns.md)

在 Apple Api 通过 C# 公开整个某些习语和模式提出反复。 如果你有使用 Xamarin.iOS 编程经验，它们看起来可能熟悉。 文档将通常引用这些模式和习语重复，因此具有清楚地了解它们将帮助你了解你找到的文档。

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[理解 Mac Api](~/mac/app-fundamentals/mac-apis.md)

对于很多使用 Xamarin.Mac 开发时间，可以认为、 读取和编写在 C# 中，而与基础 Objective C Api 过多地考虑。 但是，有时你将需要从 Apple 中读取的 API 文档转换从堆栈溢出到解决方案的答案对于您的问题，或向现有示例进行比较。

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[使用.xib 文件](~/mac/app-fundamentals/xib.md)

本文介绍如何使用在 Xcode 的接口生成器可以创建和维护 Xamarin.Mac 应用程序的用户界面中创建的.xib 文件的工作。

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[用户界面设计小于.storyboard/.xib](~/mac/app-fundamentals/xibless-ui.md)

本文介绍如何创建 C# 代码中直接 Xamarin.Mac 应用程序的用户界面，而无需与.storyboard 或.xib 文件使用 Xcode 的接口生成器。

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[处理映像](~/mac/app-fundamentals/image.md)

本文介绍如何使用图像和 Xamarin.Mac 应用程序中的图标。 介绍如何创建并维护映像所需应用程序的图标和使用 C# 代码和 Xcode 的接口生成器中的映像来创建。

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[数据绑定和键 / 值编码](~/mac/app-fundamentals/databinding.md)

本文介绍如何使用键值对的编码和观察以便用于数据绑定到 Xcode 的接口生成器中的 UI 元素的键 / 值。 使用此技术，极大地减少需要为 Xamarin.Mac 应用程序编写的 C# 代码的量。 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[使用数据库](~/mac/app-fundamentals/databases.md)

本文介绍如何使用键值对的编码和键 / 值观察以允许直接访问 SQLite 数据库到 Xcode 的接口生成器中的 UI 元素的数据绑定。 它还介绍如何使用 SQLite.NET ORM 以提供对 SQLite 数据访问。

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[使用复制和粘贴](~/mac/app-fundamentals/copy-paste.md)

本文介绍如何使用粘贴板提供复制和粘贴在 Xamarin.Mac 应用程序。 它演示如何使用可以在多个应用以及如何支持在给定应用内的自定义数据之间共享的标准数据类型。

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[沙盒处理 Xamarin.Mac 应用](~/mac/app-fundamentals/sandboxing.md)

本文介绍如何沙盒处理用于在应用商店上发布的 Xamarin.Mac 应用程序。 它涵盖的所有元素转到沙盒处理： 容器目录、 权利、-确定用户的权限、 权限分离和内核强制。

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[播放声音与 AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

这篇文章演示如何使用一个帮助器类来控制播放的声音使用 AVAudioPlayer。

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[报告 bug](~/mac/app-fundamentals/troubleshooting.md)

有时，所有我们就会在处理项目，无法获取按我们希望的方式使用 API 或尝试解决 bug 时中断。 若要成功地编写你的移动和桌面应用程序，我们在 Xamarin 的目标是和我们提供了一些可帮助资源。
