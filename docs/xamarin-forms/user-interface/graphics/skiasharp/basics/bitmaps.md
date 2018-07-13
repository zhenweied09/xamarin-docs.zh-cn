---
title: 位图中 SkiaSharp 的基础知识
description: 这篇文章介绍了如何从各种源加载位图 SkiaSharp 中的并将它们显示在 Xamarin.Forms 应用程序，并且此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 04/03/2017
ms.openlocfilehash: dec6fa1534f14836ae98677ad33e280ff510fb97
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995178"
---
# <a name="bitmap-basics-in-skiasharp"></a>位图中 SkiaSharp 的基础知识

_从各种源加载位图并将其显示。_

SkiaSharp 中的支持是位图的非常大。 本文介绍如何仅基本&mdash;如何加载位图和它们的显示方式：

![](bitmaps-images/bitmapssample.png "两个位图的显示")

SkiaSharp 位图的类型的对象[ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/)。 有许多方法来创建一个位图，但本文将限制到本身[ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/SkiaSharp.SKStream/)方法，这将加载从位图[ `SKStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStream/)对象，它引用一个位图文件。 它可以方便地使用[ `SKManagedStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKManagedStream/)派生的类`SKStream`因为它具有一个接受.NET 构造函数[ `Stream` ](xref:System.IO.Stream)对象。

**基本位图**页面**SkiaSharpFormsDemos**程序演示了如何从三个不同的源加载位图：

- 借助 Internet 多
- 从可执行文件中嵌入的资源
- 从用户的照片库

三个`SKBitmap`对象作为字段中定义了这些三个源[ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs)类：

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

若要加载基于 URL 的位图，可以使用[ `WebRequest` ](xref:System.Net.WebRequest)类，如以下代码执行中所示`BasicBitmapsPage`构造函数。 此处的 URL 指向具有一些示例位图的 Xamarin web 站点上的区域。 在网站上的包，可以追加大小调整为特定宽度位图的规范：

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

回调方法时已成功下载位图，传递给`BeginGetResponse`方法运行。 `EndGetResponse`调用需采用`try`阻止以防出现错误。 `Stream`对象，来自`GetResponseStream`不能满足在某些平台上使位图内容复制到`MemoryStream`对象。 此时，`SKManagedStream`可创建对象。 现在引用的位图文件，这可能是 JPEG 或 PNG 文件。 `SKBitmap.Decode`方法解码的位图文件，并将结果存储在内部 SkiaSharp 格式。

回调方法传递给`BeginGetResponse`运行后构造函数已完成执行，这意味着`SKCanvasView`需要失效允许`PaintSurface`处理程序以更新显示。 但是，`BeginGetResponse`回调运行在辅助线程的执行，因此需要使用`Device.BeginInvokeOnMainThread`运行`InvalidateSurface`用户界面线程中的方法。

## <a name="loading-a-bitmap-resource"></a>正在加载位图资源

在代码中，方面加载位图的最简单方法直接在您的应用程序中包括的位图资源。 **SkiaSharpFormsDemos**程序包括一个名为文件夹**媒体**包含一个名为的位图文件**monkey.png**。 在中**属性**对话框中为此文件，必须提供此类文件**生成操作**的**嵌入的资源**！

每个嵌入的资源具有*资源 ID* ，它包含项目名称、 文件夹和文件名，所有连接通过句点： **SkiaSharpFormsDemos.Media.monkey.png**。 可以通过指定该资源获取访问此资源 ID 作为参数到[ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String))方法[ `Assembly` ](xref:System.Reflection.Assembly)类：

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

还有可能要从设备的图片库加载一张照片的用户。 Xamarin.Forms 本身未提供此功能。 该作业需要一个依赖关系服务，如本文所述[图片库从选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)。

**IPicturePicker.cs**文件和三个**PicturePickerImplementation.cs**该文章中的文件已复制到的各种项目**SkiaSharpFormsDemos**解决方案，并取得新命名空间名称。 此外，Android **MainActivity.cs**文章中所述修改文件和 iOS 项目已被授予权限以访问照片库使用两行代码靠近末尾部分给**info.plist**文件。

`BasicBitmapsPage`构造函数将添加`TapGestureRecognizer`到`SKCanvasView`要通知的分流点。 在点击，收到`Tapped`处理程序获取访问图片选取器依赖关系服务并调用`GetImageStreamAsync`。 如果`Stream`返回对象，然后将内容复制到`MemoryStream`的平台的一些要求。 代码的其余部分是类似于两种方法：

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

请注意，`Tapped`处理程序调用`InvalidateSurface`方法的`SKCanvasView`对象。 这将生成到新的调用`PaintSurface`处理程序。

## <a name="displaying-the-bitmaps"></a>显示位图

`PaintSurface`显示三个位图所需的处理程序。 该处理程序假定手机处于纵向模式，并且将在画布垂直划分为三个相等部分。

使用最简单的形式显示第一个位图[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/)方法。 您需要指定是定位位图左上角的 X 和 Y 坐标：

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

尽管`SKPaint`定义参数，它具有默认值为`null`，可以忽略它。 位图的像素只被转移到具有一对一的映射显示器表面的像素为单位。

程序可以获取位图的像素尺寸[ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/)并[ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/)属性。 这些属性允许程序计算的坐标，以在画布左上第三个中心位置位图：

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

第三个版本[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/)具有两个`SKRect`指定到显示，但该版本的位图的矩形子集的参数不在本文中使用。

下面是代码，以显示从嵌入的资源位图加载位图：

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

位图将拉伸为矩形，这就是原因 monkey 水平拉伸这些屏幕截图中的尺寸：

[![](bitmaps-images/basicbitmaps-small.png "三个基本位图页的屏幕截图")](bitmaps-images/basicbitmaps-large.png#lightbox "三个基本位图页的屏幕截图")

第三个图像&mdash;，仅可看到是否运行该程序，并从你自己的图片库加载一张照片&mdash;还显示在一个矩形，但该矩形的位置和大小调整以保持位图的长宽比。 此计算是更复杂一些，因为它需要计算缩放系数基于位图和目标矩形的大小和集中在该区域的矩形：

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

如果尚未从图片库中，加载任何位图则`else`块显示一些文本以提示用户点击屏幕。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
