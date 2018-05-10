---
title: Xamarin.Essentials 振动
description: 振动类，可以启动和停止 vibrate 功能所需的一段时间。
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 53271f3cee06f33cea4fa0bd28d3cff1baf0cd3e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials 振动

![预发行 NuGet](~/media/shared/pre-release.png)

**振动**类可以启动和停止 vibrate 功能所需的一段时间。

## <a name="getting-started"></a>入门

访问**振动**功能以下平台特定的安装程序是必需的。

# <a name="androidtabandroid"></a>[Android](#tab/android)

Vibrate 权限是必需的并必须在 Android 项目中配置。 这可以通过以下方式添加：

打开**AssemblyInfo.cs**文件下**属性**文件夹并添加：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

或更新 Android 清单：

打开**AndroidManifest.xml**文件下**属性**文件夹并添加以下内的**清单**节点。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

或右键单击 Anroid 项目并打开项目的属性。 下**Android 清单**查找**所需的权限：**区域并检查**VIBRATE**权限。 这将自动更新**AndroidManifest.xml**文件。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他的安装程序。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他的安装程序。

-----

## <a name="using-vibration"></a>使用振动

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

可以为一个设定的时间或默认值为 500 毫秒请求振动功能。

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

可以使用请求的设备振动取消`Cancel`方法：

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>平台的差异

| 平台 | 差异 |
| --- | --- |
| iOS | 始终型表示 500 毫秒。 |
| iOS | 不能取消振动。 |

## <a name="api"></a>API

- [振动源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/Vibration)
- [振动 API 文档](xref:Xamarin.Essentials.Vibration)
