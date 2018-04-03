---
title: 非仿射转换
description: 转换矩阵将第三个列创建透视和锥形的效果
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: charlespetzold
ms.author: chape
ms.date: 04/14/2017
ms.openlocfilehash: ae61774a53136af3350d63658470cfb4607026db
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="non-affine-transforms"></a>非仿射转换

_转换矩阵将第三个列创建透视和锥形的效果_

平移、 缩放、 旋转和倾斜全都归类为*仿射*转换。 仿射转换保留平行直线。 如果在转换之前并行的两个行，它们保持并行后转换。 始终 parallelograms 到转换矩形。

但是，仍可非仿射转换，其具有的功能，可将矩形转换为任何凸四边形 SkiaSharp:

![](non-affine-images/nonaffinetransformexample.png "位图转换为一个凸四边形")

凸四边形是内部角度始终小于 180 度、 不相互交叉的两边的四个边图。

非仿射转换的转换矩阵的第三个行设置为 0、 0 和 1 以外的值时的结果。 完整`SKMatrix`乘法是：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

结果转换公式是：

x = ScaleX·x + SkewX·y + TransX

y = SkewY·x + ScaleY·y + TransY

z = Persp0·x + Persp1·y + Persp2

使用二维转换的 3 x 3 矩阵的基本规则是，所有内容保持在平面上其中 Z 等于 1。 除非`Persp0`和`Persp1`均为 0，和`Persp2`等于 1，转换已移动关闭该平面的 Z 坐标。

若要还原此二维转换，坐标必须移回到该平面。 另一个步骤是必需的。 X，y，和 z 值必须除以 z:

x" = x' / z'

y" = y' / z'

z" = z' / z' = 1

这些被称为*同类坐标*和开发通过数学知识的人年 8 月 Ferdinand Möbius，得更好已知为他拓扑 oddity Möbius 条带。

如果 z 为 0，无限坐标中的除法结果。 实际上，开发同类坐标 Möbius 的动机之一是能够表示的无限值的有限数字。

当显示图像，但是，你想要避免呈现内容转换为无限值的坐标。 不会呈现这些坐标。 在这些坐标附近出现的所有内容将会非常大，并且可能并不直观地连贯。

在此公式中，你不希望 z 的值变为 0:

z = Persp0·x + Persp1·y + Persp2

`Persp2`单元格可以是零或不为零。 如果`Persp2`为零，则 z 为零 （0，0） 的点和原因在于通常不需要该点是二维图形中很常见。 如果`Persp2`不等于零，则如果，则不会丢失一般性`Persp2`固定为 1。 例如，如果你确定`Persp2`应为 5，则你可以只需除以矩阵中的所有单元格 5，这使得`Persp2`等于 1，并且结果都是相同。

出于这些原因，`Persp2`通常固定为 1，这是单位矩阵中的相同值。

通常情况下，`Persp0`和`Persp1`是小的数字。 例如，假设开头单位矩阵，但不集`Persp0`0.01 到：

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

转换公式是：

x` = x / (0.01·x + 1)

y = y / （0.01·x + 1）

现在使用此转换呈现原点定位一个 100 像素方框。 下面是如何转换的四个角：

(0, 0) → (0, 0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

当 x 为 100，则 z 分母为 2，因此的 x 和 y 坐标减半。 框的右侧会成为短于左侧:

![](non-affine-images/nonaffinetransform.png "一个框受到非仿射转换")

`Persp`这些单元格名称部分指在"透视"，因为收缩建议框现在倾斜与 μ 从查看器的右侧。

**测试透视**页面允许您的值进行试验`Persp0`和`Pers1`来感受有关它们如何工作。 这些矩阵单元格的合理值是太小，`Slider`在通用 Windows 平台中不能正确地处理它们。 若要容纳 UWP 问题，这两个`Slider`中的元素[ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml)需要初始化范围为从 – 1 到 1:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.TestPerspectivePage"
             Title="Test Perpsective">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Minimum" Value="-1" />
                    <Setter Property="Maximum" Value="1" />
                    <Setter Property="Margin" Value="20, 0" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="persp0Slider"
                Grid.Row="0"
                ValueChanged="OnPersp0SliderValueChanged" />

        <Label x:Name="persp0Label"
               Text="Persp0 = 0.0000"
               Grid.Row="1" />

        <Slider x:Name="persp1Slider"
                Grid.Row="2"
                ValueChanged="OnPersp1SliderValueChanged" />

        <Label x:Name="persp1Label"
               Text="Persp1 = 0.0000"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

事件处理程序中的滑块[ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs)代码隐藏文件除以值 100，以便它们 –0.01 和 0.01 之间的范围。 此外，构造函数加载位图中：

```csharp
public partial class TestPerspectivePage : ContentPage
{
    SKBitmap bitmap;

    public TestPerspectivePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }

    void OnPersp0SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp0Label.Text = String.Format("Persp0 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }

    void OnPersp1SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp1Label.Text = String.Format("Persp1 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }
    ...
}
```

`PaintSurface`处理程序计算`SKMatrix`名为值`perspectiveMatrix`根据除以 100 这些两个滑块的值。 这与组合两个转换将此转换的中心放入位图的中心的转换：

```csharp
public partial class TestPerspectivePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Calculate perspective matrix
        SKMatrix perspectiveMatrix = SKMatrix.MakeIdentity();
        perspectiveMatrix.Persp0 = (float)persp0Slider.Value / 100;
        perspectiveMatrix.Persp1 = (float)persp1Slider.Value / 100;

        // Center of screen
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);
        SKMatrix.PostConcat(ref matrix, perspectiveMatrix);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(xCenter, yCenter));

        // Coordinates to center bitmap on canvas
        float x = xCenter - bitmap.Width / 2;
        float y = yCenter - bitmap.Height / 2;

        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

下面是一些示例映像：

[![](non-affine-images/testperspective-small.png "测试透视页面的三个屏幕截图")](non-affine-images/testperspective-large.png#lightbox "三倍的测试透视页面屏幕截图")

在试用滑块，你将找到 0.0066 超出或低于 –0.0066 的值会导致突然成为断开和不相干的图像。 所转换的位图是 300 像素的正方形。 因此范围是从 –150 到 150 位图的坐标相对于其中心转换。 回想一下，z 的值是：

z = Persp0·x + Persp1·y + 1

如果`Persp0`或`Persp1`大于 0.0066 下面 –0.0066，则没有始终导致 z 位图某些坐标或值为零。 从而除法导致被零，并呈现成为混乱。 在使用非仿射转换时，你想要避免呈现具有导致被零除的坐标的任何内容。

通常情况下，不会设置你`Persp0`和`Persp1`中隔离。 也通常很有必要，在要实现某些类型的非仿射转换矩阵中设置其他单元格。

此类的一个非仿射转换是*锥形转换*。 此类型的非仿射转换保留总体矩形的尺寸，但通过逐渐一方：

![](non-affine-images/tapertransform.png "受到锥形转换一个框")

[ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs)类执行的一种基于这些参数的非仿射转换通用的计算：

- 正在转换图像的矩形的大小
- 枚举类型的值，该值指示通过逐渐的矩形的一端
- 另一个枚举，该值指示如何通过它逐渐，和
- 斜削的范围。

以下是代码：

```csharp
enum TaperSide { Left, Top, Right, Bottom }

enum TaperCorner { LeftOrTop, RightOrBottom, Both }

static class TaperTransform
{
    public static SKMatrix Make(SKSize size, TaperSide taperSide, TaperCorner taperCorner, float taperFraction)
    {
        SKMatrix matrix = SKMatrix.MakeIdentity();

        switch (taperSide)
        {
            case TaperSide.Left:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp0 = (taperFraction - 1) / size.Width;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Top:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp1 = (taperFraction - 1) / size.Height;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Right:
                matrix.ScaleX = 1 / taperFraction;
                matrix.Persp0 = (1 - taperFraction) / (size.Width * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        break;
                }
                break;

            case TaperSide.Bottom:
                matrix.ScaleY = 1 / taperFraction;
                matrix.Persp1 = (1 - taperFraction) / (size.Height * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        break;
                }
                break;
        }
        return matrix;
    }
}
```

在使用此类**锥形转换**页。 XAML 文件实例化两个`Picker`元素来选择枚举值，和一个`Slider`选择锥形的部分。 [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55)处理程序结合在一起锥形转换有两个转换要使相对于位图的左上角的转换转换：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedIndex;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedIndex;
    float taperFraction = (float)taperFractionSlider.Value;

    SKMatrix taperMatrix =
        TaperTransform.Make(new SKSize(bitmap.Width, bitmap.Height),
                            taperSide, taperCorner, taperFraction);

    // Display the matrix in the lower-right corner
    SKSize matrixSize = matrixDisplay.Measure(taperMatrix);

    matrixDisplay.Paint(canvas, taperMatrix,
        new SKPoint(info.Width - matrixSize.Width,
                    info.Height - matrixSize.Height));

    // Center bitmap on canvas
    float x = (info.Width - bitmap.Width) / 2;
    float y = (info.Height - bitmap.Height) / 2;

    SKMatrix matrix = SKMatrix.MakeTranslation(-x, -y);
    SKMatrix.PostConcat(ref matrix, taperMatrix);
    SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(x, y));

    canvas.SetMatrix(matrix);
    canvas.DrawBitmap(bitmap, x, y);
}
```

下面是一些可能的恶意活动：

[![](non-affine-images/tapertransform-small.png "三重的锥形转换页的屏幕截图")](non-affine-images/tapertransform-large.png#lightbox "锥形转换页的三个屏幕截图")

另一种通用的非仿射转换是三维旋转下, 一步的文章中所示，[三维旋转](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)。

非仿射转换可以将矩形转换为任何凸四边形。 说明了这一点通过**显示非仿射矩阵**页。 它是非常类似于**显示仿射矩阵**来自页[矩阵转换](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)文章，只不过它具有第四个`TouchPoint`对象，以操作位图的第四个角：

[![](non-affine-images/shownonaffinematrix-small.png "显示非仿射矩阵页面的三个屏幕截图")](non-affine-images/shownonaffinematrix-large.png#lightbox "显示非仿射矩阵页面的三个屏幕截图")

只要未尝试进行内部角度的四个角位图大于 180 度，或使两条边相互交叉，程序已成功计算从使用此方法的转换[ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs)类：

```csharp
static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL, SKPoint ptLR)
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

    // Non-Affine transform
    SKMatrix inverseA;
    A.TryInvert(out inverseA);
    SKPoint abPoint = inverseA.MapPoint(ptLR);
    float a = abPoint.X;
    float b = abPoint.Y;

    float scaleX = a / (a + b - 1);
    float scaleY = b / (a + b - 1);

    SKMatrix N = new SKMatrix
    {
        ScaleX = scaleX,
        ScaleY = scaleY,
        Persp0 = scaleX - 1,
        Persp1 = scaleY - 1,
        Persp2 = 1
    };

    // Multiply S * N * A
    SKMatrix result = SKMatrix.MakeIdentity();
    SKMatrix.PostConcat(ref result, S);
    SKMatrix.PostConcat(ref result, N);
    SKMatrix.PostConcat(ref result, A);

    return result;
}
```

为了便于计算，此方法获取作为一种产品的三个单独的转换，将显示这些转换如何修改位图的四个角的箭头此处表示总转换：

（0，0） （0，0） 的 → → （0，0）-> (x，0，y0) （左上角）

(0，H) → （0，1）-> （0，1）-> (x1，y1) （左下方）

(W，0) （1，0） 的 → → （1，0） → (x2 y2) （右上角）

（W、 H）-> （1，1）-> (a、 b)-> (x3 y3) （右下方）

右侧的最终坐标是与四个触摸点相关联的四个点。 这些是位图的角的最终坐标。

W 和 H 表示的宽度和位图的高度。 第一个转换 (`S`) 只需缩放到 1 像素正方形位图。 第二个转换是非仿射转换`N`，和第三个是仿射转换`A`。 该仿射转换基于三个点，因此它具有就像前面仿射[ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68)方法，并且不涉及具有的第四个行 (a、 b） 点。

`a`和`b`值进行计算，以便为仿射转换第三个转换。 此代码包含的仿射转换的反向属性，并使用该映射的右下角。 这是点 (a、 b)。

另一个使用非仿射转换是模拟三维图形。 在下一步的文章中，[三维旋转](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)你了解如何在三维空间中的二维图形旋转。


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
