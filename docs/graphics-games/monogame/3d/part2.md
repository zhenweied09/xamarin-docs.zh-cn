---
title: MonoGame 中绘制 3D 图形的顶点
description: MonoGame 支持使用数组的顶点来定义三维对象在每个点的基础上的呈现方式。 用户可以利用顶点数组来创建动态的几何图形、 实现特殊效果，提高通过消除其呈现效率。
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: df36c149e98e8c0cbb16de4c2cf52def5713ec13
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121433"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>MonoGame 中绘制 3D 图形的顶点

_MonoGame 支持使用数组的顶点来定义三维对象在每个点的基础上的呈现方式。用户可以利用顶点数组来创建动态的几何图形、 实现特殊效果，提高通过消除其呈现效率。_

用户已通读[上呈现模型指南](~/graphics-games/monogame/3d/part1.md)都非常熟悉呈现中 MonoGame 的 3D 模型。 `Model`类是呈现 3D 图形时使用的数据 （如.fbx)，文件中定义和处理静态数据时的有效方法。 某些游戏要求要定义或在运行时动态操作的三维几何图形。 在这些情况下，我们可以使用数组*顶点*来定义和呈现几何图形。 顶点是在 3D 空间的一部分使用来定义 geometry 的排序列表中的点的常规术语。 通常的顶点进行排序并定义一系列的三角形的方式。

为帮助直观地显示如何使用顶点创建三维对象，让我们考虑以下球体：

![](part2-images/image1.png "若要帮助直观地显示如何使用顶点创建三维对象，请考虑此球体")

如上所示，球体是清楚地组合的多个三角形。 我们可以查看球体，若要查看顶点如何连接到窗体三角形的线框：

![](part2-images/image2.png "查看球体，若要查看顶点如何连接到窗体三角形的线框")

本演练将介绍以下主题：

- 创建项目
- 创建顶点
- 添加绘制代码
- 使用纹理呈现
- 修改纹理坐标
- 呈现模型的顶点

完成的项目将包含其将通过将顶点数组绘制越过方格形终点的地板：

![](part2-images/image3.png "完成的项目将包含其将通过将顶点数组绘制越过方格形终点的 floor")

## <a name="creating-a-project"></a>创建项目

首先，我们将下载的项目将充当我们的起点。 我们将使用该模型项目[这可在此处找到](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)。

下载并解压缩后，打开并运行项目。 我们会看到六个屏幕上绘制的机器人模型：

![](part2-images/image4.png "屏幕上绘制的六个机器人模型")

此项目的结束我们将为我们自己自定义顶点呈现将与结合使用机器人`Model`，因此我们不打算删除机器人呈现代码。 相反，我们只需清除`Game1.Draw`调用以删除现在 6 机器人的绘图。 若要执行此操作，打开**Game1.cs**文件，找到`Draw`方法。 对其进行修改使其包含以下代码：

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

这会导致我们的游戏中显示空的蓝色屏幕：

![](part2-images/image5.png "这将导致显示空蓝屏游戏")

## <a name="creating-the-vertices"></a>创建顶点

我们将创建顶点来定义我们几何图形的数组。 在本演练中，我们将创建一个三维平面 （在 3D 空间中的正方形，不飞机上）。 尽管我们平面具有四个边和四个角，但它将组成两个三角形，其中每个需要三个顶点。 因此，我们将定义共六个点。

到目前为止我们已经讨论了顶点在一般意义上，但 MonoGame 提供了一些标准结构可用于顶点：

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

每个类型的名称指示及其包含的组件。 例如，`VertexPositionColor`包含值的位置和颜色。 让我们看看每个组件：

- 位置 – 所有顶点类型都包括`Position`组件。 `Position`值定义顶点位置位于 3D 空间 （X、 Y 和 Z）。
- 颜色-可以选择指定顶点`Color`值来执行自定义着色。
- Normal – Normals 定义采用面向对象的图面哪种方式。 法向所需如果呈现带有以来方向照明的对象图面面向的影响光线接收。 法线通常指定为*单位向量*– 具有长度为 1 的三维矢量。
- 纹理 – 纹理是指纹理坐标 – 也就是说，纹理的哪个部分应出现在给定的顶点。 纹理的值为必要呈现三维纹理对象。 纹理坐标是规范化的坐标，这意味着，值将介于 0 和 1 之间。 我们将介绍在本指南后面的更多详细信息中的纹理坐标。

我们平面将用作基底，并且我们要将执行我们呈现，因此我们将使用时应用纹理`VertexPositionTexture`类型来定义我们的顶点。

首先，我们会将成员添加到我们的 Game1 类：

```csharp
VertexPositionTexture[] floorVerts; 
```

接下来，定义我们中的顶点`Game1.Initialize`。 请注意，本文前面部分中引用提供的模板不包含`Game1.Initialize`方法，因此我们需要添加整个方法`Game1`:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];
    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);
    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // We’ll be assigning texture values later
    base.Initialize ();
}
```

为帮助直观地我们顶点将如下所示，请参考以下图表：

![](part2-images/image6.png "若要帮助直观地显示顶点将如下所示，请考虑此关系图")

我们需要依赖于关系图来可视化顶点，直到我们完成实现我们的呈现代码。

## <a name="adding-drawing-code"></a>添加绘制代码

现在，我们已定义我们几何图形的位置，我们可以编写我们呈现代码。

首先，我们需要定义`BasicEffect`实例将保存呈现如位置和照明参数。 若要执行此操作，添加`BasicEffect`成员添加到`Game1`类的下`floorVerts`定义字段：


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

接下来，修改`Initialize`方法以定义效果：

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

现在我们可以添加代码来执行绘制：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
            // We’ll be rendering two trinalges
            PrimitiveType.TriangleList,
            // The array of verts that we want to render
            floorVerts,
            // The offset, which is 0 since we want to start 
            // at the beginning of the floorVerts array
            0,
            // The number of triangles to draw
            2);
    }
}
```

我们将需要调用`DrawGround`在我们`Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

应用程序将显示以下时执行：

![](part2-images/image7.png "应用程序执行时将显示此")

让我们看一下上面的代码中的详细信息。

### <a name="view-and-projection-properties"></a>视图和投影属性

`View`和`Projection`属性控制如何我们查看场景。 我们将修改此代码更高版本时我们重新添加模型呈现代码。 具体而言，`View`控制的位置和方向的照相机，并`Projection`控件*视角*（这可用于缩放照相机）。

### <a name="techniques-and-passes"></a>技术和传递

一次我们已将分配属性上我们可以执行我们效果的实际呈现。 

我们不会更改`CurrentTechnique`属性在此演练中，但更高级的游戏可能具有单个可执行 （例如，如何应用颜色值） 不同的方式绘制效果。 每种呈现模式可以表示为一种方法可在呈现之前可以分配。 此外，每种技术可能需要多次传递正确呈现。 如果呈现复杂的视觉对象，如光亮的图面或毛皮，效果可能需要多次传递。

需要记住的重要一点是，`foreach`循环使相同C#代码来呈现任何影响，而不考虑基础复杂性`BasicEffect`。

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` 是在其中呈现顶点。 第一个参数向方法告知我们组织我们顶点的方式。 我们已经构建它们，以便每个三角形定义由三个有序顶点，因此我们使用`PrimitiveType.TriangleList`值。

第二个参数是我们之前定义的顶点的数组。

第三个参数指定要绘制的第一个索引。 因为我们希望我们的整个顶点数组要呈现，我们将传递的值为 0。

最后，我们指定多少三角形来呈现。 我们的顶点数组包含两个三角形，因此传递值为 2。

## <a name="rendering-with-a-texture"></a>使用纹理呈现

此时我们的应用程序呈现白色平面 （在角度来看）。 接下来我们将添加到我们的项目以呈现我们平面时使用的纹理。 

为了简单起见，我们将直接向我们的项目，而不是使用 MonoGame 管道工具添加.png。 若要执行此操作，请下载[此.png 文件](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)到您的计算机。 下载完成后，右键单击**内容**在解决方案面板中，选择文件夹**添加 > 添加文件...** . 如果使用 Android 上，然后此文件夹将位于下面**资产**特定于 Android 的项目文件夹中。 如果在 iOS 上，则此文件夹将在 iOS 项目的根目录中。 导航到的位置， **checkerboard.png**保存和选择此文件。 选择此选项可以将文件复制到的目录。

接下来，我们将添加代码以创建我们`Texture2D`实例。 首先，添加`Texture2D`的成员`Game1`下`BasicEffect`实例：

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

修改`Game1.LoadContent`，如下所示：


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

接下来，修改`DrawGround`方法。 唯一必要的修改是将分配`effect.TextureEnabled`到`true`并设置`effect.Texture`到`checkerboardTexture`:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    // new code:
    effect.TextureEnabled = true;
    effect.Texture = checkerboardTexture;

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
                    PrimitiveType.TriangleList,
            floorVerts,
            0,
            2);
    }
}
```

最后，我们需要修改`Game1.Initialize`方法还将指定纹理坐标上我们的顶点：


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

如果我们运行此代码，我们可以看到我们平面现在显示棋盘图案：

![](part2-images/image8.png "在平面现在显示棋盘图案")

## <a name="modifying-texture-coordinates"></a>修改纹理坐标

MonoGame 使用规范化是介于 0 和 1 之间，而不是 0 和纹理的宽度或高度之间的坐标的纹理坐标。 下图可帮助可视化规范化的坐标：

![](part2-images/image9.png "此图可以帮助可视化规范化的坐标")

标准化的纹理坐标允许纹理调整大小而无需重新编写的代码或重新创建模型 （如.fbx 文件）。 这可能是因为规范化的坐标表示比率而不是特定的像素。 例如，(1，1) 将始终表示而不考虑纹理大小的右下角。

我们可以更改的纹理坐标分配要用于重复次数的单个变量：


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    int repetitions = 20;

    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
    floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

这会导致重复 20 次的纹理：

![](part2-images/image10.png "这会导致重复 20 次的纹理")


## <a name="rendering-vertices-with-models"></a>呈现模型的顶点

现在，我们平面将正确呈现，我们可以重新添加要同时查看所有内容的模型。 首先，我们将重新添加到的模型代码我们`Game1.Draw`方法 （包含已修改的位置）：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

我们还将创建`Vector3`在`Game1`来代表我们照相机的位置。 我们将添加一个字段下的我们`checkerboardTexture`声明：

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

接下来，删除本地`cameraPosition`变量从`DrawModel`方法：

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

同样删除本地`cameraPosition`变量从`DrawGround`方法：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

现在如果我们运行的代码可以看到模型和完全在同一时间：

![](part2-images/image11.png "模型和一开始显示在同一时间")

如果我们修改照相机的位置 （例如通过增加其 X 值，这在此情况下照相机向左移动），我们可以看到值会影响基础和模型：

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

此代码产生以下结果：

![](part2-images/image3.png "此代码会导致此视图")

## <a name="summary"></a>总结

本演练演示了如何使用顶点数组执行自定义呈现。 在这种情况下，我们通过结合使用纹理我们基于顶点的呈现创建越过方格形终点的 floor 和`BasicEffect`，但提供的代码此处却作为任何 3D 渲染的基础。 我们还介绍了基于顶点呈现，可以混合使用相同的场景中的模型。

## <a name="related-links"></a>相关链接

- [棋盘文件 （示例）](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [已完成的项目 （示例）](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
