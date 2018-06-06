---
title: 使用模型类
description: 模型类极大地简化了呈现复杂三维对象相比呈现 3D 图形的传统方法时。 从内容文件，允许进行简单集成的不包含自定义代码的内容创建模型对象。
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: e108bc6098d2c754428bf35e7312ebdc89459501
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783462"
---
# <a name="using-the-model-class"></a>使用模型类

_模型类极大地简化了呈现复杂三维对象相比呈现 3D 图形的传统方法时。从内容文件，允许进行简单集成的不包含自定义代码的内容创建模型对象。_

MonoGame API 包括`Model`类用于存储数据加载从内容文件，但用于执行呈现。 模型文件可能非常简单 （如实体的彩色三角形），或者可能包含复杂呈现，包括纹理和照明的信息。

本演练使用[是机器人三维模型](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)，其中涵盖以下内容：

- 开始一个新的游戏项目
- 为模型和其纹理创建 XNBs
- 游戏项目中包括 XNBs
- 绘制三维模型
- 绘制多个模型

完成后，我们的项目将如下所示：

![已完成的示例显示六个机器人](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>创建一个空的游戏项目

我们将需要设置第一次调用 MonoGame3D 游戏项目。 有关创建新的 MonoGame 项目的信息，请参阅[本演练创建跨平台 Monogame 项目](~/graphics-games/monogame/introduction/part1.md)。

我们应继续之前验证项目将打开，并将正确部署。 一旦部署完成，应该会看到一个空的蓝色屏幕：

![空白的蓝色游戏屏幕](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>游戏项目中包括 XNBs

.Xnb 文件格式是一个标准的生成内容扩展 (通过创建的内容[MonoGame 管道工具](http://www.monogame.net/documentation/?page=Pipeline))。 所有生成的内容的源文件 （这是在我们的模型的情况下的.fbx 文件） 和目标文件 （.xnb 文件）。 .Fbx 格式是一种常见的三维模型格式可以如由应用程序创建其[Maya](http://www.autodesk.com/products/maya/overview)和[Blender](http://www.blender.org/)。 

`Model`类可以通过从包含 3D 几何图形数据的磁盘，加载.xnb 文件构造。   通过内容项目创建此.xnb 文件。 Monogame 模板自动包括我们内容的文件夹中的 （具有扩展.mgcp 中) 的内容项目。 MonoGame 管道工具的详细讨论，请参阅[内容的管道指南](~/graphics-games/cocossharp/content-pipeline/index.md)。

本指南中我们将跳过使用 MonoGame 管道工具，将使用。此处包括的 XNB 文件。 请注意，。XNB 文件与不同，每个平台，因此请确保正确的一套 XNB 文件用于你正在使用任何平台。

我们将解压缩[Content.zip 文件](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)，以便我们可以使用包含的.xnb 文件，在我们的游戏。 如果使用 Android 项目，右键单击**资产**文件夹中的**WalkingGame.Android**项目。 如果使用 iOS 项目，右键单击**WalkingGame.iOS**项目。 选择**添加-> 添加文件...** 和您正在使用平台的文件夹中选择这两个.xnb 文件。

现在，两个文件应该是项目的我们的一部分：

![解决方案资源管理器与 xnb 文件的内容文件夹](part1-images/xnbsinxs.png)

适用于 Mac 的 visual Studio 可能不会自动设置新添加 XNBs 的生成操作。 对于 iOS，右键单击每个文件并选择**生成操作-> BundleResource**。 对于 Android，右键单击该文件并选择的每个上**生成操作-> AndroidAsset**。

## <a name="rendering-a-3d-model"></a>呈现三维模型

屏幕上查看的模型所需的最后一步是添加的加载和绘制代码。 具体而言，我们将会执行以下操作：

- 定义`Model`实例中我们`Game1`类
- 加载`Model`实例中 `Game1.LoadContent`
- 绘制`Model`实例中 `Game1.Draw`

替换`Game1.cs`代码文件 (位于**WalkingGame** PCL) 替换为以下：

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

如果我们运行此代码，我们将屏幕上查看的模型：

![显示在屏幕上的模型](part1-images/image8.png "如果运行此代码，将屏幕上显示多个模型")

### <a name="model-class"></a>模型类

`Model`类是用于从 （例如.fbx 文件） 的内容文件执行三维呈现的核心类。 它包含所有呈现，包括三维 geometry、 纹理引用所需的信息和`BasicEffect`控制定位、 照明和照相机值的实例。

`Model`类本身不直接具有定位由于单个模型实例可以呈现在多个位置，如我们将在本指南后面所示的变量。

每个`Model`由一个或多个组成`ModelMesh`实例，通过公开`Meshes`属性。 虽然我们可以考虑`Model`作为单个游戏对象 （如是机器人或一辆汽车），每个`ModelMesh`可以具有不同绘制`BasicEffect`值。 例如，单个网格部件可能代表赛程的是机器人或上一辆汽车，轮，我们可能会分配`BasicEffect`值以进行轮旋转或脚移动。 

### <a name="basiceffect-class"></a>BasicEffect 类

`BasicEffect`类提供用于控制呈现选项的属性。 我们对进行第一次修改`BasicEffect`是调用`EnableDefaultLighting`方法。 顾名思义，这使非常适用于验证的默认照明`Model`显示游戏中按预期方式。 如果我们注释掉`EnableDefaultLighting`调用，然后我们将看到呈现与只是其纹理，但没有底纹设定或反射发光的模型：

```csharp
//effect.EnableDefaultLighting ();
```

![呈现与只是其纹理，但没有底纹设定或反射发光模型](part1-images/image9.png "呈现与只是其纹理，但没有底纹设定或反射发光的模型")

`World`属性可以用于调整位置、 旋转和模型的比例。 使用上面的代码`Matrix.Identity`值，这意味着`Model`将呈现中的游戏严格按指定.fbx 文件中。 我们将矩阵和三维坐标中的更详细地讨论[第 3 部分](~/graphics-games/monogame/3d/part3.md)，但作为示例，我们可以更改的位置`Model`通过更改`World`属性，如下所示：

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

此代码将导致 3 的世界单位移动的对象：

![此代码将导致 3 的世界单位移动的对象](part1-images/image10.png "此代码将导致 3 的世界单位移动的对象")

最后两个属性上分配`BasicEffect`是`View`和`Projection`。 我们将讨论在三维摄像头[第 3 部分](~/graphics-games/monogame/3d/part3.md)，但作为示例，我们可以通过更改本地修改照相机位置`cameraPosition`变量：

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

我们可以看到相机已经移动更早，从而导致`Model`出现由于透视较小：

![照相机已移动更早，导致出现由于透视较小的模型](part1-images/image11.png "相机已移动更早，导致出现由于透视较小的模型")

## <a name="rendering-multiple-models"></a>呈现多个模型

单个上方所述`Model`可以绘制多次。 若要简化此过程我们将是移动`Model`绘制到其自己将所需的方法的代码`Model`作为参数的位置。 一次完成后，我们`Draw`和`DrawModel`方法将如下所示：


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
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
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

这会导致机器人所绘制六倍的模型：

![这会导致机器人模型所绘制六倍](part1-images/image1.png "这会导致机器人所绘制六倍的模型")

## <a name="summary"></a>总结

本演练介绍了 MonoGame 的`Model`类。 它涵盖了将.fbx 文件转换为.xnb，这可以反过来加载到`Model`类。 它还演示如何修改`BasicEffect`实例可能会影响`Model`绘制。

## <a name="related-links"></a>相关链接

- [MonoGame 模型引用](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [已完成的项目 （示例）](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)
