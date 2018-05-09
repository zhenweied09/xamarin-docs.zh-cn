---
title: Xamarin.Essentials 屏幕锁定
description: ScreenLock 类可以请求侵扰睡眠状态时，运行应用程序时使屏幕。
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 66702e9f8f5e6ad07f8f7c96f739edf1b2e66617
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials 屏幕锁定

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
        if (ScreenLock.IsMonitoring)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease;
    }
}
```

## <a name="api"></a>API

- [屏幕锁定源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/ScreenLock)
- [屏幕锁定 API 文档](xref:Xamarin.Essentials.ScreenLock)
