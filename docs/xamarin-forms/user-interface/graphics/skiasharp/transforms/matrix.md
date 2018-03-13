---
title: "矩阵转换"
description: "深入了解与通用转换矩阵的 SkiaSharp 转换"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: 9d5e65abe675ded48e9239f2cd10ceed4a7c3a52
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="matrix-transforms"></a>矩阵转换

_深入了解与通用转换矩阵的 SkiaSharp 转换_

应用于的所有转换`SKCanvas`对象合并在单个实例中的[ `SKMatrix` ](https://developer.xamarin.com/api/type/SkiaSharp.SKMatrix/)结构。 这是类似于所有现代二维图形系统中的标准 3-3 转换矩阵。

如你所见，你可以使用转换在 SkiaSharp 而不必知道有关转换矩阵，但转换矩阵是重要从理论上的角度来看，并且使用转换来修改路径时，它是重要或处理复杂的触摸屏输入，这两个这是本文和下一步中演示的。

![](matrix-images/matrixtransformexample.png "受到仿射转换位图")

当前应用于的转换矩阵`SKCanvas`可随时通过访问只读[ `TotalMatrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.TotalMatrix/)属性。 你可以设置新的转换矩阵使用[ `SetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.SetMatrix/p/SkiaSharp.SKMatrix/)方法，并且你可以还原该转换矩阵为默认值通过调用[ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix/)。

唯一其他`SKCanvas`直接适用于画布的矩阵变换的成员是[ `Concat` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Concat/p/SkiaSharp.SKMatrix@/)这通过将它们一起乘以串联两个矩阵。

默认值转换矩阵为单位矩阵，包含 1 对角线单元格和 0 其他位置中：

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

你可以创建使用静态某标识矩阵[ `SKMatrix.MakeIdentity` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeIdentity()/)方法：

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

`SKMatrix`默认构造函数未*不*返回某标识矩阵。 它返回所有单元格设置为零的矩阵。 不要使用`SKMatrix`构造函数除非你打算手动设置这些单元格。

当 SkiaSharp 呈现图形对象时，每个点 （x，y） 是有效地转换到第三列中的 1 与 1-3 矩阵：

<pre>
| x  y  1 |
</pre>

此 1-3 矩阵表示一个三维点设置为 1 的 Z 坐标。 二维矩阵转换需要在三个维度中工作的原因有数学由于 （后面所述） 的原因。 你可以将表示在三维坐标系统中，但始终在二维平面上的点作为此 1-3 矩阵，其中 Z 等于 1。

此 1-3 矩阵然后乘以变换矩阵，并且结果是呈现在画布上的点：

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

使用标准矩阵乘法，已转换的点如下所示：

x' = x

y' = y

z' = 1

这是默认转换。

当`Translate`方法调用`SKCanvas`对象，`tx`和`ty`自变量`Translate`方法成为第三行的转换矩阵中的前两个单元格：

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

乘法现，如下所示：

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

下面是转换公式：

x = x + tx

y = y + ty

比例因子具有默认值为 1。 当调用`Scale`上的新方法`SKCanvas`对象，生成的转换矩阵包含`sx`和`sy`对角线单元中的自变量：

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

转换公式如下所示：

x = sx ·x

y = sy ·y

在调用的转换矩阵`Skew`包含靠近比例因子矩阵单元中的两个自变量：

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

转换公式是：

x' = x + xSkew · y

y = ySkew ·x + y

针对调用`RotateDegrees`或`RotateRadians`对于 α 的角度，转换矩阵是，如下所示：

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

下面是转换公式：

x' = cos(α) · x - sin(α) · y

y' = sin(α) · x - cos(α) · y

0 度 α 时，它是单位矩阵。 Α 时 180 度，转换矩阵如下所示：

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

旋转 180 度相当于水平翻转对象和垂直方向，这还可以通过设置为-1 的缩放比例。

所有这些类型的转换被归类为*仿射*转换。 仿射转换永远不会涉及的矩阵，仍然保持为 0、 0 和 1 的默认值的第三个列。 文章[非仿射转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)讨论非仿射转换。

## <a name="matrix-multiplication"></a>矩阵乘法

使用的转换矩阵的一大优点是，可以通过矩阵乘法，这通常称为作为 SkiaSharp 文档中获取复合转换*串联*。 与转换相关的方法中的许多`SKCanvas`指"预串联"或"预-concat。" 这涉及很重要，因为矩阵乘法不是可交换的乘法的顺序。

例如，文档[ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/)方法显示其"Pre concats 当前与指定的平移矩阵"文档时为[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/)方法显示其"Pre-concats 当前与指定的缩放矩阵。"

这意味着指定的方法调用转换是乘数 （左操作数） 和当前的转换矩阵是被乘数 （右操作数）。

假设`Translate`称为跟`Scale`:

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

`Scale`转换乘以`Translate`有关复合转换矩阵转换：

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` 无法调用前`Translate`如下所示：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

在这种情况下，乘法的顺序被反转，和缩放系数以有效地应用于转换因素：

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

下面是`Scale`与透视点的方法：

```csharp
canvas.Scale(sx, sy, px, py);
```

这相当于下面的翻译和小数位数调用：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

从方法如何出现在代码中以相反顺序相乘三个的转换矩阵：

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

### <a name="the-skmatrix-structure"></a>SKMatrix 结构

`SKMatrix`结构定义的类型的九个读/写属性`float`对应的转换矩阵的九个单元格：

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` 此外定义了一个名为属性[ `Values` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix.Values/)类型的`float[]`。 此属性可以用于设置或获取在顺序中的一个快照中的九个值`ScaleX`， `SkewX`， `TransX`， `SkewY`， `ScaleY`， `TransY`， `Persp0`， `Persp1`，和`Persp2`。

`Persp0`， `Persp1`，和`Persp2`在文章中，讨论了单元格[非仿射转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)。 如果这些单元格有 0，0，1，其默认值，然后转换乘以如下坐标点：

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

x = ScaleX ·x + SkewX ·y + TransX

y = SkewX ·x + ScaleY ·y + TransY

z' = 1

这是完整的二维仿射转换。 仿射转换保留平行直线，这意味着一个矩形将永远不会转换为的平行四边形之外的任何内容。

`SKMatrix`结构定义了若干静态方法来创建`SKMatrix`值。 这些都返回`SKMatrix`值：

- [`MakeTranslation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeTranslation/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/System.Single/System.Single/) 使用透视点
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/) 以弧度表示的角
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/System.Single/System.Single/) 以弧度表示，与透视点的角
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/)
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/System.Single/System.Single/) 使用透视点
- [`MakeSkew`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeSkew/p/System.Single/System.Single/)

`SKMatrix` 此外定义了多个静态方法相串联两个矩阵，这意味着若要将它们相乘。 这些方法的命名`Concat`， `PostConcat`，和`PreConcat`，并且有两个版本的每个。 这些方法具有没有返回值;相反，它们引用现有`SKMatrix`值通过`ref`自变量。 在下面的示例中， `A`， `B`，和`R`（对于"结果"） 是所有`SKMatrix`值。

这两个`Concat`方法调用如下：

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

这些执行以下乘法：

R = B × A

其他方法有只有两个参数。 第一个参数是修改后，和从方法调用返回时，包含两个矩阵的乘积。 这两个`PostConcat`方法调用如下：

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

这些调用执行以下操作：

A = A × B

这两个`PreConcat`方法非常相似：

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

这些调用执行以下操作：

A = B × A

所有这些方法调用的版本`ref`自变量会稍微更多地高效中调用的基础实现，但它在某些情况下可能令人困惑的某个人可以读取你的代码和假定任何东西与`ref`自变量是修改方法。 此外，通常很方便地将传递的参数之一的结果，`Make`方法，例如：

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

这将创建以下矩阵：

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

这是缩放变换乘以转换变换。 在此特定情况下，`SKMatrix`结构可具有一个名为方法提供一个快捷方式[ `SetScaleTranslate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.SetScaleTranslate/p/System.Single/System.Single/System.Single/System.Single/):

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

这是一个可安全使用的次数少`SKMatrix`构造函数。 `SetScaleTranslate`方法会设置此矩阵的所有九个单元格。 它也是安全使用`SKMatrix`构造函数使用静态`Rotate`和`RotateDegrees`方法：

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

这些方法都执行*不*连接到的现有转换旋转转换。 方法设置矩阵的所有单元的格。 在功能上等同于`MakeRotation`和`MakeRotationDegrees`方法，只不过它们未实例化`SKMatrix`值。

假设你有`SKPath`你想要显示，但您希望使用它具有稍有不同的方向或不同中心点的对象。 你可以通过调用来修改该路径的所有坐标[ `Transform` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Transform/p/SkiaSharp.SKMatrix/)方法`SKPath`与`SKMatrix`自变量。 **路径转换**页演示如何执行此操作。 [ `PathTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs)类引用`HendecagramPath`字段中的对象但使用其构造函数将转换应用到该路径：

```csharp
public class PathTransformPage : ContentPage
{
    SKPath transformedPath = HendecagramArrayPage.HendecagramPath;

    public PathTransformPage()
    {
        Title = "Path Transform";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        SKMatrix matrix = SKMatrix.MakeScale(3, 3);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(360f / 22));
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(300, 300));

        transformedPath.Transform(matrix);
    }
    ...
}
```

`HendecagramPath`对象已在一个中心 （0，0），并从该中心由所有方向的 100 个单位由里向外扩展的星形的十一个点。 这意味着的路径包含正值和负值坐标。 **路径转换**页优先处理三次尽可能大，星号和所有正坐标。 此外，它不需要星型可垂直向上点的一个的点。 它希望改为在星型的一个点直接向下。 （由于在星型具有十一个点，它不能同时具有。）这要求通过 360 度旋转星形除以 22。

构造函数生成`SKMatrix`中使用的三个单独转换对象`PostConcat`使用以下模式，其中 A、 B 和 C 是的实例方法`SKMatrix`:

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

这是一系列连续乘法运算，因此结果为，如下所示：

A × B × C

连续乘法运算有助于了解每个转换的用途。 缩放变换路径坐标的大小增加通过因子为 3，因此坐标介于 –300 和 300 之间。 旋转变换旋转围绕其源星号。 Translate 转换然后会将它移动通过 300 像素右和向下，因此所有改为正值的坐标。

没有生成相同的矩阵的其他序列。 下面是另一个：

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

首先，旋转围绕其中心的路径和会将它转换到右的 100 个像素，并且下因此所有坐标正。 星型然后增加的相对于其新的左上角，点 （0，0） 的大小。

`PaintSurface`处理程序只需呈现此路径：

```csharp
public class PathTransformPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Magenta;
            paint.StrokeWidth = 5;

            canvas.DrawPath(transformedPath, paint);
        }
    }
}

```

它显示在画布的左上角中：

[![](matrix-images/pathtransform-small.png "三重的路径转换页的屏幕截图")](matrix-images/pathtransform-large.png#lightbox "三重的路径转换页的屏幕截图")

此程序的构造函数将矩阵应用到的路径与以下调用：

```csharp
transformedPath.Transform(matrix);
```

路径*不*保留此矩阵作为属性。 相反，它适用于所有路径的坐标转换。 如果`Transform`称为同样，同样，应用转换，则可以返回的唯一方法是通过应用撤消转换的另一个矩阵。 幸运的是，`SKMatrix`结构定义[ `TryInverse` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.TryInvert/p/SkiaSharp.SKMatrix@/)获取矩阵的方法可反转给定的矩阵：

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

此方法叫做`TryInverse`因为并非所有矩阵都是可逆转的但并不可能会使用用于图形转换的非可逆转的矩阵。

你还可以应用到矩阵变换`SKPoint`值、 数组的点， `SKRect`，或即使只是在程序内的是一个数字。 `SKMatrix`结构支持这些操作的单词开头的方法的集合`Map`，如下：

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

如果你使用该最后一个方法，请记住，`SKRect`结构不能表示的旋转的矩形。 该方法仅合理的`SKMatrix`值表示平移和缩放。

### <a name="interactive-experimentation"></a>交互式试验

感受仿射转换的一种方法是以交互方式移动在屏幕的位图的三个角、 查看哪些转换结果。 这是背后的思想**显示仿射矩阵**页。 此页需要也用在其他演示的其他两个类：

[ `TouchPoint` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/TouchPoint.cs)类显示在屏幕可以拖放一个半透明圆形。 `TouchPoint` 要求`SKCanvasView`或的父级的元素`SKCanvasView`具有[ `TouchEffect` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/TouchEffect.cs)附加。 将 `Capture` 属性设置为 `true`。 在`TouchAction`事件处理程序，程序必须调用`ProcessTouchEvent`中的方法`TouchPoint`每个`TouchPoint`实例。 该方法返回`true`如果触摸事件导致移动触摸点。 此外，`PaintSurface`处理程序必须调用`Paint`方法在每个`TouchPoint`实例，并向它传递`SKCanvas`对象。

`TouchPoint` 演示一个常见方法，可以在一个单独的类中封装 SkiaSharp 视觉对象。 该类还可以定义用于指定特征的视觉效果，属性和方法名为`Paint`与`SKCanvas`自变量可以呈现它。

`Center`属性`TouchPoint`指示该对象的位置。 可以设置此属性来初始化位置;当用户拖动画布周围的圆圈属性的更改。

**显示仿射矩阵页**还要求[ `MatrixDisplay` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/MatrixDisplay.cs)类。 此类显示的单元格`SKMatrix`对象。 它具有两个公共方法：`Measure`获取呈现矩阵的维度和`Paint`以显示它。 类包含`MatrixPaint`类型的属性`SKPaint`可以替换为不同的字体大小或颜色。

[ **ShowAffineMatrixPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml)文件实例化`SKCanvasView`并将其附加`TouchEffect`。 [ **ShowAffineMatrixPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs)隐藏代码文件创建三个`TouchPoint`对象，然后将它们设置为对应于它会从一个嵌入加载的位图的三个角的位置资源：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    SKMatrix matrix;
    SKBitmap bitmap;
    SKSize bitmapSize;

    TouchPoint[] touchPoints = new TouchPoint[3];

    MatrixDisplay matrixDisplay = new MatrixDisplay();

    public ShowAffineMatrixPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }

        touchPoints[0] = new TouchPoint(100, 100);                  // upper-left corner
        touchPoints[1] = new TouchPoint(bitmap.Width + 100, 100);   // upper-right corner
        touchPoints[2] = new TouchPoint(100, bitmap.Height + 100);  // lower-left corner

        bitmapSize = new SKSize(bitmap.Width, bitmap.Height);
        matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                           touchPoints[1].Center,
                                           touchPoints[2].Center);
    }
    ...
}
```

仿射矩阵定义唯一三个点。 这三种`TouchPoint`对象对应于位图的左上角、 右上角和左下角。 因为仅能够将矩形转换为的平行四边形的仿射矩阵，其他三个所隐含的第四个点。 构造函数通过调用到结束`ComputeMatrix`，其计算的单元格`SKMatrix`从这些三个点的对象。

`TouchAction`处理程序调用`ProcessTouchEvent`每个`TouchPoint`。 `scale`值将从 Xamarin.Forms 坐标转换为像素：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = canvasView.CanvasSize.Width / (float)canvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                               touchPoints[1].Center,
                                               touchPoints[2].Center);
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

如果任何`TouchPoint`已移动，则该方法调用`ComputeMatrix`再次和使图面无效。

`ComputeMatrix`方法确定隐含这些三个点的矩阵。 矩阵调用`A`转换变成平行四边形的一个像素正方形矩形基于三个点，而调用的缩放变换`S`缩放到一个像素正方形矩形位图。 复合矩阵是`S`× `A`:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL)
    {
        // Scale transform
        SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

        // Affine transform
        SKMatrix A = new SKMatrix
        {
            ScaleX = ptUR.X - ptUL.X,
            SkewY = ptUR.Y - ptUL.Y,
            SkewX = ptLL.X - ptUL.X,
            ScaleY = ptLL.Y - ptUL.Y,
            TransX = ptUL.X,
            TransY = ptUL.Y,
            Persp2 = 1
        };

        SKMatrix result;
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

最后，`PaintSurface`方法呈现基于该矩阵的位图、 在该屏幕的底部显示矩阵和呈现位图的三个角的触摸点：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap using the matrix
        canvas.Save();
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(matrix);

        matrixDisplay.Paint(canvas, matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));

        // Display the touchpoints
        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }
  }
```

下面的 iOS 屏幕显示位图，首次加载页，而其他两个屏幕将显示它后某些操作时：

[![](matrix-images/showaffinematrix-small.png "显示仿射矩阵页面的三个屏幕截图")](matrix-images/showaffinematrix-large.png#lightbox "显示仿射矩阵页面的三个屏幕截图")

尽管它看起来就像触摸点拖动位图的角，但这是仅这样的错觉。 从触摸点计算的矩阵变换位图，以便与触摸点的边角保持一致。

更自然用户可以移动、 调整大小，并将位图不旋转拖动角，但通过使用一个或两个手指直接拖动时，对象上捏合，与旋转。 在下一篇文章中对此进行了[Touch 操作](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)。

### <a name="the-reason-for-the-3-by-3-matrix"></a>原因 3 x 3 矩阵

被预期的二维图形系统需要仅 2-2 转换矩阵：

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

这适用于缩放、 旋转和甚至倾斜，但它不是支持的最基本的转换，即转换。

问题是 2-2 矩阵表示*线性*转换在两个维度。 线性转换保留一些基本的算术运算，但影响之一是线性转换永远不会更改点 （0，0）。 线性转换无法进行转换。

在三个维度中的线性转换矩阵如下所示：

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

标记为的单元格`SkewXY`值偏差的 Y 值为基础的 X 坐标的表示; 该单元格`SkewXZ`意味着值偏差 Z; 值为基础的 X 坐标和值扭曲同样适用于其他`Skew`单元格。

可以通过设置此三维转换矩阵限制到二维平面`SkewZX`和`SkewZY`为 0，和`ScaleZ`为 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

如果完全在其中 Z 等于 1 的三维空间中平面上绘制的二维图形，转换乘法如下所示：

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

所有内容都保持在二维平面上其中 Z 等于 1，但`SkewXZ`和`SkewYZ`单元格有效地将变为二维转换因素。

这是一个三维线性转换如何用作二维非线性转换。 （打比方，3D 图形中的转换相对于 4-4 矩阵。）

`SKMatrix`中 SkiaSharp 结构定义为该行第三个属性：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

非零值`Persp0`和`Persp1`导致移动对象移到二维平面其中 Z 等于 1 的转换。 这些对象被移回该平面时会发生什么情况中介绍了文章上[非仿射转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
