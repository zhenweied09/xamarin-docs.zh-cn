---
title: 在 MonoGame 3D 坐标
description: 了解 3D 坐标系统是开发 3D 游戏中的一个重要步骤。 MonoGame 提供多种定位、 定向，和缩放在三维空间中的对象类。
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: e3538efef107778397bd8c799bdd63eb6c2f3de3
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="3d-coordinates-in-monogame"></a>在 MonoGame 3D 坐标

_了解 3D 坐标系统是开发 3D 游戏中的一个重要步骤。MonoGame 提供多种定位、 定向，和缩放在三维空间中的对象类。_

开发 3D 游戏需要了解如何操作的 3D 坐标系统中的对象。 本演练中将介绍如何操作视觉对象 （专门的模型）。 我们将生成有关控制一个模型来创建一个三维相机类的概念。

介绍的概念源自线性代数，但我们来看一个实用的方法，以便强数学后台没有任何用户能够将应用这些概念进行了其自己的游戏。

我们将讨论以下主题：

- 创建项目
- 创建机器人实体
- 移动机器人实体
- 矩阵乘法
- 创建相机实体
- 移动与输入相机

完成后，我们将在一个圆形和照相机可以受触摸屏输入中移动是机器人具有项目：

![](part3-images/image1.gif "应用程序完成后，将包括在一个圆形和照相机可以受触摸屏输入中移动是机器人具有的项目")


## <a name="creating-a-project"></a>创建项目

本演练重点介绍在三维空间中移动对象。 我们将首先处理用于呈现模型和顶点数组项目[这可在此处找到](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)。 下载完成后，解压缩和打开项目后，若要确保其运行，并且我们应看到以下：

![](part3-images/image2.png "下载完成后，解压缩和打开项目后，若要确保其运行，并且应显示此视图")


## <a name="creating-a-robot-entity"></a>创建机器人实体

我们开始移动围绕我们机器人之前，我们将创建`Robot`类包含用于绘制和移动逻辑。 游戏开发人员参考的逻辑和数据作为此封装*实体*。

添加新的空类文件与**MonoGame3D**可移植类库 (不特定于平台的 ModelAndVerts.Android)。 将其命名为**机器人**单击**新建**:

![](part3-images/image3.png "将其命名为机器人并单击新建")

修改`Robot`类，如下所示：

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

`Robot`代码是实质上是中的相同代码`Game1`的绘图区域`Model`。 有关查看上`Model`加载和绘制，请参阅[上使用的模型本指南](~/graphics-games/monogame/3d/part1.md)。 我们现在可以删除所有`Model`加载和呈现代码从`Game1`，并将其替换`Robot`实例：

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
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
    }
}
```

如果我们运行代码，现在我们将有场景中的只有一个机器人主要在地板下绘制具有：

![](part3-images/image4.png "如果现在运行代码，该应用将使用只有一个机器人主要在地板下绘制显示场景")

## <a name="moving-the-robot"></a>移动机器人

现在，我们已经`Robot`类，我们可以将移动逻辑添加到机器人。 在这种情况下，我们只需将使在一个圆周按照游戏时间中移动机器人。 由于字符可能通常响应以输入或人工智能，但它提供用于我们来浏览 3D 定位和旋转的环境，这是实际的游戏的有点不切实际实现。

我们需要从外部的唯一信息`Robot`类是当前的游戏时间。 我们将添加`Update`方法，这样将会转`GameTime`参数。 这`GameTime`参数将用于递增一个角度变量，我们将使用以确定自动机最后一个位置。

首先，我们将添加到角度字段`Robot`类下`model`字段：

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 现在我们可以增加此值在`Update`函数：

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

我们需要确保`Update`方法调用从`Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

当然，此时角度字段不执行任何操作 – 我们需要编写代码以使用它。 我们将通过修改`Draw`方法，以便我们可以计算世界`Matrix`专用方法中： 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

接下来，我们将实施`GetWorldMatrix`中的方法`Robot`类：

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

运行此代码的结果将导致移动在一个圆周中自动机：

![](part3-images/image5.gif "在移动在一个圆周中自动机中运行此代码结果")

## <a name="matrix-multiplication"></a>矩阵乘法

上面的代码将机器人旋转创建`Matrix`中`GetWorldMatrix`方法。 `Matrix`结构包含可以用于平移 （集位置）、 旋转和缩放 16 的浮点值 （设置大小）。 当我们分配`effect.World`属性，我们将指示基础呈现系统如何定位、 大小和方向任何我们发生这种情况进行绘制 (`Model`或从顶点的几何图形)。 

幸运的是，`Matrix`结构包括大量简化的常见类型的矩阵创建的方法。 在上面的代码中使用的第一个是`Matrix.CreateTranslation`。 数学术语*转换*运算结果是在点 （或在我们的示例中的模型） 是指将其从一个位置移动到另一个没有任何其他修改 （如旋转和调整大小）。 该函数使用转换的 X、 Y 和 Z 值。 如果我们查看从顶部列表中，我们场景我们`CreateTranslation`方法 （在隔离） 执行以下：

![](part3-images/image6.png "在隔离的 CreateTranslation 方法执行此操作")

我们创建的第二个矩阵的旋转矩阵就是使用`CreateRotationZ`矩阵。 这是用于创建旋转的三种方法之一：

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

每个方法通过围绕指定轴旋转创建旋转矩阵。 在本例中，我们要轮替围绕 Z 轴，"向上"箭头。 以下可帮助直观显示如何基于轴的旋转工作原理：

![](part3-images/image7.png "这可以帮助直观显示如何基于轴的旋转工作原理")

我们将使用`CreateRotationZ`角度字段后，这段时间由于增大方法我们`Update`所调用方法。 结果是，`CreateRotationZ`方法使我们自动机随着时间的推移轨道沿原点。

代码的最后一行将两个矩阵合并为一个：

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

这被称为矩阵乘法，其工作方式与正则乘法略有不同。 *乘法的可交换属性*状态乘法运算中的数字顺序不会更改结果。 即 3 * 4 相当于 4 * 3。 矩阵乘法不同，因为它不是可交换。 即，以上的行可以显示为"应用 translationMatrix 移动模型，然后通过应用 rotationMatrix 旋转的所有内容"。 我们无法可视化以上行影响的位置和旋转，如下所示的方式：

![](part3-images/image8.png "可视化效果 pf 以上的行的位置和旋转会影响的方式")

若要帮助了解矩阵乘法的顺序可能会如何影响结果，请考虑的以下内容，其中反转矩阵乘法：

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

上面的代码首先将旋转模型中的位置，然后将其转换：

![](part3-images/image9.png "上面的代码将首先旋转模型中的位置，然后将其转换")

如果我们运行倒乘法的代码，我们会注意到，因为旋转首先会将应用，只会影响模型的方向和模型的位置将保持不变。 换而言之，模型将旋转到位：

![](part3-images/image10.gif "就地旋转模型")

## <a name="creating-the-camera-entity"></a>创建相机实体

`Camera`实体将包含所有所需执行基于输入的移动，并提供用于将属性分配上的属性的逻辑`BasicEffect`类。

首先我们将实现静态照相机 （无输入基于移动），并将其集成到我们的现有项目。 添加新类中的，以便**MonoGame3D**可移植类库 (相同项目与`Robot.cs`) 并将其命名**相机**。 将此文件的内容替换为以下代码：

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

上面的代码将非常类似于从代码`Game1`和`Robot`其上分配矩阵`BasicEffect`。 

现在我们可以将集成新`Camera`到我们的现有项目的类。 首先，我们将通过修改`Robot`类才能`Camera`实例在其`Draw`方法，将消除大量的重复代码。 替换`Robot.Draw`方法替换为以下：

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

接下来，修改`Game1.cs`文件：

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

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
    }
}
```

对进行修改`Game1`的先前版本中 (其中的标识`// New camera code`) 是：

- `Camera` 中的字段 `Game1`
- `Camera` 在实例化 `Game1.Initialize`
- `Camera.Update` 调用 `Game1.Update`
- `Robot.Draw` 现在将`Camera`参数
- `Game1.Draw` 现在使用`Camera.ViewMatrix`和 `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>移动与输入相机

到目前为止，我们已添加`Camera`实体但尚未完成具有它以更改运行时行为的任何内容。 允许用户的行为，我们将添加到：

- 触摸屏幕将向左相机的左侧
- 触摸屏幕后，将向右相机右上方
- 触摸屏幕向前移动相机的中心

### <a name="making-lookat-relative"></a>使每当相对

首先我们将更新`Camera`类，以包含`angle`字段将用来将方向设置`Camera`面向。 目前，我们`Camera`确定它通过本地对着的方向`lookAtVector`，分配给`Vector3.Zero`。 换而言之，我们`Camera`始终考察原点。 如果相机移动，则还将更改面向相机的角度：

![](part3-images/image11.gif "如果相机移动，然后面向相机的角度也会更改")

我们希望`Camera`来为面向同一方向而不考虑其位置 – 至少直到我们实现轮换的逻辑`Camera`使用的输入。 第一次更改将调整`lookAtVector`变量必须基于我们当前的位置，而不是绝对位置处查找：

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

这会导致`Camera`查看直接在世界。 请注意，初始`position`值已修改为`(0, 20, 10)`因此`Camera`X 轴上居中。 运行该游戏显示：

![](part3-images/image12.png "运行该游戏将显示此视图")

### <a name="creating-an-angle-variable"></a>创建变量的角度

`lookAtVector`变量控制查看我们相机的角度。 当前它是固定的以便向负的 Y 轴下, 查看和略有向下倾斜 (从`-.5f`Z 值)。 我们将创建`angle`变量将用于调整`lookAtVector`属性。 

在本演练前面部分中，我们介绍了矩阵可以用于旋转对象绘制的方式。 我们还可用于矩阵旋转向量示`lookAtVector`使用`Vector3.Transform`方法。 

添加`angle`字段和修改`ViewMatrix`属性，如下所示：

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>读取输入

我们`Camera`通过其位置和角度变量，实体可以现在完全控制 – 我们只需根据输入更改它们。

首先，我们将获取`TouchPanel`状态以查找用户其中触摸屏幕。 有关详细信息使用`TouchPanel`类，请参阅[触摸屏 API 参考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel)。

如果用户点击上左第三个，则我们将调整`angle`值因此`Camera`左、 旋转和如果用户在右边的第三个处理，我们将旋转另一种方法。 如果用户点击中的中间第三个屏幕上，则我们来看看`Camera`转发。

首先，添加 using 语句来限定`TouchPanel`和`TouchCollection`中的类`Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

接下来，修改`Update`方法读取触摸屏和调整`angle`和`position`变量适当地：

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

现在`Camera`将响应触摸屏输入：

![](part3-images/image1.gif "现在将响应相机触摸屏输入")

更新方法首先调用`TouchPanel.GetState`，它返回收尾工作的集合。 尽管`TouchPanel.GetState`可以返回多个触摸点，我们将仅担心为简单起见的第一个。

如果用户触摸屏幕，然后此代码检查第一个触摸在左边，、 或屏幕的右第三个。 左侧和右侧折通过增加或减少旋转摄像头`angle`变量根据`TotalSeconds`值 （以便游戏的行为而不考虑帧速率相同）。

如果用户第三个处理 center 的屏幕，然后相机将向前移动。 这通过获取的向前向量，其最初定义为指向负的 Y 轴，然后使用创建的矩阵的旋转首先实现`Matrix.CreateRotationZ`和`angle`值。 最后`forwardVector`应用于`position`使用`unitsPerSecond`系数。

## <a name="summary"></a>总结

本演练介绍如何将移动和旋转`Models`在以三维形式空间使用`Matrices`和`BasicEffect.World`属性。 这种形式的移动为 3D 游戏移动对象提供了基础。 本演练还介绍了如何实现`Camera`用于查看从任何位置以及角度世界的实体。

## <a name="related-links"></a>相关链接

- [MonoGame API 链接](http://www.monogame.net/documentation/?page=api)
- [完成的项目 （示例）](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
