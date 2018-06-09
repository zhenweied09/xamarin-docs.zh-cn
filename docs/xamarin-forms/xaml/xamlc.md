---
title: Xamarin.Forms 中的 XAML 编译
description: 此文章介绍了如何 XAML 可以根据需要编译直接为使用 Xamarin.Forms XAML 编译器 (XAMLC) 的中间语言 (IL)。
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/21/2016
ms.openlocfilehash: 0128fecebe9f6ba8f55e965a8fa65787d03d9ded
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245740"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Xamarin.Forms 中的 XAML 编译

_XAML 可以根据需要编译直接到使用 XAML 编译器 (XAMLC) 的中间语言 (IL)。_

XAMLC 提供了一些好处：

- 它会执行 XAML 的编译时检查，从而可向用户通知任何错误。
- 它会消除 XAML 元素的某些负载和实例化时间。
- 它通过不再包含 .xaml 文件，来帮助减小最终程序集的文件大小。

XAMLC 在默认情况下处于禁用状态，以便确保后向兼容性。 它可以在程序集和类级别启用通过添加`XamlCompilation`属性。

下面的代码示例演示如何启用程序集级别的 XAMLC:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

此示例中，在编译时检查的所有 XAML 包含在`PhotoApp`命名空间将执行，并且在编译时而不是运行时报告的 XAML 错误。
`assembly`到前缀`XamlCompilation`属性指定特性应用于整个程序集。

下面的代码示例演示如何启用 XAMLC 在类级别：

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

在此示例中，进行编译时检查的 XAML`HomePage`将执行类和错误报告作为编译过程的一部分。

> [!NOTE]
> `XamlCompilation`属性和`XamlCompilationOptions`枚举驻留在`Xamarin.Forms.Xaml`命名空间，必须导入以使用它们。


## <a name="related-links"></a>相关链接

- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
