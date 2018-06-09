---
title: Xamarin.Forms 中的 XAML 命名空间
description: XAML 使用的命名空间声明 xmlns XML 属性。 本文介绍 XAML 命名空间语法中，并演示如何声明一个 XAML 命名空间，以访问的类型。
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/10/2017
ms.openlocfilehash: faa4998869b918caaf5bc4252dc81a5745199c93
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245828"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>Xamarin.Forms 中的 XAML 命名空间

_XAML 使用的命名空间声明 xmlns XML 属性。本文介绍 XAML 命名空间语法中，并演示如何声明一个 XAML 命名空间，以访问的类型。_

## <a name="overview"></a>概述

有两个始终位于 XAML 文件的根元素的 XAML 命名空间声明。 第一个定义默认的命名空间，如下面的 XAML 代码示例中所示：

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
```

默认命名空间指定在没有任何前缀的 XAML 文件内定义的元素如指 Xamarin.Forms 类[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)。

第二个命名空间声明将使用`x`前缀，如下面的 XAML 代码示例中所示：

```csharp
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML 使用前缀声明非默认命名空间，以在引用的命名空间中的类型时使用的前缀。 `x`命名空间声明指定元素定义中前缀为 XAML`x`用于元素和属性是固有的 XAML （专门 2009 XAML 规范）。

下表概括了`x`Xamarin.Forms 所支持的命名空间属性：

|构造|描述|
|--- |--- |
|`x:Arguments`|指定非默认的构造函数，或为工厂方法的对象声明的构造函数自变量。|
|`x:Class`|指定在 XAML 中定义的类的命名空间和类名称。 类名称必须匹配的代码隐藏文件中的类名。 请注意，此构造只能出现在 XAML 文件的根元素。|
|`x:FactoryMethod`|指定用于初始化的对象的工厂方法。|
|`x:Key`|指定在每个资源的唯一用户定义键`ResourceDictionary`。 键的值可用于检索 XAML 资源，并通常用作参数`StaticResource`标记扩展。|
|`x:Name`|指定的 XAML 元素的运行时对象名称。 设置`x:Name`类似于在代码中声明变量。|
|`x:TypeArguments`|指定泛型类型的构造函数的泛型类型自变量。|

有关详细信息`x:Arguments`， `x:FactoryMethod`，和`x:TypeArguments`属性，请参阅[传递在 XAML 中的自变量](~/xamarin-forms/xaml/passing-arguments.md)。

在 XAML 中，命名空间声明从父元素继承到子元素。 因此，在 XAML 文件的根元素中定义命名空间，该文件中的所有元素都继承的命名空间声明。

## <a name="declaring-namespaces-for-types"></a>声明类型的命名空间

通过指定公共语言运行时 (CLR) 命名空间名称和 （可选） 程序集名称的命名空间声明与声明具有前缀的 XAML 命名空间，可以在 XAML 中引用类型。 这可通过定义命名空间声明内的以下关键字的值：

- **clr 命名空间：** 或**使用：** – 在包含要将公开为 XAML 元素的类型的程序集内的 CLR 命名空间声明。 此关键字是必需的。
- **程序集 =** – 包含引用的 CLR 命名空间的程序集。 此值是没有文件扩展名的程序集的名称。 应为包含将引用程序集的 XAML 文件的项目文件中的引用建立对程序集的路径。 此关键字，则可省略**clr 命名空间**值位于相同的程序集中引用类型的应用程序代码。

请注意，字符分隔`clr-namespace`或`using`令牌从其值是一个冒号，而字符分隔`assembly`令牌从其值是一个等号。 要使用两个标记之间的字符是一个分号。

下面的代码示例演示了一个 XAML 命名空间声明：

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

或者，这可以编写为：

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

`local`前缀是用于指示命名空间中的类型是本地的应用程序的约定。 或者，如果类型中不同的程序集，程序集名称应还定义在命名空间声明中，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

时将从导入的命名空间，类型的实例声明为以下 XAML 代码示例所示，然后指定的命名空间前缀：

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

## <a name="summary"></a>总结

这篇文章引入 XAML 命名空间语法中，并演示了如何声明一个 XAML 命名空间，以访问的类型。 XAML 使用`xmlns`可以在 XAML 中通过声明具有前缀的 XAML 命名空间引用命名空间声明和类型的 XML 属性。


## <a name="related-links"></a>相关链接

- [在 XAML 中的传递自变量](~/xamarin-forms/xaml/passing-arguments.md)
