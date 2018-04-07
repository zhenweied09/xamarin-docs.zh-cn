---
title: 路径和文本
description: 浏览路径和文本的交集
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 08/01/2017
ms.openlocfilehash: 9b3f906a23ed0d51237a244f3944104acc76e259
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="paths-and-text"></a>路径和文本

_浏览路径和文本的交集_

在现代图形系统中，文本字体是字符大纲中，通常由二次贝塞尔曲线的集合。 因此，许多现代图形系统包括一个工具用于将文本字符转换为图形路径。 

你已经了解，你可以绘制轮廓线的文本字符，以及填充它们。 这使你可以显示这些字符轮廓随特定描边宽度和甚至路径效果中所述[**路径效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)文章。 但也可以将一个字符字符串转换`SKPath`对象。 这意味着文本概述了可用于剪辑中所述的技术与[**剪辑区域路径与**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)文章。 

除了使用路径效果用于绘制笔画字符大纲，你还可以创建基于路径的效果派生自字符字符串的路径和你甚至可以合并这两个效果：

![](text-paths-images/pathsandtextsample.png "文本路径效果")

在[上一篇文章](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)你已了解如何[ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/)方法`SKPaint`可以获取一个轮廓绘制的路径。 你可以与派生自字符轮廓的路径中使用此方法。

最后，本文演示的路径和文本的另一个交集： [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/)方法`SKCanvas`使你可以显示的文本字符串，以便文本的基线遵循曲线的路径。

## <a name="text-to-path-conversion"></a>路径转换为文本

[ `GetTextPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetTextPath/p/System.String/System.Single/System.Single/)方法`SKPaint`字符将字符串转换为`SKPath`对象：

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

`x`和`y`参数指示的基线的左侧的文本内容的起始点。 它们播放中的同一 role 此处作为`DrawText`方法`SKCanvas`。 在该路径中，左侧的文本内容的基线将具有坐标 （x，y）。 

`GetTextPath`方法是太过严厉，如果你只是想要填充或描边，则产生的路径。 法线`DrawText`方法允许您执行该操作。 `GetTextPath`方法是更适用于涉及路径其他任务。

这些任务之一剪辑。 **剪辑文本**页创建剪切路径基于字符轮廓线 word"代码。 此路径就会拉伸至要剪辑包含的图像的位图的页的大小**剪辑文本**源代码：

[![](text-paths-images/clippingtext-small.png "三重的剪辑文本页的屏幕截图")](text-paths-images/clippingtext-large.png#lightbox "三倍的剪切的文本页的屏幕截图")

[ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs)类构造函数加载用于存储作为嵌入资源中的位图**媒体**的解决方案的文件夹：

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

`PaintSurface`处理程序首先创建`SKPaint`适用于文本的对象。 `Typeface`属性设置以及`TextSize`，为此特定的应用程序虽然`TextSize`属性是纯粹 arbirtrary。 另请注意没有任何`Style`设置。

`TextSize`和`Style`属性设置，则不需要由于此`SKPaint`对象仅用于`GetTextPath`调用使用文本字符串"CODE"。 处理程序然后测量所产生的`SKPath`对象并应用要使其居中和缩放到页的大小的三个转换。 然后可将路径设置为剪切路径：

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, 
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

剪切路径设置后，可以显示位图，并且它将被剪裁到字符轮廓。 请注意，使用[ `AspectFill` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRect.AspectFill/p/SkiaSharp.SKSize/)方法`SKRect`，可计算用于填充页，同时仍保持纵横比的矩形。

**文本路径效果**页将单个 & 号字符转换为要创建一个一维路径效果的路径。 具有此路径效果的绘制对象然后用于描边，则该相同的字符的更大版本的轮廓：

[![](text-paths-images/textpatheffect-small.png "三重的文本路径效果页面屏幕截图")](text-paths-images/textpatheffect-large.png#lightbox "三倍的文本路径效果页面屏幕截图")

大部分中的工作[ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs)类出现在字段和构造函数中。 这两个`SKPaint`对象定义为字段用于两个不同的用途： 第一个 (名为`textPathPaint`) 用于将转换使用 & 符`TextSize`的 50%到 1 D 路径效果的路径。 第二个 (`textPaint`) 用来显示该路径产生效果 & 符的更大版本。 为此，`Style`的此第二个绘制对象设置为`Stroke`，但`StrokeWidth`因为该属性不需要如此，使用 1 D 路径效果时，未设置属性：

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint 
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds = new SKRect();
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character, 
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

构造函数首先使用`textPathPaint`对象来测量使用 & 符`TextSize`为 50。 该矩形的中心坐标的假负然后传递给`GetTextPath`方法将文本转换为路径。 产生的路径具有 （0，0） 中的字符，非常适合于 1 D 路径效果的中心点。

你可能会认为`SKPathEffect`在构造函数的末尾创建对象无法设置为`PathEffect`属性`textPaint`而不是保存为一个字段。 而此打开出状态的不是很好地工作，因为它扭曲的结果`MeasureText`调用`PaintSurface`处理程序：

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

`MeasureText`调用用于中心页面上的字符。 若要避免出现问题，`PathEffect`测量文本之后，但它显示之前，将属性设置为绘制对象。

## <a name="outlines-of-character-outlines"></a>字符轮廓的轮廓

通常[ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/)方法`SKPaint`通过应用绘制属性，将一条路径转换为另一个最值得注意的是描边宽度和路径效果。 当使用不路径会造成影响，`GetFillPath`能有效地创建概述了另一条路径的路径。 中已说明了这**到大纲点击路径**页面[**路径效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)文章。

你还可以调用`GetFillPath`上从返回的路径`GetTextPath`但首先你可能不完全确定的将看到如下。

**字符大纲轮廓**页说明该方法。 相关的所有代码都都在`PaintSurface`处理程序[ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs)类。

构造函数首先创建`SKPaint`对象名为`textPaint`与`TextSize`属性基于页的大小。 这将转换为路径使用`GetTextPath`方法。 坐标自变量`GetTextPath`有效地中心在屏幕上的路径：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds = new SKRect();
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

`PaintSurface`处理程序然后创建名为的新路径`outlinePath`。 该名称将成为对的调用中的目标路径`GetFillPath`。 `StrokeWidth` 25 原因属性`outlinePath`来描述描边的文本字符的 25 个像素宽路径的轮廓。 此路径然后显示为红色描边宽度为 5:

[![](text-paths-images/characteroutlineoutlines-small.png "三重的字符大纲概述了页面屏幕截图")](text-paths-images/characteroutlineoutlines-large.png#lightbox "字符大纲概述了页面的三个屏幕截图")

仔细查看，你将看到重叠情况下路径大纲使尖锐的角。 这些是正常的项目，此过程。

## <a name="text-along-a-path"></a>沿着路径的文本

文本通常显示水平基线上。 可以旋转文本以运行垂直或对角线，但在基线依然是一条直线。

有的时间，但是，如果想要运行一条曲线沿文本。 这种情况下的需要[ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/)方法`SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

第一个参数中指定的文本进行以运行沿指定为第二个参数的路径。 你可以开始与具有路径的开头的偏移量处的文本`hOffset`自变量。 通常路径窗体文本的基线： 文本上行字母上的路径，一侧，并且文本下行字母位于其他。 但你可能会抵消与路径中的文本基线`vOffset`自变量。

此方法具有任何工具来提供有关设置的指导`TextSize`属性`SKPaint`进行大小调整，完全以运行从路径的开头到末尾的文本。 有时，你可以了解自己该文本大小。 其他情况下你将需要使用路径测量函数会在将来的文章中所述。

**循环文本**程序使文本换行圆环。 很容易地确定一个圆的周长，因此很容易大小来完全适合的文本。 `PaintSurface`处理程序[ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs)类计算的基于页的大小圆的半径。 该圆将成为`circularPath`:

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

`TextSize`属性`textPaint`然后调整，使文本宽度与匹配的圆的周长：

[![](text-paths-images/circulartext-small.png "三重的循环的文本页的屏幕截图")](text-paths-images/circulartext-large.png#lightbox "三倍的循环的文本页的屏幕截图")

已选择的文本本身也是某种程度上循环： 单词"circle"是两个句子的使用者和介词短语的对象。 

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
