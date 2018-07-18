---
title: Xamarin.Forms 控件模板的简介
description: Xamarin.Forms 控件模板让你轻松设计或 re 主题可以在运行时的应用程序页。 本文介绍控件模板。
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 6b7a6c6d9c9c541e1d5e821fc2dac202e98bec62
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994420"
---
# <a name="introduction-to-xamarinforms-control-templates"></a>Xamarin.Forms 控件模板的简介

_Xamarin.Forms 控件模板让你轻松设计或 re 主题可以在运行时的应用程序页。本文介绍控件模板。_

控件具有不同的属性，如`BackgroundColor`和`TextColor`，可以定义控件的外观的方面。 可以使用设置这些属性[样式](~/xamarin-forms/user-interface/styles/index.md)，这可以在运行时实现基本主题设置更改。 但是，样式不会维持页的外观和其内容之间完全分离，并且可以通过设置此类属性所做的更改受到限制。

控件模板提供页面的外观和其内容，从而可以轻松地进行主题化的页的创建之间完全分离。 例如，应用程序可能包含提供深色主题和浅色主题的应用程序级别控件模板。 每个[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)应用程序中可以有主题通过应用控件模板之一而无需更改按每页显示的内容。 此外，提供的控件模板的主题并不限于更改控件的属性。 它们还可以更改用于实现主题的控件。

## <a name="creating-a-controltemplate"></a>创建 ControlTemplate

一个[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)指定外观的页面或视图中，并包含根布局和布局，这些控件可实现在模板中。 通常情况下，`ControlTemplate`将利用[ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter)来标记要显示的页面或视图的内容出现的位置。 页面或使用的视图`ControlTemplate`然后，定义要显示的内容`ContentPresenter`。 下图演示了`ControlTemplate`包含多个控件，包括页面`ContentPresenter`由蓝色矩形标记：

![](introduction-images/control-template.png "页的控件模板")

一个[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)会应用于以下类型中，通过设置其`ControlTemplate`属性：

- [`ContentPage`](xref:Xamarin.Forms.ContentPage)
- [`ContentView`](xref:Xamarin.Forms.ContentView)
- [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)
- [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)

当[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)创建并分配到这些类型，任何现有的外观将替换中定义的外观`ControlTemplate`。 此外，以及使用设置外观`ControlTemplate`属性，还可以通过使用样式进一步应用模板的控件展开主题功能。

> [!NOTE]
>  *是什么`TemplatedPage`和`TemplatedView`类型？* `TemplatedPage` 是类的基类`ContentPage`，并为 Xamarin.Forms 提供的最基本的页类型。 与不同`ContentPage`，`TemplatedPage`没有`Content`属性。 因此，内容不能直接添加到`TemplatedPage`实例。 相反，通过设置的控件模板添加内容`TemplatedPage`实例。 同样，`TemplatedView`是类的基类`ContentView`。 与不同`ContentView`，`TemplatedView`没有`Content`属性。 因此，内容不能直接添加到`TemplatedView`实例。 相反，通过设置的控件模板添加内容`TemplatedView`实例。

在 XAML 和 C# 中，可以创建控件模板：

- 在 XAML 中创建的控件模板中定义[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)分配给[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)集合页上，或者更常见的做法到[ `Resources` ](xref:Xamarin.Forms.Application.Resources)应用程序的集合。
- 在 C# 中创建的控件模板通常定义在页面的类中，或者可全局访问的类。

选择定义的位置[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)实例可以使用它的影响：

- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 在页面级别定义的实例只能应用到的页。
- [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 在应用程序级别定义的实例可以应用于整个应用程序页。

控件模板的视图层次结构中较低级别优先于更高版本定义了。 例如， [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)名为`DarkTheme`定义在页级别将优先于在应用程序级别定义一个具有相同名称的模板。 因此，应在应用程序级别定义控件模板，用于定义要应用于应用程序中的每个页面的主题。


## <a name="related-links"></a>相关链接

- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
