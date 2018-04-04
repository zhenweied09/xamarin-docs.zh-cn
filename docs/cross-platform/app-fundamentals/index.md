---
title: 应用程序基础知识
description: 应用程序的核心概念
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: f5bd66cfcfb6ee06abac7bec9151e7325ebb32a2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="application-fundamentals"></a>应用程序基础知识

本部分提供的指南上的一些较常见的操作任务或开发人员需要时应注意的开发移动应用程序的概念。

##  <a name="building-cross-platform-applicationscross-platformapp-fundamentalsbuilding-cross-platform-applicationsindexmd"></a>[生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)

通过选择 Xamarin，并保留记住几件事，设计和开发您的移动应用程序时，可以实现跨移动平台共享的巨大代码、 缩短上市时间、 利用现有 talent，满足客户需求对于移动访问，并减少跨平台复杂性。&nbsp;本文档概述了实现这些优势实用程序和工作效率应用程序的重要原则。

## <a name="code-sharing-optionscode-sharingmd"></a>[代码共享选项](code-sharing.md)

了解不同的代码共享选项适用于 Xamarin 项目，包括可移植类库 (Pcl)、 共享项目和.NET 标准库。


## <a name="accessibilityaccessibilitymd"></a>[辅助功能](accessibility.md)

用于构建可访问应用程序的提示。


## <a name="localizationlocalizationmd"></a>[本地化](localization.md)

使可以转换为多种语言的区域设置识别应用程序的准则。


##  <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[可移植类库](~/cross-platform/app-fundamentals/pcl.md)

可移植类库项目，可以生成和分发包含共享的代码以在多个平台上运行的程序集。 若要创建可移植类库 （或"PCL"） 第一次选择哪些平台为目标，然后编写代码针对.NET Framework 中定义这些平台的配置文件提供一子组。 本文档介绍如何创建和 Xamarin 中使用 Pcl。

##  <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)

共享的项目中，可以编写大量不同的应用程序项目的引用的常见代码。 该代码编译为每个引用的项目的一部分，并可以包括编译器指令来帮助整合共享的代码库中的特定于平台的功能。 本文讨论了共享项目的工作原理以及如何创建和使用它们与 Xamarin 项目。

##  <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET 标准是跨平台共享代码的新选项。 它的可移植类库; 类似的方式在工作代码针对特定版本 (当前 1.0 到 1.6) 构建的随后可使用其他支持该级别的项目或更高版本。 标准.NET 项目 Xamarin Studio 6.2、 Visual Studio 为 Windows 和 Visual Studio 中支持的 mac。

##  <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[NuGet 项目： 代码共享的多平台库](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

可以从 PCL 或.NET 标准项目; 自动生成 NuGet 包和共享项目可以打包到使用单独的 NuGet 项目类型"诱饵 and 交换机"NuGet 包。 本部分介绍如何创建每个代码共享的方案的 NuGet 包。

##  <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[手动为 Xamarin 创建 NuGet 包](~/cross-platform/app-fundamentals/nuget-manual.md)

用于创建使用 Xamarin 平台的 NuGet 程序包的技巧。

##  <a name="cross-platform-data-accessxamarin-formsdata-cloudindexmd"></a>[跨平台数据访问](~/xamarin-forms/data-cloud/index.md)

大多数应用程序具有一些要求将数据保存在本地设备上。 除非是非常小的数据量，这通常需要一个数据库和数据层应用程序管理数据库的访问权限中。 iOS 和 Android 都具有"内置"SQLite 数据库引擎和 Xamarin 的平台由简化访问存储和检索数据。 [Android 数据访问](~/android/data-cloud/data-access/index.md)， [iOS 数据访问](~/ios/data-cloud/data/index.md)，和[Xamarin.Forms 数据访问](~/xamarin-forms/data-cloud/index.md)指南提供如何在每个平台上访问 SQLite 的示例。


##  <a name="transport-layer-securitytransport-layer-securitymd"></a>[传输层安全性](transport-layer-security.md)

Selectingthe 正确 SSL/TLS 实现保护你的应用的网络连接的信息。


##  <a name="notificationsxamarin-formsdata-cloudpush-notificationsindexmd"></a>[通知](~/xamarin-forms/data-cloud/push-notifications/index.md)

移动应用程序作为的通知的用户的某些应用程序特定事件发生的非介入式方式使用通知。 通知通常用于通知用户在后台运行应用程序进程的状态。 此示例可能下载较大的文件。 此文件可能需要很长时间才能下载，因此此活动应发生在后台。 下载完成后，系统会通知用户事实的通知。
此外，通知 ares 不只限于本地的应用程序。 还有可能对于服务器应用程序发布到移动应用程序的通知。 本文将讨论如何在 Android 和 iOS 上使用通知。
