---
title: "进度和活动指示器"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: b91c0d7504b391630beded7a52e240a0d043152f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="progress-and-activity-indicators"></a>进度和活动指示器

很可能你的应用程序将需要执行长时间运行任务，例如加载或处理数据和，此延迟可能会导致延迟更新你的 UI。 在此期间你应始终使用进度指示器，可以放心用户系统正忙于执行工作。 这样，应用程序工作在其请求时，不等待其输入的用户控件，可以提供一种详细列出它们必须完全多长时间等待。

iOS 提供两种主要方法以提供您的应用程序中此进度指示： 活动指示器 (包括特定_网络_活动指示器) 和进度栏。

## <a name="activity-indicator"></a>活动指示器

当你的应用程序正在运行一个长的过程，但你不知道确切的任务所需的时间长度，则应显示活动指示器。

Apple 具有用于处理活动指示器以下建议：

- **只要有可能，请改用进度条**-因为活动指示器向用户不提供任何反馈需要多长时间运行的过程，始终使用一个进度栏，如果长度知道 （例如，若要下载的文件中的字节数）。
- **保留指示器动画**-用户： 与静态活动指示器相关到已停止的应用程序，因此你始终应该具有动画处理时所显示的指示器。
- **描述任务正在处理**-只需单独显示活动指示器还不够，用户需要了解它们正在等待的过程。 包含任务均明确定义一个有意义标签 （通常单个、 完整句子）。

### <a name="implementing-an-activity-indicator"></a>实现活动指示器

通过实现活动指示器[ `UIActivityIndictorView` ](https://developer.xamarin.com/api/type/UIKit.UIActivityIndicatorView/)类，则指示`UIActivity`正在进行。

### <a name="activity-indicators-and-storyboards"></a>活动指示器和情节提要

如果使用 iOS 设计器来创建你的 UI，则活动指示器可以添加到你的布局中从工具箱。 可以从属性填充调整以下属性：

![属性填充](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>管理活动指示器行为

使用`StartAnimating()`和`StopAnimating()`方法来启动和停止活动指示器动画。

设置`HidesWhenStopped`属性`true`进行消失后活动指示器`StopAnimating()`已调用。 此值设置为`true`默认情况下。 随时可以查看活动指示器是否通过检查运行其旋转动画`IsAnimating`属性。 


### <a name="managing-activity-indicator-appearances"></a>管理活动指示器外观

`UIActivityIndicatorViewStyle`在实例化活动指示器时，枚举可以作为参数传递。 你可以使用这将的视觉样式设置为`Gray`， `White`，或`WhiteLarge`，例如：

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

您可以重写由提供的颜色`UIActivityIndicatorViewStyle`通过设置`Color`属性。

## <a name="progress-bar"></a>进度栏

为用来指示的状态和长度的耗时的任务的颜色填充一条线显示一个进度栏。 任务的长度是知道或可以计算时，应始终使用进度栏。

Apple 具有以下建议用于使用进度栏：

- **准确地报告进度**-进度栏应始终为准确地反映来完成任务所需的时间。 永远不会有意使应用程序显示繁忙的时间。
- **用于 Well-Defined 持续时间**-进度栏不应该仅显示花费时间较长的任务将放置，但为用户和任务中有多少已完成的指示和估计剩余时间。

### <a name="implementing-an-progress-bar"></a>实现一个进度栏

一个进度栏创建方法是实例化 [`UIProgressView`](https://developer.xamarin.com/api/type/UIKit.UIProgressView/)

### <a name="progress-bars-and-storyboards"></a>进度栏和情节提要

使用 iOS 设计器时，还可以向你的 UI 添加一个进度栏。 搜索**进度视图**中**工具箱**并将其拖到你的视图。

属性小键盘上，可以调整以下属性：

![属性填充](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>管理进度栏行为

可以通过使用最初设置的进度栏`Progress`属性：

```csharp
ProgressBar.Progress = 0f;
```

可以使用调整进度`SetProgress`方法并传递一个布尔值，如果你想进行动画处理或不更改声明。

```csharp
ProgressBar.SetProgress(1.0f, true);
```

有关使用进度栏的详细信息，请参阅[报告进度](https://developer.xamarin.com/recipes/cross-platform/networking/download_progress/#Reporting_Progress_in_iOS)配方，和[UICatalog tvOS 示例](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/)。

### <a name="managing-progress-bar-appearance"></a>管理进度栏外观

类似于活动指示器，`UIProgressViewStyle`在实例化进度栏时，枚举可以作为参数传递。

可以使用以下属性来调整的进度和跟踪映像及色调颜色：

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



