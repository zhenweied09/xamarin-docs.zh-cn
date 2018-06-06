---
title: Xamarin.Essentials： 屏幕锁定
description: 本文档介绍中 Xamarin.Essentials，为了使屏幕侵扰休眠时运行该应用程序可以请求的 ScreenLock 类。
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782903"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials： 屏幕锁定

![预发行 NuGet](~/media/shared/pre-release.png)

**ScreenLock**类可以请求为了使侵扰睡眠状态时，运行应用程序时的屏幕。

## <a name="using-screenlock"></a>使用 ScreenLock

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

屏幕锁定功能的工作方式是调用`RequestActive`和`RequestRelease`方法从关闭请求屏幕。

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
