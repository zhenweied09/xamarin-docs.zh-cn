---
title: 在 Android 上嵌入.NET
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: lobrien
ms.author: laobri
ms.date: 06/15/2018
ms.openlocfilehash: 5c8d493bf54ee1a8a1e7d4b3266451c78a4aa51e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123675"
---
# <a name="net-embedding-on-android"></a>在 Android 上嵌入.NET

在某些情况下，你可能想要将 Xamarin.NET 库添加到现有本机 Android 项目。 若要执行此操作，可以使用[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)工具，将.NET 库转变为可以合并到一个基于 Java 的本机 Android 应用的本机库。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Xamarin.Android 要求

有关 Xamarin.Android 以用于.NET 嵌入，您需要：

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本必须安装。

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/)或更高版本必须安装。

-   **Java 开发人员工具包** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本必须安装。


## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用本机的 Android 项目中的.NET 库，请使用以下步骤：

1.  创建C#Android 库项目。

2.  安装[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3.  找到**Embeddinator 4000.exe**并将其添加到你**路径**。 例如：

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  库程序集上运行 Embeddinator 4000。 例如：

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  使用 Android Studio 中的 Java 项目中生成的 AAR 文件。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Xamarin.Android 要求

有关 Xamarin.Android 以用于.NET 嵌入，您需要：

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本必须安装。

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/)或更高版本必须安装。

-   **Java 开发人员工具包** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本必须安装。

-   **Mono** &ndash; [Mono 5.0](http://www.mono-project.com/download/)或更高版本必须安装 （mono 安装与 Visual Studio for Mac）。


## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用本机的 Android 项目中的.NET 库，请使用以下步骤：

1.  创建C#Android 库项目。

2.  安装[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3.  找到**Embeddinator 4000.exe**并添加**mono**到你的路径。 例如：

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  库程序集上运行 Embeddinator 4000。 例如：

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  使用 Android Studio 中的 Java 项目中生成的 AAR 文件。

-----

介绍了使用情况和命令行选项[Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c)文档。


## <a name="callbacks"></a>回调

了解如何[之间的调用C#和 Java](callbacks.md)。

## <a name="samples"></a>示例

* [天气示例应用程序](https://github.com/jamesmontemagno/embeddinator-weather)
