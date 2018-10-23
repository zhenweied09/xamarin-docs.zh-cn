---
title: 第 1 – 创建跨平台 MonoGame 部分
description: 本演练演示如何创建用于 iOS 和 Android 使用 MonoGame 的新项目。 结果是 Visual Studio for Mac 与一个跨平台共享的代码项目，以及为每个平台的一个项目的解决方案。 此项目将显示一个空的蓝色屏幕时执行。
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: bd7990b94e678c205f9ce636f4eb0d28180fc6ec
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921984"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>第 1 – 创建跨平台 MonoGame 部分

_本演练演示了如何使用MonoGame为iOS和Android创建新项目。 演练的结果是一个Visual Studio for Mac的解决方案，其中包含跨平台共享代码工程以及每个平台的一个工程。 执行时，该项目将显示空蓝屏。_

MonoGame支持开发具有大部分代码重用的跨平台游戏。本演练将重点介绍如何设置包含iOS和Android工程以及跨平台代码的共享代码工程的解决方案。

当操作完成后，我们将拥有一个具有适当结构的项目，并以每秒30帧的速度执行游戏更新逻辑和游戏绘图逻辑。它可以用作任何MonoGame项目的基础工程。执行时我们的项目将如下所示：

![空白的蓝色屏幕](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio-for-mac"></a>将 MonoGame 添加到 Visual Studio for Mac中

可以将MonoGame添加为Visual Studio for Mac的插件。 在Mac上，选择**Visual Studio for Mac**> **Add-in Manager...** . 在Windows上，选择**Tools**>**Add-in Manager...**. 选择**Gallery**选项卡，展开**Game Development**类别并选择**MonoGame Addin**，然后单击**Install**：

![选择 MonoGame Mac 扩展库的的 visual Studio](part1-images/image2.png)

> [!IMPORTANT]
> **注意**：如果**Game Development**部分未出现在Add-in Manager中，您可以从此处手动下载并安装最新版本：http：//www.monogame.net/downloads/。 您可能需要重新启动Visual Studio for Mac才能显示模板。

安装完成后，MonoGame模板将出现在Visual Studio for Mac中，我们将在下一节中看到。

## <a name="creating-a-new-solution"></a>创建新的解决方案

在Visual Studio for Mac中，选择**File > New Solution**。在**New Project**对话框中，单击**Miscellaneous**，滚动到**General**部分，选择**Universal MonoGame Mobile application**选项，然后单击Next。

![创建 MonoGame 应用程序的新建项目对话框](part1-images/image3.png)

将项目命名为WalkingGame，然后单击Create：

![选取的名称和位置的新建项目对话框](part1-images/image4.png)

现在我们的项目将像任何其他iOS或Android项目一样执行。 项目运行时应该显示藏蓝色背景：

![空白的蓝色应用背景](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>修复 Android 编译错误

当前版本的MonoGame模板在Android的`Activity1.cs`文件中有一些语法错误。 如果要解决这些问题，请使用以下内容替换OnCreate函数：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>总结

本演练介绍了如何使用Visual Studio for Mac创建跨平台的MonoGame项目。 此演练的结果是一个空的蓝色屏幕。该项目可以作为任何iOS和Android游戏的起点。

## <a name="related-links"></a>相关链接

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文档](http://www.monogame.net/documentation/?page=main)
