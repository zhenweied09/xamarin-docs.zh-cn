---
title: 使用进度指示器
description: 本文介绍如何设计和使用在 Xamarin.tvOS 应用内的进度指示器。
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 96fc3ea0aa802f62bd697b34f7bd504eb445a4f6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-progress-indicators"></a>使用进度指示器

_本文介绍如何设计和使用在 Xamarin.tvOS 应用内的进度指示器。_


可能有 Xamarin.tvOS 应用需要加载新内容或执行时间较长的处理操作的时间。 在这些时段内你应呈现活动指示器或进度栏让用户知道应用程序仍在运行并为它们提供某些指明正在运行的任务的长度。

[![](progress-indicators-images/intro01.png "示例进度指示器")](progress-indicators-images/intro01.png#lightbox)

<a name="About-Activity-Indicators" />

## <a name="about-activity-indicators"></a>有关活动指示器

活动指示器以可视方式显示，并且旋转齿轮，用于表示将无法确定长度的任务。 当任务启动并消失完成此任务显示指示器。

Apple 具有用于处理活动指示器以下建议：

- **只要有可能，请改用进度条**-因为活动指示器向用户不提供任何反馈需要多长时间运行的过程，始终使用一个进度栏，如果长度知道 （例如，若要下载的文件中的字节数）。
- **保留指示器动画**-用户： 与静态活动指示器相关到已停止的应用程序，因此你始终应该具有动画处理时所显示的指示器。
- **描述任务正在处理**-只需单独显示活动指示器还不够，用户需要了解它们正在等待的过程。 包含任务均明确定义一个有意义标签 （通常单个、 完整句子）。

<a name="Summary" />

## <a name="about-progress-bars"></a>有关进度栏

为用来指示的状态和长度的耗时的任务的颜色填充一条线显示一个进度栏。 任务的长度是知道或可以计算时，应始终使用进度栏。

Apple 具有以下建议用于使用进度栏：

- **准确地报告进度**-进度栏应始终为准确地反映来完成任务所需的时间。 永远不会有意使应用程序显示繁忙的时间。
- **用于 Well-Defined 持续时间**-进度栏不应该仅显示花费时间较长的任务将放置，但为用户和任务中有多少已完成的指示和估计剩余时间。

<a name="Progress-Indicators-and-Storyboards" />

## <a name="progress-indicators-and-storyboards"></a>进度指示器和情节提要

使用 Xamarin.tvOS 应用中的进度指示器的最简单方法是将它们添加到使用 iOS 设计器的应用程序的 UI。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 在**解决方案 Pad**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**活动指示器**从**工具箱**并将其放在视图上： 

    [![](progress-indicators-images/activity01.png "活动指示器")](progress-indicators-images/activity01.png#lightbox)
1. 在**小组件选项卡**的**属性填充**，你可以调整活动指示器的几个属性，如其**样式**和**行为**: 

    [![](progress-indicators-images/activity02.png "小组件选项卡 ")](progress-indicators-images/activity02.png#lightbox)
1. 拖动**进度视图**从**工具箱**并将其放在视图上： 

    [![](progress-indicators-images/activity03.png "进度视图")](progress-indicators-images/activity03.png#lightbox)
1. 在**小组件选项卡**的**属性资源管理器**，你可以调整进度视图中的几个属性，如其**样式**和**进度**（完成百分比）： 

    [![](progress-indicators-images/activity04.png "小组件选项卡")](progress-indicators-images/activity04.png#lightbox)
1. 最后，将分配**名称**到控件，以便可以在 C# 代码中响应它们。 例如： 

    [![](progress-indicators-images/activity05.png "指定一个名称")](progress-indicators-images/activity05.png#lightbox)
1. 保存更改。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 在**解决方案资源管理器**，双击`Main.storyboard`文件，并打开以进行编辑。
1. 拖动**活动指示器**从**工具箱**并将其放在视图上： 

    [![](progress-indicators-images/activity01-vs.png "活动指示器")](progress-indicators-images/activity01-vs.png#lightbox)
1. 在**小组件选项卡**的**属性资源管理器**，你可以调整活动指示器的几个属性，如其**样式**和**行为**: 

    [![](progress-indicators-images/activity02-vs.png "小组件选项卡")](progress-indicators-images/activity02-vs.png#lightbox)
1. 拖动**进度视图**从**工具箱**并将其放在视图上： 

    [![](progress-indicators-images/activity03-vs.png "进度视图")](progress-indicators-images/activity03-vs.png#lightbox)
1. 在**小组件选项卡**的**属性资源管理器**，你可以调整进度视图中的几个属性，如其**样式**和**进度**（完成百分比）： 

    [![](progress-indicators-images/activity04-vs.png "小组件选项卡")](progress-indicators-images/activity04-vs.png#lightbox)
1. 最后，将分配**名称**到控件，以便可以在 C# 代码中响应它们。 例如： 

    [![](progress-indicators-images/activity05-vs.png "指定一个名称")](progress-indicators-images/activity05-vs.png#lightbox)
1. 保存更改。

-----

有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Activity-Indicators" />

## <a name="working-with-activity-indicators"></a>使用活动指示器

如上面所述，当你的应用程序正在运行一个长的过程，但你不知道确切的任务所需的时间长度应显示活动指示器。

随时可以查看活动指示器是否通过检查运行其旋转动画`IsAnimating`属性。 如果`HidesWhenStopped`属性是`true`，停止其动画时，将自动隐藏活动指示器。

下面的代码可用于启动动画： 

```csharp
ActivityIndicator.StartAnimating();
```

并且，以下将停止动画：

```csharp
ActivityIndicator.StopAnimating();
```

<a name="Working-with-Progress-Bars" />

## <a name="working-with-progress-bars"></a>使用进度栏

同样，一个进度栏应使用你的应用程序正在执行长时间运行任务已知持续时间的任何时间。 

`Progress`属性用于设置为 100%(0.0 到 1.0) 完成从 0%的任务的量。 使用`ProgressTintColor`属性设置已完成的量条形的颜色和`TrackTintColor`属性设置的背景色 （未完成的量）。

<a name="Summary" />

## <a name="summary"></a>总结

本文已覆盖设计和使用在 Xamarin.tvOS 应用内的进度指示器。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
