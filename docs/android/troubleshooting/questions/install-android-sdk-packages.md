---
title: 应安装的 Android SDK 包？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: df359fae545079c7ac1d7106ec86e9297f183f90
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732770"
---
# <a name="which-android-sdk-packages-should-i-install"></a>应安装的 Android SDK 包？

安装 Android SDK 不会自动包括用于开发的所有最小所需的包。 尽管各开发人员需要会有所不同，以下包通常将需要使用 Xamarin.Android 开发：

## <a name="tools"></a>工具

SDK 管理器中的工具文件夹中安装的最新的工具：

- Android SDK 工具
- Android SDK 平台工具
- Android SDK 生成工具

## <a name="android-platforms"></a>Android 平台

安装已设置为最小值和目标设置的 Android 版本"SDK 平台"。 

示例：

- 目标 API 23
- 最小 API 23

只需为 API 23 安装 SDK 平台

- 目标 API 23
- 最小 API 15

需要安装适用于 API 15 和 23 的 SDK 平台。 请注意，不需要安装在最小值和目标之间的 API 级别 （即使是反向移植到这两种 API 级别）。

## <a name="system-images"></a>系统映像

这些只是所需如果想要使用 Google 现成可用 Android 仿真程序。 有关详细信息，请参阅[Android 仿真程序安装程序](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>附加程序
Android SDK 其他功能通常不是必需的;但十分有用，因为它们可能需要根据您的使用案例需要注意的它们。

## <a name="further-reading"></a>其他阅读材料
以下指南适用于这些选项，将进入有关的不同程序包的详细信息 SDK 管理器已不可用： [Android SDK 管理器安装指南](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

