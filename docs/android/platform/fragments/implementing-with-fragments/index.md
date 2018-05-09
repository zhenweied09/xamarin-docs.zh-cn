---
title: 实现片段的演练
description: 本文介绍如何使用片段开发 Xamarin.Android 应用程序。
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 92c68298d7abd2570efd89e12d7cfb6364e90972
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="implementing-fragments---walkthrough"></a>实现片段的演练

_片段是自包含、 模块化组件，可帮助您解决有各种屏幕大小的目标设备的 Android 应用的复杂性。本文介绍如何创建和开发 Xamarin.Android 应用程序时使用片段。_

## <a name="overview"></a>概述

在本部分中，您将演练如何创建和使用在 Xamarin.Android 应用程序中的片段。 此应用程序将通过 William 莎士比亚显示列表中的多个起到的标题。 当用户点击 play 的标题时，应用程序将在单独的活动中显示从该 play 引号：

[![纵向模式在 Android 手机上运行应用](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

如果电话旋转以横向模式，将更改应用的外观： 列表起到和引号将出现在相同的活动。 选择播放时，引号将显示在同一活动中：

[![在横向模式中 Android 手机上运行应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

最后，如果在平板电脑上运行应用程序：

[![在 Android 平板电脑上运行的应用程序](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

此示例应用程序可以轻松地调整以适应不同的外形因素和最少的代码更改的方向使用片段和[备用布局](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources)。

应用程序的数据将存在两个 C# 的字符串数组的形式在应用程序的硬编码的字符串数组中。 每个数组将充当一个片段的数据源。  一个数组中将容纳莎士比亚，通过某些起到的名称以及与另一数组将保存该 play 引号。 当应用程序启动时，它将显示中的播放名称`ListFragment`。 当用户单击在 play 上`ListFragment`，应用程序将启动另一个活动，这将显示引号。

应用程序的用户界面将包括两个布局、 一个用于纵向和横向模式的一个。 在运行时，Android 将确定哪些布局以加载基于设备的方向，并将向要呈现的活动提供该布局。 片段中，将包含所有响应用户单击和显示数据的逻辑。 仅作为容器将承载片段存在应用程序中的活动。

本演练分为两个指南。 [首先部件](./walkthrough.md)将重点放在应用程序的核心部分。 将创建一组布局 （已优化的纵向模式），以及两个片段和两个活动：

1. `MainActivity` &nbsp; 这是应用程序的启动活动。
1. `TitlesFragment` &nbsp; 此代码段将显示标题编写的 William 莎士比亚的播放的列表。 它将通过托管`MainActivity`。
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` 将启动`PlayQuoteActivity`以响应用户选择在 play `TitlesFragment`。
1. `PlayQuoteFragment` &nbsp; 此代码段将显示 William 莎士比亚从 play 引号。 它将通过托管`PlayQuoteActivity`。

[倒数第二次本演练过程](./walkthrough-landscape.md)将讨论添加 （已优化的横向模式） 的备用布局，这将显示在屏幕上的两个片段。 此外，一些细微的代码更改将对代码进行，以便应用程序将会根据其行为与的同时屏幕显示的片段数。

## <a name="related-links"></a>相关链接

- [FragmentsWalkthrough （示例）](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [设计器概述](~/android/user-interface/android-designer/index.md)
- [实现片段](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支持包](http://developer.android.com/sdk/compatibility-library.html)
