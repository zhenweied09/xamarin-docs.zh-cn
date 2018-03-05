---
title: "触摸操作"
description: "使用矩阵转换来实现触摸拖动、 挤压，和旋转"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: b418e0179c95a424c88d5f5063a09f984bb13ec0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="touch-manipulations"></a>触摸操作

_使用矩阵转换来实现触摸拖动、 挤压，和旋转_

在如在移动设备上的多点触控环境，用户通常用于其指操作在屏幕上的对象。 一个手指拖和两个手指捏合等常见笔势可以移动和扩展对象，或甚至旋转它们。 通常使用转换矩阵，实现这些笔势和这篇文章演示如何执行该操作。

![](touch-images/touchmanipulationsexample.png "经过平移、 缩放和旋转位图")

## <a name="manipulating-one-bitmap"></a>操作一个位图

**Touch 操作**页演示单个位图上的触摸屏输入操作。
此示例使用文章中提供的触摸跟踪效果[将事件调用从效果](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。

其他几个文件都提供支持**Touch 操作**页。 第一种是[ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs)枚举，指示不同类型的触摸屏操作由你将只能看到代码实现：

```csharp
enum TouchManipulationMode
{
    None,
    PanOnly,
    IsotropicScale,     // includes panning
    AnisotropicScale,   // includes panning
    ScaleRotate,        // implies isotropic scaling
    ScaleDualRotate     // adds one-finger rotation
}
```

`PanOnly` 是通过翻译实现一个手指拖动。 所有后续的选项也包括平移但涉及两个手指：`IsotropicScale`是捏合操作导致缩放同样在水平和垂直方向的对象。 `AnisotropicScale` 允许不相等缩放。

`ScaleRotate`选项适用于两个手指缩放和旋转。 缩放是各向同性。 由于手指动作数实质上是相同，则实现与各向异性缩放的两个手指旋转会产生问题。

`ScaleDualRotate`选项将添加一个手指旋转。 当为单指将拖动的对象时，拖动的对象首先转动围绕其中心，以便对拖动矢量排列对象的中心。

[ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml)文件包括`Picker`与成员`TouchManipulationMode`枚举：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.TouchManipulationPage"
             Title="Touch Manipulation">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker Title="Touch Mode"
                Grid.Row="0"
                SelectedIndexChanged="OnTouchModePickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>None</x:String>
                <x:String>PanOnly</x:String>
                <x:String>IsotropicScale</x:String>
                <x:String>AnisotropicScale</x:String>
                <x:String>ScaleRotate</x:String>
                <x:String>ScaleDualRotate</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                4
            </Picker.SelectedIndex>
        </Picker>

        <Grid BackgroundColor="White"
              Grid.Row="1">

            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>
    </Grid>
</ContentPage>
```

底部是`SKCanvasView`和`TouchEffect`附加到单个单元格`Grid`包含它。

[ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs)代码隐藏文件具有`bitmap`字段，但它不属于类型`SKBitmap`。 类型是`TouchManipulationBitmap`（很快就会看到一个类）：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    TouchManipulationBitmap bitmap;
    ...

    public TouchManipulationPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.MountainClimbers.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            SKBitmap bitmap = SKBitmap.Decode(skStream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

构造函数实例化`TouchManipulationBitmap`对象传递给构造函数`SKBitmap`获取从嵌入的资源。 构造函数通过设置到结束`Mode`属性`TouchManager`属性`TouchManipulationBitmap`对象的成员`TouchManipulationMode`枚举。

`SelectedIndexChanged`处理程序`Picker`还将此上限设置`Mode`属性：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            TouchManipulationMode mode;
            Enum.TryParse(picker.Items[picker.SelectedIndex], out mode);
            bitmap.TouchManager.Mode = mode;
        }
    }
    ...
}
```

`TouchAction`处理程序`TouchEffect`在 XAML 文件调用中实例化两个方法中的`TouchManipulationBitmap`名为`HitTest`和`ProcessTouchEvent`:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    List<long> touchIds = new List<long>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (bitmap.HitTest(point))
                {
                    touchIds.Add(args.Id);
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    break;
                }
                break;

            case TouchActionType.Moved:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    touchIds.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

如果`HitTest`方法返回`true`& #x 2014年; 表示为指具有接触由位图 & #x 2014; 占用的区域中，屏幕然后 touch ID 添加到`TouchIds`集合。 上方的手指从屏幕提起之前，此 ID 表示该手指触摸事件序列。 如果多个手指触摸位图，则`touchIds`集合包含针对每个手指触摸 ID。

`TouchAction`处理程序还会调用`ProcessTouchEvent`类`TouchManipulationBitmap`。 这就是某些 （而非全部） 的真实触摸进行处理。

[ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs)类是用于的包装类`SKBitmap`，其中包含代码来呈现位图并处理触控事件。 它结合了更多通用化中的代码`TouchManipulationManager`类 （该类很快就会看到）。

`TouchManipulationBitmap`构造函数将保存`SKBitmap`和实例化两个属性，`TouchManager`类型的属性`TouchManipulationManager`和`Matrix`类型的属性`SKMatrix`:

```csharp
class TouchManipulationBitmap
{
    SKBitmap bitmap;
    ...

    public TouchManipulationBitmap(SKBitmap bitmap)
    {
        this.bitmap = bitmap;
        Matrix = SKMatrix.MakeIdentity();

        TouchManager = new TouchManipulationManager
        {
            Mode = TouchManipulationMode.ScaleRotate
        };
    }

    public TouchManipulationManager TouchManager { set; get; }

    public SKMatrix Matrix { set; get; }
    ...
}
```

这`Matrix`属性是累积的转换导致所有触摸活动。 如上所示，将每个触摸事件所解析的矩阵，然后与串联到`SKMatrix`存储值`Matrix`属性。

`TouchManipulationBitmap`中的对象绘制本身其`Paint`方法。 此自变量是`SKCanvas`对象。 这`SKCanvas`可能已经有一个转换应用于它，因此`Paint`方法连接`Matrix`属性与现有的转换，位图关联，并还原画布，完成后：

```csharp
class TouchManipulationBitmap
{
    ...
    public void Paint(SKCanvas canvas)
    {
        canvas.Save();
        SKMatrix matrix = Matrix;
        canvas.Concat(ref matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();
    }
    ...
}
```

`HitTest`方法返回`true`如果用户触摸屏幕在位图的点的边界内。 用户操作位图，位图可能要轮换，或者甚至 （通过各向异性缩放和旋转的组合） 为的平行四边形的形状。 你可能担心，`HitTest`方法需要在这种情况下实现相当复杂的分析几何图形。

但是，快捷方式是可用的：

确定是否已转换的矩形的边界内的某一点所在等同于确定如果反已转换的点存在于未变形的矩形的边界内。 这是更容易得多的计算，并且它可以使用的方便`Contains`方法由定义`SKRect`:

```csharp
class TouchManipulationBitmap
{
    ...
    public bool HitTest(SKPoint location)
    {
        // Invert the matrix
        SKMatrix inverseMatrix;

        if (Matrix.TryInvert(out inverseMatrix))
        {
            // Transform the point using the inverted matrix
            SKPoint transformedPoint = inverseMatrix.MapPoint(location);

            // Check if it's in the untransformed bitmap rectangle
            SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
            return rect.Contains(transformedPoint);
        }
        return false;
    }
    ...
}
```

中的第二个公共方法`TouchManipulationBitmap`是`ProcessTouchEvent`。 当调用此方法时，它已建立触摸事件属于此特定的位图。 该方法维护的字典[ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs)对象，即只需以前的点和每个手指新点：

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

下面是字典和`ProcessTouchEvent`方法本身：

```csharp
class TouchManipulationBitmap
{
    ...
    Dictionary<long, TouchManipulationInfo> touchDictionary =
        new Dictionary<long, TouchManipulationInfo>();
    ...
    public void ProcessTouchEvent(long id, TouchActionType type, SKPoint location)
    {
        switch (type)
        {
            case TouchActionType.Pressed:
                touchDictionary.Add(id, new TouchManipulationInfo
                {
                    PreviousPoint = location,
                    NewPoint = location
                });
                break;

            case TouchActionType.Moved:
                TouchManipulationInfo info = touchDictionary[id];
                info.NewPoint = location;
                Manipulate();
                info.PreviousPoint = info.NewPoint;
                break;

            case TouchActionType.Released:
                touchDictionary[id].NewPoint = location;
                Manipulate();
                touchDictionary.Remove(id);
                break;

            case TouchActionType.Cancelled:
                touchDictionary.Remove(id);
                break;
        }
    }
    ...
}
```

在`Moved`和`Released`事件、 方法调用`Manipulate`。 在这些时间`touchDictionary`包含一个或多个`TouchManipulationInfo`对象。 如果`touchDictionary`包含一个项，则很可能，`PreviousPoint`和`NewPoint`值是否不相等和表示为指的移动。 如果多个手指在触摸位图，该字典包含多个项，但这些项之一具有不同`PreviousPoint`和`NewPoint`值。 所有 rest 都具有相等`PreviousPoint`和`NewPoint`值。

这一点很重要：`Manipulate`方法可以假定它正在处理的只有一个手指移动。 此调用时在无其他指的是移动，并且如果它们实际上在迁移 （就是有可能），将在将来对的调用中处理这些动作数`Manipulate`。

`Manipulate`方法首先将字典复制到数组为方便起见。 它将忽略的前两个条目之外的任何内容。 如果两个以上指尝试以操作位图，将忽略其他成员。 `Manipulate` 是的成员，最终`TouchManipulationBitmap`:

```csharp
class TouchManipulationBitmap
{
    ...
    void Manipulate()
    {
        TouchManipulationInfo[] infos = new TouchManipulationInfo[touchDictionary.Count];
        touchDictionary.Values.CopyTo(infos, 0);
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();

        if (infos.Length == 1)
        {
            SKPoint prevPoint = infos[0].PreviousPoint;
            SKPoint newPoint = infos[0].NewPoint;
            SKPoint pivotPoint = Matrix.MapPoint(bitmap.Width / 2, bitmap.Height / 2);

            touchMatrix = TouchManager.OneFingerManipulate(prevPoint, newPoint, pivotPoint);
        }
        else if (infos.Length >= 2)
        {
            int pivotIndex = infos[0].NewPoint == infos[0].PreviousPoint ? 0 : 1;
            SKPoint pivotPoint = infos[pivotIndex].NewPoint;
            SKPoint newPoint = infos[1 - pivotIndex].NewPoint;
            SKPoint prevPoint = infos[1 - pivotIndex].PreviousPoint;

            touchMatrix = TouchManager.TwoFingerManipulate(prevPoint, newPoint, pivotPoint);
        }

        SKMatrix matrix = Matrix;
        SKMatrix.PostConcat(ref matrix, touchMatrix);
        Matrix = matrix;
    }
}
```

如果一个手指操作位图，`Manipulate`调用`OneFingerManipulate`方法`TouchManipulationManager`对象。 对于双指，它调用`TwoFingerManipulate`。 这些方法的自变量是相同：`prevPoint`和`newPoint`自变量表示正在移手指。 但`pivotPoint`自变量是不同的两个调用：

为一个手指操作`pivotPoint`是位图的中心。 这是为了允许对一个手指轮换。 对于两个手指操作事件将指明只有一个手指移动以便`pivotPoint`是不动手指。

在这两种情况下，`TouchManipulationManager`返回`SKMatrix`值，该值方法连接与当前`Matrix`属性，`TouchManipulationPage`用于呈现位图。

`TouchManipulationManager` 已通用化，并使用不含其他文件除外`TouchManipulationMode`。 你可能能够在自己的应用程序中使用而无需更改此类。 它定义类型的单个属性`TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


但是，你可能需要避免`AnisotropicScale`选项。 它是此选项以操作位图，以便其中一个缩放因素变为零得非常简单。 这使得消失不见，决不要返回的位图。 如果您真正需要各向异性缩放，你将需要增强的逻辑，以避免意外结果。

`TouchManipulationManager` 使用的平台，但由于没有任何`SKVector`中 SkiaSharp，结构`SKPoint`改为使用。 `SKPoint` 减法运算符，并且结果可以视为一个向量的支持。 添加所需的仅向量特定逻辑`Magnitude`计算：

```csharp
class TouchManipulationManager
{
    ...
    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
}
```

每当旋转已选中，这两种一个手指和两个手指操作方法首先处理旋转。 如果检测到任何旋转，则会有效地删除的旋转分量。 其余被解释为平移和缩放。

下面是`OneFingerManipulate`方法。 如果尚未启用一个手指旋转，则逻辑是简单和 #x 2014;它只需使用的上一个点和新的点来构造一个向量，名为`delta`精确对应转换。 与启用的一个手指旋转，该方法使用角度从透视点 （位图的中心） 到上一个点和新的点来构造旋转矩阵：

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }

    public SKMatrix OneFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        if (Mode == TouchManipulationMode.None)
        {
            return SKMatrix.MakeIdentity();
        }

        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint delta = newPoint - prevPoint;

        if (Mode == TouchManipulationMode.ScaleDualRotate)  // One-finger rotation
        {
            SKPoint oldVector = prevPoint - pivotPoint;
            SKPoint newVector = newPoint - pivotPoint;

            // Avoid rotation if fingers are too close to center
            if (Magnitude(newVector) > 25 && Magnitude(oldVector) > 25)
            {
                float prevAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                // Calculate rotation matrix
                float angle = newAngle - prevAngle;
                touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                // Effectively rotate the old vector
                float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                oldVector.X = magnitudeRatio * newVector.X;
                oldVector.Y = magnitudeRatio * newVector.Y;

                // Recalculate delta
                delta = newVector - oldVector;
            }
        }

        // Multiply the rotation matrix by a translation matrix
        SKMatrix.PostConcat(ref touchMatrix, SKMatrix.MakeTranslation(delta.X, delta.Y));

        return touchMatrix;
    }
    ...
}
```

在`TwoFingerManipulate`方法，透视点是不在此特定触摸事件中移动手指的位置。 旋转非常类似于一个手指旋转，然后向量的名为`oldVector`（基于上一个点） 旋转调整。 剩余移动解释为缩放：

```csharp
class TouchManipulationManager
{
    ...
    public SKMatrix TwoFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint oldVector = prevPoint - pivotPoint;
        SKPoint newVector = newPoint - pivotPoint;

        if (Mode == TouchManipulationMode.ScaleRotate ||
            Mode == TouchManipulationMode.ScaleDualRotate)
        {
            // Find angles from pivot point to touch points
            float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
            float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

            // Calculate rotation matrix
            float angle = newAngle - oldAngle;
            touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

            // Effectively rotate the old vector
            float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
            oldVector.X = magnitudeRatio * newVector.X;
            oldVector.Y = magnitudeRatio * newVector.Y;
        }

        float scaleX = 1;
        float scaleY = 1;

        if (Mode == TouchManipulationMode.AnisotropicScale)
        {
            scaleX = newVector.X / oldVector.X;
            scaleY = newVector.Y / oldVector.Y;

        }
        else if (Mode == TouchManipulationMode.IsotropicScale ||
                 Mode == TouchManipulationMode.ScaleRotate ||
                 Mode == TouchManipulationMode.ScaleDualRotate)
        {
            scaleX = scaleY = Magnitude(newVector) / Magnitude(oldVector);
        }

        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
        {
            SKMatrix.PostConcat(ref touchMatrix,
                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y));
        }

        return touchMatrix;
    }
    ...
}
```

你会注意到没有显式转换在此方法。 但是，这两个`MakeRotation`和`MakeScale`方法基于透视点，并包含隐式转换。 如果你在上位图并将它们拖相同的方向，使用双指`TouchManipulation`将获取一系列的交替双指的触摸事件。 为每个手指移动相对于其他，缩放或旋转结果，但通过其他手指的移动求反，并且结果为转换。

唯一的剩余部分**Touch 操作**页`PaintSurface`中的处理程序`TouchManipulationPage`代码隐藏文件。 这将调用`Paint`方法`TouchManipulationBitmap`，这适用于表示累积的触摸活动的矩阵：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    MatrixDisplay matrixDisplay = new MatrixDisplay();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        bitmap.Paint(canvas);

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(bitmap.Matrix);

        matrixDisplay.Paint(canvas, bitmap.Matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));
    }
}
```

`PaintSurface`处理程序最后会显示`MatrixDisplay`显示累积的触摸矩阵的对象：

[![](touch-images/touchmanipulation-small.png "三重的 Touch 操作页的屏幕截图")](touch-images/touchmanipulation-large.png "三重的 Touch 操作页的屏幕截图")

## <a name="manipulating-multiple-bitmaps"></a>处理多个位图

如隔离触摸处理代码中类的优点之一`TouchManipulationBitmap`和`TouchManipulationManager`是能够重复使用这些类允许用户用来处理多个位图的程序中。

**位图的散点图视图**页演示如何做到这一点。 而不是定义类型的字段`TouchManipulationBitmap`、 [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs)类定义`List`的位图对象：

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    List<TouchManipulationBitmap> bitmapCollection =
        new List<TouchManipulationBitmap>();
    ...
    public BitmapScatterViewPage()
    {
        InitializeComponent();

        // Load in all the available bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;
        string[] resourceIDs = assembly.GetManifestResourceNames();
        SKPoint position = new SKPoint();

        foreach (string resourceID in resourceIDs)
        {
            if (resourceID.EndsWith(".png") ||
                resourceID.EndsWith(".jpg"))
            {
                using (Stream stream = assembly.GetManifestResourceStream(resourceID))
                using (SKManagedStream skStream = new SKManagedStream(stream))
                {
                    SKBitmap bitmap = SKBitmap.Decode(skStream);
                    bitmapCollection.Add(new TouchManipulationBitmap(bitmap)
                    {
                        Matrix = SKMatrix.MakeTranslation(position.X, position.Y),
                    });
                    position.X += 100;
                    position.Y += 100;
                }
            }
        }
    }
    ...
}
```

构造函数在所有可用作嵌入的资源，位图中加载，并将它们添加到`bitmapCollection`。 请注意，`Matrix`属性初始化每个上`TouchManipulationBitmap`对象，因此每个位图的左上角偏移 x 100 像素。

`BitmapScatterView`页还需要处理多个位图的触控事件。 而不是定义`List`的 touch Id 的当前操作`TouchManipulationBitmap`对象，此程序需要一个字典：

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    Dictionary<long, TouchManipulationBitmap> bitmapDictionary =
       new Dictionary<long, TouchManipulationBitmap>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                for (int i = bitmapCollection.Count - 1; i >= 0; i--)
                {
                    TouchManipulationBitmap bitmap = bitmapCollection[i];

                    if (bitmap.HitTest(point))
                    {
                        // Move bitmap to end of collection
                        bitmapCollection.Remove(bitmap);
                        bitmapCollection.Add(bitmap);

                        // Do the touch processing
                        bitmapDictionary.Add(args.Id, bitmap);
                        bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                        canvasView.InvalidateSurface();
                        break;
                    }
                }
                break;

            case TouchActionType.Moved:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    bitmapDictionary.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

请注意如何`Pressed`逻辑循环访问`bitmapCollection`按相反的顺序。 位图通常相互重叠。 在集合中的更早版本的位图之上以可视方式位于集合中的更高版本的位图。 如果有多个位图在屏幕按下的手指下，最顶层的一个必须是由该手指操作的一个。

另请注意，`Pressed`逻辑将移动该位图到集合的末尾，以便直观地移到顶部的其他位图的超时和累积。

在`Moved`和`Released`事件，`TouchAction`处理程序调用`ProcessingTouchEvent`中的方法`TouchManipulationBitmap`就像早期版本的程序。

最后，`PaintSurface`处理程序调用`Paint`每个`TouchManipulationBitmap`对象：

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (TouchManipulationBitmap bitmap in bitmapCollection)
        {
            bitmap.Paint(canvas);
        }
    }
}
```

代码循环访问集合，并显示从集合的开头到末尾的位图的超时和累积：

[![](touch-images/bitmapscatterview-small.png "三重的位图的散点图视图页的屏幕截图")](touch-images/bitmapscatterview-large.png "位图的散点图视图页的三个屏幕截图")


## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
- [调用从效果的事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
