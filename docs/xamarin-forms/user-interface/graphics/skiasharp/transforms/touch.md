---
title: 触摸操作
description: 本文介绍了如何使用矩阵转换来实现拖动触摸、 收缩手势，和旋转，并且此示例代码进行了演示。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: charlespetzold
ms.author: chape
ms.date: 04/03/2018
ms.openlocfilehash: 2de5b9a3a6bf0d36330212a52ba5c7278b970efc
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130902"
---
# <a name="touch-manipulations"></a>触摸操作

_使用矩阵转换来实现拖动触摸、 收缩手势，和旋转_

在如那些移动设备上的多点触控环境，用户通常使用其根手指操作在屏幕上的对象。 单指拖放两个手指 pinch 等常见笔势可以移动和缩放对象，或甚至旋转。 通常使用转换矩阵，实现这些手势和本文介绍如何执行该操作。

![](touch-images/touchmanipulationsexample.png "受制于平移、 缩放和旋转位图")

## <a name="manipulating-one-bitmap"></a>处理一个位图

**触摸操作**页演示在单个位图上的触摸操作。
此示例使用一文中提供的点触控跟踪影响[效果从调用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。

其他几个文件提供的支持**触摸操作**页。 第一个是[ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs)枚举，指示不同类型的点触控操作由你将看到的代码实现：

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

`PanOnly` 是个单指的通过翻译实现的。 所有后续选项还包括平移，但涉及到两根手指：`IsotropicScale`是在水平和垂直方向上均匀缩放的对象会导致在收缩操作。 `AnisotropicScale` 允许不相等的缩放。

`ScaleRotate`选项适用于两个手指缩放和旋转。 缩放是增益。 因为手指移动实质上是相同，则实现两个手指旋转各向异性缩放会产生问题。

`ScaleDualRotate`选项添加了单指旋转。 单指拖动对象，拖动的对象将第一次循环使用围绕其中心，以便对象的中心行用拖放的向量。

[ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml)文件包括`Picker`与的成员`TouchManipulationMode`枚举：

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

底部是`SKCanvasView`和一个`TouchEffect`附加到单个单元格`Grid`包含它。

[ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs)代码隐藏文件具有`bitmap`字段，但它不属于类型`SKBitmap`。 该类型是`TouchManipulationBitmap`（稍后您将看到一个类）：

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
        {
            SKBitmap bitmap = SKBitmap.Decode(stream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

构造函数实例化`TouchManipulationBitmap`对象，将传递到构造函数`SKBitmap`获取从嵌入的资源。 最后，通过设置构造函数`Mode`的属性`TouchManager`属性`TouchManipulationBitmap`对象的成员`TouchManipulationMode`枚举。

`SelectedIndexChanged`处理程序`Picker`还会设置此`Mode`属性：

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

`TouchAction`处理程序`TouchEffect`中的两个方法中的 XAML 文件调用实例化`TouchManipulationBitmap`名为`HitTest`和`ProcessTouchEvent`:

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

如果`HitTest`方法将返回`true`&mdash;这意味着，手指接触过位图占用的区域内屏幕&mdash;则 touch ID 添加到`TouchIds`集合。 从屏幕的手指提起之前，此 ID 表示这根手指的触控事件的顺序。 如果多个手指触摸位图，则`touchIds`集合包含与每个手指的触控 ID。

`TouchAction`处理程序还会调用`ProcessTouchEvent`类中`TouchManipulationBitmap`。 这就是某些 （而不是所有） 的真实触控进行处理。

[ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs)类是一个包装类`SKBitmap`，其中包含代码来呈现位图和处理触控事件。 它结合了更多通用的代码中的`TouchManipulationManager`类 （该类稍后您将看到）。

`TouchManipulationBitmap`构造函数保存`SKBitmap`并实例化两个属性`TouchManager`类型的属性`TouchManipulationManager`并`Matrix`类型的属性`SKMatrix`:

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

这`Matrix`属性是从所有触摸活动生成累积的转换。 正如您将看到，每个触摸事件解析到矩阵，然后加`SKMatrix`存储的数值`Matrix`属性。

`TouchManipulationBitmap`中的对象绘制本身及其`Paint`方法。 参数是`SKCanvas`对象。 这`SKCanvas`可能已经有转换应用到它，因此`Paint`方法连接`Matrix`属性与现有的转换，位图关联并完成后还原画布：

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

`HitTest`方法将返回`true`如果在用户触摸屏幕上的位图的边界内的点。 根据用户操作位图，位图可能旋转角度，或甚至 （通过各向异性缩放和旋转的组合） 在一个平行四边形的形状。 你可能会担心的`HitTest`方法需要在这种情况下实现相当复杂的分析几何。

但是的快捷方式位于：

确定一个点所在的已转换的矩形边界内是否等同于确定是否反转已转换的点位于未转换的矩形的边界内。 这就是更容易得多的计算和可以使用便利`Contains`方法定义的`SKRect`:

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

中的第二个公共方法`TouchManipulationBitmap`是`ProcessTouchEvent`。 调用此方法时，它已建立的触摸事件属于此特定的位图。 该方法维护的字典[ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs)对象，它是只需在上一个点和每个手指的新点：

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

在中`Moved`并`Released`事件、 方法调用`Manipulate`。 在这些时间`touchDictionary`包含一个或多个`TouchManipulationInfo`对象。 如果`touchDictionary`包含一个项，很可能该`PreviousPoint`和`NewPoint`值是否不相等，并表示一个手指的移动。 如果多个手指触摸位图，字典包含多个项，但只有其中一项具有不同`PreviousPoint`和`NewPoint`值。 所有其余部分具有相等`PreviousPoint`和`NewPoint`值。

这一点很重要：`Manipulate`方法可以假设它正在处理的只用一个手指移动。 在此调用时没有其他手指正在移动，和如果他们真的在迁移 （如有可能），将在未来调用处理这些动作`Manipulate`。

`Manipulate`方法首先将字典复制到数组为方便起见。 它会忽略前两个条目之外的任何内容。 如果两个以上手指尝试以操作位图，其他人将被忽略。 `Manipulate` 是的最后一个成员`TouchManipulationBitmap`:

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

如果一个手指操作位图`Manipulate`调用`OneFingerManipulate`方法的`TouchManipulationManager`对象。 对于两个手指，它调用`TwoFingerManipulate`。 这些方法的参数是相同：`prevPoint`和`newPoint`参数表示正在移动的手指。 但`pivotPoint`参数是不同的两个调用：

单指操作时，`pivotPoint`是位图的中心。 这是为了允许单指旋转。 两个手指操作时，该事件指示只用一个手指的移动，以便`pivotPoint`是不动的手指。

在这两种情况下，`TouchManipulationManager`将返回`SKMatrix`值，该方法将连接与当前`Matrix`属性的`TouchManipulationPage`使用它们来呈现位图。

`TouchManipulationManager` 已通用化，并不使用除以外的任何其他文件`TouchManipulationMode`。 您可能能够在自己的应用程序中使用而无需更改此类。 它定义类型的单个属性`TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


但是，您可能需要避免`AnisotropicScale`选项。 可使用此选项以操作位图，以便其中一个比例因子变为零非常轻松。 这使消失不见，永远不会以返回的位图。 如果您真正需要各向异性缩放，你需要增强的逻辑，以免意外的结果。

`TouchManipulationManager` 使用的矢量，但由于没有任何`SKVector`SkiaSharp 中的结构`SKPoint`改为使用。 `SKPoint` 减法运算符和结果可将其视为一个向量的支持。 添加所需的仅特定于矢量的逻辑是`Magnitude`计算：

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

只要已选中旋转，这两种单指和两个手指操作方法将先处理旋转。 如果检测到任何旋转，则会有效地删除的旋转分量。 剩下被解释为平移和缩放。

下面是`OneFingerManipulate`方法。 如果尚未启用单指旋转，则逻辑是简单&mdash;它只是使用以前的点和新的点来构造一个向量，名为`delta`精确对应翻译。 使用启用了单指旋转，该方法使用角度从轴点 （位图的中心） 到以前的点和新的点来构造旋转矩阵：

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

在`TwoFingerManipulate`方法中，透视点是不在此特定触控事件中移动手指的位置。 旋转是非常类似于单指旋转，然后将向量的名为`oldVector`（基于以前的点） 调整旋转。 剩余移动被解释为缩放：

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

您会注意到没有显式转换在这种方法。 但是，这两个`MakeRotation`和`MakeScale`方法基于透视点，并包括隐式转换。 如果您使用位图并将它们拖向相同方向上的两根手指`TouchManipulation`将获得一系列的交替使用两根手指的触控事件。 为每个手指移动相对于其他，缩放或旋转的结果，但它不起作用的另一个手指的移动，结果为转换。

唯一的剩余部分**触摸操作**页面`PaintSurface`处理程序中的`TouchManipulationPage`代码隐藏文件。 这将调用`Paint`方法的`TouchManipulationBitmap`，应用该矩阵，表示累积的触摸活动：

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

`PaintSurface`处理程序最后会显示`MatrixDisplay`对象显示累计的触摸矩阵：

[![](touch-images/touchmanipulation-small.png "触摸操作页的三个屏幕截图")](touch-images/touchmanipulation-large.png#lightbox "的触摸操作页的三个屏幕截图")

## <a name="manipulating-multiple-bitmaps"></a>操作多个位图

如隔离类中的触摸处理代码的优势之一`TouchManipulationBitmap`和`TouchManipulationManager`是能够重复使用这些类允许用户用来处理多个位图的程序中。

**位图散点视图**页说明如何执行此操作。 而不是定义类型的字段`TouchManipulationBitmap`，则[ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs)类定义`List`的位图对象：

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
                {
                    SKBitmap bitmap = SKBitmap.Decode(stream);
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

构造函数在所有可作为嵌入的资源，位图中加载，并将它们添加到`bitmapCollection`。 请注意，`Matrix`属性初始化每个`TouchManipulationBitmap`对象，因此每个位图的左上角 x 100 像素偏移。

`BitmapScatterView`页还需要处理多个位图的触控事件。 而不是定义`List`触摸的 Id 的当前操作`TouchManipulationBitmap`对象，此程序需要一个字典：

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

请注意如何`Pressed`逻辑遍历`bitmapCollection`按相反的顺序。 位图通常相互重叠。 集合中的更高版本的位图以可视方式堆叠前面在集合中的位图。 如果有多个位图上方的手指在屏幕按下下，最顶层的一个必须是由该手指操作的那个。

另请注意，`Pressed`逻辑将移动该位图到集合末尾，以便它直观地移到其他位图的超时和累积的顶部。

在中`Moved`并`Released`事件，`TouchAction`处理程序调用`ProcessingTouchEvent`中的方法`TouchManipulationBitmap`就像早期版本的程序。

最后，`PaintSurface`处理程序调用`Paint`方法的每个`TouchManipulationBitmap`对象：

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

代码循环访问集合并显示从集合的开头到末尾的位图的超时和累积：

[![](touch-images/bitmapscatterview-small.png "三重位图散点图视图页屏幕截图")](touch-images/bitmapscatterview-large.png#lightbox "的位图的散点视图页的三个屏幕截图")

## <a name="single-finger-scaling"></a>单指缩放

缩放操作通常需要使用两根手指做出缩小手势。 但是，就可以实现使用单指通过手指移动的位图的边角进行缩放。

了这一点**单个手指角规模**页。 因为此示例使用的略有不同类型的扩展，在中实现`TouchManipulationManager`类，它不使用该类或`TouchManipulationBitmap`类。 相反，所有触摸逻辑都是在代码隐藏文件中。 这是比往常稍微简单一些逻辑，因为它会跟踪一次只用一个手指并只需将忽略任何可能触摸屏幕的第二根手指。

[ **SingleFingerCornerScale.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml)页实例化`SKCanvasView`类，并创建`TouchEffect`跟踪触控事件的对象：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.SingleFingerCornerScalePage"
             Title="Single Finger Corner Scale">

    <Grid BackgroundColor="White"
          Grid.Row="1">

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction"   />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

[ **SingleFingerCornerScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs)文件加载位图资源从**媒体**目录，并显示其使用`SKMatrix`对象定义为字段：

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();
    ···

    public SingleFingerCornerScalePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.SetMatrix(currentMatrix);
        canvas.DrawBitmap(bitmap, 0, 0);
    }
    ···
}
```

这`SKMatrix`触摸逻辑如下所示修改对象。

代码隐藏文件的其余部分是`TouchEffect`事件处理程序。 首先，它将转换到手指当前位置`SKPoint`值。 有关`Pressed`操作类型处理程序进行检查，没有其他手指触摸屏幕，并且手指为位图的边界内。

代码的重要部分是`if`涉及到两个调用语句`Math.Pow`方法。 此数学检查是否手指位置是外部填充位图的椭圆。 如果是这样，然后，为缩放操作。 手指未一个位图，角附近，可以确定透视点是相反的角。 如果手指位于该椭圆，它是常规的移动操作：

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();

    // Information for translating and scaling
    long? touchId = null;
    SKPoint pressedLocation;
    SKMatrix pressedMatrix;

    // Information for scaling
    bool isScaling;
    SKPoint pivotPoint;
    ···

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
                // Track only one finger
                if (touchId.HasValue)
                    return;

                // Check if the finger is within the boundaries of the bitmap
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = currentMatrix.MapRect(rect);
                if (!rect.Contains(point))
                    return;

                // First assume there will be no scaling
                isScaling = false;

                // If touch is outside interior ellipse, make this a scaling operation
                if (Math.Pow((point.X - rect.MidX) / (rect.Width / 2), 2) +
                    Math.Pow((point.Y - rect.MidY) / (rect.Height / 2), 2) > 1)
                {
                    isScaling = true;
                    float xPivot = point.X < rect.MidX ? rect.Right : rect.Left;
                    float yPivot = point.Y < rect.MidY ? rect.Bottom : rect.Top;
                    pivotPoint = new SKPoint(xPivot, yPivot);
                }

                // Common for either pan or scale
                touchId = args.Id;
                pressedLocation = point;
                pressedMatrix = currentMatrix;
                break;

            case TouchActionType.Moved:
                if (!touchId.HasValue || args.Id != touchId.Value)
                    return;

                SKMatrix matrix = SKMatrix.MakeIdentity();

                // Translating
                if (!isScaling)
                {
                    SKPoint delta = point - pressedLocation;
                    matrix = SKMatrix.MakeTranslation(delta.X, delta.Y);
                }
                // Scaling
                else
                {
                    float scaleX = (point.X - pivotPoint.X) / (pressedLocation.X - pivotPoint.X);
                    float scaleY = (point.Y - pivotPoint.Y) / (pressedLocation.Y - pivotPoint.Y);
                    matrix = SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);
                }

                // Concatenate the matrices
                SKMatrix.PreConcat(ref matrix, pressedMatrix);
                currentMatrix = matrix;
                canvasView.InvalidateSurface();
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = null;
                break;
        }
    }
}
```

`Moved`操作类型计算从手指按下此时间屏幕的时间与触控活动相对应的矩阵。 它将连接该矩阵具有矩阵实际上在手指首次按下的位图时。 在缩放操作始终是相对于相对手指触摸的角。

对于小型或外型位图内部椭圆可能会占用大部分位图，并保留在各个角来缩放位图非常小的区域。 你可能倾向在某种程度上不同的方法，在这种情况下，您可以替换为该整个`if`设置的块`isScaling`到`true`使用以下代码：

```csharp
float halfHeight = rect.Height / 2;
float halfWidth = rect.Width / 2;

// Top half of bitmap
if (point.Y < rect.MidY)
{
    float yRelative = (point.Y - rect.Top) / halfHeight;

    // Upper-left corner
    if (point.X < rect.MidX - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Bottom);
    }
    // Upper-right corner
    else if (point.X > rect.MidX + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Bottom);
    }
}
// Bottom half of bitmap
else
{
    float yRelative = (point.Y - rect.MidY) / halfHeight;

    // Lower-left corner
    if (point.X < rect.Left + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Top);
    }
    // Lower-right corner
    else if (point.X > rect.Right - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Top);
    }
}
```

此代码有效地划分到内部菱形形状位图的区域和四个角的三角形。 这允许在各个角来获取和缩放位图太多更大的区域。

## <a name="related-links"></a>相关链接

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （示例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [调用效果中的事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
