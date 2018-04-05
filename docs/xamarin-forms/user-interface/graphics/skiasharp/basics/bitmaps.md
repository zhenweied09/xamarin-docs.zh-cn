---
title: 位图基础知识
description: 从各种源加载位图并显示它们。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 04/03/2017
ms.openlocfilehash: 688c6218f9ac66e3dfd6cd157e43f9b639e124c6
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="bitmap-basics"></a>位图基础知识

_从各种源加载位图并显示它们。_

SkiaSharp 中的支持是位图的非常大。 本文介绍如何仅的基础知识&mdash;如何加载位图以及如何显示它们：

![](bitmaps-images/bitmapssample.png "两个位图的显示")

SkiaSharp 位图的类型的对象[ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/)。 有多种方法来创建位图，但是这篇文章限制到本身[ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/SkiaSharp.SKStream/)方法，后者将加载从位图[ `SKStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStream/)引用位图文件的对象。 可以方便地使用[ `SKManagedStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKManagedStream/)派生自的类`SKStream`因为它具有一个构造函数接受.NET 的[ `Stream` ](https://developer.xamarin.com/api/type/System.IO.Stream/)对象。

**基本位图**页面**SkiaSharpFormsDemos**程序演示如何从三个不同的源加载位图：

- 超额 Internet
- 从可执行文件中嵌入的资源
- 从用户的照片库

三个`SKBitmap`对象这些三个源定义为中的字段[ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs)类：

```csharp
public class BasicBitmapsPage : ContentPage
{
    SKCanvasView canvasView;
    SKBitmap webBitmap;
    SKBitmap resourceBitmap;
    SKBitmap libraryBitmap;

    public BasicBitmapsPage()
    {
        Title = "Basic Bitmaps";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ...
    }
    ...
}
```

## <a name="loading-a-bitmap-from-the-web"></a>从 Web 加载位图

若要加载基于 URL 的位图，可以使用[ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/)类，如下面的代码中执行中所示`BasicBitmapsPage`构造函数。 此处的 URL 指向与某些示例位图 Xamarin 网站上的区域。 在网站上的包允许追加调整特定宽度位图的规范：

```csharp
Uri uri = new Uri("http://developer.xamarin.com/demo/IMG_3256.JPG?width=480");
WebRequest request = WebRequest.Create(uri);
request.BeginGetResponse((IAsyncResult arg) =>
{
    try
    {
        using (Stream stream = request.EndGetResponse(arg).GetResponseStream())
        using (MemoryStream memStream = new MemoryStream())
        {
            stream.CopyTo(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            using (SKManagedStream skStream = new SKManagedStream(memStream))
            {
                webBitmap = SKBitmap.Decode(skStream);
            }
        }
    }
    catch
    {
    }

    Device.BeginInvokeOnMainThread(() => canvasView.InvalidateSurface());

}, null);
```

回调方法时已成功下载位图，传递给`BeginGetResponse`方法运行。 `EndGetResponse`调用需要处于`try`阻止以防出现错误。 `Stream`获取从对象`GetResponseStream`不能满足在某些平台上，因此位图内容复制到`MemoryStream`对象。 此时，`SKManagedStream`可以创建对象。 现在此引用的位图文件，这可能是 JPEG 或 PNG 文件。 `SKBitmap.Decode`方法对位图文件进行解码，并将结果存储在内部 SkiaSharp 格式。

回调方法传递给`BeginGetResponse`运行后构造函数已完成执行，这意味着`SKCanvasView`需要失效，以允许`PaintSurface`处理程序以更新显示。 但是，`BeginGetResponse`在执行，辅助线程中运行的回调，因此很有必要将使用`Device.BeginInvokeOnMainThread`运行`InvalidateSurface`用户界面线程中的方法。

## <a name="loading-a-bitmap-resource"></a>加载位图资源

在代码中，方面加载位图的最简单方法在你的应用程序中直接包括位图资源。 **SkiaSharpFormsDemos**程序包括一个名为文件夹**媒体**包含一个名为的位图文件**monkey.png**。 在**属性**对话框对于此文件，则必须为此类文件**生成操作**的**嵌入的资源**！

每个嵌入的资源具有*资源 ID*项目名称、 文件夹和文件名，所有连接由句点组成： **SkiaSharpFormsDemos.Media.monkey.png**。 你可以通过指定该资源获取与此资源的访问权限的自变量形式 ID [ `GetManifestResourceStream` ](https://developer.xamarin.com/api/member/System.Reflection.Assembly.GetManifestResourceStream/p/System.String/)方法[ `Assembly` ](https://developer.xamarin.com/api/type/System.Reflection.Assembly/)类：

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
using (SKManagedStream skStream = new SKManagedStream(stream))
{
    resourceBitmap = SKBitmap.Decode(skStream);
}
```

这`Stream`对象可以直接向转换`SKManagedStream`对象。

## <a name="loading-a-bitmap-from-the-photo-library"></a>从照片库加载位图

还有可能要从设备的图片库加载一张照片的用户。 Xamarin.Forms 本身未提供此工具。 该作业需要依赖服务，如文所述的那个[从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)。

**IPicturePicker.cs**文件和三个**PicturePickerImplementation.cs**已从这篇文章的文件复制到的各种项目**SkiaSharpFormsDemos**解决方案，并取得新命名空间名称。 此外，Android **MainActivity.cs**文章中所述修改文件和 iOS 项目已被授予权限以访问底部的两个行的照片库**info.plist**文件。

`BasicBitmapsPage`构造函数将添加`TapGestureRecognizer`到`SKCanvasView`的点击通知。 在收到点击，`Tapped`处理程序获取访问图片选取器依赖关系服务并调用`GetImageStreamAsync`。 如果`Stream`返回对象，然后内容复制到`MemoryStream`，某些平台所需的方式。 代码的其余部分是类似于其他两个方法：

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPicturePicker picturePicker = DependencyService.Get<IPicturePicker>();

    using (Stream stream = await picturePicker.GetImageStreamAsync())
    {
        if (stream != null)
        {
            using (MemoryStream memStream = new MemoryStream())
            {
                stream.CopyTo(memStream);
                memStream.Seek(0, SeekOrigin.Begin);

                using (SKManagedStream skStream = new SKManagedStream(memStream))
                {
                    libraryBitmap = SKBitmap.Decode(skStream);
                }
            }
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

请注意，`Tapped`处理程序调用`InvalidateSurface`方法`SKCanvasView`对象。 这将生成新调用`PaintSurface`处理程序。

## <a name="displaying-the-bitmaps"></a>显示位图

`PaintSurface`处理程序需要显示三个位图。 处理程序假定电话处于纵向模式，并将画布垂直划分为三个相等部分。

使用最简单的形式显示第一个位图[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/)方法。 只需指定为 X 和 Y 坐标，此处是与位图左上角来定位：

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

尽管`SKPaint`定义参数，它同时具有默认值为`null`，你可以忽略它。 位图的像素只需将传输到的一对一映射显示面的像素。

程序可以获取位图的像素大小[ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/)和[ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/)属性。 这些属性允许程序计算元素的坐标位图画布的左上第三个中心中：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    if (webBitmap != null)
    {
        float x = (info.Width - webBitmap.Width) / 2;
        float y = (info.Height / 3 - webBitmap.Height) / 2;
        canvas.DrawBitmap(webBitmap, x, y);
    }
    ...
}
```

其他两个位图显示使用的版本[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKPaint/)与`SKRect`参数：

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

第三个版本[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/)具有两个`SKRect`用于指定位图转换显示，但该版本的矩形子集的自变量未使用此文章中。

下面是要显示从嵌入的资源位图加载位图的代码：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (resourceBitmap != null)
    {
        canvas.DrawBitmap(resourceBitmap,
            new SKRect(0, info.Height / 3, info.Width, 2 * info.Height / 3));
    }
    ...
}
```

位图拉伸到矩形，这正是 monkey 水平拉伸这些屏幕快照中的尺寸：

[![](bitmaps-images/basicbitmaps-small.png "三重基本位图页的屏幕截图")](bitmaps-images/basicbitmaps-large.png#lightbox "三个基本位图页的屏幕快照")

第三个图像&mdash;其中是否您运行程序并从你自己的图片库加载照片上只能查看&mdash;还显示在一个矩形，但矩形的位置和大小调整以保持该位图纵横比。 此计算是稍有更为复杂，因为它需要计算的缩放系数基于位图和目标矩形的大小和使在该区域的矩形内居中：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (libraryBitmap != null)
    {
        float scale = Math.Min((float)info.Width / libraryBitmap.Width,
                               info.Height / 3f / libraryBitmap.Height);

        float left = (info.Width - scale * libraryBitmap.Width) / 2;
        float top = (info.Height / 3 - scale * libraryBitmap.Height) / 2;
        float right = left + scale * libraryBitmap.Width;
        float bottom = top + scale * libraryBitmap.Height;
        SKRect rect = new SKRect(left, top, right, bottom);
        rect.Offset(0, 2 * info.Height / 3);

        canvas.DrawBitmap(libraryBitmap, rect);
    }
    else
    {
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Blue;
            paint.TextAlign = SKTextAlign.Center;
            paint.TextSize = 48;

            canvas.DrawText("Tap to load bitmap",
                info.Width / 2, 5 * info.Height / 6, paint);
        }
    }
}
```

如果尚未从图片库中，加载任何位图则`else`块显示了一些文本以提示用户点击屏幕。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
