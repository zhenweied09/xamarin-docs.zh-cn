---
title: "与 CCDrawNode 绘制几何图形"
description: "CCDrawNode 提供用于绘制的基元对象，例如线条、 圆形和三角形方法。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: a7b62b131db3fc224ef59bdb9189b96d61129f30
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="drawing-geometry-with-ccdrawnode"></a>与 CCDrawNode 绘制几何图形

_CCDrawNode 提供用于绘制的基元对象，例如线条、 圆形和三角形方法。_

`CCDrawNode`中 CocosSharp 类提供绘制常见的几何形状的多个方法。 它继承自`CCNode`类，并通常添加到`CCLayer`实例。 本指南介绍如何使用`CCDrawNode`的实例来执行自定义的呈现。 它还具有屏幕截图和代码示例提供了可用的绘图函数的完整列表。


# <a name="creating-a-ccdrawnode"></a>创建 CCDrawNode

`CCDrawNode`类可以用于绘制圆形、 矩形和线条等的几何对象。 例如，下面的代码示例演示如何创建`CCDrawNode`中绘制圆形的实例`CCLayer`实现类：


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

此代码生成以下圆在运行时：

![](ccdrawnode-images/image1.png "此代码生成在运行时此社交圈")


# <a name="draw-method-details"></a>绘制方法详细信息

让我们看看与绘制相关的一些详细信息`CCDrawNode`:


## <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>绘图方法位置将相对到 CCDrawNode

所有绘图方法都需要至少一个位置值的绘图区域。 此位置的值是相对于`CCDrawNode`实例。 这意味着，`CCDrawNode`本身有一个位置，和所有绘制上调用`CCDrawNode`还采用一个或多个位置值。 为了帮助了解这些值的组合如何，让我们看一些示例。

首先我们将考察`DrawCircle`上面示例中：


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

在这种情况下，`CCDrawNode`将位于 (100100) 绘制的圆为 (0，0) 相对于`CCDrawNode`，这会导致正在为中心的 100 个像素，向上和向游戏屏幕的左下角的右侧圆圈，圆圈中。

`CCDrawNode`也位于原点 （的屏幕的左下角），依赖于偏移量的圆圈：


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

在 50 个单位圆的中心中的结果上面的代码 (`drawNode.PositionX` + `CCPoint.X`) 左侧的屏幕和 60 右侧 (`drawNode.PositionY` + `CCPoint.Y`) 上面屏幕底部的单位。

除非已调用 draw 方法后无法修改绘制的对象`CCDrawNode.Clear`方法调用，因此任何重定位需要进行`CCDrawNode`本身。

对象由绘制`CCNodes`也会受到影响通过`CCNode`实例的`Rotation`和`Scale`属性。


## <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>绘制方法不需要是调用每个帧

绘图方法需要只能调用一次来创建持久性视觉对象。 在示例中，调用`DrawCircle`的构造函数中`GameLayer`–`DrawCircle`不需要调用每个帧来刷新屏幕时重新绘制圆。

这不同于 MonoGame，这通常将会呈现类似以下内容的屏幕只有一个框架，并且其必须调用每个帧中的绘图方法。

如果绘图方法调用每个帧，则对象将最终累积内调用`CCDrawNode`实例，导致帧速率下降，如绘制多个对象。


## <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>每个 CCDrawNode 支持多个绘图调用

`CCDrawNode` 实例可以用于绘制多个形状。 这允许复杂 visual 对象保存在单个对象。 例如，下面的代码可以用于呈现多个圆圈，一个`CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

这将导致下图：

![](ccdrawnode-images/image2.png "这会导致此图形")


# <a name="draw-call-examples"></a>绘图调用示例

下面的绘图调用均位于`CCDrawNode`:

- [DrawCatmullRom](#DrawCatmullRom)
- [DrawCircle](#DrawCircle)
- [DrawCubicBezier](#DrawCubicBezier)
- [DrawEllipse](#DrawEllipse)
- [DrawLineList](#DrawLineList)
- [DrawPolygon](#DrawPolygon)
- [DrawQuadBezier](#DrawQuadBezier)
- [DrawRect](#DrawRect)
- [DrawSegment](#DrawSegment)
- [DrawSolidArc](#DrawSolidArc)
- [DrawSolidCircle](#DrawSolidCircle)
- [DrawTriangleList](#DrawTriangleList)


## <a name="drawcardinalspline"></a>DrawCardinalSpline

`DrawCardinalSpline` 创建通过可变数量的点线的曲线。 

`config`参数定义样条将通过传递的点。 下面的示例演示这四个点通过传递的样条。

`tension`具有陡或倒样条上的点圆角显示方式的参数控件。 A`tension`值为 0 将导致曲线样条，和一个`tension`值 1 会导致绘制的直线和硬边缘的样条。

虽然样条曲线，但 CocosSharp 绘制直线样条。 `segments`参数控制要用于绘制样条曲线的段多少。 更大的数字会导致但代价是性能的平滑曲线样条。 

代码示例：


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "段参数控制要用于绘制样条曲线的段多少")


## <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` 创建可变数量的点，类似于通过线的曲线的`DrawCardinalLine`。 此方法不包括张力参数。

代码示例：

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom 创建通过可变数量的点，类似于 DrawCardinalLine 线的曲线")


## <a name="drawcircle"></a>DrawCircle

`DrawCircle` 创建的圆的周长给定`radius`。

代码示例：

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle 创建给定半径的圆圈以外围网络")


## <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` 使用控件点设置两个点之间的路径的两个点之间绘制曲线。

代码示例：

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier 两个点之间绘制曲线")


## <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` 创建的轮廓*椭圆*，这通常称为一个椭圆 （尽管这两个不几何相同）。 可通过定义的椭圆形状`CCRect`实例。

代码示例：


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse 创建的椭圆，通常称为一个椭圆的大纲")


## <a name="drawline"></a>DrawLine

`DrawLine` 连接到给定的宽度的一条线的点。 此方法类似于是`DrawSegment`，只是它创建而不是舍入的终结点的平面终结点。

代码示例：


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine 连接到给定的宽度的一条线点")


## <a name="drawlinelist"></a>DrawLineList

`DrawLineList` 通过连接指定的点的每个对来创建多个行`CCV3F_C4B`数组。 `CCV3F_C4B`结构包含的位置和颜色值。 `verts`参数应始终包含偶数个点，由两个点定义每个行。

代码示例：


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "Verts 参数应始终包含偶数个点，由两个点定义每个行")




## <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` 用一个轮廓变量宽度和颜色的创建已填写的多边形。

代码示例：


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon 创建填入多边形用一个轮廓的可变宽度和颜色")


## <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` 一个有线条连接两个点。 其行为方式类似于`DrawCubicBezier`但只支持一个控制点。

代码示例：


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier 一条线连接两个点")


## <a name="drawrect"></a>DrawRect

`DrawRect` 用一个轮廓变量宽度和颜色的创建已填写的矩形。

代码示例： 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect 创建填入矩形用一个轮廓的可变宽度和颜色")


## <a name="drawsegment"></a>DrawSegment

`DrawSegment` 变量的宽度和颜色的线连接两个点。 它是类似于`DrawLine`，但它会创建舍入的终结点，而不是平面的终结点。

代码示例：


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment 可变宽度和颜色的线连接两个点")


## <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` 创建给定的颜色和 radius 填入的 wedge。

代码示例：


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc 创建给定的颜色和 radius 填入的 wedge")


## <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` 创建给定 radius 填入圆圈。

代码示例：


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle 所创建给定 radius 的已填写的圆形")


## <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` 创建一个三角形的列表。 每个三角形定义三个`CCV3F_C4B`数组中的实例。 数组中的顶点数传递给`verts`参数必须为三的倍数。 请注意，中包含的唯一信息`CCV3F_C4B`是 verts 和其颜色 – 的位置`DrawTriangleList`方法不支持用纹理绘制三角形。

代码示例：


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList 创建三角形的列表")


# <a name="summary"></a>摘要

本指南说明如何创建`CCDrawNode`和执行基于基元的呈现。 它提供了每个绘图调用的一个示例。

## <a name="related-links"></a>相关链接

- [CCDrawNode API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [完整示例](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
