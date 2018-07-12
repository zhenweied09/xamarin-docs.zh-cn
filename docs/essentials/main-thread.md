---
title: 'Xamarin.Essentials: MainThread'
description: MainThread 类允许应用程序的主执行线程上运行的代码。
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: charlespetzold
ms.author: chape
ms.date: 06/26/2018
ms.openlocfilehash: e07d36d3e9a5492e6e170b62dbacb36be44dbfa9
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831420"
---
# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

![预发行版 NuGet](~/media/shared/pre-release.png)

**MainThread**类允许应用程序执行，在主线程上运行代码并确定某一特定块的代码是否在主线程上当前正在运行。

## <a name="background"></a>背景

大多数操作系统 — 包括 iOS、 Android 和通用 Windows 平台-对用户界面的代码使用单线程模型。 此模型，才可正确序列化用户界面事件，包括击键和触摸屏输入。 此线程通常称为_主线程_或_用户界面线程_或_UI 线程_。 此模型的缺点是访问用户界面元素的所有代码必须在应用程序的主线程上都运行。 

应用程序有时需要使用执行在次级线程调用事件处理程序的事件。 (Xamarin.Essentials 类[ `Accelerometer` ](accelerometer.md)， [ `Compass` ](compass.md)， [ `Gyroscope` ](gyroscope.md)， [ `Magnetometer` ](magnetometer.md)，和[ `OrientationSensor` ](orientation-sensor.md)所有可能会返回与更快的速度一起使用时在次级线程上的信息。)如果事件处理程序需要访问用户界面元素，则必须在主线程上运行该代码。 **MainThread**类允许应用程序主线程上运行此代码。

## <a name="running-code-on-the-main-thread"></a>在主线程上运行代码

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要在主线程上运行代码，请调用静态`MainThread.BeginInvokeOnMainThread`方法。 参数是[ `Action` ](xref:System.Action)对象，它是只需具有任何参数，没有返回值的方法：

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

它也是可以定义一个单独的方法必须在主线程上运行的代码：

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

然后，可以运行此方法在主线程上通过引用在`BeginInvokeOnMainThread`方法：

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms 有一个名为[ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread)执行相同的`MainThread.BeginInvokeOnMainThread(Action)`。 虽然在 Xamarin.Forms 应用中，可以使用任何一种方法，请考虑调用代码 Xamarin.Forms 上有任何其他需要的某个依赖项。 如果没有，`MainThread.BeginInvokeOnMainThread(Action)`可能是更好的选择。

## <a name="determining-if-code-is-running-on-the-main-thread"></a>确定代码在主线程上运行

`MainThread`类还允许应用程序确定主线程上运行的一个特定的代码块。 `IsMainThread`属性返回`true`代码调用该属性是否在主线程上运行。 程序可以使用此属性来运行不同的主线程或辅助线程的代码：

```csharp
if (MainThread.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

您可能想知道是否应检查代码是否正在运行之前，调用在次级线程上`BeginInvokeOnMainThread`，例如，如下所示：

```csharp
if (MainThread.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

您可能怀疑如果已在主线程上运行的代码块，此检查可能会提高性能。

_但是，不需要此项检查。_ 平台实现`BeginInvokeOnMainThread`本身检查调用主线程上。 如果您调用，则很少的性能损失`BeginInvokeOnMainThread`不真正必要时。

## <a name="api"></a>API

- [MainThread 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [MainThread API 文档](xref:Xamarin.Essentials.MainThread)
