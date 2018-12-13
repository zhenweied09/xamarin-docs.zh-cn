---
title: Xamarin.Forms 应用程序生命周期
description: 此文章介绍了如何响应应用程序生命周期，包括生命周期方法、页面导航事件和模式导航事件。
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675115"
---
# <a name="xamarinforms-app-lifecycle"></a>Xamarin.Forms 应用程序生命周期

[`Application`](xref:Xamarin.Forms.Application) 基类具有以下特点：

* [生命周期方法](#Lifecycle_Methods) `OnStart`、`OnSleep` 和 `OnResume`。
* [页导航](#page) [ `PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)、[`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)。
* [模式导航事件](#modal) `ModalPushing`、`ModalPushed`、`ModalPopping` 和 `ModalPopped`。

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>生命周期方法

[`Application`](xref:Xamarin.Forms.Application) 类包含三个虚拟方法，可以替代以处理生命周期方法：

* **OnStart** - 在应用程序启动时调用。

* **OnSleep** - 每当应用程序转到后台时调用。

* **OnResume** - 应用程序发送到后台后恢复时调用。

请注意，没有方法用于应用程序终止。
在正常情况下（即不崩溃），应用程序终止将从 OnSleep 状态发生，并且没有对代码的其他任何通知。

若要观察何时调用这些方法，请在每个平台上实现 `WriteLine` 调用（如下所示）并进行测试。

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

更新较旧的 Xamarin.Forms 应用程序（例如， 使用 Xamarin.Forms 1.3 或更早版本创建），请确保 Android 主活动在 `[Activity()]` 属性中包含 `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation`。 如果此项不存在，将观察到 `OnStart` 方法在旋转以及应用程序首次启动时被调用。 此属性将自动包含在当前的 Xamarin.Forms 应用程序模板中。

<a name="page" />

## <a name="page-navigation-events"></a>页导航事件

[`Application`](xref:Xamarin.Forms.Application) 类上有两个事件，可提供页面显示和消失通知：

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) - 页面即将在屏幕上显示时引发。
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) - 页面即将从屏幕上消失时引发。

这些事件可用于要在页面在屏幕上显示时进行跟踪的场景。

> [!NOTE]
> 在 [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) 和 [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) 事件后，从 [`Page`](xref:Xamarin.Forms.Page) 基类中将分别引发 [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) 和 [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) 事件。

<a name="modal" />

## <a name="modal-navigation-events"></a>模式导航事件

[`Application`](xref:Xamarin.Forms.Application) 类上有四个事件，每个都有其自己的参数，使你能够响应如下所示和解除的模式页面：

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - `ModalPoppingEventArgs` 类包含 `Cancel` 属性。 如果 `Cancel` 设置为 `true`，将取消模式弹出。
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> 若要实现应用程序生命周期方法和模式导航事件，需要更新创建 Xamarin.Forms 应用（即以使用静态 `GetMainPage` 方法编写的版本 1.2 或更早版本应用程序）的 pre-`Application` 方法，以创建设置为 `MainPage` 的父级的默认 `Application`。
>
> 使用这一旧行为的 Xamarin.Forms 应用程序必须更新到[应用程序类](~/xamarin-forms/app-fundamentals/application-class.md)页上所述的 `Application` 子类。
