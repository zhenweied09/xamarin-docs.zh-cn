---
title: "自定义呈现器简介"
description: "自定义呈现器提供了一个功能强大的方法，用于自定义外观和 Xamarin.Forms 控件的行为。 它们可以用于小样式更改或复杂的特定于平台的布局和行为自定义项。 本文介绍自定义呈现器，并概述了用于创建自定义呈现器的过程。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: a9908429994f4575a9e41936d500bfd8906a843b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-custom-renderers"></a>自定义呈现器简介

_自定义呈现器提供了一个功能强大的方法，用于自定义外观和 Xamarin.Forms 控件的行为。它们可以用于小样式更改或复杂的特定于平台的布局和行为自定义项。本文介绍自定义呈现器，并概述了用于创建自定义呈现器的过程。_

Xamarin.Forms[页、 布局和控件](~/xamarin-forms/user-interface/controls/index.md)提供通用 API 来描述跨平台移动用户界面。 每个页、 布局和控件呈现都不同，每个平台上使用`Renderer`反过来创建 （对应于 Xamarin.Forms 表示形式） 的本机控件的类对其进行排列在屏幕上，并将添加中指定的行为共享的代码。

开发人员可以实现自定义 `Renderer` 类，以自定义控件的外观和/或行为。 对于给定的类型的自定义呈现器可以添加到一个应用程序项目，以自定义控件在一个位置，同时允许在其他平台; 上的默认行为或不同的自定义呈现器可以添加到每个应用程序项目以在 iOS、 Android 和 Windows Phone 上创建不同的外观和感觉。 但是，实现一个自定义呈现器类以执行的简单控件自定义项通常是重型的响应。 效果简化此过程中，并通常用于小样式更改。 有关详细信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="examining-why-custom-renderers-are-necessary"></a>正在检查为何自定义呈现器将需要

更改 Xamarin.Forms 控件的外观，而无需使用自定义呈现器，是一个两步过程，涉及到创建通过子类化，，然后在原始控件的位置的自定义控件的自定义控件。 下面的代码示例显示的子类化示例`Entry`控件：

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

`MyEntry`控件是`Entry`控制在何处`BackgroundColor`设置为灰色，并可以在 Xaml 中通过声明其位置的命名空间和控制元素上使用的命名空间前缀引用。 下面的代码示例演示如何`MyEntry`可供自定义控件`ContentPage`:

```xaml
<ContentPage
    ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

`local`命名空间前缀可以是任何内容。 但是，`namespace`和`assembly`值必须匹配自定义控件的详细信息。 一旦声明的命名空间，前缀用于引用自定义控件。

> [!NOTE]
> **请注意**： 定义`xmlns`比要简单得在 Pcl 中共享的项目。 PCL 会编译成程序集，因此很容易地确定什么`assembly=CustomRenderer`值应为。 在使用共享项目时，所有共享的资产 （其中包括 XAML） 编译为每个引用的项目中，这意味着，如果，iOS、 Android 和 Windows Phone 项目都具有其自己*程序集名称*不可能如何编写`xmlns`声明因为值需要对每个应用程序不同。 对于共享项目的 XAML 中的自定义控件需要用相同的程序集名称来配置每个应用程序项目。

`MyEntry`自定义控件然后呈现在每个平台，具有灰色背景，如以下屏幕截图中所示：

![](introduction-images/screenshots.png "每个平台上的 MyEntry 自定义控件")

只是通过将控件子类化已完成更改每个平台上的控件的背景色。 但是，因为它不是可以利用特定于平台的增强功能以及自定义项它可以实现的限制此技术。 在需要时，必须实现自定义呈现器。

## <a name="creating-a-custom-renderer-class"></a>创建自定义呈现器类

用于创建自定义呈现器类的过程如下所示：

1. 创建的呈现本机控件的呈现器类的子类。
1. 重写呈现本机控件的方法并编写逻辑以自定义控件。 通常情况下，`OnElementChanged`方法用于呈现本机控件，创建相应的 Xamarin.Forms 控件时调用。
1. 添加`ExportRenderer`到自定义呈现器类，以指定，它将用于呈现 Xamarin.Forms 控件属性。 此属性用于与 xamarin.forms 结合注册自定义呈现器。

> [!NOTE]
> **请注意**： 对于大多数 Xamarin.Forms 元素，它是可选的提供每个平台项目中的自定义呈现器。 如果自定义呈现器未注册，则将使用控件的基类的默认呈现器。 但是，自定义呈现器需要每个平台项目中呈现时[视图](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)或[ViewCell](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)元素。

这一系列中的主题将提供演示和不同的 Xamarin.Forms 元素在此过程中的说明。

## <a name="troubleshooting"></a>疑难解答

如果已添加到解决方案 (即不 PCL Mac/Visual Studio Xamarin.Forms 应用程序项目模板的 Visual Studio 创建) 的 PCL 项目中包含自定义控件，，在尝试访问自定义控件时，可能会在 iOS 中发生异常。 如果出现此问题，可以通过创建从自定义控件的引用来解决该`AppDelegate`类：

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

这将强制编译器识别`ClassInPCL`由解析该名称的类型。 或者，`Preserve`可以将属性添加到`AppDelegate`类来实现相同的结果：

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

这将创建对引用`ClassInPCL`指示它已在运行时所需的类型。 有关详细信息，请参阅[保留代码](~/ios/deploy-test/linker.md)。

## <a name="summary"></a>摘要

这篇文章提供了自定义呈现器的简介，并介绍了用于创建自定义呈现器的过程。 自定义呈现器提供了一个功能强大的方法，用于自定义外观和 Xamarin.Forms 控件的行为。 它们可以用于小样式更改或复杂的特定于平台的布局和行为自定义项。


## <a name="related-links"></a>相关链接

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
