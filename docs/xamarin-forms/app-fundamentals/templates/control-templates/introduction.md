---
title: "介绍"
description: "Xamarin.Forms 控件模板提供轻松主题和重新主题的功能在运行时的应用程序页。 本文介绍控件模板。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: c3973b94168706e047ee5c312a8823503c9b0fd8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="introduction"></a>介绍

_Xamarin.Forms 控件模板提供轻松主题和重新主题的功能在运行时的应用程序页。本文介绍控件模板。_

控件具有不同的属性，如`BackgroundColor`和`TextColor`，可以定义控件的外观的方面。 可以使用设置这些属性[样式](~/xamarin-forms/user-interface/styles/index.md)，这可以更改在运行时实现基本的主题。 但是，样式不维护页的外观和其内容，之间完全分离，并且可以通过设置此类属性所做的更改受到限制。

控件模板提供页的外观和其内容，因此支持很容易就能主题的页创建之间完全分离。 例如，一个应用程序可能包含提供深色主题和浅色主题的应用程序级控件模板。 每个[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)应用程序中可以有主题通过应用控件模板之一而无需更改按每页显示的内容。 此外，提供的控件模板的主题并不限于更改控件的属性。 他们还可以更改用于实现主题的控件。

## <a name="creating-a-controltemplate"></a>创建 ControlTemplate

A [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)指定页或视图的外观和包含的根布局和布局，这些控件可实现该模板中。 通常情况下，`ControlTemplate`将利用[ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)将标记出现通过页面或视图中显示的内容的位置。 在页面或使用的视图`ControlTemplate`然后定义显示由内容`ContentPresenter`。 下图说明了`ControlTemplate`页包含大量的控件，包括`ContentPresenter`由蓝色矩形标记：

![](introduction-images/control-template.png "一个页的控件模板")

A [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)通过设置应用于以下类型其`ControlTemplate`属性：

- [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)
- [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/)
- [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/)

当[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)创建并分配到这些类型中，任何现有的外观将替换中定义的外观`ControlTemplate`。 此外，以及通过使用设置外观`ControlTemplate`属性，也可以通过使用进一步的样式应用模板的控件扩展了主题能力。

> [!NOTE]
>  *什么是`TemplatedPage`和`TemplatedView`类型？* `TemplatedPage` 是的基类`ContentPage`，并且是通过 Xamarin.Forms 提供的最基本的页类型。 与不同`ContentPage`，`TemplatedPage`没有`Content`属性。 因此，内容不能直接添加到`TemplatedPage`实例。 但是，通过设置的控件模板添加内容`TemplatedPage`实例。 同样，`TemplatedView`是为基类`ContentView`。 与不同`ContentView`，`TemplatedView`没有`Content`属性。 因此，内容不能直接添加到`TemplatedView`实例。 但是，通过设置的控件模板添加内容`TemplatedView`实例。

在 XAML 和 C# 中，可以创建控件模板：

- 在 XAML 中创建的控件模板中定义[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) ，分配给[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)组页上，或到更常见的做法[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/)的应用程序的集合。
- 在页面的类中，或可全局访问的类中通常定义在 C# 中创建的控件模板。

选择定义的位置[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)实例可以使用它的影响：

- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) 在页级别上定义的实例仅能应用于页。
- [`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) 在应用程序级别定义的实例可以应用于整个应用程序中的页。

控件模板视图层次结构中较低级别优先于那些定义更高版本上。 例如， [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)名为`DarkTheme`定义在页级别将优先于在应用程序级别定义一个具有相同名称的模板。 因此，应在应用程序级别定义控件模板，用于定义要应用于应用程序中每个页的主题。


## <a name="related-links"></a>相关链接

- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)
