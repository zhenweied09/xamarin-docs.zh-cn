---
title: Xamarin.Essentials 启动器
description: Xamarin.Essentials 中的启动器类使应用程序以打开系统的 URI。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 252bb873c1494265aafb2285057490ca29ce7419
ms.sourcegitcommit: bf51592be39b2ae3d63d029be1d7745ee63b0ce1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39573628"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials： 启动器

![预发行版 NuGet](~/media/shared/pre-release.png)

**启动器**类，应用程序可以由系统打开 URI。 这通常用于深入链接到另一个应用程序的自定义 URI 方案。 如果想要打开浏览器访问网站，则应参考 **[浏览器](open-browser.md)** API。

## <a name="using-launcher"></a>使用启动器

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要使用的启动器功能调用`OpenAsync`方法并传入`string`或`Uri`打开。 （可选）`CanOpenAsync`方法可用于检查是否可以由在设备上的应用程序来处理 URI 架构。

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="api"></a>API

- [启动程序源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [启动器 API 文档](xref:Xamarin.Essentials.Launcher)
