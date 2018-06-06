---
title: 在 Xamarin.iOS backgrounding
description: 后台处理或 backgrounding 是让应用程序在前台运行另一个应用程序时在后台执行任务的过程。 该指南可作为后台处理 iOS 中的简介。
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b22f3ef3276129f7f46c23cc1d06666f151f5ac4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783537"
---
# <a name="backgrounding-in-xamarinios"></a>在 Xamarin.iOS backgrounding

_后台处理或 backgrounding 是让应用程序在前台运行另一个应用程序时在后台执行任务的过程。该指南可作为后台处理 iOS 中的简介。_

Backgrounding 在移动应用程序是从根本上不同于在桌面上的多任务的传统概念。 台式计算机有多种可用于应用程序，包括屏幕的实际空间、 电源和内存资源。 应用程序都能够运行的并行并保持高性能且可用。 在移动设备上，资源是很多的限制。 很难在小屏幕上显示多个应用程序和全速运行多个应用程序，则将耗尽的电池。 Backgrounding 之间进行了常量折为应用程序提供的资源来运行后台任务所需很好地运行，并使 foregrounded 应用程序和设备保持响应状态。 IOS 和 Android 可以 backgrounding，但它们非常不同的方式处理它。

在 iOS 中，backgrounding 被识别为应用程序状态，并应用移入和移出应用和用户具体取决于行为的背景状态。 iOS 还提供了有关布线应用程序的多个选项在后台，包括要求要完成一项重要任务，作为一种已知背景需要应用程序，操作时间使操作系统中运行，并刷新应用程序的内容指定时间间隔。

在本指南和随附的演练中，我们将了解如何在后台执行应用程序任务。 我们将介绍主要概念和最佳实践，并且然后单步执行创建的实际应用程序在后台接收位置更新。

## <a name="contents"></a>内容

1.  [iOS 中的后台处理简介](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1.  [应用程序生命周期演示](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1.  [iOS 后台处理技术](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1.  [演练：iOS 中的后台处理](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1.  [iOS 后台处理指南](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>总结

在本指南中，我们引入了执行后台处理在 iOS 中的不同方法。 我们涵盖 iOS 应用程序状态和检查 backgrounding 在 iOS 应用程序生命周期中的扮演的角色。 此外，我们已了解如何，我们无法注册各个任务或整个应用程序在 iOS 中的背景中进行操作。 最后，我们强化我们了解在 iOS 上 backgrounding 通过构建的应用程序在后台中执行更新。



## <a name="related-links"></a>相关链接

- [在 Android 上 backgrounding](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo （示例）](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
- [位置 （示例）](https://developer.xamarin.com/samples/monotouch/Location/)
- [简单后台传输 （示例）](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
- [iOS 后台执行](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
