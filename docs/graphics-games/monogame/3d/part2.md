---
title: 在 MonoGame 中绘制具有顶点的 3D 图形
description: MonoGame 支持使用顶点的数组来定义一个三维对象上的每个点基础的呈现方式。 用户可以利用顶点数组来创建动态几何图形、 实现特殊效果，并提高通过剔除其呈现的效率。
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 4fa6929a8de19608c06690e4cf24514f6749480a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>在 MonoGame 中绘制具有顶点的 3D 图形

_MonoGame 支持使用顶点的数组来定义一个三维对象上的每个点基础的呈现方式。用户可以利用顶点数组来创建动态几何图形、 实现特殊效果，并提高通过剔除其呈现的效率。_

用户已通过阅读[上呈现模型指南](~/graphics-games/monogame/3d/part1.md)将熟悉呈现中 MonoGame 的三维模型。 `Model`类是一种可以呈现 3D 图形处理 （如.fbx)，文件中定义的数据时和静态数据在处理时有效。 有些游戏要求 3D 几何要定义或在运行时动态操作。 在这些情况下，我们可以使用的数组*顶点*定义和呈现几何图形。 顶点是用于在三维空间是用于定义几何图形的已排序列表的一部分中的点的常用术语。 通常的方式定义三角形一系列按顺序排列顶点。

为了帮助直观显示如何使用顶点来创建 3D 对象，让我们考虑以下球体：

![](part2-images/image1.png "若要帮助直观地显示如何使用顶点来创建 3D 对象，请考虑此球")

如上所示，球体由清楚地组成多个三角形。 我们可以查看球体若要查看顶点如何连接到窗体三角形线框：

![](part2-images/image2.png "查看球体若要查看顶点如何连接到窗体三角形的线框")

本演练将涉及以下主题：

- 创建项目
- 创建顶点
- 将绘制代码添加
- 用纹理的呈现
- 修改纹理坐标
- 使用模型的呈现顶点

完成的项目将包含其将使用顶点数组绘制棋盘格的层：

![](part2-images/image3.png "完成的项目将包含棋盘格的层将使用顶点数组绘制")

## <a name="creating-a-project"></a>创建项目

首先，我们将下载的项目，它将充当我们的起点。 我们将使用该模型项目[这可在此处找到](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)。

下载并解压缩后，打开并运行该项目。 我们会看到屏幕上绘制的六个机器人模型：

![](part2-images/image4.png "屏幕上绘制的六个机器人模型")

此项目的结束我们将为我们自己的自定义顶点呈现将与结合使用机器人`Model`，因此我们不会删除机器人呈现代码。 相反，我们只需要清除`Game1.Draw`调用以删除现在 6 机器人的绘制。 若要执行此操作，打开**Game1.cs**文件并找到`Draw`方法。 对其进行修改以便它包含以下代码：

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

这将导致我们的游戏中显示空的蓝色屏幕：

![](part2-images/image5.png "这将导致显示空蓝屏游戏")

## <a name="creating-the-vertices"></a>创建顶点

我们将创建顶点来定义我们几何图形的数组。 在本演练中，我们将创建一个三维平面 （在三维空间中的正方形、 不飞机）。 尽管我们平面具有四个边都与四个角，但它将由两个三角形，其中每个需要三个顶点的构成。 因此，我们将定义总数六个点。

到目前为止我们已经讨论了中的常规的意义上，顶点但 MonoGame 提供一些标准结构可以用于顶点：

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

每个类型的名称指示它包含的组件。 例如，`VertexPositionColor`包含针对位置和颜色值。 让我们看一下每个组件：

- 位置 – 所有顶点类型都包括`Position`组件。 `Position`值定义顶点所在的位置在三维空间 （X、 Y 和 Z） 中。
- 颜色 – （可选） 可以指定顶点`Color`要执行自定义、 颜色值。
- 普通 – 法线定义采用面向对象的图面哪种方式。 法线是必需如果呈现具有自方向的照明的对象图面面向的影响多少浅色它接收的。 法线通常指定为*单元向量*– 一个三维向量，其长度为 1。
- 纹理 – 纹理是指纹理坐标 – 也就是说，对纹理的哪个部分应出现在给定的顶点。 纹理值如果，则需要呈现具有纹理的三维对象。 纹理坐标是规范化的坐标，这意味着，值将介于 0 和 1 之间。 我们将介绍更详细地在本指南后面的纹理坐标。

我们平面将用作层，并且我们将需要执行我们呈现，因此我们将使用时应用纹理`VertexPositionTexture`类型来定义我们顶点。

首先，我们将向我们 Game1 类添加成员：

```csharp
VertexPositionTexture[] floorVerts; 
```

接下来，定义中的我们顶点`Game1.Initialize`。 请注意，本文前面部分中引用的提供的模板不包含`Game1.Initialize`方法，因此我们需要添加整个方法`Game1`:

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

若要帮助直观地显示我们顶点将如下所示，请考虑下图：

![](part2-images/image6.png "若要帮助可视化顶点将如下所示，请考虑此关系图")

我们需要依赖于我们的关系图来可视化顶点，直到我们完成实现我们呈现代码。

## <a name="adding-drawing-code"></a>添加绘图代码

现在，我们已为定义我们几何图形的位置，我们可以编写我们呈现代码。

首先，我们将需要定义`BasicEffect`实例将保存有关呈现如位置和照明的参数。 若要执行此操作，将添加`BasicEffect`成员`Game1`位置下面的类`floorVerts`定义字段：


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

接下来，修改`Initialize`方法用来定义产生的效果：

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
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

现在我们可以添加代码以执行绘制：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
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

我们将需要调用`DrawGround`中我们`Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

应用程序将显示以下执行时：

![](part2-images/image7.png "该应用将在执行时显示此")

让我们看看其中一些在上面的代码的详细信息。

### <a name="view-and-projection-properties"></a>视图和投影属性

`View`和`Projection`属性控制如何我们查看场景。 我们将对进行修改此代码更高版本时我们重新添加模型呈现代码。 具体而言，`View`的位置和方向相机、 控制和`Projection`控件*视野*（这可以用于缩放相机）。

### <a name="techniques-and-passes"></a>技术和传递

一次我们已分配属性我们可以执行我们效果上的实际呈现。 

我们不会更改`CurrentTechnique`在此演练中，但更高级的游戏中的属性可能会产生单个影响它们可以执行绘制 （如颜色值的应用方式） 不同的方式。 每种呈现模式下可以表示为一种方法可在呈现之前可以分配。 此外，每个方法可能需要多轮验证来正确呈现。 如果呈现复杂的视觉对象，如发光面或后部，效果可能需要多次传递。

需要牢记的重要事项在于`foreach`循环可使的同一个 C# 代码来呈现的基础复杂性无论任何效果`BasicEffect`。

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` 是呈现顶点的位置。 第一个参数告知我们组织我们顶点的方式的方法。 我们有结构化它们，这样每个三角形可以定义由三个有序的顶点，以便我们使用`PrimitiveType.TriangleList`值。

第二个参数是我们之前定义的顶点的数组。

第三个参数指定要绘制的第一个索引。 由于我们希望我们整个顶点数组要呈现，我们将传递 0 的值。

最后，我们指定多少三角形来呈现。 我们顶点数组包含两个三角形，因此通过值为 2。

## <a name="rendering-with-a-texture"></a>用纹理的呈现

此时我们的应用程序呈现 （透视） 中的白色平面。 接下来我们将添加到我们的项目以在呈现我们平面时使用的纹理。 

若要为简单起见，我们将直接向我们的项目，而不是使用 MonoGame 管道工具添加.png。 若要执行此操作，下载[此.png 文件](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)到你的计算机。 下载完成后，右键单击**内容**文件夹中的解决方案的填充和选择**添加 > 添加文件...** . 如果使用在 Android 上，然后此文件夹将位于**资产**Android 特定于项目中的文件夹。 如果在 iOS 上则此文件夹将 iOS 项目的根目录中。 导航到的位置其中**checkerboard.png**保存和选择此文件。 选择此项可将文件复制到的目录。

接下来，我们将添加代码以创建我们`Texture2D`实例。 首先，添加`Texture2D`作为的成员`Game1`下`BasicEffect`实例：

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

修改`Game1.LoadContent`，如下所示：


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

接下来，修改`DrawGround`方法。 必需的唯一修改是将分配`effect.TextureEnabled`到`true`并设置`effect.Texture`到`checkerboardTexture`:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    // new code:
    effect.TextureEnabled = true;
    effect.Texture = checkerboardTexture;

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
                    PrimitiveType.TriangleList,
            floorVerts,
            0,
            2);
    }
}
```

最后，我们需要修改`Game1.Initialize`方法还将分配纹理坐标在我们顶点上：


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

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

如果我们运行代码，我们可以看到我们平面现在显示呈现为棋盘样式：

![](part2-images/image8.png "平面现在显示呈现为棋盘样式")

## <a name="modifying-texture-coordinates"></a>修改纹理坐标

MonoGame 使用规范化为 0 和 1 之间，而不是 0 和纹理的宽度或高度之间的坐标的纹理坐标。 下图可帮助实现规范化的坐标可视化效果：

![](part2-images/image9.png "此关系图可帮助可视化规范化的坐标")

规范化的纹理坐标允许纹理调整大小而无需重新编写代码或重新创建模型 （如.fbx 文件）。 这可能是因为规范化的坐标表示比率而不是特定像素。 例如，(1，1) 将始终表示而不考虑纹理大小的右下角。

我们可以更改用于单个变量数目的重复的纹理坐标分配：


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

    int repetitions = 20;

    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
    floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

这会导致重复 20 倍的纹理：

![](part2-images/image10.png "这会导致重复 20 倍的纹理")


## <a name="rendering-vertices-with-models"></a>使用模型的呈现顶点

现在，我们平面正确呈现时，我们可以重新添加模型以查看所有内容在一起。 首先，我们将重新模型将代码添加到我们`Game1.Draw`（使用修改后的位置） 的方法：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

我们还将创建`Vector3`中`Game1`来代表我们照相机的位置。 我们将添加下的字段我们`checkerboardTexture`声明：

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

接下来，删除本地`cameraPosition`变量从`DrawModel`方法：

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

同样删除本地`cameraPosition`变量从`DrawGround`方法：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

现在如果我们运行代码我们可以看到模型和完全在同一时间：

![](part2-images/image11.png "模型和完全显示在同一时间")

如果我们修改相机位置 （如通过增加其 X 值，这在此情况下相机向左移动） 我们可以看到值会影响完全和模型：

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

此代码产生以下结果：

![](part2-images/image3.png "此代码将导致此视图")

## <a name="summary"></a>总结

本演练演示了如何使用顶点数组来执行自定义呈现。 在这种情况下，我们通过结合使用纹理我们基于顶点的呈现创建棋盘格的 floor 和`BasicEffect`，但提供的代码此处充当为任何三维呈现的基础。 我们还介绍了，可以与模型相同的场景中混合基于顶点呈现。

## <a name="related-links"></a>相关链接

- [呈现为棋盘文件 （示例）](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [已完成的项目 （示例）](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
