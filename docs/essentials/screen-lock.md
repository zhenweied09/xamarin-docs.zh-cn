---
title: Xamarin.Essentials： 屏幕锁定
description: 本文档介绍 Xamarin.Essentials，可以请求使屏幕侵扰睡眠状态时，应用程序运行时中的 ScreenLock 类。
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848565"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials： 屏幕锁定

![预发行版 NuGet](~/media/shared/pre-release.png)

**ScreenLock**类可以请求使屏幕侵扰睡眠状态时，应用程序运行时。

## <a name="using-screenlock"></a>使用 ScreenLock

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

屏幕锁定功能适用于通过调用`RequestActive`和`RequestRelease`从关闭请求在屏幕的方法。

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
