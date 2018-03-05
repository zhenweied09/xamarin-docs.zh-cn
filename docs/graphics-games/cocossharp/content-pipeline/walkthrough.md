---
title: "使用 MonoGame 管道工具"
description: "MonoGame 管道工具用于创建和管理 MonoGame 内容项目。 内容的项目中的文件处理 Monogame 管道工具和输出为.xnb 文件以供在 CocosSharp 和 MonoGame 应用程序的使用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: df3692777eaa0791385c9ef3d114fbc8a9ab752e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="using-the-monogame-pipeline-tool"></a>使用 MonoGame 管道工具

_MonoGame 管道工具用于创建和管理 MonoGame 内容项目。内容的项目中的文件处理 Monogame 管道工具和输出为.xnb 文件以供在 CocosSharp 和 MonoGame 应用程序的使用。_

MonoGame 管道工具提供用于将转换到的内容文件的简单易用的环境**.xnb**在 CocosSharp 和 MonoGame 应用程序中使用的文件。 有关内容的管道和它们为何有用游戏开发中的信息，请参阅[对内容管道本简介](~/graphics-games/cocossharp/content-pipeline/introduction.md)

本演练中将包括下列各项：

 - 安装 MonoGame 管道工具
 - 创建 CocosSharp 项目
 - 创建内容的项目
 - 处理 MonoGame 管道工具中的文件
 - 使用运行时的文件

本演练使用 CocosSharp 项目来演示如何**.xnb**可以加载文件，并将其应用程序中使用。 MonoGame 的用户也将无法引用本演练中，如 CocosSharp 和 MonoGame 都使用相同**.xnb**内容文件。

完成的应用程序将显示显示从纹理子单个画面**.xnb**文件和单个标签显示从画面字体**.xnb**文件：

![](walkthrough-images/image1.png "完成的应用程序将显示单个子画面显示从.xnb 文件纹理")


# <a name="monogame-pipeline-platform-discussion"></a>MonoGame 管道平台讨论

在 Windows、 OS X 和 Linux，MonoGame 管道工具才可用。 本演练将在 Windows 上，运行该工具，但它可以后接 Mac 和 Linux 上以及。 获取在最大或 Linux 上设置该工具的信息，请参阅[本页](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/)。

MonoGame 管道工具是能够创建内容，为 iOS 应用程序甚至时在 Windows 上运行，因此开发人员使用[Xamarin Mac 代理](~/ios/get-started/installation/windows/connecting-to-mac/index.md)将能够继续在 Windows 上开发。


# <a name="installing-the-monogame-pipeline-tool"></a>安装 MonoGame 管道工具

首先，我们将安装 MonoGame，其中包括 MonoGame 内容管道。 请注意，MonoGame 内容管道是单独的下载 for mac。 上找不到所有 MonoGame 安装程序[MonoGame 下载页](http://www.monogame.net/downloads/)。 我们将下载 MonoGame for Visual Studio，但一旦安装开发人员可以使用 MonoGame Visual Studio 中为 Mac 太：

![](walkthrough-images/image2.png "对于 Visual Studio 中，但一旦开发人员使用 MonoGame 在 Visual Studio 中为安装 Mac 太下载 MonoGame")

下载完成后，我们通过安装运行，接受默认值选项。 安装程序完成后，MonoGame 管道工具已安装，并且可以在搜索开始菜单中找到：

![](walkthrough-images/image3.png "安装程序完成后，MonoGame 管道工具已安装，并且可在开始菜单搜索")

启动 MonoGame 管道工具：

![](walkthrough-images/image4.png "启动 MonoGame 管道工具")

MonoGame 管道工具运行后，我们可以开始，让我们游戏和内容的项目。


# <a name="creating-an-empty-cocossharp-project"></a>创建一个空的 CocosSharp 项目

下一步是创建 CocosSharp 项目。 很重要，我们 CocosSharp 首先创建项目，以便我们可以创建 CocosSharp 项目的文件夹结构中保存我们内容项目。 有关如何创建新项目的信息，请参阅[BouncingGame 指南](~/graphics-games/cocossharp/first-game/part1.md)。 在本指南中，我们将创建一个名为 BouncingGame，项目，但任何现有的 CocosSharp 项目可以正常工作。 如果你有想要将内容添加到现有 CocosSharp 项目，随意而不是 BouncingGame 项目中使用该项目。

一旦创建项目后，我们将运行它以验证它生成和我们具有所有内容正确设置：

![](walkthrough-images/image5.png "一旦创建项目后，运行以确认生成过程中，并且，所有内容正确设置")


# <a name="creating-a-content-project"></a>创建内容的项目

现在，我们已游戏项目，我们可以创建 MonoGame 管道项目。 为此，请在 MonoGame 管道工具选择**文件 > 新建...**并导航到你的项目的内容文件夹。 对于 Android，文件夹位于 **[项目 root]\BouncingGame.Android\Assets\Content\**。 对于 iOS，文件夹位于 **[项目 root]\BouncingGame.iOS\Content\**。

更改**文件名**到**ContentProject**单击**保存**按钮：

![](walkthrough-images/image8.png "将文件名称更改为 ContentProject 并单击保存按钮")

MonoGame 管道工具创建项目后，将显示有关项目的信息时根**ContentProject**选择项：

![](walkthrough-images/image9.png "选择根 ContentProject 项目时 MonoGame 管道工具创建项目后，将显示有关项目的信息")

让我们看看其中一些内容项目的最重要选项。


## <a name="output-folder"></a>输出文件夹

这是 （相对于内容项目本身） 的文件夹位置输出**.xnb**将保存文件。 若要为简单起见，我们将使用相同的文件夹以保存我们的输入和输出文件。 换而言之，我们将更改**输出文件夹**要 **。\** :

![](walkthrough-images/image10.png "")


## <a name="platform"></a>平台

这定义内容的目标平台。 请注意，这是**Windows**默认情况下，因此我们将需要将其更改为我们的目标平台即**Android** （或如果按照 iOS 项目以及 iOS）。

![](walkthrough-images/image11.png "请注意这是 Windows，默认情况下，因此将其更改为目标平台，这是 Android 或 iOS，如果以下以及 iOS 项目")


# <a name="processing-files-in-the-monogame-pipelinetool"></a>处理 MonoGame PipelineTool 中的文件

接下来，我们将添加到内容我们**ContentProject**。 对于此项目，我们会将文件添加到该项目的根，但更大型的项目通常将组织文件夹中的其内容。

我们将向我们的项目中添加两个文件：

 - A **.png**文件该文件将用于绘制动画层。 此文件可以[此处下载](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)。
 - A **.spritefont**将用于在屏幕上绘制文本的文件。 ContentPipeline 工具支持创建新.spritefont 文件，因此没有要下载的文件。


## <a name="adding-a-png-file"></a>添加.png 文件

若要添加**.png**文件到项目中，我们将首先将其复制到与管道项目中，具有相同的目录**.mgcb**扩展。

![](walkthrough-images/image12.png "向项目添加.png 文件")

接下来，我们将添加到管道项目的文件。 若要执行此操作在 MonoGame 管道工具中，选择**编辑 > 添加项...**，选择**ball.png**文件并单击**打开**。 该文件现在将内容项目的一部分，并选中，将显示其属性：

![](walkthrough-images/image13.png "该文件现在将内容项目的一部分，并选中，将显示其属性")

我们将正在将所有值都保留为各自的默认值，如需加载在 CocosSharp.xnb 文件不进行任何更改。 我们可以通过选择生成文件**生成 > 生成**菜单选项，它将启动生成，并显示有关生成的输出。 我们可以验证生成正常通过检查**内容**新文件夹**ball.xnb**文件：

![](walkthrough-images/image14.png "验证生成正常通过检查新的 ball.xnb 文件的内容文件夹")


## <a name="adding-a-spritefont-file"></a>添加.spritefont 文件

我们可以创建通过 MonoGame 管道工具.spritefont 文件。 CocosSharp 需要字体处于**字体**文件夹中和 CocosSharp 模板自动自动创建 Fonts 文件夹。 我们可以通过选择此文件夹添加到 MonoGame 管道工具**编辑 > 添加 > 现有文件夹...**.浏览到**内容**文件夹，然后选择**字体**文件夹，然后单击**确定**:

![](walkthrough-images/browsetofonts.png "浏览到内容文件夹并选择字体文件夹，然后单击确定")

若要添加新的.sprintefont 文件，右键单击字体文件夹，然后选择**添加 > 新建项...**，选择**SpriteFont 说明**选项中，输入名称**arial 36**，然后单击**确定**。 CocosSharp 需要非常具体的方式命名的字体文件 – 它们必须采用格式 [FontType]-[FontSize]。 如果一种字体与此命名格式，它将不加载 CocosSharp 通过在运行时不匹配。

![](walkthrough-images/image15.png "如果一种字体的格式不一致，此命名它将不加载 CocosSharp 通过在运行时")

.Spritefont 文件是实际 XML 文件，其中可以编辑在任何文本编辑器中，包括 Visual Studio for mac。 编辑.spritefont 文件中的最常见变量包括`FontName`和`Size`属性：


```csharp
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

我们将在任何文本编辑器中打开该文件。 作为我们**arial 36.spritefont**名称所示，我们将其保留`FontName`作为`Arial`但更改`Size`值赋给`36`:


```csharp
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
# <a name="using-files-at-runtime"></a>使用运行时的文件

.Xnb 文件现在构建，并准备好在我们的项目中使用。 我们将添加文件到 Visual Studio for Mac 然后我们将添加代码，以便我们`GameScene.cs`加载这些文件并将它们显示的文件。


## <a name="adding-xnb-files-to-visual-studio-for-mac"></a>将.xnb 文件添加到 Visual Studio 中，适用于 Mac

首先我们将向我们的项目中添加文件。 在适用于 Mac 的 Visual Studio，我们将展开**BouncingGame.Android**项目中，展开**资产**文件夹中，右键单击**内容**文件夹，并选择**添加 > 添加文件...**首先，我们将选择**ball.xnb**我们之前生成，并单击**打开**。 然后重复上述步骤，但将添加**arial 36.xnb**文件。 我们将选择**使文件保持其当前子目录中**如果适用于 Mac 的 Visual Studio 会要求如何将文件添加边界选项。 一次完成这两个文件应该是我们的项目的一部分：

![](walkthrough-images/image20.png "一次完成这两个文件应为项目的一部分")


## <a name="adding-gamescenecs"></a>添加 GameScene.cs

我们将创建一个名为类`GameScene,`其中将包含我们动画层和文本的对象。 若要执行此操作，右键单击**BouncingGame** (不 BouncingGame.Android) 项目，然后选择**添加 > 新文件...**.选择**常规**类别中，选择**空类**选项，，然后输入名称**GameScene**。

创建后，我们将通过修改`GameScene.cs`文件，以包含以下代码：


```csharp
using System;
using CocosSharp;

namespace BouncingGame
{
    public class GameScene : CCScene
    {
        // All visual elements must be added to a CCLayer:
        CCLayer mainLayer;

        // The CCSprite is used to display the "ball" texture
        CCSprite sprite;
        // The CCLabelTtf is used to display the Arial36 sprite font
        CCLabelTtf label;

        public GameScene(CCWindow mainWindow) : base(mainWindow)
        {
            // Instantiate the CCLayer first:
            mainLayer = new CCLayer ();
            AddChild (mainLayer);

            // Now we can create the Sprite using the ball.xnb file:
            sprite = new CCSprite ("ball");
            sprite.PositionX = 200;
            sprite.PositionY = 200;
            mainLayer.AddChild (sprite);

            // The font name (arial) and size (36) need to match 
            // the .spritefont definition and file name.  
            label = new CCLabelTtf ("Using font 36", "arial", 36);
            label.PositionX = 200;
            label.PositionY = 300;
            mainLayer.AddChild (label);
        }
    }
} 
```

我们将不会讨论上面的代码由于中介绍了使用 CocosSharp 视觉对象，如 CCSprite 和 CCLabelTtf [CocosSharp 简介指南](~/graphics-games/cocossharp/first-game/index.md)。

我们还需要添加代码以加载我们新创建`GameScene`。 若要这样做我们将打开`GameAppDelegate.cs`文件 (位于**BouncingGame** PCL) 和修改`ApplicationDidFinishLaunching`方法，以便它如下所示：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";

    // New code:
    GameScene gameScene = new GameScene (mainWindow);
    mainWindow.RunWithScene (gameScene);
} 
```

运行时，我们游戏将如下所示：

![](walkthrough-images/image1.png "运行时，游戏将如下所示")


# <a name="summary"></a>摘要

本演练演示了如何使用 MonoGame 管道工具来创建从输入的.png 文件.xnb 文件，以及如何从新创建.sprintefont 文件创建新的.xnb 文件。 此外讨论了如何构建 CocosSharp 项目以使用.xnb 文件以及如何加载这些文件在运行时。

## <a name="related-links"></a>相关链接

- [MonoGame 下载](http://www.monogame.net/downloads/)
- [MonoGame 管道文档](http://www.monogame.net/documentation/?page=Pipeline)
- [适用于 Android （示例） 的起始 BouncingGame 项目](https://developer.xamarin.com/samples/BouncingGameCompleteAndroid/)
- [ball.png 图 （示例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)
