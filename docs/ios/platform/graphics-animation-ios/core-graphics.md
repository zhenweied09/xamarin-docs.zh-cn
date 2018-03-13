---
title: "核心图形"
description: "本文讨论的核心图形 iOS 框架。 它演示如何使用核心图形绘制几何图形、 图像和 Pdf。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d53494e61d702b83a28534c644f33fb5327b5958
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="core-graphics"></a>核心图形

_本文讨论的核心图形 iOS 框架。它演示如何使用核心图形绘制几何图形、 图像和 Pdf。_

iOS 包括[*核心图形*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) framework 以提供低级别的绘制支持。 这些框架是什么启用 UIKit 内的丰富图形功能。 

核心图形是一个低级别的二维图形框架，允许绘制设备独立图形。 所有在 UIKit 中绘制的二维在内部使用核心图形。

核心图形支持绘制了大量方案包括：

-  [绘制向屏幕中，通过`UIView` ](#Drawing_in_a_UIView_Subclass) 。
-  [在内存中或在屏幕上绘制图像](#Drawing_Images_and_Text)。
-  创建并绘制为 PDF。
-  读取和绘制现有 PDF。


## <a name="geometric-space"></a>几何空间

而不考虑此方案中，核心图形完成的所有绘图都完成几何空间中，这意味着它在抽象的点，而不是像素工作。 描述要绘制几何图形和绘制状态例如颜色、 行样式等方面和核心图形句柄将所有内容转换为像素为单位。 此类状态添加到你可以将像刷的画布的图形上下文。

有这种方法的几个优点：

-  绘制代码变得动态的并且随后可以修改在运行时的图形。
-  减少应用程序捆绑中的静态图像的需要，可以减少应用程序大小。
-  图形会变得更具弹性分辨率更改到跨设备。

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>在 UIView 子类中绘制

每个`UIView`具有`Draw`时它需要绘制由系统调用的方法。 将绘制代码添加到视图中，子类`UIView`，并重写`Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

绘图应永远不会直接调用。 它是在运行的循环处理过程中由系统调用您的问题。 首次通过运行循环后视图添加到视图层次结构，其`Draw`调用方法。 后续调用`Draw`视图被标记为无需通过调用绘制时发生`SetNeedsDisplay`或`SetNeedsDisplayInRect`视图上。

### <a name="pattern-for-graphics-code"></a>图形代码模式

中的代码`Draw`实现应描述它想绘制。 绘制代码遵循它在其中设置某些绘制的状态，并调用一个方法来请求要绘制的模式。 此模式可以将映像一般化，如下所示：

1. 获取图形上下文。

2. 设置的绘制特性。

3. 创建由绘图基元的某些几何图形。

4. 调用绘图或笔划方法。

### <a name="basic-drawing-example"></a>基本绘图示例

例如，考虑下面的代码段：

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {
            
    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100), 
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);       
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

让我们将此代码分解：

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
与这行内容，它首先获取当前的图形上下文，用于进行绘制。 可以将图形上下文视为画布，绘制上发生的情况，包含有关绘图，如笔画和填充颜色，以及要绘制的几何图形的所有状态。

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

获取的图形上下文后的代码设置某些属性用于绘制，以上所示。 在这种情况下设置线条的宽度、 笔划和填充颜色。 任何后续绘图随后将使用这些属性，因为它们都保持的图形上下文状态。

若要创建几何图形的代码使用`CGPath`，这样，用于描述从直线和曲线的图形路径。 在这种情况下，路径将添加连接的点，以组成一个三角形，三角形数组的行。 如下所示核心图形使用一个坐标系统，用于绘制的视图，其中的源是在左上角，使用为右边，正 y 方向向下的正数 x direct:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

路径创建后，它添加到的图形上下文，以便调用`AddPath`和`DrawPath`分别可以绘制它。

获得的视图如下所示：

 ![](core-graphics-images/00-bluetriangle.png "示例输出三角形")

## <a name="creating-gradient-fills"></a>创建渐变填充

此外，还提供更丰富的形式绘制。 例如，核心图形允许创建渐变填充和应用剪切路径。 若要从前面的示例绘制在路径内渐变填充，首先需要路径设置为剪切路径：

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

将当前路径设置为剪切路径约束内的几何图形的路径，如下面的代码中，所有后续绘图绘制的线性渐变：

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient, 
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top), 
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom), 
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

这些更改生成渐变填充，如下所示：

 ![](core-graphics-images/01-gradient-fill.png "具有渐变填充示例")

## <a name="modifying-line-patterns"></a>修改行模式

此外可以使用核心图形修改行的绘制特性。 这包括更改线条的宽度和描边颜色，以及行模式本身，如下面的代码中所示：

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

以虚线描边 10 使用 4 个单位的短划线，之间的间距的单位长，添加任何绘制操作结果之前此代码，如下所示：

 ![](core-graphics-images/02-dashed-stroke.png "虚线描边中添加此代码之前任何绘制操作结果")
 
请注意，当在 Xamarin.iOS 中使用统一的 API，数组类型需要`nfloat`，并且还需要显式强制转换为 Math.PI。

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>绘制图像和文本

除了在视图的图形上下文中绘制轨迹，核心图形还支持绘制的图像和文本。 若要绘制图像，只需创建`CGImage`和将其传递到`DrawImage`调用：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

但是，这将生成倒置，如下所示绘制的图像：

 ![](core-graphics-images/03-upside-down-monkey.png "颠倒绘制的图像")

这样做的原因是为图像绘制的核心图形原点为左下方，而此视图在左上角具有其源。 因此，若要正确显示图像，原点需要进行修改，这可以通过修改来完成*当前转换矩阵* *(CTM)*。 CTM 定义点居住在哪里，也称为*用户空间*。 反转 y 方向的 CTM 和移位通过负 y 方向的边界的高度可以翻转图像。

图形上下文具有用于转换 CTM 的帮助器方法。 在这种情况下， `ScaleCTM` "翻转"绘图和`TranslateCTM`会将它移动到左上角，如下所示：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
    
    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}   
```

然后显示生成的映像竖向：

 ![](core-graphics-images/04-upright-monkey.png "示例显示图像直立")

> [!IMPORTANT]
>  **注意：**的图形上下文更改适用于所有后续的绘制操作。 因此，在 CTM 转换后，它将会影响任何其他绘图。 例如，如果 CTM 转换后绘制三角形，，它会可以正面朝下显示。

### <a name="adding-text-to-the-image"></a>将文本添加到映像

作为使用路径和映像，与核心图形绘制文本包括设置某些图形状态和调用方法来绘制相同的基本的模式。 对于文本，该方法以显示文本是`ShowText`。 添加到图像绘制示例时，下面的代码绘制使用核心图形一些文本：

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);
    
    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

如你所见，绘制的文本的图形状态设置为类似于绘制几何图形。 对于文本但是绘图，以及应用绘制模式和字体的文本。 在这种情况下，还应用阴影，但应用阴影的工作方式对于绘制的路径。

生成的文本将显示与映像，如下所示：

 ![](core-graphics-images/05-text-on-image.png "生成的文本将显示与映像")

## <a name="memory-backed-images"></a>支持内存的映像

除了绘图到视图的图形上下文中，核心图形支持绘制内存备份映像，也称为屏幕绘制。 执行此操作需要：

-  创建的图形上下文由内存中支持位图
-  设置绘图状态和颁发绘图命令
-  从上下文中获取图像
-  删除上下文


与不同`Draw`方法，其中上下文提供的视图，在这种情况下你创建的上下文中两种方式之一：

1. 通过调用`UIGraphics.BeginImageContext`(或`BeginImageContextWithOptions`)

2. 通过创建新 `CGBitmapContextInstance`

 `CGBitmapContextInstance` 当你正在直接使用的映像位，如： 您正在使用自定义映像操作算法的情况下非常有用。 在所有其他情况下，应使用`BeginImageContext`或`BeginImageContextWithOptions`。

映像上下文之后，添加绘制代码就像它是在一样`UIView`子类。 例如，使用前面用于绘制三角形的代码示例而不是在内存中的图像绘制`UIView`，如下所示：

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100), 
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }
    
    return triangleImage;
}
```

支持内存的位图的绘图的一个常见用途是从任何捕获映像`UIView`。 例如，下面的代码呈现到位图上下文的视图的层，并创建`UIImage`从它：

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>绘制 Pdf

除了映像，核心图形支持 PDF 绘图。 如映像，你可以呈现在内存中的 PDF，以及读取以呈现 PDF `UIView`。

### <a name="pdf-in-a-uiview"></a>在 UIView PDF

核心图形还支持从文件读取 PDF 和呈现在视图中使用`CGPDFDocument`类。 `CGPDFDocument`类表示在代码中，PDF，可以用于读取和绘制页。

例如，下面的代码中`UIView`子类从到文件中读取 PDF `CGPDFDocument`:

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }
  
     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

`Draw`方法可以然后使用`CGPDFDocument`读取到页`CGPDFPage`，并使其通过调用`DrawPDFPage`，如下所示：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
        
    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);
        
        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {
            
        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);
        
        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);
        
        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>支持内存的 PDF

对于内存中 PDF，你需要通过调用创建的 PDF 上下文`BeginPDFContext`。 绘制为 PDF 是精确到页。 每一页开始通过调用`BeginPDFPage`并通过调用完成`EndPDFContent`，使用图形的代码之间。 此外，如使用图像绘制内存支持 PDF 绘制使用左下方，可以考虑通过只需修改 CTM origin 喜欢个映像。

下面的代码演示如何为 PDF 绘制文本：

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();
       
using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);      
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }
    
//complete a PDF page
UIGraphics.EndPDFContent ();
```

生成的文本绘制到 PDF，然后将其包含在`NSData`可以被保存，上载、 通过电子邮件发送，等等。


## <a name="summary"></a>摘要

在本文中我们讨论过的图形功能通过提供*核心图形*framework。 我们已了解如何使用核心图形的上下文中绘制几何图形、 图像和 Pdf`UIView,`并与支持内存的图形上下文。

## <a name="related-links"></a>相关链接

- [核心图形示例](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [图形和动画演练](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [核心动画](~/ios/platform/graphics-animation-ios/core-animation.md)
- [核心动画配方](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
