---
title: Xamarin.Essentials 平台
description: 平台类允许应用程序的主执行线程上运行代码。
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E0
author: charlespetzold
ms.author: chape
ms.date: 06/03/2018
ms.openlocfilehash: 82e248ee702e104dff98b342aec72179273fc34f
ms.sourcegitcommit: d9ecac62bcf743aff52408fbbd09c5509921a000
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36329944"
---
# <a name="xamarinessentials-platform"></a>Xamarin.Essentials 平台

![预发行 NuGet](~/media/shared/pre-release.png)

**平台**类允许应用程序的执行，主线程上运行代码并确定特定的代码块是否当前的主线程上运行。

## <a name="background"></a>背景

大多数操作系统-包括 iOS、 Android 和通用 Windows 平台-对涉及用户界面的代码中使用单线程模型。 此模型是正确序列化用户界面事件，包括击键和触控输入所需的。 此线程情况通常称作_主线程_或_用户界面线程_或_UI 线程_。 此模型的缺点是访问用户界面元素的所有代码必须在应用程序的主线程上都运行。 

应用程序有时需要使用执行的辅助线程调用事件处理程序的事件。 (Xamarin.Essentials 类[ `Accelerometer` ](accelerometer.md)， [ `Compass` ](compass.md)， [ `Gyroscope` ](gyroscope.md)， [ `Magnetometer` ](magnetometer.md)，和[ `OrientationSensor` ](orientation-sensor.md)所有可能会在辅助线程更快的速度一起使用时返回信息。)如果事件处理程序需要访问用户界面元素，则必须在主线程上运行该代码。 **平台**类允许应用程序主线程上运行此代码。

## <a name="running-code-on-the-main-thread"></a>在主线程上运行代码

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要在主线程中运行代码，可以调用静态`Platform.BeginInvokeOnMainThread`方法。 此自变量是[ `Action` ](xref:System.Action)对象，它是只需具有任何自变量和没有返回值的方法：

```csharp
Platform.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

如果你需要在从派生类中的 Xamarin.Forms 应用程序中调用此方法`Element`(其中包括任何派生自的类`View`或`Page`)，则`Platform`需要使用完全限定类名命名空间名称：

```csharp
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

它还可定义单独的方法必须在主线程上运行的代码：

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

然后，可以运行此方法在主线程通过引用在`BeginInvokeOnMainThread`方法：

```csharp
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms 具有一个方法[ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread)执行与相同，均`Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)`。 尽管你可以在 Xamarin.Forms 应用中使用任何一种方法，请考虑调用代码 Xamarin.Forms 上具有依赖项的任何其他需求。 如果没有，`Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)`可能是更好的选择。

## <a name="determining-if-code-is-running-on-the-main-thread"></a>确定主线程上是否正在运行代码

`Platform`类还允许应用程序确定的主线程上运行的特定的代码块。 `IsMainThread`属性返回`true`如果在主线程上运行的代码调用属性。 程序可以使用此属性来运行不同的主线程或辅助线程的代码：

```csharp
if (Xamarin.Essentials.Platform.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

你可能想知道是否应检查之前调用的辅助线程上是否正在运行代码`BeginInvokeOnMainThread`，例如，如下所示：

```csharp
if (Xamarin.Essentials.Platform.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

不难怀疑这一检查可能会提高性能，如果已在主线程上运行的代码块。

_但是，此检查是不必要的。_ 平台实现`BeginInvokeOnMainThread`本身检查调用主线程上。 存在是很少的性能产生负面影响，如果调用`BeginInvokeOnMainThread`时不真正必要。

## <a name="api"></a>API

- [平台源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Platform)
- [平台 API 文档](xref:Xamarin.Essentials.Platform)
