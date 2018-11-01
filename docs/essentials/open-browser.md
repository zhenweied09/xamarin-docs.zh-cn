---
title: Xamarin.Essentials：Open Browser
description: Xamarin.Essentials 中的 Browser 类允许应用程序在优化的系统首选浏览器或外部浏览器中打开 Web 链接。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a68837ac4447dabcf52a1d1b27913adf80b4cbd7
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675388"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials：Browser

![预发行版 NuGet](~/media/shared/pre-release.png)

Browser 类允许应用程序在优化的系统首选浏览器或外部浏览器中打开 Web 链接。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-browser"></a>使用 Browser

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

Browser 功能通过调用具有 `Uri` 和 `BrowserLaunchMode` 的 `OpenAsync` 方法工作。

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

启动模式确定浏览器的启动方式：

## <a name="system-preferred"></a>系统首选

[Chrome 自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)将尝试用于加载 URI 并保持导航识别。

## <a name="external"></a>外部

`Intent` 将用于请求通过系统常规浏览器打开的 URI。

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>系统首选

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) 用于加载 URI 并保持导航识别。

## <a name="external"></a>外部

主应用程序上的标准 `OpenUrl` 用于启动应用程序之外的默认浏览器。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无论 `BrowserLaunchMode` 如何，将始终启动用户的默认浏览器。

--------------

## <a name="api"></a>API

- [Browser 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Browser API 文档](xref:Xamarin.Essentials.Browser)
