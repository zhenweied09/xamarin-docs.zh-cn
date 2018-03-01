---
title: "片段"
description: "Android 3.0 引入了片段，显示如何在手机和平板电脑上找到的许多不同的屏幕大小支持更灵活的设计。 本文将介绍如何使用片段开发 Xamarin.Android 应用程序，以及如何在预 Android 3.0 (API 级别 11) 设备上支持片段。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 0486b9e4371a1bcab02921da42bcb929f00a782f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="fragments"></a>片段

_Android 3.0 引入了片段，显示如何在手机和平板电脑上找到的许多不同的屏幕大小支持更灵活的设计。本文将介绍如何使用片段开发 Xamarin.Android 应用程序，以及如何在预 Android 3.0 (API 级别 11) 设备上支持片段。_

## <a name="fragments-overview"></a>片段概述

在大多数平板电脑上找到更大的屏幕大小添加到 Android 开发的一层额外的复杂性 — 一种布局设计为在小屏幕不一定适合也更大的屏幕，反之亦然。 若要减少的复杂性，这就产生了数，Android 3.0 添加两项新功能，*片段*和*支持包*。

片段可视为用户界面模块。 它们使开发人员可以在单独的活动中运行的独立的可重用部件的用户界面分割。 在运行时，活动自身将决定要使用的片段。

支持包最初调用*兼容性库*和允许的片段以在运行版本的 Android 3.0 (API 级别 11) 之前的 Android 设备上使用。

例如下, 图显示了单个应用程序如何使用跨不同设备的外形因素的片段。

[![如何在平板电脑和手机使用片段的关系图](images/00.png)](images/00.png)

*片段 A*包含列表，而*片段 B*包含该列表中选定的项的详细信息。 当平板电脑上运行应用程序时，它可以在同一个活动上显示两个片段。 当同一个应用程序运行 （使用其较小的屏幕大小） 手持设备上时，两个单独的活动中托管片段。 片段 A 和片段 B 上都相同这两种外形因素，但将其托管的活动不同。

为了帮助协调和管理所有这些片段的活动，Android，引入了新的类称为*FragmentManager*。 每个活动具有自己的实例`FragmentManager`用于添加、 删除和查找承载片段。 下图说明了片段和活动之间的关系：

[![图示活动、 片段管理器中和片段之间的关系](images/01.png)](images/01.png)

在某些方面，片段可以认为是作为复合控件或迷你活动。 它们捆绑到可重用由在活动中的开发人员然后可单独使用的模块的部分 UI 向上。 片段不必查看层次结构，就像活动-但与不同的是一项活动，它可以共享跨屏幕。 视图与不同片段，片段具有其自己的生命周期;视图不希望这样做。

虽然该活动是一个或多个片段的主机，不直接感知到的本身的片段。 同样，片段不是直接感知到的其他片段中托管的活动的。 但是，片段和活动都认识`FragmentManager`中其活动。 通过使用`FragmentManager`，它是活动或片段，可以获得对片段的特定实例的引用，然后对该实例中调用方法。 这种方式，可以通信的活动或片段并将其与其他片段进行交互。

本指南包含有关如何使用片段，包括的全面介绍：

-   **创建片段**– 如何创建基本片段和必须实现的主要方法。
-   **管理和事务片段**– 如何在运行时操作片段。
-   **Android 支持包**– 如何为使用允许库片段用于在较旧版本的 Android。


## <a name="requirements"></a>惠?

片段位于 Android SDK 开头 API 级别 11 (Android 3.0)，如下面的屏幕截图中所示：

[![选择在 Android SDK 管理器中的 API 级别](images/02.png)](images/02.png)

片段是位于 Xamarin.Android 4.0 和更高版本。 Xamarin.Android 应用程序必须至少为目标 API 级别 11 (Android 3.0) 或更高版本才能使用片段。 可能的项目选项中设置目标框架，如下所示：

[![在项目选项中设置的目标框架 API 级别](images/03.png)](images/03.png)

很可能要在较旧版本的 Android 通过使用 Android 的支持包和 Xamarin.Android 4.2 或更高版本中使用片段。 在本部分文档中的更详细地介绍了如何执行此操作。


## <a name="related-links"></a>相关链接

- [Honeycomb 库 （示例）](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [片段](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支持包](http://developer.android.com/sdk/compatibility-library.html)
- [MOTODEV 网络研讨会： 引入片段](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
