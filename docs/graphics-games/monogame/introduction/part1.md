---
title: "第 1 – 创建跨平台 MonoGame 部分"
description: "本演练演示如何创建用于 iOS 和 Android 使用 MonoGame 的新项目。 结果是 Visual Studio for Mac 与一个跨平台共享的代码项目，以及为每个平台的一个项目的解决方案。 此项目将显示一个空的蓝色屏幕时执行。"
ms.topic: article
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 0d1352b4129dc1cf8be42e813787b9b73f80cd3e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>第 1 – 创建跨平台 MonoGame 部分

_本演练演示如何创建用于 iOS 和 Android 使用 MonoGame 的新项目。结果是 Visual Studio for Mac 与一个跨平台共享的代码项目，以及为每个平台的一个项目的解决方案。此项目将显示一个空的蓝色屏幕时执行。_

MonoGame，可以使用大部分代码重用的跨平台游戏开发。 本演练将重点介绍设置包含适用于 iOS 和 Android 的项目，以及用于跨平台代码的共享的代码项目的解决方案。

操作完成后，我们已具有正确执行游戏更新逻辑结构的项目，游戏在 30 秒帧数绘制逻辑。 任何 MonoGame 项目，可以为基的项目使用它。 我们的项目将如下所示执行时：

![](part1-images/image1.png "项目将如下所示执行时")


# <a name="adding-monogame-to-visual-studio-for-mac"></a>将 MonoGame 添加到 Visual Studio 中，for Mac

MonoGame 可以作为添加外接程序到 Visual Studio for mac。 在 Mac 上，选择**Visual Studio for Mac** > **加载项管理器...** . 在 Windows 上，选择 * * 工具 * * >**加载项管理器...** . 选择**库**选项卡上，展开**游戏开发**类别，然后选择**MonoGame 外接程序**，然后单击**安装**:

![](part1-images/image2.png "选择库选项卡，展开游戏开发类别并选择 MonoGame 外接程序，然后单击安装")

> [!IMPORTANT]
> **请注意**： 如果**游戏开发**部分未显示在外接程序管理器中，你可以手动下载并可从此处安装最新版本： http://www.monogame.net/downloads/。 你可能需要重启 Visual Studio 模板的 Mac，以显示。



安装完成后，MonoGame 模板将显示在 Visual Studio 中的 Mac 上，我们将会看到在下一节中。


# <a name="creating-a-new-solution"></a>创建新的解决方案

Visual Studio 中用于 Mac 选择**文件 > 新解决方案**。 在**新项目**对话框中，单击**杂项**，滚动到**常规**部分中，选择 * * 通用 MonoGame 移动应用程序 * * 选项，然后单击下一步。

![](part1-images/image3.png "在新建项目对话框中，单击杂项，滚动到常规部分，选择通用 MonoGame 移动应用程序选项，然后单击下一步")

命名项目 WalkingGame，并单击创建:

![](part1-images/image4.png "命名项目 WalkingGame 并单击创建")

现在我们的项目将执行就像任何其他 iOS 或 Android 项目一样。 该项目应运行显示藏蓝色背景：

![](part1-images/image5.png "该项目应运行显示藏蓝色背景")


# <a name="fixing-android-compile-errors"></a>修复 Android 编译错误

MonoGame 的模板的当前版本包括在 Android 中的几个语法错误`Activity1.cs`文件。 若要解决这些问题，请替换`OnCreate`函数替换为以下：


```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```


# <a name="summary"></a>摘要

本演练介绍如何创建跨平台 MonoGame 项目使用 Visual Studio for mac。 此操作的结果是一个空的蓝色屏幕。 此项目可以用作任务的起始点的任何 iOS 和 Android 游戏。

## <a name="related-links"></a>相关链接

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文档](http://www.monogame.net/documentation/?page=main)
