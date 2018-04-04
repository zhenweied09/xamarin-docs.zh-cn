---
title: 应用生命周期
description: 如何响应应用程序生命周期
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 511591482a0e7512be34f6a210c6f44a1826be24
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="app-lifecycle"></a>应用生命周期

`Application`基类提供了以下功能：

* [生命周期方法](#Lifecycle_Methods) `OnStart`， `OnSleep`，和`OnResume`。
* [模式导航事件](#modal) `ModalPushing`， `ModalPushed`， `ModalPopping`，和`ModalPopped`。

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>生命周期方法

`Application`类包含三个虚拟方法，可以重写以处理生命周期方法：

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

<a name="modal" />

## <a name="modal-navigation-events"></a>模式导航事件

上有四个新事件`Application`Xamarin.Forms 1.4，每个都有其自己的事件自变量中的类：

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** -`ModalPoppingEventArgs`类包含`Cancel`属性。 当`Cancel`设置为`true`模式 pop 已取消。
* **ModalPopped** - `ModalPoppedEventArgs`

这些事件将帮助你更好地管理应用程序生命周期，通过让你响应模式页正在显示并关闭。

> [!NOTE]
> 若要实现的应用程序生命周期方法和模式的导航事件，所有预`Application`创建 Xamarin.Forms 应用的方法 (即。 在版本 1.2 或更低版本中编写的应用程序使用静态`GetMainPage`方法) 已经更新，以创建默认`Application`该值设置为的父级`MainPage`。
>
> 使用这一旧行为的 Xamarin.Forms 应用程序必须更新到`Application`子类上所述[应用程序类](~/xamarin-forms/app-fundamentals/application-class.md)页。
