---
title: "使用 UrhoSharp ARKit"
ms.topic: article
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/01/2016
ms.openlocfilehash: 4c6928af364870541581b784b7c4e9e7717eb2db
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="using-arkit-with-urhosharp"></a>使用 UrhoSharp ARKit

通过引入[ARKit](https://developer.apple.com/arkit/)，Apple 变得更加简单开发人员能够创建增强的现实应用程序。 ARKit 可以跟踪你的设备的确切位置和检测各种曲面上世界，而且很然后由开发人员 blend 来自 ARKit 到你的代码的数据。

[UrhoSharp](~/graphics-games/urhosharp/index.md)提供一个全面且易于使用的 3D API，可用于创建三维应用程序。   这些都可以为混合在一起的 ARKit 以提供有关世界各地的物理信息和 Urho 呈现结果。

此页介绍了如何连接在一起以创建出色的增强的现实应用程序这两个领域。


## <a name="the-basics"></a>基本知识

我们希望做什么是世界上的存在三维内容，通过 iPhone 所示。   思路是 blend 来自电话的相机使用三维内容的内容并在用户的电话移动周围的空间以确保三维对象的行为符合是它们的该空间的一部分-这通过锚定到此世界上的对象。

![在 ARKit 动画的图](urhosharp-images/image1.gif)


我们将使用 Urho 库加载我们三维资产并将其放在世界上和我们将使用 ARKit 以获取视频流来自照相机，以及在世界的电话的位置。   当用户使用其电话时，我们将使用在位置所做的更改来更新显示 Urho 引擎的坐标系统。

这样一来，当在三维空间中放置一个对象，并将移动用户，三维对象的位置的反映的位置和放置位置的位置。

## <a name="setting-up-your-application"></a>设置应用程序

### <a name="ios-application-launch"></a>iOS 应用程序启动

IOS 应用程序需要创建和启动三维内容，执行此操作通过创建实现的一个子类[ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/)并通过重写中提供你安装代码`Start`方法。  这是其中场景获取填充了数据，事件处理程序是安装程序，依此类推。

我们发布了`Urho.ArkitApp`类子类`Urho.Application`并在其`Start`方法执行完成的繁重任务。   你需要为你的应用程序的现有 Urho 做更改为类型的基类`Urho.ArkitApp`并且具有的应用程序将在世界中运行你 urho 场景。

### <a name="the-arkitapp-class"></a>ArkitApp 类

此类提供的方便的默认值，这两个同一场景中的某些键对象具有一组以及 ARKit 事件的处理，由于它们由操作系统不传递。

安装程序将发生在`Start`虚拟方法。   当你在你子类重写此方法时，你需要确保链接到你的家长使用`base.Start()`关注您自己的实现。

`Start`方法将设置场景、 视区、 相机和定向光，并显示这三个公共属性：

- [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/)来保存你的对象，
- 双向[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/)阴影，并且其位置可以通过使用`LightNode`属性
- [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) ARKit 将更新传递到应用程序后，将更新其组件和
- [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/)显示结果。


### <a name="your-code"></a>你的代码

然后，你需要子类化`ArkitApp`类并重写`Start`方法。   你的方法应执行的第一个操作是链达`ArkitApp.Start`通过调用`base.Start()`。  之后，你可以使用的任何属性设置 ArkitApp 通过将你的对象添加到场景，自定义灯、 阴影或你想要处理的事件。

ARKit/UrhoSharp 示例加载用纹理的动画的字符并播放动画，具有以下实现：

    ```csharp
    public class MutantDemo : ArkitApp
    {
        [Preserve]
        public MutantDemo(ApplicationOptions opts) : base(opts) { }

        Node mutantNode;

        protected override void Start()
        {
            base.Start ();

            // Mutant
            mutantNode = Scene.CreateChild();
            mutantNode.Rotation = new Quaternion(x: 0, y:15, z:0);
            mutantNode.Position = new Vector3(0, -1f, 2f); /*two meters away*/
            mutantNode.SetScale(0.5f);

            var mutant = mutantNode.CreateComponent<AnimatedModel>();
            mutant.Model = ResourceCache.GetModel("Models/Mutant.mdl");
            mutant.Material = ResourceCache.GetMaterial("Materials/mutant_M.xml");

            var animation = mutantNode.CreateComponent<AnimationController>();
            animation.Play("Animations/Mutant_HipHop1.ani", 0, true, 0.2f);
        }
    }
    ```

而这才是你所要做此时具有三维内容显示在增强现实中的所有这些。

Urho 为三维模型和动画，使用自定义格式，因此你需要为此格式导出你的资产。   你可以使用类似的工具[Urho3D Blender 外接程序](https://github.com/reattiva/Urho3D-Blender)和[UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) ，可以将转换这些资产从 DBX、 DAE、 OBJ、 Blend，之类的常用格式三维-最大值为格式所需的 Urho。

若要了解有关创建使用 Urho 的三维应用程序的详细信息，请访问[简介 UrhoSharp](~/graphics-games/urhosharp/introduction.md)指南。

## <a name="arkitapp-in-depth"></a>在深度 ArkitApp

> [!NOTE]
> 本部分旨在为开发人员，想要自定义默认值体验 UrhoSharp 和 ARKit 或想要获取集成的工作原理的更深入的见解。   不需要阅读本节。

ARKit API 很简单，创建和配置[ARSession](https://developer.apple.com/documentation/arkit/arsession)该对象，然后启动交付[ARFrame](https://developer.apple.com/documentation/arkit/arframe)对象。   它们包含这两个映像捕获照相机，以及设备的估计的实际位置。

我们将编写传递通过照相机到我们与我们三维内容的映像并调整相机沿 UrhoSharp 以匹配在设备位置和位置的可能性。

下图显示正在执行什么操作`ArkitApp`类：

[ ![类和 ArkitApp 中的屏幕的关系图](urhosharp-images/image2.png)](urhosharp-images/image2.png)

### <a name="rendering-the-frames"></a>呈现帧

其理念是简单，合并来自与我们 3D 图形，以生成的组合的图像的相机视频。     我们将在序列中，获取一系列的这些捕获的映像并将其我们将使用 Urho 场景混合此输入。

执行此操作的最简单方法是插入[ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/)到主[ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/)。  这是一套执行要绘制的单个帧的命令。  此命令将填充我们向它传递任何纹理与视区。    我们对此进行设置上的第一帧时，过程，并在 th 中完成的实际定义**ARRenderPath.xml**此时加载的文件。

但是，我们都面临着两个问题，若要在一起混合这两个领域：


1. 在 iOS 上 GPU 纹理必须具有的两个，幂的分辨率但我们将从照相机中获取的帧没有是的幂，例如的分辨率： 1280 x 720。
2. 帧中进行编码[YUV](https://en.wikipedia.org/wiki/YUV)格式，表示由两个图像的亮度和色度。

YUV 帧有两个不同的解决方法。  表示亮度 （基本上灰度图像） 和多较小 640x360 色度组件 1280 x 720 映像：

![演示组合 Y 和 UV 组件的映像](urhosharp-images/image3.png)


若要绘制使用 OpenGL ES 的完整彩色的映像我们必须编写小的着色器从纹理槽采用亮度 （Y 组件） 和色度 （UV 平面）。  在 UrhoSharp 它们具有名称-"sDiffMap"和"sNormalMap"，并将它们转换为 RGB 格式：

```csharp
mat4 ycbcrToRGBTransform = mat4(
    vec4(+1.0000, +1.0000, +1.0000, +0.0000),
    vec4(+0.0000, -0.3441, +1.7720, +0.0000),
    vec4(+1.4020, -0.7141, +0.0000, +0.0000),
    vec4(-0.7010, +0.5291, -0.8860, +1.0000));

vec4 ycbcr = vec4(texture2D(sDiffMap, vTexCoord).r,
                    texture2D(sNormalMap, vTexCoord).ra, 1.0);
gl_FragColor = ycbcrToRGBTransform * ycbcr;
```

若要呈现的纹理中不具有两个解决方法的幂我们必须使用以下参数定义 Texture2D:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

因此，我们都可以作为背景呈现捕获的图像，并呈现其上的任何场景类似的可怕的变化。

### <a name="adjusting-the-camera"></a>调整相机

`ARFrame`对象还包含估计的设备位置。  我们现在我们需要移动游戏照相机 ARFrame-之前 ARKit 找不到跟踪设备方向 （式、 间距和旋转） 和呈现在视频之上一固定张全息图-但为你的设备如果移动有点-了不起全息将存在偏差。

发生这种情况是因为如陀螺内置传感器不能跟踪动作数，它们可以仅加速。  每个帧和提取功能点来跟踪，因此能够向我们提供准确的 ARKit 分析转换包含数据移动和旋转的矩阵。

例如，这是我们可以如何获取当前位置：

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

我们使用`-row.Z`因为 ARKit 使用右手坐标系统中。


### <a name="plane-detection"></a>平面检测

ARKit 是能够检测到水平平面和此功能允许你与真实世界交互，例如，我们可以放置在实际的表或层上的变化的。 执行此操作的最简单方法是使用 HitTest 方法 (raycasting)。 它将转换屏幕坐标 (0.5; 0.5 是中心) 成实际区坐标 (0; 0; 0 是第一个帧的位置)。

```chsarp
protected Vector3? HitTest(float screenX = 0.5f, float screenY = 0.5f)
{
    var result = ARSession.CurrentFrame.HitTest(new CGPoint(screenX, screenY),
        ARHitTestResultType.ExistingPlaneUsingExtent)?.FirstOrDefault();
    if (result != null)
    {
        var row = result.WorldTransform.Row3;
        return new Vector3(row.X, row.Y, -row.Z);
    }
    return null;
}
```

现在我们可以在我们点击设备屏幕上的位置根据了水平曲面上放置变化的：

```chsarp
void OnTouchEnd(TouchEndEventArgs e)
{
    float x = e.X / (float)Graphics.Width;
    float y = e.Y / (float)Graphics.Height;
    var pos = HitTest(x, y);
    if (pos != null)
    mutantNode.Position = pos.Value;
}
```

![更改的动画的图平面视图移动](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>现实照明

根据实际的照明条件，虚拟场景应该是较浅或较深以更好地匹配其周围。 ARFrame 包含一个 LightEstimate 属性，我们可以用于调整 Urho 光线，这是如下所示：


    var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
    var zone = Scene.GetComponent<Zone>();
    zone.AmbientColor = Color.White * ambientIntensity;


### <a name="beyond-ios---hololens"></a>IOS-HoloLens 超出

UrhoSharp[所有主要的操作系统上运行](~/graphics-games/urhosharp/platform/index.md)，因此可以重复使用现有的代码在其他位置。

HoloLens 是其运行的最令人兴奋平台之一。   这意味着您可以轻松地切换 iOS 和 HoloLens 构建出色扩充的实际应用程序使用 UrhoSharp 之间。

你可以找到的 MutantDemo 源[github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo)。


## <a name="related-links"></a>相关链接

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [（与 UrhoSharp) ARKitXamarinDemo （示例）](https://github.com/EgorBo/ARKitXamarinDemo)
