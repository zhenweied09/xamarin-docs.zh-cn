---
title: 在 Xamarin 的进度指示器使用 tvOS
description: 本文档介绍如何使用在 tvOS 应用程序中使用 Xamarin 生成的进度指示器。 它讨论进度条和活动指示器。
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/25/2018
ms.openlocfilehash: f8812f6b3f8a461487dcaf548637c84b16631d6b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789211"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>在 Xamarin 的进度指示器使用 tvOS

_本文介绍如何设计和使用 Xamarin.tvOS 应用内的进度指示器。_

可能有 Xamarin.tvOS 应用需要加载新内容或执行时间较长的处理操作的时间。 在这些时段内你应呈现活动指示器或让用户知道应用程序仍在运行并为它们提供正在运行的任务的长度某些指明一个进度栏。

![示例进度指示器](progress-indicators-images/intro01.png "示例进度指示器")

## <a name="about-activity-indicators"></a>有关活动指示器

活动指示器显示，并且旋转齿轮，用于表示将无法确定长度的任务。 当任务启动并消失完成此任务显示指示器。

Apple 具有用于处理活动指示器以下建议：

- **只要有可能，请改为使用进度栏**-因为如果长度已知 （例如，若要下载的文件中的字节数） 后，活动指示器提供的用户如何没有反馈长正在运行的进程将执行，将始终使用一个进度栏。
- **保留进行动画处理的指示器**-用户与静态活动指示器相关到已停止的应用程序，因此显示它时，应始终动画指示器。
- **描述任务正在处理**-只需单独显示活动指示器还不够; 用户需要了解在其等待的过程。 包含任务均明确定义一个有意义标签 （通常单个、 完整句子）。

## <a name="about-progress-bars"></a>有关进度栏

为用来指示的状态和长度的耗时的任务的颜色填充一条线显示一个进度栏。 任务的长度为已知或可以计算时，应始终使用进度栏。

Apple 具有以下建议用于使用进度栏：

- **准确地报告进度**-进度栏应始终存在的准确表示形式来完成任务所需的时间。 永远不会有意使应用程序显示繁忙的时间。
- **用于定义完善的持续时间**-进度条不应仅显示花费时间较长的任务将放置，但为用户和任务中有多少已完成的指示和估计剩余时间。

## <a name="progress-indicators-and-storyboards"></a>进度指示器和情节提要

进度指示器 Xamarin.tvOS 应用中使用的最简单方法是将其添加到使用 iOS 设计器的应用程序的 UI。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 在**解决方案 Pad**，双击**Main.storyboard**文件，并打开以进行编辑。

2. 拖动**活动指示器**从**工具箱**并将其放在视图上： 

    ![活动指示器](progress-indicators-images/activity01.png "活动指示器")

3. 在**小组件**选项卡**属性填充**，你可以调整活动指示器的几个属性，如其**样式**，**行为**，和**名称**: 

    ![用于活动的指示符小组件选项卡](progress-indicators-images/activity02.png "活动指示器的小组件选项卡")
    
    **名称**确定表示该活动指示器在 C# 代码中的属性的名称。

4. 拖动**进度视图**从**工具箱**并将其放在视图上： 

    ![进度视图](progress-indicators-images/activity03.png "进度视图")

5. 在**小组件**选项卡**属性资源管理器**，你可以调整进度视图的几个属性，如其**样式**，**进度**（完成百分比）、 和**名称**: 

    ![小组件选项卡查看进度图](progress-indicators-images/activity04.png "进度视图的小组件选项卡")
    
    **名称**确定表示 C# 代码中的进度视图的属性的名称。

6. 保存更改。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 在**解决方案资源管理器**，双击**Main.storyboard**文件，并打开以进行编辑。

2. 拖动**活动指示器**从**工具箱**并将其放在视图上： 

    ![活动指示器](progress-indicators-images/activity01-vs.png
    "活动指示器")

3. 在**小组件**选项卡**属性资源管理器**，你可以调整活动指示器的几个属性，如其**样式**，**行为**，和**名称**: 

    ![用于活动的指示符小组件选项卡](progress-indicators-images/activity02-vs.png "活动指示器的小组件选项卡")

    **名称**确定表示该活动指示器在 C# 代码中的属性的名称。

4. 拖动**进度视图**从**工具箱**并将其放在视图上： 

   ![进度视图](progress-indicators-images/activity03-vs.png "进度视图")

5. 在**小组件**选项卡**属性资源管理器**，你可以调整进度视图的几个属性，如其**样式**，**进度**（完成百分比）、 和**名称**: 

    ![小组件选项卡查看进度图](progress-indicators-images/activity04-vs.png "进度视图的小组件选项卡")
    
    **名称**确定表示 C# 代码中的进度视图的属性的名称。

6. 保存更改。

-----

有关使用情节提要的详细信息，请参阅我们[Hello，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

## <a name="working-with-activity-indicators"></a>使用活动指示器

如上面所述，在你的应用程序不确定长度长进程内运行时，应显示活动指示器。

在任何时候，你可以查看活动指示器具有动画效果通过检查其`IsAnimating`属性。 如果`HidesWhenStopped`属性是`true`，停止其动画时，将自动隐藏活动指示器。

下面的代码可用于启动动画： 

```csharp
ActivityIndicator.StartAnimating();
```

并且，以下将停止动画：

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> 这些代码段假定活动指示器**名称**已设置为**ActivityIndicator**中**小组件**的 ios 设计器的选项卡。

## <a name="working-with-progress-bars"></a>使用进度栏

同样，一个进度栏应使用你的应用程序正在执行的已知的持续时间的一个长时间运行任务的任何时间。 

`Progress`属性用于设置为 100%(0.0 到 1.0) 完成从 0%的任务的量。 使用`ProgressTintColor`属性设置已完成的量条形的颜色和`TrackTintColor`属性设置的背景色 （未完成的量）。

## <a name="summary"></a>总结

本文已覆盖设计和使用 Xamarin.tvOS 应用内的进度指示器。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
