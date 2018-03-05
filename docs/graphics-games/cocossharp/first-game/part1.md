---
title: "创建多平台 CocosSharp 项目"
description: "本演练演示如何创建新的多平台 CocosSharp 解决方案。 本演练的结果是 Visual Studio for Mac 解决方案包括三个项目： 一个可移植类库项目、 一个特定于 Android 的项目和一个特定于 iOS 的项目。 生成的项目将显示一个空的黑色屏幕时执行。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 37C97693-B0A8-4064-97B6-A6FAB5BA4FB7
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 2906035ce9bd44d111b89ccfe7443896775315b7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-multi-platform-cocossharp-project"></a>创建多平台 CocosSharp 项目

_本演练演示如何创建新的多平台 CocosSharp 解决方案。本演练的结果是 Visual Studio for Mac 解决方案包括三个项目： 一个可移植类库项目、 一个特定于 Android 的项目和一个特定于 iOS 的项目。生成的项目将显示一个空的黑色屏幕时执行。_

CocosSharp 二维游戏引擎允许代码和要在多个平台之间共享内容。 本演练演示如何创建可支持 iOS 和 Android 开发的项目。 具体而言，本演练将涉及以下主题：

 - 安装 CocosSharp
 - 创建新的解决方案
 - `LoadGame` 方法

# <a name="installing-cocossharp"></a>安装 CocosSharp

首先，我们将添加 CocosSharp 到 Visual Studio for mac。 如果在 Mac 上运行，则选择**Visual Studio for Mac** > **加载项管理器...** . 如果在 Windows 上运行，则选择**工具** > **加载项管理器...** . 单击**库**选项卡上，展开**CocosSharp 项**，选择**CocosSharp 项目模板**，最后单击**安装...** .

![CocosSharp 外接程序](part1-images/xamarinstudioaddinsmac.png "")

# <a name="creating-a-new-solution"></a>创建新解决方案

安装 CocosSharp 后，我们将创建一个解决方案。 在适用于 Mac 的 Visual Studio，选择**文件** > **新建** > **解决方案...**.选择**应用**选项下**跨平台**部分中，选择**CocosSharp 空项目**，然后单击**下一步**:

![](part1-images/image1.png "选择跨平台部分下的应用选项，选择 CocosSharp 空项目，然后单击下一步")

输入的名称**BouncingGame**为**项目名称**，然后单击**创建**:

![](part1-images/image2.png "名称 BouncingGame 输入项目名称，然后单击创建")

一旦创建项目和适用于 Mac 的 Visual Studio，我们可以编译和运行它以查看灰色背景： 

![](part1-images/image3.png "一旦项目已创建且 Visual Studio for Mac，编译并运行它以查看灰色背景")


# <a name="loadgame-method"></a>LoadGame 方法

默认 CocosSharp 项目包含类特定于 iOS 和 Android 设置`CCGameView`，用于启动 CocosSharp。 `CCGameView`实例创建的特定于平台的方式： iOS 项目创建`CCGameView`中`Main.storyboard`文件，当 Android 创建`CCGameView`中`Main.axml`文件。 每个平台使用中的 CCGameView 实例`LoadGame`方法用于执行一些基本的安装程序。 即使我们不会修改此代码，让我们看一看一些重要详细信息：

 - 该代码设置`gameView.DesignResolution`到 1024 × 768。 这可使标准化定位跨设备而不考虑当前设备的纵横比、 物理分辨率或方向。 
 - 该代码将添加几个搜索路径。 搜索路径还允许内容要加载没有目录前缀。 例如，由于`"Sounds"`路径添加搜索路径，则目录中的文件为`"Content/Sounds/mysound.xnb"`只是无法加载为`"mysound.xnb"`。 搜索路径是类似于`using`语句在代码中 – 它们可以降低代码中，但它们还可能会导致多义性。
 - 代码构造了`GameLayer`实例。 `GameLayer` 是`CCLayer`-继承其中我们将添加所有我们游戏逻辑的类。 更大的游戏可能需要多个`CCLayer`实例或甚至多个`CCScene`实例 (其中可以包含多个`CCLayer`实例)，但我们将只讨论单个`CCLayer`为此游戏。

#  <a name="summary"></a>摘要

本演练介绍如何创建跨平台 CocosSharp 项目使用 Visual Studio for mac。 结果是空的屏幕，其中可以用作任何游戏项目的起始点。