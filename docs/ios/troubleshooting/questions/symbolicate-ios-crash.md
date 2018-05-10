---
title: 在哪里可以找到要 symbolicate iOS 崩溃日志的.dSYM 文件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/09/2018
ms.openlocfilehash: 60d897be8739ff5b78a322bc4ea3f43011785bb5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>在哪里可以找到要 symbolicate iOS 崩溃日志的.dSYM 文件？

用于 Mac 或 Visual Studio 2017 生成 iOS 应用程序使用 Visual Studio 时, 需要 symbolicate 崩溃报告.dSYM 文件将放置在应用的项目文件 (.csproj) 相同的目录层次结构中。 确切的位置取决于你的项目的生成设置：

- 如果已启用了特定于设备的版本中，位于以下目录中找不到.dSYM:

    **&lt;项目目录&gt;/bin/&lt;平台&gt;/&lt;配置&gt;/device-builds /&lt;设备&gt;- &lt;操作系统版本&gt;/**

    例如：
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- 如果你尚未启用特定于设备的生成，可以在以下目录中找到.dSYM:

    **&lt;项目目录&gt;/bin/&lt;平台&gt;/&lt;配置&gt;/**

    例如：

    **TestApp/bin/iPhone/发布 /**

> [!NOTE]
> 作为生成过程的一部分，Visual Studio 2017 将.dSYM 文件复制从 Mac 生成主机到 Windows。 如果看不到 Windows 上的.dSYM 文件，请确保你已配置你的应用的生成设置到[创建.ipa 文件](~/ios/deploy-test/app-distribution/ipa-support.md)。

## <a name="see-also"></a>请参阅

- [Symbolicating iOS 崩溃文件 (Xamarin.iOS)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Demystifying iOS 应用程序崩溃日志](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

