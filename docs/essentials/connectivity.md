---
title: Xamarin.Essentials： 连接
description: Xamarin.Essentials 中的连接类允许您监视的设备的网络状况变化，检查当前的网络访问权限，以及当前连接方式。
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 54c165e15e725caaecb1573b74cfe295170db141
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848604"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials： 连接

![预发行版 NuGet](~/media/shared/pre-release.png)

**连接**类可让你监视的更改在设备的网络条件，检查当前的网络访问权限，以及当前连接方式。

## <a name="getting-started"></a>入门

访问**连接**功能以下平台特定的安装程序是必需的。

# <a name="androidtabandroid"></a>[Android](#tab/android)

`AccessNetworkState`权限是必需的必须在 Android 项目中配置。 这可以通过以下方法添加：

打开**AssemblyInfo.cs**文件下**属性**文件夹并添加：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

或更新 Android 清单：

打开**AndroidManifest.xml**文件下**属性**文件夹，并添加以下的内部**清单**节点。

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

或右键单击 Anroid 项目并打开项目的属性。 下**Android 清单**查找**所需的权限：** 区域并检查**访问网络状态**权限。 这将自动更新**AndroidManifest.xml**文件。

# <a name="iostabios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="using-connectivity"></a>使用连接

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

检查当前的网络访问：

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

[网络访问](xref:Xamarin.Essentials.NetworkAccess)划分为以下类别：

* **Internet** – 本地和 internet 访问。
* **ConstrainedInternet** – 有限的 internet 访问。 指示强制网络门户连接性，其中提供了本地访问 web 门户，但访问 Internet，需要通过门户提供了特定的凭据。
* **本地**– 本地网络只能访问。
* **无**– 不不提供任何连接。
* **未知**– 无法确定 internet 连接。

你可以检查哪种[连接配置文件](xref:Xamarin.Essentials.ConnectionProfile)设备正在主动使用：

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

只要连接配置文件或网络访问的更改可以接收的事件触发时：

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(ConnectivityChangedEventArgs  e)
    {
        var access = e.NetworkAccess;
        var profiles = e.Profiles;
    }
}
```

## <a name="limitations"></a>限制

务必要注意的是可能的`Internet`报告的`NetworkAccess`但对 web 完全访问权限不可用。 由于每个平台上的连接工作原理它仅可以保证的连接可用。 例如此设备可能连接到 Wi-fi 网络，但与 internet 断开路由器。 在这种情况可能会报告 Internet，但活动连接不可用。

## <a name="api"></a>API

* [连接源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [连接 API 文档](xref:Xamarin.Essentials.Connectivity)
