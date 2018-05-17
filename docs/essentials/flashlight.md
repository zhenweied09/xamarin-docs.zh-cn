---
title: Xamarin.Essentials 手电筒
description: 手电筒类具有启用或禁用设备的照相机闪存要将转变为手电筒的能力。
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0fe07f60502b27322db9486f3382b8f67d50945b
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials 手电筒

![预发行 NuGet](~/media/shared/pre-release.png)

**手电筒**类能够打开或关闭设备的照相机闪存要将转变为手电筒。

## <a name="getting-started"></a>入门

访问**手电筒**功能以下平台特定的安装程序是必需的。

# <a name="androidtabandroid"></a>[Android](#tab/android)

手电筒和照相机权限需要，但必须在 Android 项目中配置。 这可以通过以下方式添加：

打开**AssemblyInfo.cs**文件下**属性**文件夹并添加：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

或更新 Android 清单：

打开**AndroidManifest.xml**文件下**属性**文件夹并添加以下内的**清单**节点。

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

或右键单击 Anroid 项目并打开项目的属性。 下**Android 清单**查找**所需的权限：** 区域并检查**手电筒**和**相机**权限。 这将自动更新**AndroidManifest.xml**文件。

通过添加这些权限[Google Play 将自动筛选出设备](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features)而无需特定硬件。 可以通过将以下内容添加到你在 Android 项目的 AssemblyInfo.cs 文件获取解决此：

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他的安装程序。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他的安装程序。

-----

## <a name="using-flashlight"></a>使用手电筒

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

手电筒可以打开和关闭通过`TurnOnAsync`和`TurnOffAsync`方法：

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

## <a name="platform-implementation-specifics"></a>平台实现细节

### <a name="androidtabandroid-specifics"></a>[Android](#tab/android-specifics)

手电筒类已经过优化基于设备的操作系统。

#### <a name="api-level-23-and-higher"></a>API 级别 23 和更高版本

在较新的 API 级别[创建模式](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode)将用于打开或关闭设备 flash 单元。

#### <a name="api-level-22-and-lower"></a>API 级别 22 和更低

照相机表面纹理创建来打开或关闭`FlashMode`的相机单元。 

### <a name="iostabios-specifics"></a>[iOS](#tab/ios-specifics)

[AVCaptureDevice](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureDevice/)用于打开和关闭的创建和闪存设备模式。

### <a name="uwptabuwp-specifics"></a>[UWP](#tab/uwp-specifics)

[Lamp](https://docs.microsoft.com/en-us/uwp/api/windows.devices.lights.lamp)用于检测要打开或关闭的设备背面的第一个灯泡。

-----

## <a name="api"></a>API

- [手电筒源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [手电筒 API 文档](xref:Xamarin.Essentials.Flashlight)
