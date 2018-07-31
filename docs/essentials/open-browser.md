---
title: Xamarin.Essentials 打开浏览器
description: Xamarin.Essentials 中的浏览器类使应用程序能够在优化的系统首选的浏览器或外部浏览器中打开 web 链接。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e58d439f5a6eaafe9b1b5e7ca874a986e468cb9
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353277"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials： 浏览器

![预发行版 NuGet](~/media/shared/pre-release.png)

**浏览器**类使应用程序能够在优化的系统首选的浏览器或外部浏览器中打开 web 链接。

## <a name="using-browser"></a>使用浏览器

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

浏览器功能的工作方式是调用`OpenAsync`方法替换`Uri`和`BrowserLaunchMode`。

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>平台实现的细节

# <a name="androidtabandroid"></a>[Android](#tab/android)

启动模式将确定启动浏览器的方式：

## <a name="system-preferred"></a>首选的系统

[Chrome 自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)将尝试使用加载 Uri 和保留导航意识。

## <a name="external"></a>外部

`Intent`将用于请求 Uri 通过系统正常浏览器将其打开。

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>首选的系统

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)用于加载 Uri 和保留导航意识。

## <a name="external"></a>外部

标准`OpenUrl`上主应用程序用于启动默认浏览器在应用程序之外。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

用户的默认浏览器将始终启动而不考虑`BrowserLaunchMode`。

--------------

## <a name="api"></a>API

- [浏览器的源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [浏览器 API 文档](xref:Xamarin.Essentials.Browser)
