---
title: 三维旋转
description: 使用非仿射转换轮换在三维空间中的 2D 对象。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: charlespetzold
ms.author: chape
ms.date: 04/14/2017
ms.openlocfilehash: 47daca2ab94f718aeb5ce474c87edce3c3bd77a4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="3d-rotations"></a>三维旋转

_使用非仿射转换轮换在三维空间中的 2D 对象。_

一个常见的应用程序的非仿射转换模拟在三维空间中的 2D 对象的旋转：

![](3d-rotation-images/3drotationsexample.png "在三维空间中旋转的文本字符串")

此工作涉及使用三维旋转，以及然后派生非仿`SKMatrix`执行这些三维旋转的转换。

很难开发这`SKMatrix`转换仅在两个维度中工作。 当此 3 x 3 矩阵派生从 4-4 矩阵 3D 图形中使用时，作业就变得容易。 SkiaSharp 包括[ `SKMatrix44` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.PreConcat/p/SkiaSharp.SKMatrix44/)此目的，但一些后台 3D 图形中的类是所需了解三维旋转和 4-4 转换矩阵。

三维坐标系添加 Z.从概念上讲调用的第三个轴，Z 轴是直角到屏幕。 三个数字用指示在三维空间中的坐标点: （x，y，z）。 在以三维形式在本文中，增加 X 值中使用的坐标系统是向右和增加的 Y 值均停运，两个维度中一样。 增加正 Z 值产生的屏幕。 来源是左上角，二维图形中一样。 可以将屏幕视为直角于此平面 Z 轴 XY 平面。

这称为左侧的坐标系统。 如果点的 X 坐标 （右侧），正方向左手食指和中间手指的增大 Y 方向协调 （向下），然后将拇指中点增加 Z 坐标的方向-从扩展出是必需的。

在三维图形中，转换都按 4-4 矩阵。 以下是 4-4 标识矩阵：

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

在使用 4-4 矩阵，可以很方便地使用其行和列的编号标识的单元：

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

但是，SkiaSharp`Matrix44`类是稍有不同。 设置或获取单个单元格的值的唯一办法`SKMatrix44`是使用[ `Item` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix44.Item/p/System.Int32/System.Int32/)索引器。 行和列索引是从零开始而不是基于 1 和交换的行和列。 使用索引器访问上图中的单元格 M14`[3, 0]`中`SKMatrix44`对象。

在三维图形系统中，到用于乘以 4-4 转换矩阵的 1-4 矩阵转换三维点 （x，y，z）：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

类似于二维转换发生在三个维度，三维转换假定采用四个维度中的位置。 第四个维度被称为 W，并假定三维空间存在 W 坐标是等于 1 的 4 D 空间内。 转换公式如下所示：

x' = M11·x + M21·y + M31·z + M41

y' = M12·x + M22·y + M32·z + M42

z' = M13·x + M23·y + M33·z + M43

w' = M14·x + M24·y + M34·z + M44

很明显的转换公式的单元格`M11`， `M22`，`M33`将 X、 Y 和 Z 的说明，以提升因素和`M41`， `M42`，和`M43`转换中是因素 X、 Y 和 Z说明。

若要将这些坐标转换回三维空间其中 W 等于 1，x，y，和 z 坐标所有除以 w:

x" = x' / w'

y"= y / w

z"= z / w

w" = w' / w' = 1

该除数 w 提供在三维空间中的透视。 如果 w 等于 1，就会不发生任何透视。

在三维空间中的旋转可以很复杂，但最简单的旋转绕 X、 Y 和 Z 轴。 旋转角度 α 围绕 X 轴的旋转是此矩阵：

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

X 值保持不变时受到此转换。 绕 Y 轴旋转会保留条目保持不变的 Y 值：

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

绕 Z 轴旋转是与二维图形中的相同：

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

坐标系统的左右手使用习惯所隐含的旋转方向。 这是左手系统，因此，如果点左手越来越多的值对于特定的轴的 thumb-绕 X 轴旋转的右侧围绕 Y 轴并面向使用者绕 Z 轴旋转的轮换的向下-然后的曲线则其他手指指示正角度的旋转的方向。

`SKMatrix44` 已通用化静态[ `CreateRotation` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.CreateRotation/p/System.Single/System.Single/System.Single/System.Single/)和[ `CreateRotationDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.CreateRotationDegrees/p/System.Single/System.Single/System.Single/System.Single/)方法，您可以指定发生围绕其旋转的轴：

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

绕 X 轴的旋转，将设置为 1，0，0 的前三个参数。 围绕 Y 轴的旋转，将它们设置为 0，1，0，并为围绕 Z 轴旋转，请将它们设置为 0，0，1。

透视是通过 4 4 的第四列。 `SKMatrix44`没有方法用于创建透视转换，但你可以创建一个自己使用下面的代码：

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

自变量名称的原因`depth`会很快明显。 该代码创建的矩阵。

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

转换公式导致以下计算的 w:

w = – z / 深度 + 1

这可减少 X 和 Y 坐标时的 Z 值小于零时 （从概念上讲后面 XY 平面） 以及增加正的 Z 值的 X 和 Y 坐标。当的 Z 坐标等于`depth`、 然后 w 是零，并且坐标变无限。 三维图形系统围绕相机隐喻和`depth`此处值代表从坐标系统的原点的照相机的距离。 图形对象是否具有 Z 坐标，它是`depth`单位从原点，它从概念上讲接触相机的角度和变得无限期大。

请记住，你可能要使用此`perspectiveMatrix`与旋转矩阵结合使用的值。 如果要轮替的图形对象具有 X 或 Y 坐标大于`depth`，则很可能涉及 Z 坐标大于此对象在三维空间中的旋转角度，是`depth`。 必须避免此 ！ 在创建时`perspectiveMatrix`你想要设置`depth`为足够大，无论如何转动的图形对象中的所有坐标值。 这可确保永远不会有任何除数为零。

将三维旋转和透视进行组合需要 4-4 矩阵相乘。 为此，`SKMatrix44`定义串联方法。 如果`A`和`B`是`SKMatrix44`对象时，下面的代码会将等设置为 × b:

```csharp
A.PostConcat(B);
```

二维图形系统中使用 4-4 转换矩阵时，它被适用于二维的对象。 这些对象保持不变，并且假定具有为零的 Z 坐标。 转换乘法是前面所示的转换比简单得多稍有：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

在不涉及任何第三行的矩阵中的单元格的转换公式中的 z 结果的 0 值：

x' = M11·x + M21·y + M41

y' = M12·x + M22·y + M42

z' = M13·x + M23·y + M43

w' = M14·x + M24·y + M44

此外，z 坐标也不相关此处使用。 当二维图形系统中显示一个三维对象时，它处于折叠状态到二维对象通过忽略 Z 坐标值。 转换公式是实际上只是这两个：

x" = x' / w'

y"= y / w

这意味着，第三行*和*可以忽略第三列的 4-4 矩阵。

但如果是这样，为什么第一个位置是甚至必要的 4-4 矩阵？

尽管第三行和第三个列的 4 4 是二维转换、 第三个行和列不相关*执行*播放之前，当角色各种`SKMatrix44`值相乘。 例如，假设乘围绕 Y 轴透视转换的旋转：

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

在产品中，该单元格`M14`现在包含的透视值。 如果你想要应用于二维对象的该矩阵，第三个行和列将消除可用于将转换的 3x3 矩阵：

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

现在它可以用于转换的二维点：

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

转换公式是：

x' = cos(α)·x

y' = y

z = (sin （α） / 深度) ·x + 1

现在将所有内容除以 z:

x" = cos(α)·x / ((sin(α)/depth)·x + 1)

y"= y / ((sin （α） / 深度) ·x + 1)

X 值时二维对象是否与一个正的角度围绕 Y 轴中，则正旋转 recede 负时后台 X 值是来自前台。 X 值看起来更接近到的 Y 轴 （的受的余弦值） 作为最远的地方 Y 轴坐标变得较小或更大，因为它们远离观察者或更接近于查看器。

使用时`SKMatrix44`，通过将各种乘以执行所有的三维旋转和透视操作`SKMatrix44`值。 然后你可以从通过 4 4 中提取二维的 3 x 3 矩阵矩阵使用[ `Matrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix44.Matrix/)属性`SKMatrix44`类。 此属性返回熟悉`SKMatrix`值。

**旋转三维**页中，可以在试用三维旋转。 [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml)文件实例化的四个滑块设置绕 X、 Y 和 Z 轴旋转以及设置深度值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.Rotation3DPage"
             Title="Rotation 3D">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
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
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Maximum" Value="360" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="xRotateSlider"
                Grid.Row="0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference xRotateSlider},
                              Path=Value,
                              StringFormat='X-Axis Rotation = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="yRotateSlider"
                Grid.Row="2"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference yRotateSlider},
                              Path=Value,
                              StringFormat='Y-Axis Rotation = {0:F0}'}"
               Grid.Row="3" />

        <Slider x:Name="zRotateSlider"
                Grid.Row="4"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zRotateSlider},
                              Path=Value,
                              StringFormat='Z-Axis Rotation = {0:F0}'}"
               Grid.Row="5" />

        <Slider x:Name="depthSlider"
                Grid.Row="6"
                Maximum="2500"
                Minimum="250"
                ValueChanged="OnSliderValueChanged" />

        <Label Grid.Row="7"
               Text="{Binding Source={x:Reference depthSlider},
                              Path=Value,
                              StringFormat='Depth = {0:F0}'}" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="8"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

请注意，`depthSlider`使用初始化`Minimum`250 的值。 这意味着此处要轮替的二维对象有限制为由沿原点 250 像素半径定义圆形的 X 和 Y 坐标。 此对象在三维空间中任何旋转始终会小于 250 坐标值。

[ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs)位图为 300 像素正方形中的代码隐藏文件加载：

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
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

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

如果三维转换位于此位图，然后 X 和 Y 坐标 –150 和 150，之间的范围时则角为 212 像素中心中，从所有这些内容都的 250 像素半径范围之内。

`PaintSurface`处理程序创建`SKMatrix44`对象基于滑块，并将其在一起使用相乘`PostConcat`。 `SKMatrix`值从最终提取`SKMatrix44`括在对象通过将转换要中心屏幕的中心中旋转转换：

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
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

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Use 3D matrix for 3D rotations and perspective
        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, (float)xRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, (float)yRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, (float)zRotateSlider.Value));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / (float)depthSlider.Value;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the bitmap
        canvas.SetMatrix(matrix);
        float xBitmap = xCenter - bitmap.Width / 2;
        float yBitmap = yCenter - bitmap.Height / 2;
        canvas.DrawBitmap(bitmap, xBitmap, yBitmap);
    }
}
```

使用第四个滑块进行试验，你会注意到不同的深度设置不移动进一步离开查看器中，该对象，但改为 alter 透视影响的程度：

[![](3d-rotation-images/rotation3d-small.png "三重的旋转 3D 页的屏幕截图")](3d-rotation-images/rotation3d-large.png#lightbox "旋转 3D 页面的三个屏幕截图")

**进行动画处理旋转三维**还使用`SKMatrix44`要进行动画处理在三维空间中的文本字符串。 `textPaint`对象设置为一个字段的构造函数中使用以确定文本的边界：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    SKCanvasView canvasView;
    float xRotationDegrees, yRotationDegrees, zRotationDegrees;
    string text = "SkiaSharp";
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        TextSize = 100,
        StrokeWidth = 3,
    };
    SKRect textBounds;

    public AnimatedRotation3DPage()
    {
        Title = "Animated Rotation 3D";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Measure the text
        textPaint.MeasureText(text, ref textBounds);
    }
    ...
}
```

`OnAppearing`替代定义三个 Xamarin.Forms`Animation`对象要进行动画处理`xRotationDegrees`， `yRotationDegrees`，和`zRotationDegrees`字段以不同的速率。 请注意，这些动画的段设置为优化数字-5 秒，7 秒和 11 秒 — 因此总体组合仅重复每个 385 秒或超过 10 分钟：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();

        new Animation((value) => xRotationDegrees = 360 * (float)value).
            Commit(this, "xRotationAnimation", length: 5000, repeat: () => true);

        new Animation((value) => yRotationDegrees = 360 * (float)value).
            Commit(this, "yRotationAnimation", length: 7000, repeat: () => true);

        new Animation((value) =>
        {
            zRotationDegrees = 360 * (float)value;
            canvasView.InvalidateSurface();
        }).Commit(this, "zRotationAnimation", length: 11000, repeat: () => true);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        this.AbortAnimation("xRotationAnimation");
        this.AbortAnimation("yRotationAnimation");
        this.AbortAnimation("zRotationAnimation");
    }
    ...
}
```

如下所示的原程序`PaintCanvas`处理程序创建`SKMatrix44`轮换和角度来看，值并相乘它们：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Scale so text fits
        float scale = Math.Min(info.Width / textBounds.Width,
                               info.Height / textBounds.Height);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(scale, scale));

        // Calculate composite 3D transforms
        float depth = 0.75f * scale * textBounds.Width;

        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, xRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, yRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, zRotationDegrees));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / depth;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the text
        canvas.SetMatrix(matrix);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;
        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

此三维旋转是用了几种二维转换将旋转中心的移到该屏幕的中心和缩放的文本字符串的大小，以便它在屏幕的宽度相同括起来：

[![](3d-rotation-images/animatedrotation3d-small.png "三重的动画旋转 3D 页的屏幕截图")](3d-rotation-images/animatedrotation3d-large.png#lightbox "三重的动画旋转 3D 页的屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
