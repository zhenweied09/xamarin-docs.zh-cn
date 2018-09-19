---
title: Android 资源
description: 本文介绍 Android 中的资源 Xamarin.Android 的概念，并将文档如何使用它们。 它介绍如何在 Android 应用程序中使用资源以支持应用程序本地化和多个设备，包括不同的屏幕大小和密度。
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: 7b6ba9cdc222019bfa2e1cb9a61b54e290e69bba
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764406"
---
# <a name="android-resources"></a>Android 资源

_本文介绍 Android 中的资源 Xamarin.Android 的概念，并将文档如何使用它们。它介绍如何在 Android 应用程序中使用资源以支持应用程序本地化和多个设备，包括不同的屏幕大小和密度。_


## <a name="overview"></a>概述

Android 应用程序很少仅源的代码。 通常有很多其他构成应用程序的文件： 视频、 图像、 字体和音频文件，只是为了仅举几例。 共同，这些非源代码文件被称为资源和编译 （以及源代码） 生成过程并打包为 APK 对于分发和安装到设备：

![打包关系图](images/packaging-diagram.png)

资源到 Android 应用程序有几个优点：

-  **代码分离**&ndash;将与映像、 字符串、 菜单、 动画、 颜色、 等分隔源代码。在这种资源可帮助显著在本地化时。

-  **面向多个设备**&ndash;提供更简单的支持，而不进行代码更改的不同的设备配置的网络。

-  **编译时检查**&ndash;资源是静态的和已编译到应用程序。 这允许在编译时，当它很容易就可以捕获和更正错误，而不是运行时更难找到且成本高昂，更正时要检查的资源的使用情况。

时启动新的 Xamarin.Android 项目时，将创建一个名为资源的特殊目录，以及某些子目录：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![资源的文件夹和内容](images/resources-folder-vs.png)

如上图所示，应用程序资源根据其类型被组织到这些子目录中：图像将会在**drawable**目录; 视图在**layout**子目录，等等。
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![资源的文件夹和内容](images/resources-folder-xs.png)

如上图所示，应用程序资源根据其类型被组织到这些子目录中：图像将会在**mipmap**目录; 视图在**layout**子目录，等等。
 
-----

若要访问这些资源在 Xamarin.Android 应用程序中的使用两种方式：*以编程方式*在代码中和*以声明方式*中使用特殊的 XML 语法的 XML。

这些资源称为*默认资源*和除非指定更精确的匹配项使用的所有设备。 此外，每种类型的资源可选择性地包含*备用资源*Android 可用于面向特定的设备。 例如，可以提供资源以面向用户的区域设置，屏幕的大小，或者如果设备旋转 90 度，在从纵向改为横向，等等。在每个这种情况下，Android 将加载使用的资源，而无需任何额外编码工作由开发人员应用程序。

通过添加一个简短的字符串，调用指定备用资源*限定符*，到持有的资源给定的类型的目录的末尾。

例如，**resources/drawable-de**将指定的设备的设置为德语的区域设置中，映像时**resources/drawable-fr**会保存映像的设备设置为法语的区域设置。 与仅的设备更改区域设置中运行同一应用程序，下面的图中，可以看到提供备用资源的示例：

![不同的区域设置的示例屏幕](images/localized-screenshots.png)

本文将全面一下使用资源，并涵盖以下主题：

-  **Android 资源基础知识**&ndash;使用应用程序中添加资源类型，如图像和字体默认资源以编程方式和以声明方式。

-  **设备特定配置**&ndash;应用程序中支持的不同屏幕分辨率和密度。

-  **本地化**&ndash;使用资源以支持不同的区域可能使用应用程序。


## <a name="related-links"></a>相关链接

- [使用 Android 资产](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [应用程序基础知识](http://developer.android.com/guide/topics/fundamentals.html)
- [应用程序资源](http://developer.android.com/guide/topics/resources/index.html)
- [支持多个屏幕](http://developer.android.com/guide/practices/screens_support.html)
