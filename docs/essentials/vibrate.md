---
title: Xamarin.Essentials： 振动
description: 本文档介绍中 Xamarin.Essentials，可以启动和停止振动功能所需的时间内的振动类。
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 530273543c6cb71038613c22fa4a6bfbde4928d7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947252"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials： 振动

![预发行版 NuGet](~/media/shared/pre-release.png)

**振动**类，可以启动和停止在所需的时间内的振动功能。

## <a name="getting-started"></a>入门

访问**振动**功能以下平台特定的安装程序是必需的。

# <a name="androidtabandroid"></a>[Android](#tab/android)

振动权限是必需的必须在 Android 项目中配置。 这可以通过以下方法添加：

打开**AssemblyInfo.cs**文件下**属性**文件夹并添加：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

或更新 Android 清单：

打开**AndroidManifest.xml**文件下**属性**文件夹，并添加以下的内部**清单**节点。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

或右键单击 Anroid 项目并打开项目的属性。 下**Android 清单**查找**所需的权限：** 区域并检查**振动**权限。 这将自动更新**AndroidManifest.xml**文件。

# <a name="iostabios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

任何平台的差异。

-----

## <a name="using-vibration"></a>使用振动

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

可以请求一个设定的时间或默认值为 500 毫秒的振动功能。

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

可以使用请求取消的设备振动`Cancel`方法：

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

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

任何平台的差异。

# <a name="iostabios"></a>[iOS](#tab/ios)

* 仅手机铃声时设备设置为"振动上环"。
* 始终手机铃声 500 毫秒。
* 无法取消振动。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

任何平台的差异。

-----

## <a name="api"></a>API

- [振动源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [振动 API 文档](xref:Xamarin.Essentials.Vibration)
