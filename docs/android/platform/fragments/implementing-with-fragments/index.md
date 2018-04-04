---
title: 实现使用的碎片
description: Android 3.0 引入了片段。 片段是自包含的模块化组件，用于帮助解决可能在不同大小的屏幕上运行的写入应用程序的复杂性。 本文介绍如何使用片段开发 Xamarin.Android 应用程序，以及如何 3.0 的预 Android 设备上支持片段。
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 81f1f992de450ee62c4c1d2e80da858b024be594
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="implementing-with-fragments"></a>实现使用的碎片

_Android 3.0 引入了片段。片段是自包含的模块化组件，用于帮助解决可能在不同大小的屏幕上运行的写入应用程序的复杂性。本文介绍如何使用片段开发 Xamarin.Android 应用程序，以及如何 3.0 的预 Android 设备上支持片段。_


## <a name="overview"></a>概述

在此部分中，我们将演练如何创建的应用程序将显示莎士比亚的起到和从每个所选 play 的引号的列表。 我们的应用程序将利用片段，以便我们可以在一个位置，定义我们的 UI 组件，但然后使用这些不同的外形因素。 例如，以下屏幕截图显示运行在 10"平板电脑，以及在电话上的应用程序：

[![平板电脑和手机上运行的示例应用的屏幕快照](images/intro-screenshot-sml.png)](images/intro-screenshot.png#lightbox)

本部分将介绍以下主题：

- **创建片段**&ndash;演示如何创建要显示的莎士比亚的播放列表片段和另一个要显示从每个 play 引号片段。

- **支持不同的屏幕大小**&ndash;演示如何布局应用程序以利用更大的屏幕大小。

- **使用 Android 的支持包**&ndash;实现 Android 的支持包，然后给中应用程序，使其能够在较旧版本的 Android 上运行的活动做出少量更改。


## <a name="requirements"></a>要求

本演练需要 Xamarin.Android 4.0 或更高版本。 它还将安装 Android 的支持包，所需的片段文档中所述。


## <a name="introduction"></a>介绍

在示例中我们生成了在本部分中，活动不包含加载的列表，响应用户选择，或显示有关所选 play 引号逻辑。 此逻辑存在单个片段中。
通过将此逻辑放入本身的片段中，我们可以分解的工作流的应用程序以支持一个活动或使用多个活动的小屏幕大屏幕，而无需为每个活动编写不同的逻辑。 平板电脑上, 一个活动将为两个片段。 在手机上，片段将托管在不同的活动。

此应用程序包括以下部分：

 **MainActivity** – 显示一个或两个片段，具体取决于屏幕的大小。 这是启动活动。

 **TitlesFragment** – 显示的用户可以从中选择的莎士比亚的播放列表。

 **DetailsFragment** – 显示所选 play 引号。

 **DetailsActivity** – 承载并显示 DetailsFragment。
通过使用小的屏幕，如手机设备使用此活动。



## <a name="related-links"></a>相关链接

- [FragmentsWalkthrough （示例）](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [设计器概述](~/android/user-interface/android-designer/index.md)
- [Xamarin.Android 示例： Honeycomb 库](https://developer.xamarin.com/samples/HoneycombGallery/)
- [实现片段](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支持包](http://developer.android.com/sdk/compatibility-library.html)
