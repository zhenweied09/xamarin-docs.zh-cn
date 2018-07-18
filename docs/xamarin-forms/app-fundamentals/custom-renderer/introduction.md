---
title: 自定义呈现器简介
description: 本文介绍了自定义呈现器，并概述了用于创建自定义呈现器的过程。
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: 180a196e0b95854815c8a74ef1d2df63407dd04f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997999"
---
# <a name="introduction-to-custom-renderers"></a>自定义呈现器简介

_自定义呈现器为自定义外观和行为的 Xamarin.Forms 控件提供一种有效方法。它们可用于小型样式更改或复杂的特定于平台的布局和行为自定义。本文介绍了自定义呈现器，并概述了用于创建自定义呈现器的过程。_

Xamarin.Forms[页面、 布局和控件](~/xamarin-forms/user-interface/controls/index.md)提供常见的 API，用于描述跨平台移动用户界面。 每个页面、 布局和控件的呈现不同，每个平台上使用`Renderer`类，该类又创建本机控件 （对应于 Xamarin.Forms 表示形式），对其进行排列在屏幕上，并将添加中指定的行为共享的代码。

开发人员可以实现自定义 `Renderer` 类，以自定义控件的外观和/或行为。 为给定类型的自定义呈现器可以添加到一个应用程序项目，以自定义控件在一个位置，同时允许在其他平台; 上的默认行为或不同的自定义呈现器可以添加到 iOS、 Android 和通用 Windows 平台 (UWP) 上创建不同的外观和感觉的每个应用程序项目。 但是，实现执行简单控件自定义的自定义呈现器类通常是重型的响应。 效果简化此过程中，并通常用于较小的样式更改。 有关详细信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="examining-why-custom-renderers-are-necessary"></a>正在检查为何自定义呈现器有必要

更改 Xamarin.Forms 控件的外观，而无需使用自定义呈现器，是涉及到创建自定义控件子类化，然后使用自定义控件代替原始控件通过一个两步过程。 下面的代码示例显示了子类化的示例`Entry`控件：

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

`MyEntry`控件是`Entry`控制在何处`BackgroundColor`设置为灰色，并可以通过声明其位置的命名空间和控件元素上使用的命名空间前缀在 Xaml 中引用。 下面的代码示例演示如何`MyEntry`自定义控件可供`ContentPage`:

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

`local`命名空间前缀可以是任何内容。 但是，`namespace`和`assembly`值必须匹配的自定义控件的详细信息。 一旦声明的命名空间，前缀用于引用自定义控件。

> [!NOTE]
> 定义`xmlns`比要简单得在.NET Standard 类库项目中共享的项目。 .NET Standard 库编译为程序集，因此很容易地确定什么`assembly=CustomRenderer`值应为。 （包括 XAML） 的所有共享的资源时使用共享项目，将编译到每个引用的项目，这意味着，如果 iOS、 Android 和 UWP 项目具有其自己*程序集名称*它是不可能编写`xmlns`声明由于需要为每个应用程序不同的值。 共享的项目的 XAML 中的自定义控件需要用相同的程序集名称来配置每个应用程序项目。

`MyEntry`自定义控件然后呈现在每个平台，具有灰色背景上，如以下屏幕截图中所示：

![](introduction-images/screenshots.png "每个平台上 MyEntry 自定义控件")

仅通过将控件子类化已经完成更改每个平台上的控件的背景色。 但是，这种技术中因为不能充分利用特定于平台的增强功能和自定义它可以实现限制。 在需要时，必须实现自定义呈现器。

## <a name="creating-a-custom-renderer-class"></a>创建自定义呈现器类

创建自定义呈现器类的过程如下所示：

1. 创建呈现本机控件呈现器类的子类。
1. 重写呈现本机控件的方法并编写逻辑以自定义控件。 通常情况下，`OnElementChanged`方法用于呈现本机控件，创建相应的 Xamarin.Forms 控件时调用。
1. 添加`ExportRenderer`到自定义呈现器类，以指定它将用于呈现 Xamarin.Forms 控件属性。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 对于大多数 Xamarin.Forms 元素，它是可选提供每个平台项目中的自定义呈现器。 如果未注册的自定义呈现器，则将使用默认的呈现器的控件的基类。 但是，自定义呈现器呈现时所需的每个平台项目中[视图](xref:Xamarin.Forms.View)或[ViewCell](xref:Xamarin.Forms.ViewCell)元素。

本系列中的主题将提供演示并解释了此过程的不同的 Xamarin.Forms 元素。

## <a name="troubleshooting"></a>疑难解答

如果自定义控件包含在.NET Standard 库项目已添加到解决方案 （即不是.NET 标准库由 Visual Studio for Mac/Visual Studio Xamarin.Forms 应用程序项目模板创建），异常可能会发生在 iOS 中时尝试访问自定义控件。 如果发生此问题，可以通过创建从自定义控件的引用来解决`AppDelegate`类：

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

这将强制使编译器可以识别`ClassInPCL`通过解析它的类型。 或者，`Preserve`可以将属性添加到`AppDelegate`类来实现相同的结果：

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

这将创建对引用`ClassInPCL`指示它已在运行时所需的类型。 有关详细信息，请参阅[保留代码](~/ios/deploy-test/linker.md)。

## <a name="summary"></a>总结

本文提供了自定义呈现器的简介，并概述了在创建自定义呈现器的过程。 自定义呈现器为自定义外观和行为的 Xamarin.Forms 控件提供一种有效方法。 它们可用于小型样式更改或复杂的特定于平台的布局和行为自定义。


## <a name="related-links"></a>相关链接

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
