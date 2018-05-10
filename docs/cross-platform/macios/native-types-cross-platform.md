---
title: 使用跨平台应用中的本机类型
description: 本文介绍如何使用新的 iOS 统一 API 本机类型 （nint、 nuint、 nfloat） 在跨平台应用程序代码与非 iOS 设备，例如 Android 或 Windows Phone 操作系统的共享位置。
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: eb6979691eeef6dd7436d74fdfd501c747d9b3c6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>使用跨平台应用中的本机类型

_本文介绍如何使用新的 iOS 统一 API 本机类型 （nint、 nuint、 nfloat） 在跨平台应用程序代码与非 iOS 设备，例如 Android 或 Windows Phone 操作系统的共享位置。_


本机类型中使用 iOS 和 Mac Api 64 类型。 如果你正在编写也在 Android 或 Windows 运行的共享的代码，你将需要管理常规可以共享的.NET 类型的统一类型的转换。

本文档讨论了不同的方式与在共享/公共代码中统一 API 进行互操作。

## <a name="when-to-use-the-native-types"></a>何时使用本机类型

Xamarin.iOS 和 Xamarin.Mac 统一的 Api 仍包含`int`，`uint`和`float`数据类型，以及`RectangleF`，`SizeF`和`PointF`类型。 这些现有的数据类型应继续在任何共享、 跨平台的代码中使用。 在 Mac 或 iOS API 到其中支持的体系结构感知类型所需的调用时，应仅使用新的本机数据类型。

根据共享的代码的性质，有时可能跨平台代码可能需要使用来处理`nint`，`nuint`和`nfloat`数据类型。 例如： 处理以前使用过的矩形数据转换库`System.Drawing.RectangleF`若要共享的应用程序的 Xamarin.iOS 和 Xamarin.Android 版本之间的功能将需要更新，以在 iOS 上处理本机类型。

如何处理这些更改依赖于的大小和复杂性的应用程序并且已使用了共享的代码的形式，我们将会看到以下部分中。

## <a name="code-sharing-considerations"></a>代码共享的注意事项

所述[共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)文档中，有两个主要跨平台项目间共享代码的方法： 共享项目和可移植类库。 这两种类型已被使用，将限制我们已在处理跨平台代码中的本机数据类型时的选项。

### <a name="portable-class-library-projects"></a>可移植类库项目

可移植类库 (PCL) 允许你为目标设定你想要支持，并使用接口来提供特定于平台的功能的平台。

自 PCL 项目类型编译直至`.DLL`和它具有统一的 API 没有意义，将强制你继续使用现有的数据类型 (`int`， `uint`， `float`) 在 PCL 中源代码和类型强制转换到 Pcl 调用类和前端应用程序中的方法。 例如：

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>共享的项目

共享资产项目类型允许你将在单独的项目，然后获取包括并编译你的源代码组织成各个特定于平台的前端应用程序，并使用`#if`编译器指令需要管理特定于平台的要求。

大小和复杂性的前面结束正在消耗共享的代码，以及大小和共享代码的复杂性，需要考虑到在时选择的方法的支持本机数据类型使用的跨平台移动应用程序共享的资产项目类型。

基于这些因素，以下类型的解决方案可能会实现使用`if __UNIFIED__ ... #endif`编译器指令以处理统一 API 特定更改的代码。

#### <a name="using-duplicate-methods"></a>使用重复的方法

需要执行的操作，在上面给出的矩形数据转换的库的示例。 如果库只包含一个或两个非常简单的方法，你可以选择为 Xamarin.iOS 和 Xamarin.Android 创建重复的这些方法的版本。 例如：

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

在上面的代码中，由于`CalculateArea`例程过程非常简单，我们有使用条件编译，并创建单独的、 统一的 API 版本的方法。 另一方面，如果库包含许多例程或多个复杂的例程，此解决方案不会可行，因为会出现保持所有方法的修改或 bug 修复同步问题。

#### <a name="using-method-overloads"></a>使用方法重载

在这种情况下，解决方案可能需要创建使用 32 位数据类型，以便它们现在采用的方法的重载版本`CGRect`为参数和/或返回值，该将值转换为`RectangleF`(知道该转换从`nfloat`到`float`有损转换)，并调用执行实际工作的例程的原始版本。 例如：

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

            // Call original routine to calculate area
            return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

同样，这是一个不错的解决方案，只要精度损失不会影响应用程序的特定需求的结果。

#### <a name="using-alias-directives"></a>Using 别名指令

对于其中的精度损失是问题的区域，另一个可能的解决方法是使用`using`指令以创建本机和 CoreGraphics 数据类型的别名的包括到共享的源代码文件的顶部，下面的代码并将任何转换需要`int`，`uint`或`float`值复制到`nint`，`nuint`或`nfloat`:

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

以便我们的示例代码随后将成为：

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

请注意，此处我们已更改`CalculateArea`方法返回`nfloat`而不是标准`float`。 执行此操作，以便我们不会遇到尝试编译错误_隐式_转换`nfloat`我们计算的结果的 (因为所相乘两个值都`nfloat`) 到`float`返回值。

如果代码在编译和运行在非统一 API 设备上，`using nfloat = global::System.Single;`映射`nfloat`到`Single`其将隐式转换为`float`允许使用的前端应用程序，以调用`CalculateArea`不带方法修改。


#### <a name="using-type-conversions-in-the-front-end-app"></a>在前端应用程序中使用类型转换

中，前端应用程序进行少量的对你的共享的代码库的调用，另一个解决方案可能是将保留不变的库和执行操作时调用的现有例程在 Xamarin.iOS 或 Xamarin.Mac 应用程序中键入强制转换。 例如：

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

如果使用方应用程序进行数百个调用共享的代码库，这同样，可能不是一个不错的解决方案。

根据我们的应用程序的体系结构，我们可能会得到使用一个或多个以上的解决方案以支持本机数据类型 （如果必需） 在跨平台代码。


## <a name="xamarinforms-applications"></a>Xamarin.Forms 应用程序

使用 Xamarin.Forms 还将与统一 API 应用程序共享的跨平台用户界面需要以下：

- 整个解决方案必须使用版本 1.3.1 （或更高版本） 的 Xamarin.Forms NuGet 包。
- 对于任何 Xamarin.iOS 自定义呈现，使用基于如何 UI 代码已共享 （共享项目或 PCL） 的解决方案上面介绍的相同类型。

如下所示标准的跨平台应用程序中，现有 32 位数据类型应在任何共享的跨平台代码大多数的所有问题。 在 Mac 或 iOS API 到其中支持的体系结构感知类型所需的调用时，应仅使用新的本机数据类型。

有关更多详细信息，请参阅我们[更新现有 Xamarin.Forms 应用](http://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/)文档。

## <a name="summary"></a>总结

这篇文章中我们将有，请参阅，当我们应该使用统一 API 应用程序和其含义跨平台中的本机数据类型。 我们已提供几种解决方案，可在新的本机数据类型必须使用跨平台库中的情况。 并且，我们已了解 Xamarin.Forms 跨平台应用程序中受支持的统一的 Api 的快速指南。



## <a name="related-links"></a>相关链接

- [Unified API](~/cross-platform/macios/unified/index.md)
- [本机类型](~/cross-platform/macios/nativetypes.md)
- [共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)
- [共享的代码示例](https://developer.xamarin.com/samples/mobile/SharingCode/)
