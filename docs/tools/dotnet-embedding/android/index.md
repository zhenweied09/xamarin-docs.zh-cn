---
title: 在 Android 上嵌入的.NET
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: topgenorth
ms.author: toopge
ms.date: 06/15/2018
ms.openlocfilehash: e90d1e6258d4cfd9c918c566c9e18c358ee7668a
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067277"
---
# <a name="net-embedding-on-android"></a>在 Android 上嵌入的.NET

在某些情况下，你可能想要将 Xamarin.NET 库添加到现有的本机 Android 项目。 若要执行此操作，可以使用[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)工具要将转变为可以合并到一个基于 Java 的本机 Android 应用的本机库的.NET 库。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="xamarinandroid-requirements"></a>Xamarin.Android 要求

对于要使用.NET 嵌入的 Xamarin.Android，你需要以下：

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本必须安装。

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/)或更高版本必须安装。

-   **Java 开发人员工具包** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本必须安装。


## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用本机 Android 项目中的.NET 库，请使用以下步骤：

1.  创建 C# Android 库项目。

2.  安装[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3.  找到**Embeddinator 4000.exe**和将其添加到你**路径**。 例如：

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  在类库程序集上运行 Embeddinator 4000。 例如：

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  使用 Android Studio 中的 Java 项目中的生成的 AAR 文件。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="xamarinandroid-requirements"></a>Xamarin.Android 要求

对于要使用.NET 嵌入的 Xamarin.Android，你需要以下：

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本必须安装。

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/)或更高版本必须安装。

-   **Java 开发人员工具包** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本必须安装。

-   **单声道** &ndash; [单声道 5.0](http://www.mono-project.com/download/)或更高版本必须安装 （mono 随 Visual Studio 安装 for Mac）。


## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用本机 Android 项目中的.NET 库，请使用以下步骤：

1.  创建 C# Android 库项目。

2.  安装[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3.  找到**Embeddinator 4000.exe**并添加**单声道**到你的路径。 例如：

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  在类库程序集上运行 Embeddinator 4000。 例如：

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  使用 Android Studio 中的 Java 项目中的生成的 AAR 文件。

-----

介绍了使用情况和命令行选项[Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c)文档。


## <a name="callbacks"></a>回调

了解有关[C# 和 Java 之间的调用](callbacks.md)。

## <a name="samples"></a>示例

* [天气示例应用程序](https://github.com/jamesmontemagno/embeddinator-weather)
