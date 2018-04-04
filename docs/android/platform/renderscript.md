---
title: Renderscript 简介
description: 本指南介绍 Renderscript，并说明如何使用该目标 API 级别 17 或更高版本的内部函数 Renderscript 中的 API 的 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f9e21a51c409c5444f137a63eb2c6fadfef03cbe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="an-introduction-to-renderscript"></a>Renderscript 简介

_本指南介绍 Renderscript，并说明如何使用该目标 API 级别 17 或更高版本的内部函数 Renderscript 中的 API 的 Xamarin.Android 应用程序。_

## <a name="overview"></a>概述

Renderscript 是一个编程框架，创建 google 以便改进需要大量计算资源的 Android 应用程序的性能。 较低级别、 高性能基于 API [C99](http://en.wikipedia.org/wiki/C99)。 因为它的低级别将在 Cpu、 Gpu 或 Dsp 运行的 API，Renderscript 是非常适用于 Android 的应用程序可能需要执行以下任一项：

* 图形
* 图像处理
* 加密
* 信号处理
* 数学例程

将使用 Renderscript`clang`和编译到的捆绑到 APK LLVM 字节代码的脚本。 当首次运行该应用程序时，LLVM 字节代码将编译到设备上的处理器的机器代码。 此体系结构允许 Android 应用程序本身无需编写它为每个处理器在设备上本身利用而无需开发人员的机器代码的好处。

有两个组件到 Renderscript 例程：

1. **Renderscript 运行时**&ndash;这是将负责执行 Renderscript 的本机 Api。 这包括针对应用程序编写的任何 Renderscripts。

2. **从 Android 框架托管包装器**&ndash;托管允许 Android 应用程序控制并 Renderscript 运行时和脚本与之交互的类。 用于控制 Renderscript 运行时的 framework 提供的类，除了 Android 工具链将检查 Renderscript 源代码和生成 Android 应用程序使用的托管的包装类。

下图说明了这些组件之间的关系：

![图示 Android 框架与 Renderscript 运行时交互的方式](renderscript-images/renderscript-01.png)

有三个 Android 应用程序中使用 Renderscripts 的重要概念：

1. **上下文**&ndash;托管由 Android SDK，可将资源分配给 Renderscript 并允许 Android 应用程序传递和接收来自 Renderscript 数据提供的 API。

2. **A_计算内核_** &ndash;也称为_根内核_或_内核_，则此执行工作的例程。 内核是非常类似于 C 函数;它是将在分配的内存中的所有数据上运行的可并行化例程。

3. **分配的内存**&ndash;数据将传递到和从通过内核_[分配](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_。 内核可能具有一个输入和/或一个输出分配。

[Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)命名空间包含用于与 Renderscript 运行时交互的类。 具体而言， [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)类将管理的生命周期和 Renderscript 引擎的资源。 Android 应用程序必须初始化一个或多个[ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)对象。 分配是一种托管的 API，负责分配和访问 Android 应用程序和 Renderscript 运行时之间共享的内存。 通常情况下，为输入，创建一个资源分配和 （可选） 另一个分配创建来保存内核的输出。 Renderscript 运行时引擎和关联的托管的包装类将管理对由分配的内存的访问，无需 Android 应用程序开发人员用来执行任何额外的操作。

分配将包含一个或多个[Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/)。
元素是描述数据中每个分配的专用的类型。
分配必须与匹配的输出的元素类型的输入元素的类型。 执行时，将循环访问每个元素中输入分配并行，并将结果写入到输出 Renderscript 分配。 有两种类型的元素：

- **简单类型**&ndash;这是从概念上讲是相同的 C 数据类型，`float`或`char`。

- **复杂类型**&ndash;此类型是类似于 C `struct`。

Renderscript 引擎将执行运行时检查以确保每次分配中的元素与所需内核兼容。 如果在分配的元素的数据类型与内核所需的数据类型不匹配，则将引发异常。

将的后代的类型由包装所有 Renderscript 内核[ `Android.Renderscripts.Script` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/)类。 `Script`类用于为 Renderscript 设置的参数、 设置相应的`Allocations`，并运行 Renderscript。 有两个`Script`子类 Android SDK 中：


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; 一些更常见的 Renderscript 任务在 Android SDK 中捆绑和都可以访问的一个子类[ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/)类。 为开发人员进行任何额外的步骤，要在其应用程序中使用这些脚本，因为它们已提供，没有必要。

- **`ScriptC_XXXXX`** &ndash; 也称为_用户脚本_，这些是由开发人员编写并打包在 APK 中的脚本。 在编译时，Android 工具链将生成会允许脚本以使用 Android 应用中的托管的包装类。
  这些生成的类的名称是带有前缀的 Renderscript 文件的名称`ScriptC_`。 编写和合并用户脚本不正式支持通过 Xamarin.Android 和超出了本指南的范围。

这两种类型，仅`StringIntrinsic`Xamarin.Android 支持。 本指南将讨论如何在 Xamarin.Android 应用程序中使用内部函数的脚本。

## <a name="requirements"></a>要求

本指南适用于 Xamarin.Android 应用该目标 API 级别 17 或更高版本。 使用_用户脚本_本指南中未涉及。

[Xamarin.Android V8 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/)backports 内部 Renderscript API 的应用程序面向的 Android sdk 的较旧版本。 将此包添加到 Xamarin.Android 项目应允许应用面向旧版本的 Android SDK，可利用的内部函数的脚本。

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>在 Xamarin.Android 中使用内部函数 Renderscripts

内部函数的脚本是代码的执行密集型计算任务使用最少的额外的好办法。 它们已被手动调整以提供对大型剖面的设备的最佳性能。
它是很常见的一个内部函数的脚本来运行 10 倍快于托管代码和后自定义 C 实现比的 2-3 x 时间。 许多典型处理方案所涵盖的内部函数的脚本。 此内部函数的脚本列出 Xamarin.Android 中的当前脚本：

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash;将转换 RGB 到 RGBA 使用 3D 查找表。 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh 性能 Renderscript Api 到[BLAS](http://www.netlib.org/blas/)。 BLAS （基本线性代数子程序） 是用于执行基本向量和矩阵操作提供标准构建基块的例程。 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash;混合在一起的两个分配。

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash;适用于分配的高斯模糊。

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash;适用于分配 （即更改色彩调整色调） 颜色矩阵。

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash;适用于分配的 3 x 3 颜色矩阵。

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash;适用于分配 5 x 5 颜色矩阵。

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash;内部直方图筛选器。

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash;将每个通道查找表应用到的缓冲区。

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash;执行二维分配的大小调整的脚本。

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash;将 YUV 缓冲区转换为 RGB。

请有关每个内部函数的脚本的详细信息，参阅 API 文档。

接下来将介绍在 Android 应用程序中使用 Renderscript 的基本步骤。

**创建 Renderscript 上下文** &ndash; [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)类是围绕 Renderscript 上下文的托管包装器将控件初始化，资源管理和清理。 使用创建 Renderscript 对象`RenderScript.Create`工厂方法，后者采用 Android 上下文 （如活动） 作为参数。 以下代码行演示如何初始化 Renderscript 上下文：

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**创建分配**&ndash;具体取决于内部函数的脚本，它可能需要创建一个或两个`Allocation`s。 [ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)类具有多种工厂方法来帮助进行实例化的内部函数分配。 例如，下面的代码段演示如何创建位图的分配。

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

通常情况下，它将创建所需`Allocation`以保存脚本的输出数据。 此以下代码段演示如何使用`Allocation.CreateTyped`用于实例化第二个帮助`Allocation`，相同的类型与原始：

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**实例化脚本包装**&ndash;的每个内部函数的脚本包装类应具有帮助器方法 (通常称为`Create`) 用于实例化该脚本的包装对象的。 下面的代码段演示了如何实例化`ScriptIntrinsicBlur`模糊对象。 `Element.U8_4`帮助器方法将创建描述是 4 的 8 位无符号整数值，适用于保存的数据字段的数据类型的元素`Bitmap`对象：

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**分配 Allocation(s)、 设置参数和运行脚本** &ndash; `Script`类提供`ForEach`实际运行 Renderscript 方法。 此方法将循环访问每个`Element`中`Allocation`保存输入的数据。 在某些情况下，可能有必要提供`Allocation`，用于保存输出。
`ForEach` 将覆盖输出的内容分配。 若要执行与前面步骤中的代码段，此示例演示如何分配输入的分配、 设置的参数，并最后运行脚本 (将结果复制到输出分配):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

你可能想要签出[使与 Renderscript 图像模糊](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/)原因之一，它是如何在 Xamarin.Android 中使用的内部函数的脚本的完整示例。

## <a name="summary"></a>总结

本指南引入 Renderscript 以及如何在 Xamarin.Android 应用程序中使用它。 它简要讨论 Renderscript 是什么及其工作原理在 Android 应用程序。 它介绍一些 Renderscript 和之间的差异中的关键组件_用户脚本_和_内部脚本_。 最后，本指南讨论中使用的内部函数的脚本在 Xamarin.Android 应用程序中的步骤。



## <a name="related-links"></a>相关链接

- [Android.Renderscripts namespace](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [模糊 Renderscript 的映像](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [教程： 开始使用 Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
