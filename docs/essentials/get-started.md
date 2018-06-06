---
title: 要开始使用 Xamarin.Essentials
description: Xamarin.Essentials 提供一个单一的跨平台 API，适用于任何 iOS、 Android 或 UWP 应用程序可以从访问共享不管如何创建用户界面的代码。
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f0f6eebbd12041a7be2d8e2dc00a9146b40d675f
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783069"
---
# <a name="get-started-with-xamarinessentials"></a>要开始使用 Xamarin.Essentials

![预发行 NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials 提供一个单一的跨平台 API，适用于任何 iOS、 Android 或 UWP 应用程序可以从访问共享不管如何创建用户界面的代码。

## <a name="platform-support"></a>平台支持

Xamarin.Essentials 支持以下平台和操作系统：

| 平台 | 版本 |
| --- | --- |
| Android | 4.4 (API 19) 或更高版本 |
| iOS |10.0 或更高版本 |
| UWP | 10.0.16299.0 或更高版本 |

## <a name="installation"></a>安装

Xamarin.Essentials 可作为 NuGet 程序包可添加到使用 Visual Studio 的任何现有或新项目。

1. 下载并安装[Visual Studio](http://visualstudio.com)与[Xamarin 的 Visual Studio 工具](~/cross-platform/get-started/installation/index.md)。

2. 打开现有项目，或创建新项目使用空白应用模板位于**Visual Studio C#** （Android、 iPhone 和 iPad 或跨平台）。 **重要**： 如果将添加到 UWP 项目确保在项目属性中设置生成 16299 或更高版本。

3. 添加**Xamarin.Essentials**到每个项目的 NuGet 包：

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    在解决方案资源管理器面板中，右键单击解决方案名称，然后选择**管理 NuGet 包**。 搜索**Xamarin.Essentials**并安装到包**所有**项目包括 Android、 iOS、 UWP，和.NET 标准库。

    > [!TIP]
    > 检查**包括预发行版**时框[ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials)处于预览状态。

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

    在解决方案资源管理器面板中，右键单击项目名称并选择**添加 > 添加 NuGet 包...**.搜索**Xamarin.Essentials**并安装到包**所有**项目包括 Android、 iOS 和.NET 标准库。

    > [!TIP]
    > 检查**显示预发行包**时框[ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials)处于预览状态。

    -----

4. 在任何 C# 类来引用 Api 中添加对 Xamarin.Essentials 的引用。

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials 需要特定于平台的安装程序：

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    在 Android 项目的`MainLauncher`或任何`Activity`，它是必须在中初始化启动的 Xamarin.Essentials`OnCreate`方法：

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    若要处理运行时在 Android 上的权限，Xamarin.Essentials 必须接收任何`OnRequestPermissionsResult`。 将以下代码添加到所有`Activity`类：

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="iostabios"></a>[iOS](#tab/ios)

    不需要其他的安装程序。

    # <a name="uwptabuwp"></a>[UWP](#tab/uwp)

    不需要其他的安装程序。

    -----

6. 请按照[Xamarin.Essentials 指南](index.md)，你可以复制并粘贴为每个功能的代码段。

## <a name="other-resources"></a>其他资源

我们建议开发人员熟悉 Xamarin，请访问[开始使用 Xamarin 开发](~/cross-platform/getting-started/index.md)。

请访问[Xamarin.Essentials GitHub 存储库](http://github.com/xamarin/Essentials)若要查看当前源代码，什么接下来，运行示例，并关闭存储库。 社区贡献是欢迎 ！

通过浏览[API 文档](xref:Xamarin.Essentials)Xamarin.Essentials 每个功能。
