---
title: 第 2 – 实现 WalkingGame 部分
description: 本演练演示如何添加游戏逻辑和内容分发到一个空的 MonoGame 项目创建与移动动画画面一个演示触控输入。
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 89f5148dacfb229750839e4554199f78c8d15126
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="part-2--implementing-the-walkinggame"></a>第 2 – 实现 WalkingGame 部分

_本演练演示如何添加游戏逻辑和内容分发到一个空的 MonoGame 项目创建与移动动画画面一个演示触控输入。_

本演练的前面部分介绍了如何创建空 MonoGame 项目。 我们将通过简单的游戏演示生成这些以前部分上。 本文包含以下各节：

 - 解压缩我们游戏的内容
 - MonoGame 类概述
 - 呈现我们第一个动画层
 - 创建 CharacterEntity
 - 游戏正在添加 CharacterEntity
 - 创建动画类
 - 将第一个动画添加到 CharacterEntity
 - 添加移动的字符
 - 匹配移动和动画


# <a name="unzipping-our-game-content"></a>解压缩我们游戏的内容

我们开始编写代码之前，我们将想要解压缩我们游戏*内容*。 游戏开发人员通常使用术语*内容*来引用非代码文件，这通常由 visual 艺术家、 游戏设计器中或音频的设计器创建。 常见类型的内容包含用于显示视觉对象、 播放声音，或控制人工智能 (AI) 行为的文件。 从游戏开发团队的角度来看内容通常会创建非程序员。

找不到此处使用的内容[github 上](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)。 我们将需要这些文件下载到我们将稍后在本演练中访问的位置。


# <a name="monogame-class-overview"></a>MonoGame 类概述

对于初学者，我们将探讨在基本呈现中使用的 MonoGame 类：

 - `SpriteBatch` – 用于二维图形绘制到屏幕。 *子画面*是二维可视元素，可用于在屏幕上显示图像。 `SpriteBatch`对象之间的一次可以绘制单个画面其`Begin`和`End`可以在一起，分组方法或多个子画面或*批处理*。
 - `Texture2D` – 表示在运行时的图像对象。 `Texture2D` 尽管它们也可以创建动态在运行时，从文件格式如.png 或.bmp，通常创建实例。 `Texture2D` 与在呈现时，将使用实例`SpriteBatch`实例。
 - `Vector2` – 表示的二维的坐标系统，它通常用来定位视觉对象中的位置。 此外包括 MonoGame`Vector3`和`Vector4`但我们将只使用`Vector2`在本演练中。
 - `Rectangle` – 与位置、 宽度和高度的四个边区域。 我们将使用此定义的哪一部分我们`Texture2D`呈现时我们开始使用动画。

我们还应注意，MonoGame 不由 Microsoft 维护 – 不管其命名空间。 `Microsoft.Xna`命名空间使用 MonoGame 中，以使其更轻松地将现有的 XNA 项目迁移到 MonoGame。


# <a name="rendering-our-first-sprite"></a>呈现我们第一个动画层

接下来我们将向屏幕以显示如何在 MonoGame 中执行二维呈现绘制单个画面。


## <a name="creating-a-texture2d"></a>创建 Texture2D

我们需要创建`Texture2D`实例呈现我们画面时可用。 所有游戏内容最终包含在名为的文件夹**内容，**位于特定于平台的项目。 MonoGame 共享项目不能包含内容，因为内容必须使用特定于平台的生成操作。 CocosSharp 开发人员会发现内容的文件夹的熟悉的概念 – 位于 CocosSharp 和 MonoGame 项目中的同一位置。 在 iOS 项目中，并在 Android 项目中的资产文件夹内，可以找到的内容文件夹。

若要添加我们的游戏中的内容，请右键单击**内容**文件夹，然后选择**添加 > 添加文件...**导航到解压缩 content.zip 文件的位置并选择**charactersheet.png**文件。 如果系统询问有关如何将文件添加到文件夹，我们应选择**复制**选项：

![](part2-images/image1.png "如果系统询问有关如何将文件添加到文件夹，选择复制选项")

内容文件夹现在包含 charactersheet.png 文件：

![](part2-images/image2.png "内容文件夹现在包含 charactersheet.png 文件")

接下来，我们将添加代码以加载 charactersheet.png 文件并创建`Texture2D`。 为此，请打开`Game1.cs`文件并将以下字段添加到 Game1.cs 类：


```csharp
Texture2D characterSheetTexture;
```

接下来，我们将创建`characterSheetTexture`中`LoadContent`方法。 任何修改之前`LoadContent`方法如下所示：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```
我们应注意已实例化默认项目`spriteBatch`为我们的实例。 我们将使用此更高版本，但我们不会添加任何附加的代码，若要准备`spriteBatch`供使用。 另一方面，我们`spriteSheetTexture`需要初始化，因此我们将初始化之后`spriteBatch`创建：


```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

现在，我们已经`SpriteBatch`实例和一个`Texture2D`实例，我们可以添加到我们呈现代码`Game1.Draw`方法：


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

现在运行游戏显示单个子显示纹理从 charactersheet.png 创建画面：

![](part2-images/image3.png "现在运行游戏显示单个子显示纹理从 charactersheet.png 创建画面")


#  <a name="creating-the-characterentity"></a>创建 CharacterEntity

到目前为止，我们已添加代码以呈现到屏幕; 子单个画面但是，如果要开发完整的游戏，而不创建任何其他类，我们将运行到代码的组织问题。


## <a name="what-is-an-entity"></a>实体是什么？

组织游戏代码的常见模式是创建每个游戏的新类*实体*类型。 游戏开发中的实体是可包含一些 （并非所有是必需的） 的以下特征的对象：

 - 如画面、 文本或 3D 模型可视元素
 - 物理或每个帧的行为，例如 patrolling 设置的路径或响应输入播放器字符单元
 - 可以创建和销毁动态，如电出现和收集的播放器

实体组织系统可能十分复杂，而且许多游戏引擎提供帮助管理实体的类。 我们将实现非常简单的实体的系统中，因此值得注意的完整的游戏，通常需要更有条理的开发人员部分。


## <a name="defining-the-characterentity"></a>定义 CharacterEntity

我们的实体，我们将调用`CharacterEntity`，将具有以下特征：

 - 加载其自己的能力 `Texture2D`
 - 功能来呈现自身，包括包含调用的方法来更新步行动画
 - `X `和 Y 属性来控制字符的位置。
 - 若要更新本身-具体而言中读取值从触摸屏幕和相应地调整位置, 能力。

若要添加`CharacterEntity`到我们的游戏、 右键单击或按住 control 键单击上**WalkingGame**项目，然后选择**添加 > 新文件...**.选择**空类**选项，然后将新文件命名**CharacterEntity**，然后单击**新建**。

首先，我们将添加的能力`CharacterEntity`加载`Texture2D`以及如同绘制本身一样。 我们将修改新添加`CharacterEntity.cs`文件，如下所示：


```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

上面的代码将添加责任定位、 呈现，并加载内容到`CharacterEntity`。 让我们看一段时间来指出的是在上面的代码中执行一些注意事项。


## <a name="why-are-x-and-y-floats"></a>为何 X 和 Y 的浮动内容？

开发人员不熟悉如何游戏编程可能想知道为什么`float`使用的类型而非`int`或`double`。 原因是最常见的定位中低级别呈现代码中的 32 位值。 双精度类型所占据的精度，这很少进行定位的对象所必需的 64 位。 32 位差异可能看似无关紧要，而许多现代游戏包括图形需要处理数万个位置值 （30 编译或每秒 60 次） 每个帧。 剪切的内存量必须移动通过图形一半的管道可以对游戏的性能产生显著的影响。

`int`数据类型可以是相应的度量单位为定位，但它可以在实体中的放置的方式加以限制。 例如，使用整数值使更难实现平滑移动游戏支持放大或 3D 照相机 (这允许通过`SpriteBatch`)。

最后，我们将看到，这将在屏幕我们字符的逻辑将执行此操作使用游戏的计时值。 通过在给定框架中经过多少时间相乘速度的结果将极少数情况下导致整数数量，因此我们需要使用`float`为`X`和`Y`。


## <a name="why-is-charactersheettexture-static"></a>为何 characterSheetTexture 静态？

`characterSheetTexture` `Texture2D`实例是 charactersheet.png 文件的运行时表示。 换而言之，它包含每个像素的颜色值从源提取**charactersheet.png**文件。 如果我们的游戏是创建两个`CharacterEntity`实例，则每个需要访问信息从 charactersheet.png。 在此情况下，我们不需要带来的性能损失，加载 charactersheet.png 两次，也不我们想要存储在内存中的重复纹理内存。 使用`static`字段使我们可以共享同一个`Texture2D`所有`CharacterEntity`实例。

使用`static`内容的运行时表示的成员是一个简单的解决方案，它不能解决从一个级别移到另一个时遇到如卸载内容的较大游戏的问题。 更完善的解决方案，已超出本演练的范围，包括使用 MonoGame 的内容的管道或创建自定义的内容管理系统。


## <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>为何 SpriteBatch 传递作为参数而不是通过实例化实体？

`Draw`方法，如上面采用实现`SpriteBatch`自变量，但相比之下，`characterSheetTexture`通过实例化`CharacterEntity`。

这样做的原因是因为时，最高效呈现是可能相同`SpriteBatch`实例用于所有`Draw`调用，以及何时所有`Draw`一组之间进行调用`Begin`和`End`调用。 当然，我们的游戏中将仅包括一个实体实例，但允许多个实体使用相同的模式将获得更复杂的游戏`SpriteBatch`实例。


# <a name="adding-characterentity-to-the-game"></a>游戏正在添加 CharacterEntity

现在，我们添加了我们`CharacterEntity`用于呈现自身的代码，我们可以替换中的代码`Game1.cs`以使用这个新实体的实例。 若要这样做我们会将`Texture2D`字段`CharacterEntity`字段`Game1`:


```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

接下来，我们将添加中的此实体的实例化`Game1.Initialize`:


```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

我们还需要删除`Texture2D`从创建`LoadContent`由于，现在处理的内部我们`CharacterEntity`。 `Game1.LoadContent` 应如下所示：


```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

值得注意，尽管其名称的是`LoadContent`方法不是其中可以加载内容 – 许多游戏实现内容在其初始化方法中，或作为内容，更新代码中甚至加载可能不需要直到玩家达到的唯一位置游戏特定点。

最后我们可以按如下方式修改 Draw 方法：


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

如果我们运行游戏时，我们现在将看到的字符。 因为 X 和 Y 默认为 0，然后字符位于屏幕左上角对：

![](part2-images/image4.png "因为 X 和 Y 默认为 0，然后字符位于屏幕左上角针对")


# <a name="creating-the-animation-class"></a>创建动画类

目前我们`CharacterEntity`显示完整**charactersheet.png**文件。 在一个文件中的多个映像这种安排称为*画面表*。 通常情况下，子画面将呈现仅画面表的一部分。 我们将修改`CharacterEntity`呈现的这一部分**charactersheet.png**，和此部分将会发生更改以显示步行动画。

我们将创建`Animation`类来控制的逻辑和 CharacterEntity 动画的状态。 动画类将是常规类无法用于任何实体，而不仅仅是`CharacterEntity`动画。 Ultimate`Animation`类将提供`Rectangle`其中`CharacterEntity`绘制本身时，将使用。 我们还将创建`AnimationFrame`将用来定义动画的类。


## <a name="defining-animationframe"></a>定义 AnimationFrame

`AnimationFrame` 将不包含动画与相关的任何逻辑。 我们将使用它只是为了存储数据。 若要添加`AnimationFrame`类、 右键单击或按住 control 键单击上**WalkingGame**共享项目并选择**添加 > 新文件...**输入的名称**AnimationFrame**单击**新建**按钮。 我们将通过修改`AnimationFrame.cs`文件，以便它包含以下代码：


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

`AnimationFrame`类包含两条信息：

 - `SourceRectangle` – 定义的区域`Texture2D`这将显示的`AnimationFrame`。 此值以像素为单位，顶部左侧为 （0，0）。
 - `Duration` – 多长时间定义`AnimationFrame`中使用时显示`Animation`。


## <a name="defining-animation"></a>定义动画

`Animation`类将包含`List<AnimationFrame>`以及逻辑，以切换哪个框架当前显示根据经过多少时间。

若要添加`Animation`类、 右键单击或按住 control 键单击上**WalkingGame**共享项目并选择**添加 > 新文件...**.输入的名称**动画**单击**新建**按钮。 我们将通过修改`Animation.cs`文件使其包含以下代码：


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

让我们来看看其中的详细信息的一些`Animation`类。


## <a name="frames-list"></a>帧列表

`frames`成员是什么将存储我们动画的数据。 实例化动画的代码将添加`AnimationFrame`实例到`frames`列表通过`AddFrame`方法。 更完整的实现可能会提供`public`方法或属性用于修改`frames`，但我们将仅添加在本演练中的帧的功能。


## <a name="duration"></a>持续时间

持续时间返回的总持续时间`Animation,`从中获取加上所有的所包含的持续时间`AnimationFrame`实例。 无法缓存此值，如果`AnimationFrame`的不可变的对象，但由于我们可以添加到动画后更改的类作为实现的 AnimationFrame，我们需要计算此值，每当访问此属性。


## <a name="update"></a>更新

`Update`应该调用方法 （即，每次更新整个游戏） 的每一帧。 其目的是提高`timeIntoAnimation`成员用来返回当前显示的帧。 中的逻辑`Update`阻止`timeIntoAnimation`遭到曾经大于整个动画持续时间。

因为我们将使用`Animation`类来显示步行动画，则我们想要当它已达到末尾时，重复此动画。 我们可以完成此操作通过检查如果`timeIntoAnimation`大于`Duration`值。 如果是这样它将循环回开头，并保留其余部分中，从而导致循环的动画。


## <a name="obtaining-the-current-frames-rectangle"></a>获取当前帧的矩形

用途`Animation`类是最终提供`Rectangle`绘制动画层时，可以使用其中。 当前`Animation`类包含用于更改逻辑`timeIntoAnimation`成员，我们将使用来获取其`Rectangle`应显示。 我们需要执行此操作通过创建`CurrentRectangle`中的属性`Animation`类。 将复制到此属性`Animation`类：

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime;
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

# <a name="adding-the-first-animation-to-characterentity"></a>将第一个动画添加到 CharacterEntity

`CharacterEntity`将包含遍历和持续，以及对当前的引用的动画效果`Animation`显示。

首先，我们将添加我们的第一个`Animation,`我们将用写入到目前为止所功能测试。 让我们将以下成员添加到 CharacterEntity 类：


```csharp
Animation walkDown;
Animation currentAnimation;
```

 接下来，让我们定义`walkDown`动画。 若要执行此修改`CharacterEntity`构造函数，如下所示：

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```
如前面所述，我们需要调用`Animation.Update`用于基于时间的动画播放。 我们还需要分配`currentAnimation`。 现在我们将分配为`currentAnimation`到`walkDown`，但我们将将要替换此代码更高版本时我们实现我们移动逻辑。 我们将添加`Update`方法`CharacterEntity`，如下所示：


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

现在，我们已经`currentAnimation`正在分配，并且更新，我们可以使用它执行我们绘图。 我们将通过修改`CharacterEntity.Draw`，如下所示：


```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

获取最后一步`CharacterEntity`进行动画处理是调用新添加`Update`方法从`Game1`。 修改`Game1.Update`，如下所示：


```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

现在`CharacterEntity`将播放其`walkDown`动画：

![](part2-images/image5.gif "现在 CharacterEntity 将播放其 walkDown 动画")


# <a name="adding-movement-to-the-character"></a>添加移动的字符

接下来，我们将为使用触摸屏输入控件我们字符添加移动。 当用户触摸屏幕时，字符将移向其中接触屏幕的点。 如果检测到没有收尾工作，字符将独立到位。


## <a name="defining-getdesiredvelocityfrominput"></a>Defining GetDesiredVelocityFromInput

我们将使用 MonoGame 的`TouchPanel`类，该类提供的触摸屏的当前状态有关的信息。 让我们添加的方法将检查`TouchPanel`并返回我们字符所需的速度：


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

`TouchPanel.GetState`方法返回`TouchCollection`其中包含有关用户其中触摸屏幕信息。 如果用户不在处理屏幕上，则`TouchCollection`将为空，这种情况下，我们不应移动字符。

如果用户触摸屏幕，我们会将其移入第一个触摸，字符换而言之，`TouchLocation`位于索引 0 处。 最初，我们将设置为相等的字符的位置和第一个触摸位置之间的差异的所需的速度：


```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

下面是数学将以相同的速度保持移动的字符的位。 为了帮助说明为何这很重要，让我们考虑的情况下，用户在处理屏幕 500 像素以外字符所在的位置。 第一个行位置`desiredVelocity.X`为组分配的值为 500。 但是，如果用户已处理的字符，从 100 个单位的距离在屏幕则`desiredVelocity.X `将设置为 100。 结果将是，该字符的移动速度将响应如何远触摸点都是从字符。 由于我们想要始终以相同的速度移动的字符，我们需要修改 desiredVelocity。

`if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)`语句的作用检查是否速度非-零 – 换而言之，它正在检查以确保用户未处理的字符的当前位置为相同的作用点。 如果不是，然后我们需要将该字符的速度要远常量而不考虑如何设置触摸屏输入。 我们通过规范化这它作为长度为 1 会导致速度向量完成此操作。 1 表示字符将每秒 1 像素移动速度向量。 我们将加快此速度值乘以 200 的所需速度。


## <a name="applying-velocity-to-position"></a>应用到的位置的速度

从返回的速度`GetDesiredVelocityFromInput`需要应用于的字符`X`和`Y`值，以在运行时产生任何影响。 我们将通过修改`Update`方法，如下所示：


```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

我们已经实现称为*基于时间的*移动 (相对于*基于框架的*移动)。 基于时间的移动乘以速度值 (在本例中的值存储在`velocity`变量) 由存储在上次更新后经过多少时间`gameTime.ElapsedGameTime.TotalSeconds`。 如果在更少每秒帧数运行游戏，帧之间经过的时间将进入 – 最终结果是使用基于时间的移动的对象将始终移动而不考虑帧速率的速度。

如果我们现在运行我们的游戏中，我们将看到的字符是转移到触摸位置：

![](part2-images/image6.gif "字符正在转向触摸位置")


# <a name="matching-movement-and-animation"></a>匹配移动和动画

一旦我们有移动和播放单个动画我们字符，我们可以定义我们动画的其余部分，然后使用它们来反映字符的移动。 当完成后，我们必须八个动画总数：

 - 遍历，向下、 左和向右的动画效果
 - 持续的动画仍和正面朝上向下，左右，


## <a name="defining-the-rest-of-the-animations"></a>定义动画的其余部分

首先，我们将添加`Animation`实例到`CharacterEntity`我们动画我们添加了其中的同一位置中的所有类`walkDown`。 一旦我们执行此操作，`CharacterEntity`将具有以下`Animation`成员：


```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

现在我们将定义中的动画`CharacterEntity`构造函数，如下所示：


```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

我们应注意上面的代码已添加到`CharacterEntity`构造函数要保留较短本演练。 游戏通常将分隔字符动画到它们自己的类的定义，或者加载该信息从 XML 或 JSON 之类的数据格式。

接下来，我们将调整的逻辑，如果字符已只是停止使用根据要移动的字符的方向，或根据最后一个动画的动画。 若要执行此操作，我们将修改`Update`方法：


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

若要切换动画的代码分解为两个块。 第一个检查速度是否不等于`Vector2.Zero`– 这将告诉我们的字符是否是移动。 如果字符是移动，则我们可以看看`velocity.X`和`velocity.Y`值来确定哪些步行动画播放。

如果不移动字符，则我们想要设置的字符`currentAnimation`于持续动画 – 我们仅在这样做，但`currentAnimation`是遍历动画或如果未设置动画。 如果`currentAnimation`不是一个四个步行动画，则字符都已经可以顺利因此我们无需更改`currentAnimation`。

此代码的结果为字符将正确进行动画处理时遍历，且然后面临着它已遍历停止时的最后一个方向：

![](part2-images/image7.gif "此代码的结果是字符将正确进行动画处理时遍历，以及然后面临着它已遍历停止时的最后一个方向")


# <a name="summary"></a>总结

本演练演示了如何使用 MonoGame 创建跨平台游戏与子画面、 移动对象、 输入的检测和动画。 它介绍如何创建通用动画类。 它还介绍了如何创建用于组织代码逻辑字符实体。

## <a name="related-links"></a>相关链接

- [CharacterSheet 图像资源 （示例）](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [遍历游戏完成 （示例）](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
