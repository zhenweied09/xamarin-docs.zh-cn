---
title: 效果简介
description: 效果允许进行自定义，每个平台上的本机控件，通常用于较小的样式更改。 本文介绍了影响，概述了影响与自定义呈现器之间的边界并描述 PlatformEffect 类。
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d3fa958e999a10832d5fa15e4190077955b0e6df
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997370"
---
# <a name="introduction-to-effects"></a>效果简介

_效果允许进行自定义，每个平台上的本机控件，通常用于较小的样式更改。本文介绍了影响，概述了影响与自定义呈现器之间的边界并描述 PlatformEffect 类。_

Xamarin.Forms[页面、 布局和控件](~/xamarin-forms/user-interface/controls/index.md)提供常见的 API，用于描述跨平台移动用户界面。 每个页面、 布局和控件以不同的方式使用呈现在每个平台`Renderer`类，该类又创建本机控件 （对应于 Xamarin.Forms 表示形式），对其进行排列在屏幕上，并将添加在共享指定的行为代码。

开发人员可以实现自定义 `Renderer` 类，以自定义控件的外观和/或行为。 但是，实现执行简单控件自定义的自定义呈现器类通常是重型的响应。 效果简化此过程中，允许更轻松地自定义每个平台上的本机控件。

特定于平台的项目中创建通过子类化效果`PlatformEffect`由控件，然后效果将其附加到 Xamarin.Forms.NET 标准库或共享库项目中相应的控件。

## <a name="why-use-an-effect-over-a-custom-renderer"></a>为什么要使用通过自定义呈现器的影响？

效果简化控件的自定义、 可重用，和可以进行参数化以重用，从而进一步提升。

也可以使用自定义呈现器可以实现具有某种效果的任何内容。 但是，自定义呈现器提供更多灵活性和自定义效果比。 以下指导原则列出在其中选择效果，而不是自定义呈现器的情况：

- 更改特定于平台的控件的属性将会获得所需的结果时，建议使用一种效果。
- 需要重写特定于平台的方法时，自定义呈现器是控件的必需的。
- 需要替换实现的 Xamarin.Forms 控件的特定于平台的控件时需要自定义呈现器。

## <a name="subclassing-the-platformeffect-class"></a>子类化 PlatformEffect 类

下表列出的命名空间`PlatformEffect`上每个平台和其属性的类型的类：

|平台|命名空间|容器|控件|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|视图分组|视图|
|通用 Windows 平台 (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

每个特定于平台的`PlatformEffect`类公开以下属性：

- `Container` – 引用被用来实现布局的特定于平台的控件。
- `Control` – 引用被用来实现的 Xamarin.Forms 控件的特定于平台的控件。
- `Element` – 引用 Xamarin.Forms 控件要呈现。

效果没有容器、 控件或元素与其所附加到，因为它们可以附加到任何元素的类型信息。 因此，影响附加到的元素，它不支持时它应适当降级或引发异常。 但是， `Container`， `Control`，和`Element`属性可以强制转换为其实现的类型。 有关类型的这些详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

每个特定于平台的`PlatformEffect`类公开以下方法，必须重写以实现一种效果：

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached) – 当效果附加到 Xamarin.Forms 控件时调用。 重写的版本中的每个特定于平台的效果类，此方法是执行自定义控件，以及异常处理以防效果不能应用于指定的 Xamarin.Forms 控件的位置。
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached) – 当效果与 Xamarin.Forms 控件时调用。 重写的版本中的每个特定于平台的效果类，此方法是执行任何效果清理，例如取消注册事件处理程序的位置。

此外，`PlatformEffect`公开[ `OnElementPropertyChanged` ](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs))方法，还可以重写该方法。 当该元素的属性发生更改时，调用此方法。 重写的版本中的每个特定于平台的效果类，此方法是对 Xamarin.Forms 控件上的可绑定的属性更改进行响应的位置。 始终应进行检查更改的属性，如可以多次调用此重写。


## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
