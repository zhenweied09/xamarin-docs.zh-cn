---
title: "触摸"
description: "许多现有设备上的触摸屏允许用户快速、 高效地交互与设备自然和直观的方式。 这种交互并不只限于简单触摸检测-可以使用手势以及。 例如，捏合缩放手势是一个非常常见的例子挤压用户可以放大或缩小的两个手指屏幕的一部分。本指南检查触摸，并在 Android 笔势。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A17FD28-313F-4AAC-B82B-3847B4D64A88
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 8a207b327706bc23d946e5ade0efabd3d321a1d1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="touch"></a>触摸

_许多现有设备上的触摸屏允许用户快速、 高效地交互与设备自然和直观的方式。这种交互并不只限于简单触摸检测-可以使用手势以及。例如，捏合缩放手势是一个非常常见的例子挤压用户可以放大或缩小的两个手指屏幕的一部分。本指南检查触摸，并在 Android 笔势。_

## <a name="touch-overview"></a>触摸概述

iOS 和 Android 是它们处理触摸方式类似。 它们都可以支持多点触控在屏幕上的联系人的许多点-和复杂的笔势。 本指南介绍一些概念中的相似之处以及实现触摸 particularities 并笔势均这两个平台。

Android 使用`MotionEvent`对象封装触摸数据和要侦听的收尾工作的视图对象上的方法。

除了捕获触摸数据，iOS 和 Android 提供种解释模式到手势收尾工作。 这些笔势识别器反过来可用来解释特定于应用程序的命令，如图像的旋转或页面的一个轮次。 Android 提供几种支持的手势，以及资源，以使添加复杂的自定义笔势轻松。

无论你正在 Android 或 iOS 上，触控和手势识别器之间的选择可以是一个令人困惑。 本指南建议，一般情况下，首选项应授予给笔势识别器。 笔势识别器作为离散的类，该类提供更大程度分离问题和更好地封装的实现。 这使得可以方便地共享不同的视图，最大限度编写的代码之间的逻辑。

本指南后面的每个操作系统的类似格式： 首先，该平台的触摸 Api 是引入和所述，因为它们是的触摸构建交互的基础。 然后，我们深入探讨笔势识别器 – 世界首先通过浏览一些常见的手势，并完成创建应用程序的自定义特定动作。 最后，你将了解如何跟踪各个指使用低级别触摸跟踪以创建 finger-paint 程序。

## <a name="sections"></a>部分

-  [Android 中的触控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [演练： 在 Android 中使用触摸](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [多点触控跟踪](touch-tracking.md)

## <a name="summary"></a>摘要

在本指南中，我们将探讨在 Android 中的触控。 对于这两个操作系统，我们学习了如何启用触摸以及如何响应触控事件。 接下来，我们所了解的有关笔势和某些笔势识别器，Android 和 iOS 提供来处理某些更常见的方案。 我们探讨了如何创建自定义笔势和应用程序中实现它们。 演练演示的概念和 Api 了为每个操作系统在操作，您还了解到如何跟踪各个指。



## <a name="related-links"></a>相关链接

- [Android Touch 启动 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch 最终 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [FingerPaint （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
