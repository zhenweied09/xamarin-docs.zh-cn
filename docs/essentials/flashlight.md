---
title: Xamarin.Essentials： 闪光灯
description: 本文档介绍中 Xamarin.Essentials，可以启用或禁用设备的照相机的闪存将其转换手电筒的闪光灯类。
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 8c471f64c14a2e41693c450e02f89e7ac845d060
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353355"
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials： 闪光灯

![预发行版 NuGet](~/media/shared/pre-release.png)

**手电筒**类具有启用或禁用设备的照相机的闪存将其转换手电筒的功能。

## <a name="getting-started"></a>入门

访问**手电筒**功能以下平台特定的安装程序是必需的。

# <a name="androidtabandroid"></a>[Android](#tab/android)

闪光灯和照相机权限所需，必须在 Android 项目中配置。 这可以通过以下方法添加：

打开**AssemblyInfo.cs**文件下**属性**文件夹并添加：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

或更新 Android 清单：

打开**AndroidManifest.xml**文件下**属性**文件夹，并添加以下的内部**清单**节点。

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

或右键单击 Android 项目，并打开项目的属性。 下**Android 清单**查找**所需的权限：** 区域并检查**手电筒**并**照相机**权限。 这将自动更新**AndroidManifest.xml**文件。

通过添加这些权限来[Google Play 将自动筛选出设备](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features)无需任何特定硬件。 可以通过以下代码添加到你在 Android 项目的 AssemblyInfo.cs 文件获取解决此问题：

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

# <a name="iostabios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="using-flashlight"></a>使用闪光灯

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

闪光灯可以启用和禁用通过`TurnOnAsync`和`TurnOffAsync`方法：

```csharp
try
{
    // Turn On
    await Flashlight.TurnOnAsync();

    // Turn Off
    await Flashlight.TurnOffAsync();
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to turn on/off flashlight
}
```

## <a name="platform-implementation-specifics"></a>平台实现的细节

### <a name="androidtabandroid"></a>[Android](#tab/android)

经过优化的闪光灯类，基于设备的操作系统。

#### <a name="api-level-23-and-higher"></a>API 级别 23 和更高版本

在较新的 API 级别[Torch 模式](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode)将用于打开或关闭设备立刻正式投入工作单元。

#### <a name="api-level-22-and-lower"></a>API 级别 22 和更低

照相机图面上纹理创建以打开或关闭`FlashMode`的照相机单元。 

### <a name="iostabios"></a>[iOS](#tab/ios)

[AVCaptureDevice](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureDevice/)用于打开和关闭 Torch 和闪存设备模式。

### <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[Lamp](https://docs.microsoft.com/en-us/uwp/api/windows.devices.lights.lamp)用于检测要启用或禁用的设备背面的第一个灯泡。

-----

## <a name="api"></a>API

- [闪光灯源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [闪光灯 API 文档](xref:Xamarin.Essentials.Flashlight)
