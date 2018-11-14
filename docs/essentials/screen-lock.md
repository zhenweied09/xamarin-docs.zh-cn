---
title: Xamarin.Essentials：屏幕锁定
description: 本文档介绍 Xamarin.Essentials 中的 ScreenLock 类，此类可以请求在应用程序运行时防止屏幕进入睡眠状态。
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3bf8c949650cf9f039a5a516366a90e717dc944b
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675310"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials：屏幕锁定

![预发行版 NuGet](~/media/shared/pre-release.png)

ScreenLock 类可以请求在应用程序运行时防止屏幕进入睡眠状态。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenlock"></a>使用 ScreenLock

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用 `RequestActive` 和 `RequestRelease` 方法使用屏幕锁定功能来防止屏幕关闭。

```csharp
public class ScreenLockTest
{
    public void ToggleScreenLock()
    {
        if (!ScreenLock.IsActive)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [屏幕锁定源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [屏幕锁定 API 文档](xref:Xamarin.Essentials.ScreenLock)
