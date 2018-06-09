---
title: Xamarin.Forms 应用生命周期
description: 此文章介绍了如何对应用程序生命周期，包括生命周期方法、 页面导航事件和模式的导航事件做出响应。
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: fb651494b63a77ede47dd246ee054b5c67af2a35
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240264"
---
# <a name="xamarinforms-app-lifecycle"></a>Xamarin.Forms 应用生命周期

[ `Application` ](xref:Xamarin.Forms.Application)基类提供了以下功能：

* [生命周期方法](#Lifecycle_Methods) `OnStart`， `OnSleep`，和`OnResume`。
* [页导航事件](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing)， [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing)。
* [模式导航事件](#modal) `ModalPushing`， `ModalPushed`， `ModalPopping`，和`ModalPopped`。

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>生命周期方法

[ `Application` ](xref:Xamarin.Forms.Application)类包含三个虚拟方法，可以重写以处理生命周期方法：

* **OnStart** -应用程序启动时调用。

* **OnSleep** -应用程序将为与背景每次调用。

* **OnResume** -时恢复应用程序，发送到背景之后调用。

请注意，没有*没有*应用程序终止的方法。
在正常情况下 (即。 不崩溃) 应用程序终止时，也会从*OnSleep*状态，而无需任何其他通知到您的代码。

若要观察调用这些方法时，实现`WriteLine`（如下所示），在每个调用和每个平台上测试。

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

在更新时*旧*Xamarin.Forms 应用程序 （如。 创建与 Xamarin.Forms 1.3 或更低版本），请确保 Android 主活动包括`ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation`中`[Activity()]`属性。 如果此项不存在将观察`OnStart`旋转以及应用程序首次启动时，获取调用方法。 此属性将自动包含在当前的 Xamarin.Forms 应用模板中。

<a name="page" />

## <a name="page-navigation-events"></a>页面导航事件

上有两个事件[ `Application` ](xref:Xamarin.Forms.Application)提供页出现和消失的通知的类：

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) -将要屏幕上显示一个页面时引发。
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) -将要从屏幕上消失页时引发。

这些事件可以在你想要跟踪页，因为它们显示在屏幕上在方案中使用。

> [!NOTE]
> [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing)和[ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing)从引发事件[ `Page` ](xref:Xamarin.Forms.Page)基类后立即[ `Page.Appearing`](xref:Xamarin.Forms.Page.Appearing)和[ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing)事件，分别。

<a name="modal" />

## <a name="modal-navigation-events"></a>模式导航事件

上有四个事件[ `Application` ](xref:Xamarin.Forms.Application)类，每个都有其自己的事件自变量，以便在响应模式页正在显示并关闭：

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** -`ModalPoppingEventArgs`类包含`Cancel`属性。 当`Cancel`设置为`true`模式 pop 已取消。
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> 若要实现的应用程序生命周期方法和模式的导航事件，所有预`Application`创建 Xamarin.Forms 应用的方法 (即。 在版本 1.2 或更低版本中编写的应用程序使用静态`GetMainPage`方法) 已经更新，以创建默认`Application`该值设置为的父级`MainPage`。
>
> 使用这一旧行为的 Xamarin.Forms 应用程序必须更新到`Application`子类上所述[应用程序类](~/xamarin-forms/app-fundamentals/application-class.md)页。
