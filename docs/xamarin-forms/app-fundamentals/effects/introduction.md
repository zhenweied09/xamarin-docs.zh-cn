---
title: 效果简介
description: 效果允许进行自定义，每个平台上的本机控件，通常用于小样式更改。 本文提供效果的简介，概述了效果和自定义呈现器之间的边界，并对 PlatformEffect 类进行描述。
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 805b27139d644d0fac4a7c5a9e9b2c2c331d7130
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="introduction-to-effects"></a>效果简介

_效果允许进行自定义，每个平台上的本机控件，通常用于小样式更改。本文提供效果的简介，概述了效果和自定义呈现器之间的边界，并对 PlatformEffect 类进行描述。_

Xamarin.Forms[页、 布局和控件](~/xamarin-forms/user-interface/controls/index.md)提供通用 API 来描述跨平台移动用户界面。 每个页、 布局和控件呈现都不同，每个平台使用`Renderer`反过来创建 （对应于 Xamarin.Forms 表示形式） 的本机控件的类对其进行排列在屏幕上，并将添加中共享指定的行为代码。

开发人员可以实现自定义 `Renderer` 类，以自定义控件的外观和/或行为。 但是，实现一个自定义呈现器类以执行的简单控件自定义项通常是重型的响应。 效果简化此过程中，允许更轻松地自定义每个平台上的本机控件。

子类化在特定于平台的项目中创建效果`PlatformEffect`供控件，然后效果将其附加到 Xamarin.Forms.NET 标准库或共享库项目中的相应控件。

## <a name="why-use-an-effect-over-a-custom-renderer"></a>为什么要通过自定义呈现器使用效果？

效果简化控件的自定义项，可以重复使用，并可以对其进行参数化，以重用，从而进一步提升。

可以通过一种效果的任何内容还可以使用自定义呈现器实现。 但是，自定义呈现器提供更多灵活性和比效果的自定义。 以下准则所列出的在其中选择一种效果，而不是自定义呈现器的情况：

- 更改特定于平台的控件的属性将会获得所需的结果时，建议效果。
- 需要重写方法的特定于平台时，自定义呈现器是控件的必需的。
- 需要替换实现 Xamarin.Forms 控件的特定于平台的控件时，自定义呈现器是必需的。

## <a name="subclassing-the-platformeffect-class"></a>子类化 PlatformEffect 类

下表列出的命名空间`PlatformEffect`对每个平台和其属性的类型的类：

|平台|命名空间|容器|控件|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|分组|视图|
|通用 Windows 平台 (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

每个特定于平台的`PlatformEffect`类公开以下属性：

- `Container` – 引用正在用来实现布局的特定于平台的控件。
- `Control` – 引用正在用来实现 Xamarin.Forms 控件的特定于平台的控件。
- `Element` – 引用呈现 Xamarin.Forms 控件。

效果没有容器、 控制或只附加到，因为它们可以附加到任何元素的元素的类型信息。 因此，当效果附加到它不支持的元素时应适当降级或引发异常。 但是， `Container`， `Control`，和`Element`属性可以强制转换为其实现的类型。 有关详细信息，有关这些类型，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

每个特定于平台的`PlatformEffect`类公开以下方法，必须重写以实现的效果：

- [`OnAttached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnAttached()/) – 当影响附加到 Xamarin.Forms 控件时调用。 在每个特定于平台的效果类，此方法的重写的版本是执行自定义控件，以及在产生的效果不能应用于指定的 Xamarin.Forms 控件的情况下的异常处理的位置。
- [`OnDetached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnDetached()/) – 当效果分离 Xamarin.Forms 控件中时调用。 在每个特定于平台的效果类，此方法的重写的版本是执行任何效果的清理操作如取消注册事件处理程序的位置。

此外，`PlatformEffect`公开[ `OnElementPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E.OnElementPropertyChanged/p/System.ComponentModel.PropertyChangedEventArgs/)方法，也可能被替代。 当元素的属性发生更改时，调用此方法。 在每个特定于平台的效果类，此方法的重写的版本是 Xamarin.Forms 控件上的可绑定的属性更改作出响应的位置。 更改的属性应始终会进行检查，如可以多次调用此重写。


## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
