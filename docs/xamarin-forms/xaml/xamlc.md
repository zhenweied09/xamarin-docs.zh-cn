---
title: 在 Xamarin.Forms 中 XAML 编译
description: 本文介绍如何 XAML 可以根据需要直接编译为中间语言 (IL) 使用 Xamarin.Forms XAML 编译器 (XAMLC)。
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/02/2018
ms.openlocfilehash: b828e62ef1037bf47a2ae5fb303fbf8fcace6549
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997128"
---
# <a name="xaml-compilation-in-xamarinforms"></a>在 Xamarin.Forms 中 XAML 编译

_XAML 可根据需要编译为中间语言 (IL) 使用 XAML 编译器 (XAMLC) 直接。_

XAMLC 提供了一些好处：

- 它会执行 XAML 的编译时检查，从而可向用户通知任何错误。
- 它会消除 XAML 元素的某些负载和实例化时间。
- 它通过不再包含 .xaml 文件，来帮助减小最终程序集的文件大小。

XAMLC 在默认情况下处于禁用状态，以便确保后向兼容性。 它可以在程序集和类级别启用通过添加`XamlCompilation`属性。

下面的代码示例演示如何启用 XAMLC 在程序集级别：

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

在此示例中，编译时检查的程序集内包含的所有 XAML 将执行，但在编译时而不是在运行时报告的 XAML 出现错误。 因此，`assembly`为前缀`XamlCompilation`属性指定该属性应用于整个程序集。

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

在此示例中，编译时检查的 XAML 的`HomePage`类将在执行和错误报告作为编译过程的一部分。

> [!NOTE]
> `XamlCompilation`属性和`XamlCompilationOptions`枚举位于`Xamarin.Forms.Xaml`命名空间，必须导入使用它们。


## <a name="related-links"></a>相关链接

- [XamlCompilation](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [XamlCompilationOptions](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
