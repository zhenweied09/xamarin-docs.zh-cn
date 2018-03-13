---
title: "使用 Xamarin.Forms 中 CocosSharp"
description: "CocosSharp 可以用于将精确形状、 图像和文本呈现添加到用于高级可视化效果的应用程序"
ms.topic: article
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/03/2016
ms.openlocfilehash: 395defa300da7b8f68746162d877a4fdb17ded9e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="using-cocossharp-in-xamarinforms"></a>使用 Xamarin.Forms 中 CocosSharp

_CocosSharp 可以用于将精确形状、 图像和文本呈现添加到用于高级可视化效果的应用程序_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**发展 2016年： 科科斯 # Xamarin.Forms 中**

## <a name="overview"></a>概述

CocosSharp 是一种灵活、 功能强大的技术，用于显示图像、 读取触摸屏输入、 播放音频，和管理内容。 本指南说明如何将 CocosSharp 添加到 Xamarin.Forms 应用程序。 它涵盖以下方面：

* [什么是 CocosSharp？](#what)
* [添加 CocosSharp Nuget 包](#nuget)
* [演练： 向 Xamarin.Forms 应用添加 CocosSharp](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>什么是 CocosSharp？

[CocosSharp](~/graphics-games/cocossharp/index.md)是 Xamarin 平台可用的开放源代码游戏引擎。
CocosSharp 是一个高效运行时库，其中包括以下功能：

* 图像呈现使用[CCSprite 类](https://developer.xamarin.com/api/type/CocosSharp.CCSprite/)
* 形状呈现使用[CCDrawNode 类](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
* 每个帧逻辑使用[CCNode.Schedule 方法](https://developer.xamarin.com/api/member/CocosSharp.CCNode.Schedule/p/System.Action%7BSystem.Single%7D/)
* （加载和卸载如.png 文件的资源） 的内容管理使用[CCTextureCache 类](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
* 使用动画[CCAction 类](https://developer.xamarin.com/api/type/CocosSharp.CCAction/)

CocosSharp 的主要重点是简化跨平台 2D 游戏; 的创建但是，也可以是到 Xamarin 窗体应用程序的极好补充。 由于游戏通常需要高效呈现和精确的控制视觉对象，CocosSharp 可以用于向非游戏应用程序中添加功能强大的可视化和效果。

Xamarin.Forms 基于本机、 特定于平台的 UI 系统。 例如， [ `Button`s](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)在 iOS 和 Android，以不同方式显示和甚至可能因操作系统版本而异。 与此相反，CocosSharp 不使用任何特定于平台的视觉对象，因此所有视觉对象出现在所有平台上完全相同。 当然，解析和纵横比不同设备之间，并且可能会影响如何 CocosSharp 呈现其视觉对象。 在本指南的后面将讨论这些详细信息。

更多详细的信息可在[CocosSharp 部分](~/graphics-games/cocossharp/index.md)。

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>添加 CocosSharp Nuget 包

在使用之前 CocosSharp，开发人员需要少量添加内容到其 Xamarin.Forms 项目。
本指南假定 Xamarin.Forms 项目与 iOS、 Android 和 PCL 项目。
所有代码将用 PCL 项目中;但是，库必须添加到 iOS 和 Android 项目。

CocosSharp Nuget 包包含所有所需创建 CocosSharp 对象的对象。
CocosSharp.Forms nuget 包包含`CocosSharpView`类，该类用于托管 CocosSharp Xamarin.Forms 中。
添加**CocosSharp.Forms** NuGet 和**CocosSharp**将也会自动添加。
若要执行此操作，右键单击 PCL<span class="UIItem">包</span>文件夹，然后选择<span class="UIItem">添加包...</span>.输入搜索词<span class="UIItem">CocosSharp.Forms</span>，选择<span class="UIItem">Xamarin.Forms 的 CocosSharp</span>，然后单击<span class="UIItem">添加包</span>。

![](cocossharp-images/image1.png "添加包对话框")

同时**CocosSharp**和**CocosSharp.Forms** NuGet 包将添加到项目：

![](cocossharp-images/image2.png "包文件夹")

对于特定于平台的项目 （如 iOS 和 Android） 重复上述步骤。

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>演练： 向 Xamarin.Forms 应用添加 CocosSharp

请按照下列步骤以向 Xamarin.Forms 应用添加简单 CocosSharp 视图：

1. [创建 Xamarin 窗体页](#1)
1. [添加 CocosSharpView](#2)
1. [创建 GameScene](#3)
1. [添加一个圆形](#4)
1. [与 CocosSharp 交互](#5)

后向 Xamarin.Forms 应用程序，你已成功添加 CocosSharp 视图，请访问[CocosSharp 文档](~/graphics-games/cocossharp/index.md)若要了解有关使用 CocosSharp 创建内容的详细信息。

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1.创建 Xamarin 窗体页

CocosSharp 可以承载于任何 Xamarin.Forms 容器。 此示例了解此页所使用的页调用`HomePage`。 `HomePage` 一半通过拆分`Grid`以显示 Xamarin.Forms 和 CocosSharp 可以呈现方式同时在同一页上。

首先，设置页面，使其包含`Grid`和两个`Button`实例：


```csharp
public class HomePage : ContentPage
{
public HomePage ()
    {
        // This is the top-level grid, which will split our page in half
        var grid = new Grid ();
        this.Content = grid;
        grid.RowDefinitions = new RowDefinitionCollection {
            // Each half will be the same size:
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
        };
        CreateTopHalf (grid);
        CreateBottomHalf (grid);
    }
    void CreateTopHalf(Grid grid)
    {
        // We'll be adding our CocosSharpView here:
    }
    void CreateBottomHalf(Grid grid)
    {
        // We'll use a StackLayout to organize our buttons
        var stackLayout = new StackLayout();
        // The first button will move the circle to the left when it is clicked:
        var moveLeftButton = new Button {
            Text = "Move Circle Left"
        };
        stackLayout.Children.Add (moveLeftButton);

        // The second button will move the circle to the right when clicked:
        var moveCircleRight = new Button {
            Text = "Move Circle Right"
        };
        stackLayout.Children.Add (moveCircleRight);
        // The stack layout will be in the bottom half (row 1):

        grid.Children.Add (stackLayout, 0, 1);
    }
}
```

在 iOS 上`HomePage`出现在下图中所示：

![](cocossharp-images/image3.png "主页屏幕快照")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2.添加 CocosSharpView

`CocosSharpView`类用于将 CocosSharp 嵌入到 Xamarin.Forms 应用。 由于`CocosSharpView`继承自[Xamarin.Forms.View](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)类，它提供熟悉的界面的布局，并且它可在布局容器内如[Xamarin.Forms.Grid](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)。 添加新`CocosSharpView`来完成向项目`CreateTopHalf`方法：


```csharp
void CreateTopHalf(Grid grid)
{
    // This hosts our game view.
    var gameView = new CocosSharpView () {
        // Notice it has the same properties as other XamarinForms Views
        HorizontalOptions = LayoutOptions.FillAndExpand,
        VerticalOptions = LayoutOptions.FillAndExpand,
        // This gets called after CocosSharp starts up:
        ViewCreated = HandleViewCreated
    };
    // We'll add it to the top half (row 0)
    grid.Children.Add (gameView, 0, 0);
}
```

CocosSharp 初始化不是即时的因此时注册一个事件`CocosSharpView`已完成其创建。 在执行此操作`HandleViewCreated`方法：


```csharp
void HandleViewCreated (object sender, EventArgs e)
{
    var gameView = sender as CCGameView;
    if (gameView != null)
    {
        // This sets the game "world" resolution to 100x100:
        gameView.DesignResolution = new CCSizeI (100, 100);
        // GameScene is the root of the CocosSharp rendering hierarchy:
        gameScene = new GameScene (gameView);
        // Starts CocosSharp:
        gameView.RunWithScene (gameScene);
    }
}
```

`HandleViewCreated`方法具有两个重要的详细信息，我们将考察。 第一种是`GameScene`类，该类将在下一节中创建。 务必要注意，应用程序将不会编译直到`GameScene`创建与`gameScene`实例引用被解析。

第二个重要的详细信息是`DesignResolution`属性，定义游戏的可见区域的 CocosSharp 对象。 `DesignResolution`属性将在创建后看`GameScene`。

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3.创建 GameScene

`GameScene`类继承自 CocosSharp 的`CCScene`。 `GameScene` 是其中我们处理纯 CocosSharp 的第一个点。 中包含代码`GameScene`或不存放在 Xamarin.Forms 项目中是否将在任何 CocosSharp 应用中的函数。

`CCScene`类是所有 CocosSharp 呈现 visual 根。 任何可见 CocosSharp 对象必须包含在`CCScene`。 更具体地说，必须将视觉对象添加到`CCLayer`实例和这些`CCLayer`实例必须添加到`CCScene`。

下图可帮助直观显示典型的 CocosSharp 层次结构：

![](cocossharp-images/image4.png "典型 CocosSharp 层次结构")

只有一个`CCScene`一次可以处于活动状态。 大多数游戏使用多个`CCLayer`到排序内容，但我们的应用程序的实例使用只有一个。 同样，大多数游戏使用多个视觉对象，但我们将只能有一个我们的应用程序。 更详细讨论在找不到可视层次结构 CocosSharp[会传来传去游戏演练](~/graphics-games/cocossharp/first-game/index.md)。

最初`GameScene`类将几乎是空-我们只需将创建它以满足中的引用`HomePage`。 将新类添加到名为你 PCL `GameScene`。 它应从继承`CCScene`类，如下所示：


```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

现在，`GameScene`是定义，我们可以返回到`HomePage`和添加字段：


```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

现在，我们可以编译我们的项目，并运行它以查看 CocosSharp 运行。 我们尚未添加任何内容到我们`GameScene,`因此我们的页面的上半是黑色 – CocosSharp 场景的默认颜色：

![](cocossharp-images/image5.png "Blank GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4.添加一个圆形

应用程序当前具有 CocosSharp 引擎，显示一个空的运行实例`CCScene`。 接下来，我们将添加视觉对象： 一个圆圈。 `CCDrawNode`类可以用于绘制各种几何形状中所述[CCDrawNode 指南绘制几何图形](~/graphics-games/cocossharp/ccdrawnode.md)。

添加与我们`GameScene`类和实例化此构造函数中下面的代码中所示：


```csharp
public class GameScene : CCScene
{
    CCDrawNode circle;
    public GameScene (CCGameView gameView) : base(gameView)
    {
        var layer = new CCLayer ();
        this.AddLayer (layer);
        circle = new CCDrawNode ();
        layer.AddChild (circle);
        circle.DrawCircle (
            // The center to use when drawing the circle,
            // relative to the CCDrawNode:
            new CCPoint (0, 0),
            radius:15,
            color:CCColor4B.White);
        circle.PositionX = 20;
        circle.PositionY = 50;
    }
}
```

现在运行应用程序 CocosSharp 显示区域左侧显示一个圆圈：

![](cocossharp-images/image6.png "GameScene 圈")


#### <a name="understanding-designresolution"></a>了解 DesignResolution

现在，将显示一个可视 CocosSharp 对象，我们可以调查`DesignResolution`属性。

`DesignResolution`表示的宽度和高度的 CocosSharp 区域的放置和调整对象大小。 实际的分辨率区域的大小的单位为*像素*时`DesignResolution`在世界测量*单位*。 下图显示的视图显示在 iPhone 5 与 640 x 1136 像素为单位的屏幕分辨率的各个部分的解决方法：

![](cocossharp-images/image7.png "iPhone 5 条设计解析")

上面的关系图显示像素维度上的黑色文本中的屏幕的外部。 单位内部的白色文本中的关系图上显示。 以下是一些重要的详细信息，如上所示：

* 在左下角是 CocosSharp 显示的原点。 向右移动增加的 X 值，并向上移动将增加的 Y 值。 请注意反转的 Y 值相比其他一些二维布局引擎，其中 (0，0) 是在画布的左上角。
* CocosSharp 的默认行为是保持其视图的纵横比。 由于网格中的第一行是宽度大于高度，CocosSharp 并未填满其的单元格的整个宽度的以点分隔的白色矩形所示。 可以更改此行为，如中所述[CocosSharp 中处理多个解决方案指南](~/graphics-games/cocossharp/resolutions.md)。
* 在此示例中，CocosSharp 将维护 100 个单位的宽度和高度而不考虑大小的显示区域或其设备的纵横比。 这意味着，代码可以假定，X = 100 表示最右边绑定的 CocosSharp 显示，在所有设备上一致的保留布局。


#### <a name="ccdrawnode-details"></a>CCDrawNode 详细信息

我们简单的应用程序使用`CCDrawNode`若要绘制圆形的类。 此类可以是对企业应用程序非常有用，因为它提供基于矢量的几何图形呈现 – Xamarin.Forms 中缺少的功能。 除了圆形`CCDrawNode`类可以用于绘制矩形、 样条、 线条和自定义的多边形。 `CCDrawNode` 也是易于使用由于不需要图像文件 （例如.png) 使用。 在找不到 CCDrawNode 的更多详细的讨论[CCDrawNode 指南绘制几何图形](~/graphics-games/cocossharp/ccdrawnode.md)。

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5.与 CocosSharp 交互

CocosSharp 可视元素 (如`CCDrawNode`) 继承`CCNode`类。 `CCNode` 提供两个属性用于确定相对于其父对象的位置：`PositionX`和`PositionY`。 下面的代码中当前使用这两个属性来定位的圆，中心此代码段中所示：


```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

请务必注意 CocosSharp 对象位于显式位置值，而不是大多数 Xamarin.Forms 视图，根据其父布局控件的行为自动调整位置。

我们将添加代码以允许用户单击其中一个由 10 个单位 （不像素为单位，因为在 CocosSharp 世界单元空间中绘制圆） 向左或向右移动圆的两个按钮。 首先，我们将创建两个公共方法中的`GameScene`类：


```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

接下来，我们将添加到中的两个按钮的处理程序`HomePage`以响应的单击。 完成后，我们`CreateBottomHalf`方法包含以下代码：


```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

单击响应现在移动 CocosSharp 圆圈。 我们可以通过将圆要足够远移动到左侧或右侧，也清楚地看到 CocosSharp 画布的边界：

![](cocossharp-images/image8.png "与移动圆 GameScene")

## <a name="summary"></a>摘要

本指南演示如何将 CocosSharp 添加到现有 Xamarin.Forms 项目，如何创建 Xamarin.Forms 和 CocosSharp，之间的交互，并讨论各种注意事项时在 CocosSharp 中创建布局。

CocosSharp 游戏引擎提供了大量功能和深度，因此本指南仅粗略 CocosSharp 可以执行哪些操作。 开发人员感兴趣阅读有关 CocosSharp 详细信息可以发现中的许多文章[CocosSharp 部分](~/graphics-games/cocossharp/index.md)。



## <a name="related-links"></a>相关链接

- [CocosSharp Api](https://developer.xamarin.com/api/root/CocosSharp/)
- [CocosSharpForms （示例）](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)
