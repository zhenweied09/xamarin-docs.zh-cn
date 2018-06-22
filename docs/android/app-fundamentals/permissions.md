---
title: 在 Xamarin.Android 的权限
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/09/2018
ms.openlocfilehash: b8a8005c69c8aaee5d92bdabb3429bd52fc76b4a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30770230"
---
# <a name="permissions-in-xamarinandroid"></a>在 Xamarin.Android 的权限


## <a name="overview"></a>概述

Android 应用程序在其自己的沙盒中运行和的安全原因不能访问对某些系统资源或硬件设备上。 它可能会使用这些资源之前，用户必须显式授予对应用程序的权限。 例如，应用程序无法通过用户访问而无需显式权限的设备上 GPS。 Android 将引发`Java.Lang.SecurityException`如果应用程序尝试访问不具备权限受保护的资源。

权限声明中**AndroidManifest.xml**由应用程序开发人员在开发应用程序时。 Android 有两个不同的工作流以获取这些权限的用户的同意：
 
* 对于针对 Android 5.1 （API 级别 22） 或更低版本的应用程序，安装应用程序时，将出现的权限请求。 如果用户没有授予权限，将不安装此应用。 安装应用程序后，没有方法通过卸载应用程序来撤消权限除外。
* 从 Android 6.0 （API 级别 23） 开始，用户已提供更好地控制权限;在可授予或撤消权限，只要该应用程序安装在设备上。 此屏幕截图显示 Google 联系人应用设置的权限设置。 它列出各种权限，并允许用户启用或禁用权限：

![示例权限屏幕](permissions-images/01-permissions-check.png) 

Android 应用程序必须检查在运行时以查看它们是否具有访问受保护的资源的权限。 如果应用程序不具有权限，它必须进行使用适用于用户的 Android SDK 提供的新 Api 以授予的权限请求。 权限分为两个类别：

* **正常权限**&ndash;这些是这会带来很少的安全风险，用户的安全或隐私的权限。 在安装时，android 6.0 将自动授予普通权限。 请参阅 Android 文档[的普通权限的完整列表](https://developer.android.com/guide/topics/permissions/normal-permissions.html)。
* **危险权限**&ndash;与普通权限相比危险权限是指那些保护用户的安全或隐私。 这些必须显式授予用户。 发送或接收 SMS 消息是操作的需要非常危险的权限的示例。

> [!IMPORTANT]
> 随着时间的推移，可能会更改权限所属的类别。  有可能，被归类为"正常"权限可能是权限提升在将来 API 级别为非常危险的权限。

危险权限进一步细分为多个[_权限组_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups)。 权限组将保留在逻辑上相关的权限。 当用户将权限授予的权限组的一个成员时，Android 自动向该组的所有成员授予权限。 例如， [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE)权限组保留着两`WRITE_EXTERNAL_STORAGE`和`READ_EXTERNAL_STORAGE`权限。 如果用户授予权限`READ_EXTERNAL_STORAGE`，则`WRITE_EXTERNAL_STORAGE`同时自动授予权限。

请求一个或多个权限之前, 它是一种最佳做法以提供有关为什么应用所需的权限，然后请求权限的理由。 一旦用户了解基本原理，应用程序可从用户请求权限。 通过了解基本原理，用户可以做出明智的决定，如果他们想要授予权限，并了解带来的影响，如果它们不这样做。 

检查和请求权限的整个工作流称为_运行时权限_检查，并可总结在下图中： 

[![运行时权限检查流程图](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

Android 支持库 backports 的一些新 Api 对较旧版本的 Android 的权限。 这些向后移植 Api 将自动检查设备上的 Android 版本，因此不需要执行 API 级别检查每个时间。  

本文档将讨论如何将权限添加到 Xamarin.Android 应用程序以及如何面向 Android 6.0 （API 级别 23） 应用程序，或更高版本应执行运行时权限检查。


> [!NOTE]
> 很可能硬件的权限可能会影响应用程序通过 Google Play 的筛选方式。 例如，如果应用照相机的所需的权限，然后 Google Play 将不显示应用程序中不具有安装了摄像头的设备上的 Google Play 商店。


<a name="requirements" />

## <a name="requirements"></a>要求

强烈建议 Xamarin.Android 项目包括[Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) NuGet 包。 与旧版本的 Android，提供一个常见的特定 Api 不断接口而无需为此包将向后移植权限检查 Android 运行应用程序的版本。


## <a name="requesting-system-permissions"></a>请求的系统权限

在使用 Android 权限中的第一步是声明 Android 中的权限清单文件。 此操作必须执行而不考虑 API 级别已针对该应用程序。

面向 Android 6.0 或更高版本无法假定，因为用户授予在过去，在某个时间点的权限，权限将有效的下一步的时间的应用。 面向 Android 6.0 的应用必须始终执行运行时权限检查。 面向 Android 5.1 或更低的应用不需要执行运行时权限检查。

> [!NOTE]
> 应用程序只应请求他们所需的权限。


### <a name="declaring-permissions-in-the-manifest"></a>声明在清单中的权限

权限添加到**AndroidManifest.xml**与`uses-permission`元素。 例如，如果某个应用程序以查找设备的位置，它都将正常需要并课程位置的权限。 以下两个元素添加到清单中： 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

它是可以声明使用内置于 Visual Studio 中的工具支持的权限：

1. 双击**属性**中**解决方案资源管理器**和选择**Android 清单**属性窗口中的选项卡：

    [![在 Android 清单选项卡中的所需的权限](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. 如果应用程序尚不包含 AndroidManifest.xml，单击**否 AndroidManifest.xml 找到。单击此项可添加一个**如下所示：

    [![No AndroidManifest.xml message](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. 选择你的应用程序需要任何权限**所需的权限**列出并保存：

    [![选择权限示例相机](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

它是可以声明使用内置于 Visual Studio 中适用于 Mac 的工具支持的权限：

1. 双击中的项目**解决方案 Pad**和选择**选项 > 生成 > Android 应用程序**:

    [![所示的所需的权限部分](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. 单击**添加 Android 清单**按钮如果项目不具有**AndroidManifest.xml**:

    [![项目的 Android 清单是缺少](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. 选择你的应用程序需要任何权限**所需的权限**列表，然后单击**确定**:

    [![选择权限示例相机](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin.Android 会自动添加某些权限在生成时到调试版本。 这将使调试应用程序更轻松。 特别是，两个值得注意的权限是`INTERNET`和`READ_EXTERNAL_STORAGE`。 这些自动设置的权限并不会显示在中启用**所需的权限**列表。 发行版本，但是，使用仅在中显式设置的权限**所需的权限**列表。 

对于面向 Android 5.1 （API 级别 22） 或更低版本的应用程序，没有任何详细需要进行。 将运行于 Android 6.0 （API 23 等级 23） 或更高版本的应用程序应继续执行到下一节的执行权限检查的运行的时。 


### <a name="runtime-permission-checks-in-android-60"></a>在 Android 6.0 中的运行时权限检查

`ContextCompat.CheckSelfPermission`方法 （适用于 Android 的支持库） 用来检查是否已被授予特定权限。 此方法将返回[ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/)枚举具有两个值之一：

* **`Permission.Granted`** &ndash; 已授予指定的权限。
* **`Permission.Denied`** &ndash; 尚未授予指定的权限。

此代码片段演示了如何检查在活动中的相机权限： 

```csharp
if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.Camera) == (int)Permission.Granted) 
{
    // We have permission, go ahead and use the camera.
} 
else 
{
    // Camera permission is not granted. If necessary display rationale & request.
}
```

它是一种最佳做法以通知用户有关为什么权限是必需的应用程序，以便可以进行明智的决策用于授予的权限。 此示例将采用的照片和地域标记应用它们。 就可以明确告知用户相机权限是必需的但它可能不清楚应用程序还需要设备的位置的原因。 阐释应显示一条消息，以帮助用户了解位置权限的需要和相机权限是必需的原因。

`ActivityCompat.ShouldShowRequestPermissionRational`方法用于确定是否应向用户显示的基本原理。 此方法将返回`true`如果应显示给定的权限的基本原理。 此屏幕截图中显示应用程序解释为什么应用程序需要了解设备的位置显示 Snackbar 的示例：

![位置的理由](permissions-images/07-rationale-snackbar.png) 

如果用户授予权限，`ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)`应该调用方法。 此方法需要以下参数：

* **活动**&ndash;这是请求权限，并是结果的 android 通知的活动。
* **权限**&ndash;正在请求的权限的列表。
* **requestCode** &ndash;一个整数值，用于匹配到权限请求的结果`RequestPermissions`调用。 此值应大于零。

此代码片段是讨论了两种方法的示例。 首先，进行检查以确定是否应显示的权限基本原理。 如果基本原理被显示，然后 Snackbar 则会显示基本原理。 如果用户单击**确定**中 Snackbar，然后应用程序将请求的权限。 如果用户不接受基本原理，然后应用程序应不会继续以请求权限。 如果未显示基本原理，该活动将请求权限：

```csharp
if (ActivityCompat.ShouldShowRequestPermissionRationale(this, Manifest.Permission.AccessFineLocation)) 
{
    // Provide an additional rationale to the user if the permission was not granted
    // and the user would benefit from additional context for the use of the permission.
    // For example if the user has previously denied the permission.
    Log.Info(TAG, "Displaying camera permission rationale to provide additional context.");

    var requiredPermissions = new String[] { Manifest.Permission.AccessFineLocation };
    Snackbar.Make(layout, 
                   Resource.String.permission_location_rationale,
                   Snackbar.LengthIndefinite)
            .SetAction(Resource.String.ok, 
                       new Action<View>(delegate(View obj) {
                           ActivityCompat.RequestPermissions(this, requiredPermissions, REQUEST_LOCATION);
                       }    
            )
    ).Show();
}
else 
{
    ActivityCompat.RequestPermissions(this, new String[] { Manifest.Permission.Camera }, REQUEST_LOCATION);
}
```

`RequestPermission` 可以调用，即使用户已授予权限。 后续调用不是必需的但它们为用户提供机会确认 （或撤消） 权限。 当`RequestPermission`是调用，控件将转移到操作系统，将显示用户界面，用于接受权限：  

![Permssion 对话框](permissions-images/08-location-permission-dialog.png)

用户已完成后，Android 会返回结果到活动的回调方法，通过`OnRequestPermissionResult`。 此方法是该接口的一部分`ActivityCompat.IOnRequestPermissionsResultCallback`必须由活动。 此接口具有一个方法， `OnRequestPermissionsResult`，其将调用以通知用户的选择的活动的 android。 如果用户已授予的权限，应用程序可以继续并使用受保护的资源。 举例说明如何实现`OnRequestPermissionResult`如下所示： 

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Permission[] grantResults)
{
    if (requestCode == REQUEST_LOCATION) 
    {
        // Received permission result for camera permission.
        Log.Info(TAG, "Received response for Location permission request.");

        // Check if the only required permission has been granted
        if ((grantResults.Length == 1) && (grantResults[0] == Permission.Granted)) {
            // Location permission has been granted, okay to retrieve the location of the device.
            Log.Info(TAG, "Location permission has now been granted.");
            Snackbar.Make(layout, Resource.String.permision_available_camera, Snackbar.LengthShort).Show();            
        } 
        else 
        {
            Log.Info(TAG, "Location permission was NOT granted.");
            Snackbar.Make(layout, Resource.String.permissions_not_granted, Snackbar.LengthShort).Show();
        }
    } 
    else 
    {
        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```  


## <a name="summary"></a>总结

本指南讨论如何添加和 Android 设备中检查权限。 权限 （API 级别 < 23） 的旧 Android 应用和新的 Android 应用程序之间的工作方式的差异 （API 级别 > 22）。 它讨论如何在 Android 6.0 中执行运行时权限检查。


## <a name="related-links"></a>相关链接

- [普通权限的列表](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [运行时权限示例应用](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [在 Xamarin.Android 的处理权限](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest)
