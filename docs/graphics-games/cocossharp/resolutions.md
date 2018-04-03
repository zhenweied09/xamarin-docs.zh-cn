---
title: 处理 CocosSharp 中的多个解决方案
description: 本指南演示如何使用 CocosSharp 开发在不同的解决方案中的设备正确显示的游戏。
ms.topic: article
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 772b0d6408a5ba438c5eb0be04a9b549e29b40f9
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>处理 CocosSharp 中的多个解决方案

_本指南演示如何使用 CocosSharp 开发在不同的解决方案中的设备正确显示的游戏。_

CocosSharp 提供标准化在无论物理设备的显示器上的像素数游戏中的对象尺寸的方法。 传统上，游戏开发的电脑和游戏控制台可以作为目标的单个分辨率。 必须生成现代的游戏 – 和尤其是为移动设备的游戏 – 以适应各种设备。 本指南演示如何标准化 CocosSharp 无论物理的显示分辨率特征的游戏。

CocosSharp 的默认解析行为是与中的游戏坐标匹配物理像素。 下表显示了各种设备将会呈现在与宽度和高度 368 x 240 子后台环境画面。 从技术上讲不是实际设备，但而不是子画面，而不考虑设备分辨率的预期的呈现，则第一行是：


| **设备** | **显示分辨率** | **示例屏幕快照** |
|--- | --- |--- |
|所需的显示|368 x 240 （带为纵横比的黑色条）| ![368 x 240 （带为纵横比的黑色条）](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960x640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920 x 1080](resolutions-images/image3.png) |

本文档介绍如何使用 CocosSharp 来修复上述表中所示的问题。 也就是说，我们将介绍如何进行呈现的第一行 – 无论屏幕分辨率中所示的任何设备。


## <a name="working-with-setdesignresolutionsize"></a>使用 SetDesignResolutionSize

`CCScene`类通常用作根容器的所有视觉对象，但它还提供一个静态方法`SetDesignResolutionSize`用于指定所有场景的默认大小。 换而言之`SetDesignResolutionSize`方法允许开发人员可以开发游戏，它将硬件解决方案中的各种上正确显示。 CocosSharp 项目模板使用此方法以将默认项目大小设置为 1024 x 768，如下面的代码中所示：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```

你可以更改`desiredWidth`和`desiredHeight`变量更高版本以修改以匹配你的游戏的所需的分辨率显示。 例如，即可，如下所示修改上面的代码显示为全屏幕的 368 x 240 背景：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 368.0f;
    float desiredHeight = 240.0f;
    
    // This will set the world bounds to(0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```


## <a name="ccsceneresolutionpolicy"></a>CCSceneResolutionPolicy

`SetDesignResolutionSize` 让我们来指定如何在游戏窗口调整到所需的分辨率。 以下部分演示 500 x 500 映像具有不同的显示方式`CCSceneResolutonPolicy`值传递给`SetDesignResolutionSize`方法。 通过提供了以下值`CCSceneResolutionPolicy`枚举：

 - `ShowAll` – 显示整个请求的解析，保持纵横比。
 - `ExactFit` – 显示整个请求解决方法，但不能保持纵横比。
 - `FixedWidth` – 显示的请求的整个宽度，保持纵横比。
 - `FixedHeight` – 显示整个请求的高度，保持纵横比。
 - `NoBorder` – 显示整个高度或保持纵横比的整个宽度。 如果设备纵横比，则大于所需的分辨率纵横比，则将显示的整个宽度。 如果设备纵横比小于所需的分辨率纵横比，则将显示整个高度。
 - `Custom` -显示依赖于`Viewport`属性的当前`CCScene`。

所有屏幕快照的分辨率 iPhone 4s (960 x 640) 以横向方式生成和使用此图像：

![](resolutions-images/image4.png "所有的屏幕快照的分辨率 iPhone 4s 960 x 640 横向生成和使用此图像")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` 指定整个游戏解析屏幕上，将可以看到，但可能会显示*会造成宽屏*（黑色条） 来调整不同的纵横比。 通常使用此策略，因为它可保证将没有任何失真屏幕上显示的整个游戏视图。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

会造成宽屏是对左侧和右侧的映像，来应对正在宽于所需的分辨率物理纵横比可见：

![](resolutions-images/image5.png "会造成宽屏是左侧和右侧的映像，来应对正在宽于所需的分辨率物理纵横比对可见")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` 指定整个游戏解析将不会造成宽屏与屏幕上可见。 可视区域可能会扭曲 （可能未维护纵横比） 根据硬件纵横比。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

不会造成宽屏可见，但由于设备分辨率是矩形失真游戏视图：

![](resolutions-images/image6.png "不会造成宽屏可见，但由于设备分辨率是矩形失真游戏视图")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` 指定视图的宽度将匹配传递给的宽度值`SetDesignResolutionSize`，但可查看高度可能会发生的物理设备纵横比。 传递给的高度值`SetDesignResolutionSize`被忽略，因为它将计算在运行时基于物理设备的纵横比。 这意味着，可能小于所需的高度 （这会导致游戏视图正在屏幕之外的部分） 或计算所得的高度可能会大于 （这会导致多个要显示的游戏视图） 的所需高度计算所得的高度。 因为这可能会导致多个显示游戏，那么它可能会出现就像发生会造成宽屏;但是，将额外的空间不一定会有任何视觉对象出现黑色。 

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

IPhone 4s 有 3:2，纵横比，因此计算所得的高度为大约 333 单位：

![](resolutions-images/image7.png "IPhone 4s 有 3:2，纵横比，因此计算所得的高度为大约 333 单位")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

从概念上讲，`FixedHeight`行为方式类似于`FixedWidth`– 游戏将遵循在传递给的高度值`SetDesignResolutionSize,`，但将计算在运行时基于物理分辨率的宽度。 如上所述，这意味着，显示的宽度是小于或大于所需的宽度，从而导致的游戏正在一部分关闭屏幕或多个正在显示，分别游戏。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

由于在横向模式中运行的应用程序后创建下面的屏幕截图，计算所得的宽度大于 500 – 专门 750。 此策略保留 0 的 X 值左对齐，因此额外的解决方法可查看屏幕右侧。

![](resolutions-images/image8.png "此策略保留 0 的 X 值左对齐，因此额外的分辨率屏幕右侧可查看")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` 尝试显示具有不会造成宽屏的应用程序，同时保持原始纵横比 （无扭曲）。 如果请求的解析纵横比匹配设备的物理纵横比，将发生没有剪辑。 如果不匹配纵横比，然后剪辑将发生。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

下面的屏幕快照显示剪切，显示所有 500 像素的显示宽度时显示的顶部和底部组成部分：

![](resolutions-images/image9.png "此屏幕截图显示剪切，显示所有 500 像素的显示宽度时显示的顶部和底部组成部分")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` 使每个`CCScene`指定相对于中指定的分辨率自己自定义视区`SetDesignResolutionSize`。

场景定义其`Viewport`属性通过构造`CCViewport`，这反过来构造`CCRect`。 有关详细信息`CCViewport`和`CCRect`请参阅[CocosSharp API 文档](https://developer.xamarin.com/api/namespace/CocosSharp/)。 创建的上下文中`CCViewport``CCRect`构造函数的参数指定 （按顺序）：

 - x – 水平偏移量视区内，其中每个单元表示一个整个屏幕宽度。 例如，在向右移动的屏幕宽度的一半场景中使用.5f 结果的值。
 - y – 垂直偏移量视区内，其中每个单元表示一个整个屏幕的高度。 例如，在向下移动一半的屏幕高度场景中使用.5f 结果的值。
 - 宽度 – 场景应占用的水平部分。 例如，使用值为 1 / 3.0f 导致水平占用的屏幕三分之一的场景。
 - 高度 – 场景应占用的垂直部分。 例如，使用值为 1 / 3.0f 导致垂直占用的屏幕三分之一的场景。

代码示例：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.Custom);
...
float horizontalDisplayPortion = 2 / 3.0f;
float verticalDisplayPortion = 1 / 2.0f;
float displayOffsetInScreenWidths = 0;
float displayOffsetInScreenHeights = .5f;
var rectangle = new CCRect (
    displayOffsetInScreenWidths, 
    displayOffsetInScreenHeights, 
    horizontalDisplayPortion, 
    verticalDisplayPortion);
scene.Viewport = new CCViewport (rectangle);
```

上面的代码产生以下结果：

![](resolutions-images/image10.png "上面的代码导致此屏幕截图")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

`DefaultTexelToContentSizeRatio`简化了具有更高分辨率屏幕设备上使用更高分辨率纹理。 具体而言，此属性使游戏的进行而无需使用更高分辨率资产更改的大小或定位的可视元素。 

例如，游戏可能包括对游戏字符是 200 像素、 高和游戏屏幕艺术作品资产 (所指定的`SetDesignResolutionSize`) 可能 400 像素高。 在这种情况下，字符占用屏幕大小的一半。 但是，如果 400 像素高资产已用于更高分辨率设备的字符，字符将占用显示整个高度。 如果想要保留相同的大小，以利用更高版本的解析设备的字符，则必要使字符画面屏幕的半高一些额外的代码。

上面介绍的简单示例表示游戏开发 – 而无需开发人员可以执行对每个 visual 元素根据设备分辨率调整大小添加更大的资产中常见的问题。 `DefaultTexelToContentSizeRatio` 全局属性用于调整大小的可视元素。 此值调整特定类型的所有可视化元素大小由所赋的值。

此属性是位于以下类： 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

CocosSharp Visual Studio for Mac 模板集`CCSprite.DefaultTexelToContentSizeRatio`根据作为示例，了解如何使用设备的宽度。 下面的代码包含在`CCApplicationDelegate.ApplicationDidFinishLaunching`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
           
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```


### <a name="defaulttexeltocontentsizeratio-example"></a>DefaultTexelToContentSizeRatio example

若要查看如何`DefaultTexelToContentSizeRatio`影响视觉对象的大小元素，请考虑上面显示的代码：


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

这将导致在下图中使用 500 x 500 纹理：

![](resolutions-images/image5.png "这将导致使用 500 x 500 纹理此映像")

IPad Retina 运行物理分辨率为 2048 x 1536。 这意味着游戏按照上面显示将拉伸 1536年物理像素上的 500 像素。 游戏可以充分利用此额外的解决方法，通过创建什么通常称为*hd*资产 – 它们可在更高分辨率屏幕上运行的资产。 例如，此游戏无法使用大小为 1000 x 1000 此纹理 hd 版本。 但是，使用更大的映像将导致`CCSprite`具有宽度和高度的 1000 个单位。 由于我们游戏将始终显示 500 个单位 (由于`SetDesignResolutioSize`调用)，则我们 1000 x 1000 画面将太大 （仅的底部左侧的部分它显示）：

![](resolutions-images/image11.png "由于游戏将始终显示 500 个单位由于 SetDesignResolutioSize 调用，1000 x 1000 画面将因过大而仅的底部左侧的部分它显示")

我们可以设置`CCSprite.DefaultTexelToContentSizeRatio`来应对两次正在为宽度和高度作为原始 500 x 500 纹理 1000 x 1000 纹理：


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

现在如果我们运行游戏 1000 x 1000 纹理将完全可见：

![](resolutions-images/image12.png "现在如果我们运行游戏 1000 x 1000 纹理将完全可见")


### <a name="defaulttexeltocontentsizeratio-details"></a>DefaultTexelToContentSizeRatio details

`DefaultTexelToContentSizeRatio`属性是`static,`这意味着应用程序中的所有子画面将共享相同的值。 对不同的解决方法进行的资产与游戏的典型方法是包含一组完整的资产的每个解决方法类别。 默认情况下，CocosSharp Visual Studio for Mac 模板提供**ld**和**hd**资产，可用于支持两个集的纹理的游戏的文件夹。 使用内容的示例内容文件夹可能如下所示：

![](resolutions-images/image13.png "使用内容的示例内容文件夹可能看起来像")

请注意，默认模板还包含用于有条件地添加到应用程序的代码`ContentSearchPaths`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```

这意味着应用程序将搜索根据是否正在高分辨率或正则解决模式运行的路径中的文件。 例如，如果**hd**和**ld**文件夹包含名为的文件**background.png**然后下面的代码将运行并选择正确的文件的解析：


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>总结

本文介绍如何创建游戏它而不考虑设备分辨率正确显示。 显示使用的示例不同`CCSceneResolutionPolicy`用于调整大小根据设备分辨率游戏的值。 它还提供的一个示例`DefaultTexelToContentSizeRatio`可用来容纳多个内容集而无需单独调整大小的可视元素。

## <a name="related-links"></a>相关的链接

- [CocosSharp 简介](~/graphics-games/cocossharp/index.md)
- [CocosSharp API 文档](https://developer.xamarin.com/api/namespace/CocosSharp/)
