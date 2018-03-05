---
title: "广泛的颜色"
description: "本文介绍如何广泛的颜色和如何可以在 Xamarin.iOS 或 Xamarin.Mac 应用中使用它。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: ab6124e2b11d26d4c10330e7b824e4761ebf4603
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="wide-color"></a>广泛的颜色

_本文介绍如何广泛的颜色和如何可以在 Xamarin.iOS 或 Xamarin.Mac 应用中使用它。_

iOS 10 和 macOS Sierra 增强了对扩展范围像素格式和整个系统包括框架，例如核心图形、 Core 映像、 金属和 AVFoundation 宽色域颜色空间的支持。 通过提供在整个图形堆栈整个此行为可以进一步简化对具有广泛的颜色显示设备的支持。

## <a name="about-wide-color"></a>有关广泛的颜色

如上面所述，iOS 10 和 macOS Sierra 增强了对扩展范围像素格式和整个系统，包括框架，例如核心图形、 Core 映像、 金属和 AVFoundation 宽色域颜色空间的支持。 通过提供在整个图形堆栈整个此行为可以进一步简化对具有广泛的颜色显示设备的支持。

在 90 年代 Apple 创建 ColorSync 来处理在 mac 上处理的颜色 它们还帮助找到国际色彩联合会 (ICC) 来创建和提升组用于处理在计算机硬件上的颜色的标准。 与 ICC Apple 的工作已包含在 ColorSync，它内置于 OS X （现在称为 macOS） 的核心。

Apple 已经进行了与硬件例如 Retina 显示、 新 P3 显示和显示 P3 颜色空间 （2015 年中 iMac 发布） 的显示技术的最前沿和 TrueTone 显示在 iPad 专业人员、 iPhone 7 和 iPhone 7 加上。

在 macOS Sierra 广泛的颜色和 iOS 10，与 Apple 正在改变 macOS 和 iOS 处理要充分利用这些新的显示技术颜色的方式。

## <a name="core-color-concepts"></a>核心颜色概念

下面的核心颜色概念需要操作才能进行深入了解一下在 macOS 和 iOS 的颜色，即可涵盖：

### <a name="color-space"></a>颜色空间

颜色空间是在其中颜色可以表示和比较的环境。 它可以是由其颜色组件的强度一至四个维空间。 

[ ![](wide-color-images/color00.png "颜色空间")](wide-color-images/color00.png)

### <a name="color-channels"></a>颜色通道

也可以作为颜色通道称为颜色组件。 某些熟悉的表示形式将 RGB 空间、 灰色空间、 CMYK 空间或设备独立空格。 

[ ![](wide-color-images/color02.png "颜色组件可以也被称为颜色通道")](wide-color-images/color02.png)

### <a name="color-primaries"></a>颜色主节点

颜色主节点提供用于比较和计算颜色坐标系统。 颜色主节点通常位于在最高强度版本可以在颜色通道内生成的给定颜色。

[ ![](wide-color-images/color01.png "颜色主节点提供用于比较和计算颜色坐标系统")](wide-color-images/color01.png)

对于上面表示 RGB 颜色空间，颜色主副本是 where`1.0`定位坐标 (如`[1.0, 0.0, 0.0]`红色)。

### <a name="color-gamut"></a>色域

色域是指所有可以为给定颜色空间内各个颜色通道的组合定义的颜色。

[ ![](wide-color-images/color03.png "颜色域示例")](wide-color-images/color03.png)

## <a name="what-is-wide-color"></a>什么是广泛的颜色

在涵盖的广泛的颜色主题之前, 讨论应具有的当前的行业标准颜色，标准的 RGB 颜色空间 (sRGB) 有关。 它是在今天计算最广泛使用的颜色空间，并且是适用于 iOS 和 macOS 的默认颜色空间。

SRGB 颜色空间具有以下属性：

- 它基于 ITU-R BT.709 标准。
- 它具有 2.2 近似伽玛。
- 它表示典型照明条件 (D65)。

由于 sRGB 是广泛使用行业中，开发人员可以进行某些假设，将显示在任何设备上忠实表示指定的颜色。 但是，这可能不始终是这种情况。 此外，还有几个颜色不适合 sRGB 颜色空间，因此，不能以它表示。

例如，许多织物旨在与使用许多墨迹和染料落在 sRGB 之外的线程。 此外在其日常人员遇到的许多产品来创建亮、 更逼真会超出 sRGB 颜色空间的颜色。 某些最具吸引力的不能在 sRGB 中表示的颜色示例是在日落、 秋季使、 尖端花等热带准备工作的性质的事项。

具有已捕获原始格式的数字图像的用户可能已在其包含所有这些颜色数据，即使它不能在 sRGB 颜色空间中正确表示，并因此无法正确显示在屏幕上的设备上的图像。

### <a name="the-display-p3-color-space"></a>显示 P3 颜色空间

在 2015 中，Apple 发布提供新的显示 P3 颜色空间来处理由 sRGB 颜色空间的问题的新产品 （iMac 和 iPad Pro 9.7"）。

[ ![](wide-color-images/color04.png "新的显示 P3 颜色空间")](wide-color-images/color04.png)

显示 P3 颜色空间具有以下属性：

- 为现代硬件平台支持广泛的颜色空间。
- 基于 SMPTE DCI P3 标准。 DCI P3 旨在用于数字投影仪但修改了 Apple 支持显示器。
- 它具有 2.2 近似伽玛。
- 它表示典型照明条件 (D65)。

Apple，根据用户正在移动到其移动平台的工作流。 解决由 sRGB 所需不仅包括广泛的颜色显示专业移动设备 (iPad 专业人员) 中的颜色演示文稿问题。 解决方法之一是升级工厂校准，以使每个设备已被校准在工厂，确保设备的颜色显示的准确性和一致性。

另一种解决方案是完整的系统范围的颜色管理 Apple 已内置到 iOS 10 和 macOS Sierra 包含。 

### <a name="the-extended-range-srgb-color-space"></a>扩展范围 sRGB 颜色空间

新的 iOS 10 系统级颜色管理必须是构建和为 sRGB 微调的现有 iOS 应用的所有帐户。 它旨在确保它不影响这些现有的应用或颜色表示形式或应用程序性能。

若要处理这种情况，Apple 已包含在 iOS 10 中的扩展范围 sRGB 颜色空间 （和 macOS Sierra 也）。

扩展范围 sRGB 颜色空间具有以下属性：

- 包含所有相同 sRGB 主节点。
- 它具有 2.2 近似伽玛。
- 它表示典型照明条件 (D65)。
- 它支持负值，并且值大于一 (1)。

通过并允许值小于零且大于 1，对于现有的应用中没有性能影响或扭曲，但是它 sRGB 存在颜色不只允许颜色空间扩展范围 sRGB 允许来表示任何颜色内可见的色彩空间频谱图。 同时仍保持与 sRGB 颜色空间相同的锚点完成所有这些操作。

### <a name="extended-range-srgb-in-action"></a>在操作中扩展的范围 sRGB

若要查看之外 0 和 1 的值中扩展范围 sRGB 颜色空间的工作原理，请执行下面的示例中显示 P3 颜色空间的饱和度最大红色的：

[ ![](wide-color-images/color05.png "零，另一个之外的值中扩展范围 sRGB 颜色空间的工作原理")](wide-color-images/color05.png)

在显示 P3 此颜色将表示为`[1.0, 0.0, 0.0]`和中扩展范围 sRGB 会`[1.358, -0.074, -0.012]`。 因为 sRGB 值是完全内显示 P3 包含和显示 P3 值布局"外部"的 sRGB 范围。

有关允许像素值从极端正转到极端负值的物理硬件，它可以显示任何可见色谱中可用的颜色和这些值可以表示中扩展范围 sRGB 色彩空间。

### <a name="device-pixel-formats"></a>设备像素格式 

颜色空间已很大程度上 sRGB 标准化上使用 8 位的像素格式，因为每个颜色通道的 8 位主要是不足以描述 sRGB 中的颜色。 这并不完美而足够好，而且它提供的内存和处理器使用情况，以显示图像之间的良好权衡。

显示 P3 可以代表颜色坐标 sRGB 颜色空间外部，因为它要求每个颜色通道以正确反映扩展范围 sRGB 颜色空间的颜色的 16 位。

## <a name="system-wide-wide-color-support"></a>系统范围广泛的颜色支持

若要完全支持广泛的颜色和内部 iOS 10 宽色域和 macOS Sierra，Apple 已扩展才能利用扩展范围 sRGB 颜色空间和显示 P3 以下框架：

- UIKit （对于仅限 iOS)
- SceneKit
- 核心图形
- ImageIO
- Core 映像
- 易于使用的功能
- SpriteKit
- 核心动画
- （对于仅 macOS) AppKit

此外，支持已得到增强的扩展范围 sRGB 颜色空间的 Retina 显示屏和显示 P3 显示。

广泛的颜色受支持，可在以下应用程序内容类型：

- 静态图像资源包含在应用捆绑包。
- 文档和基于网络的图像资源。
- 高级的媒体如 Live 照片或原始格式的图像。
- 3D 图形着色器纹理的图像。

## <a name="solving-the-color-problem"></a>解决颜色问题

应用程序中显示的内容可以来自各种颜色丰富源。 此外，此内容可以显示在大范围的设备，每个都有其自己的颜色显示功能的范围。

IOS 10 应用桥接这两个问题使用新内置的系统级颜色管理系统之间的差异。 此系统可确保，图像看上去相同的色彩空间无论映像已编码中的任何 iOS 设备上。

颜色管理从开始具有关联的颜色空间 （或颜色配置文件） 的每个图像。 中使用此信息_颜色匹配过程_源映像中的颜色符合输出设备中的颜色。 图像中的每个像素需要得到颜色匹配，因为它可以需要很长时间，并在设备的 CPU 压力。

由于的性质_颜色匹配过程_，此转换可以是可能显示"损"，如果输出设备具有较小色域比源映像。

幸运的是，请转到的计算_颜色匹配过程_可以轻松地会硬件加速 （或者 CPU 或 GPU 上），而 Apple 可确保，它的工作原理自动生成到如 Quartz 二维进制系统的支持ColorSync 和核心动画。 对于带有正确标签的内容，无需进行编码需要利用这些功能。

颜色管理已支持每个平台上，如下所示：

- **macOS** -macOS 进行了从一开始就托管的颜色。
- **iOS** -具有支持 iOS 自动配色管理以来 iOS 9.3 （和更高版本）。

### <a name="designing-for-wide-gamut"></a>为宽域设计

Apple 提供的有关设计以下建议，并使用宽颜色、 iOS 和 macOS 应用程序中的宽色域映像内容：

- 仅使用宽色域内容，请在此处好的效果在应用中，它们不应自动使用无处不在。
- 仅使用宽色域内容鲜艳颜色将增强用户体验，其中。
- 在没有必要更改为 P3 的所有内容，对于现有的应用。

Apple 的工具链可以对内容的支持宽色域映像逐步选择加入，因此在应用程序支持广泛的颜色不是一个全盘接受或者全盘情况。

### <a name="upgrading-existing-content-to-wide-color"></a>升级到广泛的颜色的现有内容

Apple 具有以下建议升级到广泛的颜色的现有映像内容：

- 不只是"将分配"P3 配置文件中编辑应用程序的映像的内容。 这样将只需重新映射到新的颜色空间出现意外结果，如拉伸颜色分别放入新的空间，因此更改映像内容的现有颜色。
- 将需要将"转换为"使用编辑应用程序的映像的显示 P3 配置文件的映像包内容。

### <a name="file-formats-and-color-profiles"></a>文件格式和颜色配置文件

Apple 具有的文件格式和在应用程序的广泛的颜色映像内容中使用的颜色配置文件的以下建议：

- 为 RGB 工作空间中使用"显示 P3"颜色配置文件。
- 使用每个颜色通道模式 16 位。
- 使用延迟 2015 iMac （或更高版本） 准确地预览图像内容。
- 将图像资产导出为具有嵌入的"显示 P3"ICC 配置文件的 16 位 PNG 文件。

> [!IMPORTANT]
> **注意：**使用**保存用于 Web**或**导出资产**功能位于编辑软件的最常用映像_不将_适合广泛的颜色图像自这些功能尚未更新以尚未支持所需的文件格式规范。

### <a name="supporting-wide-color-with-asset-catalogs"></a>支持广泛的颜色与资产目录

在 iOS 10 和 macOS Sierra，Apple 已扩展资产目录用于包括并进行分类，应用的捆绑中的静态图像内容来支持广泛的颜色。

使用资产目录到应用程序具有以下优势：

- 它们提供静态图像资产的最佳部署选项。
- 它们支持自动颜色校正。
- 它们支持自动的像素格式优化。
- 它们支持应用程序进行切片和应用细化这样可以确保仅为相关 get 内容传递给最终用户的设备。

Apple 具有广泛的颜色支持对资产目录进行以下增强功能：

- 它们支持 16 位 （每个颜色通道） 源内容。
- 它们显示色域通过支持编录的内容。 可以为 sRGB 或显示 P3 色域标记内容。

开发人员可以在其应用中支持广泛的颜色内容的三个选项：

1. **不执行任何操作**-由于广泛的颜色内容只应在应用程序需要显示鲜艳颜色 （其中，它们将对用户的体验进行增强） 的情况下，在此要求之外的内容应保留为-是。 它将继续在各种硬件情况下正确呈现。
2. **将现有内容升级到显示 P3** -这要求开发人员将其资产目录中的现有映像包内容替换为一个新的、 升级 P3 格式文件和标记这种情况下，它在资产编辑器中。 在生成期间，将从这些资产生成 sRGB 派生映像。
3. **提供优化资产内容**-在此情况下，开发人员将提供 8 位 sRGB 和每个图像 （正确资产编辑器中有标记） 资产目录中的 16 位显示 P3 愿景。

### <a name="asset-catalog-deployment"></a>资产目录部署

当开发人员提交与包括广泛的颜色映像内容的资产目录应用商店应用程序时，将执行以下操作：

- 应用程序部署到最终用户，应用程序进行切片将以确保只有适当的内容变体传递给用户的设备。
- 在设备上支持广泛的颜色，没有负载免费的包括广泛的颜色的内容，因为永远不会将其传送到设备。
- `NSImage` 在 macOS Sierra 上 （和更高版本） 将自动选择的硬件的显示的最佳内容表示形式。
- 或设备硬件显示特征更改时，将自动刷新显示的内容。

### <a name="asset-catalog-storage"></a>资产目录存储

资产目录存储具有以下属性和应用程序的影响：

- 在生成期间，Apple 尝试优化通过高质量映像转换的映像包内容的存储。
- 16 位用于每个颜色通道广泛的颜色内容。
- 使用内容相关的映像压缩，从而降低可交付结果的内容大小。 添加了新的"有损"压缩，以进一步优化内容的大小。

## <a name="colors-in-user-interfaces"></a>在用户界面中的颜色

在使用用户界面中的颜色，屏幕上的像素的大部分都是在纯色。 此外，大部分这些像素不来自图像资产，但直接通过应用程序 （或通过代表应用程序的操作系统） 绘制。

UI 级别上工作时，宽色域颜色可以存在多个难题：

- **通信颜色**-之间设计人员和开发人员通常会有颜色所指_假定_sRGB 颜色空间涉及。 以便一种颜色可能传递作为`rgb(128, 45, 56)`或`#FF0456`。 在宽色域设计中，这些表示形式未提供足够的信息来准确地表示的指定的颜色，还必须包括颜色空间的工作。 Apple 提供的建议使用`P3(128, 45, 56)`和`P3#FF0456`相反。 
- **选取颜色**-大多数常用映像编辑和设计使用其内置颜色选取器时，软件会降低从 sRGB 颜色空间相同的限制。 设计器应确保它们处于颜色选取器中的"显示 P3"颜色空间使用广泛的颜色设计时。
- **编码颜色**-这两个`NSColor`(macOS) 和`UIColor`(iOS & tvOS) 具有新的方便方法来直接生成 P3 颜色和同时已扩展为支持中扩展范围 sRGB 颜色空间以及的颜色。
- **存储颜色**-应用程序的文档中的存储宽色域颜色时应特别小心。 其中每个颜色通道正常 sRGB 颜色空间 8 位、 16 位每个颜色通道应用于宽的颜色。 开发人员也需要提防假定颜色空间 （以来的所有内容传统上仅 sRGB） 的实例。

## <a name="colors-on-the-web"></a>在 Web 上找到的颜色

使用在 web 页和支持广泛的颜色显示的设备上广泛的颜色时应小心。 如果已包括在该网站的映像包内容的所有已正确标记，将自动颜色匹配 iOS 和 macOS 并将其正确地显示它们。

媒体查询也是可用于帮助解决 P3 和 sRGB 支持设备之间的资产：

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple 还具有将允许 CSS，可在其他颜色空间除了假定的 sRGB 空间中指定的易于使用的功能建议书。

## <a name="rendering-off-screen-images-in-app"></a>呈现在应用程序中的屏幕图像

根据正在创建的应用程序的类型，则可能会使用户为包括映像内容他们已从 internet 收集或创建直接内 （类似于矢量，例如绘制应用程序） 的应用的映像内容。

在这两种情况下，应用程序可以呈现所需的图像屏幕中使用增强的功能添加到 iOS 和 macOS 广泛的颜色。

### <a name="drawing-wide-color-in-ios"></a>在 iOS 中绘制广泛的颜色

在讨论之前如何正确绘制在 iOS 10 中的广泛的颜色映像，看一看以下常见 iOS 绘制代码：

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    UIGraphics.BeginImageContext (size);

    ...
    
    UIGraphics.EndImageContext ();
    return image = UIGraphics.GetImageFromCurrentImageContext ();
}
```

有问题需要解决此问题的标准代码_之前_它可以用于绘制的广泛的颜色映像。 `UIGraphics.BeginImageContext (size)`方法用来启动 iOS 图像绘制具有以下限制：

- 它不能具有多个 8 位每个颜色通道来创建映像上下文。
- 它不能表示中扩展范围 sRGB 颜色空间的颜色。
- 它没有能够提供一个非 sRGB 颜色空间中创建上下文，由于在后台调用的低级别 C 例程的接口。

若要处理这些限制，并在 iOS 10 中绘制的广泛的颜色映像，请改为使用下面的代码：

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    var render = new UIGraphicsImageRenderer (size);

    var image = render.CreateImage ((UIGraphicsImageRendererContext context) => {
        var bounds = context.Format.Bounds;
        CGRect slice;
        CGRect remainder;
        bounds.Divide (bounds.Width / 2, CGRectEdge.MinXEdge, out slice, out remainder);

        var redP3 = UIColor.FromDisplayP3 (1.0F, 0.0F, 0.0F, 1.0F);
        redP3.SetColor ();
        context.FillRect (slice);

        var redRGB = UIColor.Red;
        redRGB.SetColor ();
        context.FillRect (remainder);
    });

    // Return results
    return image;
}
```

新`UIGraphicsImageRenderer`类创建新的映像上下文能够处理扩展范围 sRGB 颜色空间并在它具有以下功能：

- 它完全是由默认的颜色。
- 它支持默认情况下的扩展范围 sRGB 色彩空间。
- 它可以智能地决定它应呈现在 sRGB 或扩展范围 sRGB 的 iOS 设备上运行应用程序的功能的基础的色彩空间。
- 它完全并自动管理的映像上下文 (`CGContext`) 生存期使开发人员无需担心调用开始和结束上下文命令。
- 它是与兼容`UIGraphics.GetCurrentContext()`方法。

`CreateImage`方法`UIGraphicsImageRenderer`类是调用创建的广泛的颜色映像并传递具有要绘制到的映像上下文的完成处理程序。 所有绘图完成此完成处理程序内，如下所示：

- `UIColor.FromDisplayP3`方法宽色域显示 P3 颜色空间内创建新的完全饱和的红色，它用于绘制矩形的第一半。 
- 第二个一半的矩形中绘制正常 sRGB 完全饱和进行比较的红色。

### <a name="drawing-wide-color-in-macos"></a>在 macOS 中绘制广泛的颜色

`NSImage`类已得到扩展在 macOS Sierra 以支持广泛的颜色的图像的绘制。 例如:

```csharp
var size = CGSize(250,250);
var wideColorImage = new NSImage(size, false, (drawRect) =>{
    var rects = drawRect.Divide(drawRect.Size.Width/2,CGRect.MinXEdge);
    
    var color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    var path = new NSBezierPath(rects.Slice).Fill();
    
    color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    path = new NSBezierPath(rects.Remainder).Fill();
    
    // Return modified
    return true;
});
```

## <a name="rendering-on-screen-images-in-app"></a>屏幕上呈现应用程序中的映像

屏幕上呈现广泛的颜色的图像，过程的工作类似于绘制为 macOS 和上面介绍的 iOS 屏幕广泛的颜色图像。

### <a name="rendering-on-screen-in-ios"></a>在 iOS 中屏幕上呈现

当应用程序需要在屏幕上在 iOS 中广泛的颜色呈现图像时，重写`Draw`方法`UIView`照常问题。 例如:

```csharp
using System;
using UIKit;
using CoreGraphics;

namespace MonkeyTalk
{
    public class MonkeyView : UIView
    {
        public MonkeyView ()
        {
        }

        public override void Draw (CGRect rect)
        {
            base.Draw (rect);

            // Draw the image to screen
            ...
        }
    }
}
``` 

与 iOS 10 一样`UIGraphicsImageRenderer`上面所示，它以智能方式确定它应呈现 sRGB 或扩展范围 sRGB 的 iOS 设备的应用程序运行时上功能的基础的色彩空间中的类`Draw`调用方法。 此外，`UIImageView`进行了管理 iOS 9.3 以及以来的颜色。

如果应用程序需要知道正在如何完成呈现上`UIView`或`UIViewController`，它可以检查新`DisplayGamut`属性`UITraitCollection`类。 此值将为`UIDisplayGamut`以下枚举：

- P3
- Srgb
- 未指定

如果想要将应用程序使用的颜色空间绘制图像的控件，它可以使用新`ContentsFormat`属性`CALayer`指定所需的色彩空间。 此值可为`CAContentsFormat`以下枚举：

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>在 macOS 屏幕上呈现

当应用程序需要在屏幕上在 macOS 广泛的颜色呈现图像时，重写`DrawRect`方法`NSView`照常问题。 例如:

```csharp
using System;
using AppKit;
using CoreGraphics;
using CoreAnimation;

namespace MonkeyTalkMac
{
    public class MonkeyView : NSView
    {
        public MonkeyView ()
        {
        }

        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Draw graphics into the view
            ...
        }
    }
}
```

同样，它以智能方式确定它应呈现在 sRGB 或扩展范围 sRGB 颜色空间的应用程序运行时在 Mac 硬件功能的基础`DrawRect`调用方法。

如果想要将应用程序使用的颜色空间绘制图像的控件，它可以使用新`DepthLimit`属性`NSWindow`类指定所需的色彩空间。 此值可为`NSWindowDepth`以下枚举：

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb

## <a name="summary"></a>摘要

本文已覆盖广泛的颜色以及它可能是实现并且在 Xamarin.iOS 或 Xamarin.Mac 应用中使用的方式。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
