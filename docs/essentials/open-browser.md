---
title: Xamarin.Essentials 打开浏览器
description: 浏览器类可让应用程序在优化的系统首选的浏览器或外部浏览器中打开 web 链接。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b7d04bec569e911df75af999f980da07c868e18f
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials 浏览器

![预发行 NuGet](~/media/shared/pre-release.png)

**浏览器**类可让应用程序在优化的系统首选的浏览器或外部浏览器中打开 web 链接。

## <a name="using-browser"></a>使用浏览器

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

浏览器功能的工作方式是调用`OpenAsync`方法替换`Uri`和`BrowserLaunchType`。

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.RequestAsync(uri, BrowserLaunchType.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>平台实现细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

启动类型确定如何启动浏览器：

## <a name="system-preferred"></a>首选的系统

[Chrome 自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)将尝试使用加载 Uri 和保持导航感知。

## <a name="external"></a>外部

`Intent`将用于请求通过系统正常浏览器打开 Uri。

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>首选的系统

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)用于加载 Uri 和保持导航感知。

## <a name="external"></a>外部

标准`OpenUrl`在主应用程序将使用来启动默认浏览器应用程序之外。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

用户的默认浏览器将始终启动而不考虑`BrowserLaunchType`。

--------------

## <a name="api"></a>API

- [浏览器源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [浏览器 API 文档](xref:Xamarin.Essentials.Browser)
