---
title: "在 Java 中嵌入.NET"
description: "如何使用基于 Java 的本机 Android 项目中的 Xamarin.NET 库"
ms.topic: article
ms.prod: xamarin
ms.assetid: A489EEF3-1008-4257-BF63-FE21D8C23821
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 1a25f4bc39e39ce58a07ed399082bf13284c16e9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="embedding-net-in-java"></a>在 Java 中嵌入.NET

在某些情况下，你可能想要将 Xamarin.NET 库添加到现有的本机 Android 项目。 若要执行此操作，可以使用[Embeddinator 4000](https://mono.github.io/Embeddinator-4000/)工具要将转变为可以合并到一个基于 Java 的本机 Android 应用的本机库的.NET 库。

 
## <a name="requirements"></a>惠?

若要使用在 Android 上的 Java Embeddinator 4000，你将需要以下各项：

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/preview/index.html)或更高版本必须安装。

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.4.99](https://jenkins.mono-project.com/view/Xamarin.Android/job/xamarin-android/lastSuccessfulBuild/Azure/)或更高版本必须安装。

-   **Java 开发人员工具包** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本必须安装。

-   **Mono** &ndash; [Mono 5.0](http://www.mono-project.com/download/)或更高版本必须安装。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

你可以使用 Visual Studio 编辑和编译 C# 代码。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

你可以使用适用于 Mac 的 Visual Studio 编辑和编译 C# 代码。

-----

 
## <a name="using-the-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用本机 Android 项目中的.NET 库，你可以使用以下步骤：

1.  创建 C# Android 库项目。

2.  安装通过 NuGet Embeddinator-4000。

3.  在 Android 库程序集上运行 Embeddinator。

4.  使用 Android Studio 中的 Java 项目中的生成的 AAR 文件。

在中详细描述了这些步骤[Embeddinator 4000](https://mono.github.io/Embeddinator-4000/getting-started-java-android.html)文档。
