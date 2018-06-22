---
title: Android 生成错误 – LinkAssemblies 任务意外失败
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: de6e0b66cac688955d27ba2d0165d5a059d36c38
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30789535"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Android 生成错误 – LinkAssemblies 任务意外失败

你可能会看到一条错误消息`The "LinkAssemblies" task failed unexpectedly`时生成，该服务 Xamarin.Android 项目使用窗体。 链接器处于活动状态时将发生这种情况 (通常在*版本*生成，以减少应用包的大小); 这是由于 Android 目标不更新为最新的框架和。 (详细信息： [Android 要求的 Xamarin.Forms](~/xamarin-forms/get-started/installation.md#android))

与此问题的解决方法是确保你拥有最新支持 Android SDK 版本，并将设置**目标框架**到**使用最新安装平台**。 此外建议你设置**目标 Android 版本**到**使用目标框架版本**和**最低 Android 版本**为 API 15 或更高。 这被视为受支持的配置。

## <a name="setting-in-visual-studio-for-mac"></a>Visual Studio 中适用于 Mac 的设置

1.  右键单击的 Android 项目。
2.  转到**生成 > 常规 > 面向 Framework**。
3.  设置**目标框架： 使用最新安装平台**。
4.  仍在的项目选项中，转到**生成 > Android 应用程序**。
5.  设置**最低 Android 版本**到 API 级别 15 或更高版本 &**目标 Android 版本**到**自动-使用目标框架版本**。

## <a name="setting-in-visual-studio"></a>Visual Studio 中的设置

1.  右键单击的 Android 项目。
2.  转到**应用程序**中的项目选项。
3.  设置**使用 Android 版本进行编译** & **目标 Android 版本**设置应用于**使用最新的平台** / **使用使用 SDK 版本进行编译**。
4.  设置**到目标最低 Android**设置为 API 15 或更高。

更新这些设置之后，请清除并重新生成项目以确保所做的更改会选取。
