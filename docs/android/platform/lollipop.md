---
title: 棒糖形功能
description: 本文章提供了 Android 5.0 （棒糖形） 中引入的新功能的高级别概述。 这些功能包括一个新的用户界面样式，它调用材料主题，以及新的支持功能，例如动画、 视图阴影和可绘制、 颜色。 Android 5.0 还包括增强的通知、 两个新的 UI 小组件、 新的作业计划程序和少量的新的 Api 来提高存储、 网络、 连接和多媒体功能。
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: cdef611525abbe4f066959c0ac56380b1c617747
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="lollipop-features"></a>棒糖形功能

_本文章提供了 Android 5.0 （棒糖形） 中引入的新功能的高级别概述。这些功能包括一个新的用户界面样式，它调用材料主题，以及新的支持功能，例如动画、 视图阴影和可绘制、 颜色。Android 5.0 还包括增强的通知、 两个新的 UI 小组件、 新的作业计划程序和少量的新的 Api 来提高存储、 网络、 连接和多媒体功能。_

## <a name="lollipop-overview"></a>棒糖形概述

Android 5.0 （棒糖形） 引入了一种新的设计语言，*材料设计*，并与之支持强制转换的新功能，可以更轻松、 更直观，用于使应用程序。 与材料设计 Android 5.0 不仅会授予 Android 手机 facelift;它还提供一组新的设计规则，用于基于 Android 平板电脑、 台式计算机、 观察和智能电视机。 这些设计规则强调简单起见和 minimalism 同时充分利用熟悉边用属性 （例如现实面和边缘提示），以帮助用户快速、 直观地了解接口。

*材料主题*是体现在 Android 中这些 UI 设计原则。 本文首先涵盖材料主题支持功能：

-   **动画** &ndash; *Touch 反馈*动画，*活动转换*动画，*查看状态转换*动画和*显示效果*。

-   **查看阴影和提升**&ndash;视图现在具有`elevation`属性; 视图与更高版本`elevation`值强制转换的背景上的更大阴影。

-   **颜色功能** &ndash; *可绘制、 颜色*使你可以通过更改其颜色进行区分，重新使用图像资产和*突出显示颜色提取*动态可帮助你你的应用程序基于在映像中颜色主题。

功能已内置到许多材料主题 Android 5.0 UI 时，会，其他人必须显式添加到应用。 例如，某些标准视图 （如按钮） 时，应用程序必须启用大多数视图阴影已包含触摸反馈动画。

除了通过材料主题也带动 UI 改进，Android 5.0 还这篇文章中包含多个属于其他新功能：

-   **增强型通知** &ndash; Android 5.0 中的通知已显著更新使用的新面貌、 锁屏通知的支持和新*提醒*通知显示格式。

-   **新 UI 小组件**&ndash;新`RecyclerView`小组件可方便应用程序，以便传递了大型数据集和复杂的信息和新`CardView`小组件提供简化的卡式演示文稿格式的显示文本和图像。

-   **新的 Api** &ndash; Android 5.0 添加新的 Api 的多个网络支持，改进蓝牙连接、 更简单的存储管理和更灵活地控制多媒体播放器和照相机设备。 新作业计划功能是可用于运行任务以异步方式在计划的时间。 此功能有助于改善电池寿命，例如，计划任务能够发生在设备接通电源时以及收费。


## <a name="requirements"></a>要求

以下是所需基于 Xamarin 的应用中使用新的 Android 5.0 功能：

-   **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更高版本必须安装并配置了 Visual Studio 或 Visual Studio for mac。 

-   **Android SDK** &ndash; Android 5.0 (API 21) 或更高版本必须安装通过 Android SDK 管理器。

-   **Java 开发人员工具包** &ndash; Xamarin.Android 需要[JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或如果你正在开发的 API 级别 24，更高版本或更高版本 （JDK 1.8 还支持 API 级别早于 24 个，包括棒糖形）。 如果你正在使用自定义控件或窗体预览程序，64 位版本的 JDK 1.8 是必需的。

你可以继续使用[JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果你是专门为 API 级别 23 开发或更早版本。


## <a name="setting-up-an-android-50-project"></a>设置 Android 5.0 项目

若要创建 Android 5.0 项目时，必须安装最新工具和 SDK 包。 使用以下步骤来设置 Xamarin.Android 项目针对 Android 5.0:

1. 安装 Xamarin.Android 工具并激活你的 Xamarin 许可证。 请参阅[设置和安装](~/android/get-started/installation/index.md)有关安装 Xamarin.Android 的详细信息。

2. 如果为 Mac 使用 Visual Studio，安装最新的 Android 5.0 更新。

3. 启动 Android SDK 管理器 (在适用于 Mac 的 Visual Studio，使用**工具&gt;打开 Android SDK 管理器&hellip;**) 并安装 Android SDK 工具 23.0.5 或更高版本：

    [![选择在 Android SDK 管理器中的 Android SDK 工具](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   另外，请安装最新的 Android 5.0 SDK 包 (API 21 或更高版本):

    [![安装 Android SDK 管理器中的 Android 5.0 SDK 包](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   有关使用 Android SDK 管理器的详细信息，请参阅[SDK 管理器](http://developer.android.com/tools/help/sdk-manager.html)。

4. 创建新的 Xamarin.Android 项目。 如果你不熟悉如何使用 Xamarin Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md)若要了解有关创建 Android 项目。 在创建 Android 项目时，一定要配置的 Android 5.0 版本设置。
   在 Visual Studio for Mac 中，导航到**项目选项&gt;生成&gt;常规**并设置**目标框架**到**Android 5.0 （棒糖形）**或更高版本：

    ![将目标 Framwework 设置为 Android 5.0 棒糖形](lollipop-images/target-framework.png)

   下**项目选项&gt;生成&gt;Android 应用程序**、 设置最小并着力到 Android 版本**自动-使用目标框架版本**:

    ![将最小值和目标 Android 版本设置为自动](lollipop-images/minimum-android-version.png)

5. 配置仿真程序或 Android 设备，若要测试你的应用程序。 如果你使用仿真程序，请参阅[Android 仿真程序安装程序](~/android/get-started/installation/android-emulator/index.md)若要了解如何通过 Xamarin Studio 或 Visual Studio 配置为使用 Android 仿真程序。 如果使用 Android 设备，请参阅[设置预览版 SDK](https://developer.android.com/preview/setup-sdk.html)若要了解如何为 Android 5.0 更新你的设备。 若要配置 Android 设备的运行和调试 Xamarin.Android 应用程序，请参阅[设置向上用于开发的设备](~/android/get-started/installation/set-up-device-for-development.md)。

注意： 如果你要更新针对 Android L 预览的现有 Android 项目，则必须更新**目标框架**和**Android 版本**上面所述的值。

## <a name="important-changes"></a>重要的更改

以前已发布的 Android 应用程序可能会受到 Android 5.0 中的更改。 具体而言，Android 5.0 使用新的运行时和显著更改的通知格式。

### <a name="android-runtime"></a>Android 运行时

Android 5.0 使用默认运行时，而不是 Dalvik 作为新 Android 运行时 （图形）。 艺术作品实现多个主要的新功能：

-   **预-(AOT) 编译** &ndash; AOT 可以通过在第一次启动应用程序前编译应用程序代码来提高应用性能。 在安装应用后，艺术作品会生成编译的应用的目标设备可执行文件。

-   **改进了垃圾回收 (GC)** &ndash;画中的 GC 改进还可提高应用性能。 现在垃圾回收而不是两个，使用一个 GC 暂停和并发 GC 操作更及时完成。

-   **改进了应用程序调试**&ndash;艺术作品提供详细的诊断信息可帮助你在分析异常和故障报告。

现有应用程序应进行任何修改下艺术作品&ndash;除外利用技术特有之前的 Dalvik 运行的应用程序，这可能不工作下艺术作品。 有关这些更改的详细信息，请参阅[验证应用行为上 Android 运行时 （画）](http://developer.android.com/guide/practices/verifying-apps-art.html)。


### <a name="notification-changes"></a>通知更改

Android 5.0 中，通知的发生了重大变化：

-   **以不同方式处理声音和振动**&ndash;通知听起来和振动现在由处理`Notification.Builder`而不是`Ringtone`， `MediaPlayer`，和`Vibrator`。

-   **新的配色方案**&ndash;根据材料主题通知呈现深文本与通过白色或非常轻量的背景。 此外，Android 协调使用系统配色方案可能修改通知图标中的 alpha 通道。 

-   **锁屏通知**&ndash;通知可以现在显示在设备锁屏上。

-   **提醒**&ndash;优先级较高通知现在显示在一个小的浮动窗口 （警告通知） 时设备未锁定且屏幕已开启。

在大多数情况下，移植到 Android 5.0 的现有应用程序通知功能需要执行下列步骤：

1.  转换代码以使用`Notification.Builder`(或`NotificationsCompat.Builder`) 创建通知。 

2.  验证你的现有通知资产可在新材料主题配色方案中查看。

3.  决定在锁屏上显示时，则通知应具有何种可见性。 如果通知不是公共的哪些内容应出现在锁屏上？

4.  设置你的通知的类别，因此它们在新的 Android 5.0 中正确处理*不打扰*模式。

如果你的通知显示传输控件，显示媒体播放状态，使用`RemoteControlClient`，或调用`ActivityManager.GetRecentTasks`，请参阅[重要行为更改](http://developer.android.com/preview/api-overview.html#Behaviors)有关更新适用于 Android 通知的详细信息5.0。

在 Android 中创建通知的信息，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。 [兼容性](~/android/app-fundamentals/notifications/local-notifications.md#compatibility)这篇文章的部分介绍如何创建通知来向下兼容与早期版本的 Android。


## <a name="material-theme"></a>材料主题

对 Android UI 的外观和感觉，新的 Android 5.0 材料主题提供了大规模更改。 可视元素现在使用边用图面，以粗体显示的图形、 版式，以及打印基于设计的亮色随身携带。 材料主题的示例以下屏幕截图所示：

[![材料主题主页屏幕、 应用屏幕上，以及设置屏幕的屏幕快照](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 那么当使用显示在左边的主屏幕。 Center 屏幕快照是应用列表中，第一个屏幕，在右侧的屏幕截图是**设置**屏幕。 Google[材料设计](https://material.io/guidelines/material-design/introduction.html)规范介绍新材料主题概念背后的基础设计规则。

材料主题包括可以在应用中使用的三个内置风格：`Theme.Material`深色主题 （默认值）、`Theme.Material.Light`主题，和`Theme.Material.Light.DarkActionBar`主题： 

[![屏幕快照的深色、 浅色，和 DarkActionBar 主题](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

有关 Xamarin.Android 应用程序中使用材料主题功能的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。


## <a name="animations"></a>动画

Android 5.0 提供触摸反馈动画、 活动过渡动画和视图状态过渡动画来使应用程序接口使用更加直观。 此外，可以使用 Android 5.0 应用*显示效果*动画来隐藏或显示视图。 你可以使用*弯曲运动*呈现如何快速配置的设置或缓慢动画。


### <a name="touch-feedback-animations"></a>触摸反馈动画

触摸反馈动画为用户提供可视反馈，已触摸视图时。 例如，按钮现在显示连锁反应接触时&ndash;这是 Android 5.0 中的默认触摸反馈动画。 由新实现 ripple 动画`RippleDrawable`类。 Ripple 效果可以配置为在视图的边界结束或超出边界的视图。 例如，下面的屏幕截图序列期间触摸动画说明一个按钮中的 ripple 效果：

![框架中通过的上一个按钮 ripple 动画帧屏幕快照](lollipop-images/touch-animation.png)

剩余序列 （从左到右） 说明了如何 ripple 效果伸展按钮的边缘时，则将出现与按钮的初始触摸联系中左侧的第一个图像。 在 ripple 动画结束时，视图会返回到其原始的外观。 默认 ripple 动画都在几秒钟，但长或较短的时间的长度可自定义动画的长度。

Touch Android 5.0 中的反馈动画的详细信息，请参阅[自定义 Touch 反馈](http://developer.android.com/training/material/animations.html#Touch)。


### <a name="activity-transition-animations"></a>活动过渡动画

一个活动将转换为另一个时，活动过渡动画为用户提供一种 visual 连续性。 应用程序可以指定三种类型的转换动画：

-   **输入转换**&ndash;为在活动进入场景。

-   **退出转换**&ndash;为活动退出场景时。

-   **共享的元素转换**&ndash;为普遍适用于两个活动的视图到下的第一个活动转换为的更改时。

例如，下面的屏幕截图序列阐释了共享的元素转换：

[![框架中通过共享的元素的转换动画帧屏幕快照](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

共享的元素 （毛虫的照片） 是一个多个视图中的第一个活动;它放大成为当第一个活动过渡到第二个第二个活动中的唯一视图。

#### <a name="enter-transition-animation-types"></a>输入过渡动画类型

对于 enter 转换 Android 5.0 提供了三种类型的动画：

-   **分解动画**&ndash;放大场景的中心中的一个视图。

-   **滑动动画**&ndash;将视图中移动从一个场景的边缘。

-   **淡入淡出动画**&ndash;淡入场景的视图。

#### <a name="exit-transition-animation-types"></a>退出过渡动画类型

对于退出转换 Android 5.0 提供了三种类型的动画：

-   **分解动画**&ndash;收缩到场景的中心视图。

-   **滑动动画**&ndash;将视图移到一个场景的边缘。

-   **淡入淡出动画**&ndash;淡入淡出场景的视图。

#### <a name="shared-element-transition-animation-types"></a>共享元素过渡动画类型

共享的元素转换支持多个类型的动画，如：

-   更改视图的布局或剪辑边界。

-   更改缩放和视图的旋转。

-   更改视图的大小和比例类型。

有关 Android 5.0 中的活动过渡动画有关的详细信息，请参阅[自定义活动转换](http://developer.android.com/training/material/animations.html#Transitions)。


### <a name="view-state-transition-animations"></a>视图状态过渡动画

Android 5.0 使动画的视图状态更改时运行。 你可以通过使用以下方法之一对视图状态转换进行动画处理：

-   创建 drawables 进行动画处理与特定视图关联的状态更改。 新`AnimatedStateListDrawable`类，可以创建显示视图状态更改之间的动画的 drawables。

-   定义在视图的状态更改时运行的动画功能。 新`StateListAnimator`类，您可以定义在视图的状态更改时运行生成器。

有关详细信息视图状态过渡动画 Android 5.0 中，请参阅[进行动画处理的视图状态更改](http://developer.android.com/training/material/animations.html#ViewState)。


### <a name="reveal-effect"></a>显示效果

*显示效果*是该更改半径，以显示或隐藏视图的一个剪辑的圆。 可以通过设置剪辑圆的初始和最终半径来控制此效果。 下面的屏幕截图序列阐释了屏幕的显示效果动画从中心：

[![框架中通过帧的动画显示的屏幕快照](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

下一步序列说明发生从屏幕的左下角显示效果动画：

[![框架中通过的剪辑动画帧屏幕快照](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

显示可逆转动画;也就是说，剪辑圆圈可以收缩以隐藏视图而扩大以显示该视图。

中的 Android 5.0 显示效果的详细信息，请参阅[使用显示效果](http://developer.android.com/training/material/animations.html#Reveal)。


### <a name="curved-motion"></a>曲线的运动

除了这些动画功能，Android 5.0 还提供使您能够指定动画的时间和运动曲线的新 Api。 Android 5.0 使用这些曲线进行期间动画插值临时和空间移动。 Android 5.0 中定义了三个曲线：

-   **快速\_出\_线性\_中**&ndash;快速加快并仍在动画结束之前一直增长。

-   **快速\_出\_慢速\_中**&ndash;加快快速和渐变减速的动画结束。

-   **线性\_出\_慢速\_中**&ndash;与峰值速度缓慢的开头到末尾的动画减速。

你可以使用新`PathInterpolator`类，以指定如何进行运动内插。 `PathInterpolator` 是遍历根据指定的控制点和动画曲线的动画路径插值程序。 有关如何在 Android 5.0 中指定曲线的运动设置的详细信息，请参阅[使用曲线运动](http://developer.android.com/training/material/animations.html#CurvedMotion)。


## <a name="view-shadows--elevation"></a>视图阴影和提升

在 Android 5.0 中，你可以指定*提升*通过设置一个新视图的`Z`属性。 大于`Z`值将导致要投影更大的背景，使视图显示转换为浮点型上面后台更高版本上的视图。 你可以通过配置来设置视图初始提升其`elevation`布局中的属性。

下面的示例演示一个空进行强制转换的阴影`TextView`控制当与其提升属性设置为 2dp、 4dp 和 6dp，分别：

[![Progessively 更大的视图阴影的屏幕快照](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

查看卷影设置可以是静态 （如上所示），或它们可在动画进行暂时露出视图的背景显示的视图。 你可以使用`ViewPropertyAnimator`类进行动画处理视图的提升。 视图的提升是其布局的总和`elevation`设置加上`translationZ`属性，可通过设置`ViewPropertyAnimator`方法调用。

有关详细信息视图阴影 Android 5.0 中，请参阅[定义阴影和剪辑视图](http://developer.android.com/training/material/shadows-clipping.html)。


## <a name="color-features"></a>颜色功能

Android 5.0 提供用于管理应用程序中的颜色的两项新功能：

-   *可绘制、 颜色*允许你通过更改布局属性 alter 图像资产的颜色。

-   *突出显示颜色提取*使得可以动态自定义应用的颜色主题，以便与显示的图像的调色板保持协调。


### <a name="drawable-tinting"></a>可绘制、 颜色

Android 5.0 布局识别新`tint`可用于设置 drawables 的颜色，而无需创建这些资产，以显示不同的颜色的多个版本的属性。 若要使用此功能，您定义位图作为 alpha 蒙板用`tint`属性来定义资产的颜色。 这使你可以一次创建资产并在你的布局以匹配您的主题颜色它们。

在下面的示例中，单个图像资产&ndash;带有透明背景的白色徽标&ndash;用于创建浅色变体：

![带有透明背景的空白 Xamarin 徽标](lollipop-images/xamarin-logo-white.png)

此徽标将显示上面循环背景为蓝色，如下面的示例中所示。 在左侧的图像是徽标的显示不`tint`设置。 在中心图中，该徽标的`tint`属性设置为深灰色的过渡。 在右侧，映像中`tint`设置为浅灰色：

![使用不同浅色设置上述徽标的示例](lollipop-images/drawable-tinting.png)

有关详细信息 Android 5.0 中绘制、 颜色，请参阅[可绘制、 颜色](http://developer.android.com/training/material/drawables.html#DrawableTint)。


### <a name="prominent-color-extraction"></a>突出显示颜色提取

新的 Android 5.0`Palette`类，可从映像中提取颜色，以便你可以动态将其应用到自定义调色板。 `Palette`类从映像中提取六种颜色和标签的颜色饱和度和亮度其相对级别根据这些颜色：

-   活力

-   活力深色

-   活力 light

-   静音

-   静音的深色

-   静音的 light

例如，在下面的屏幕截图，查看应用程序一张照片上显示的映像从提取突出显示颜色，以及使用这些颜色来调整应用程序的配色方案，以匹配图像：

[![绿色、 粉红色，和蓝色主题颜色提取的屏幕快照](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

在上面的屏幕截图，操作栏设置为提取"活力光"提取"活力深色"到设置颜色和背景颜色。 在上述每个示例中，小型彩色平方和的行是包含在内，以说明从映像中提取的调色板颜色。

有关 Android 5.0 中的颜色提取有关的详细信息，请参阅[提取映像中的突出显示颜色](http://developer.android.com/training/material/drawables.html#ColorExtract)。


## <a name="new-ui-widgets"></a>新 UI 小组件

Android 5.0 引入了两个新的 UI 小组件：

-   `RecyclerView` &ndash; 一组视图，显示的可滚动的项的列表。

-   `CardView` &ndash; 具有圆角基本布局。

这两个小组件包括内置功能的支持材料主题;例如，`RecyclerView`动画用于添加和删除视图，和`CardView`使用查看阴影，以使显示浮在后台每个卡。 在下面的屏幕截图显示了这些新的小组件的示例：

[![用 RecyclerView 生成的应用的屏幕快照](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

在左侧的屏幕截图是一种`RecyclerView`右侧为在电子邮件应用程序，并屏幕快照中使用上是一种`CardView`在旅行预订应用程序中使用。


### <a name="recyclerview"></a>RecyclerView

`RecyclerView` 类似于`ListView,`但它更好地适合于大量视图或具有动态更改的元素的列表。 如`ListView,`指定一个适配器来访问基础数据集。 但是，与不同`ListView,`你使用*布局管理器*位置内的项`RecyclerView`。 布局管理器还负责的视图回收;它管理将不再对用户可见的项视图重用。

当你使用`RecyclerView`小组件中，你必须指定`LayoutManager`并适配器。 此图中所示`LayoutManager`是适配器之间的媒介和`RecyclerView`:

![提供的支持 LayoutManager、 适配器和数据集的 RecyclerView 图示](lollipop-images/recyclerview-diagram.png)

以下屏幕截图演示`RecyclerView`包含 100 个项目 (每个项包含`ImageView`和`TextView`):

[![滚动映像 RecyclerView 应用的屏幕快照](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` 轻松地处理此大型数据集&ndash;滚动列表以结束从头列表在此示例中的应用程序就仅需几秒。 `RecyclerView` 此外支持动画;事实上，默认情况下启用添加和移除项的动画效果。 当将项添加到`RecyclerView`，它淡在这一序列的屏幕截图中所示：

[![框架中通过帧的中照片项淡入淡出的屏幕截图](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

有关详细信息`RecyclerView`，请参阅[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。


### <a name="cardview"></a>卡片视图-

`CardView` 是一个简单的视图，可模拟具有圆角的浮点卡。 因为`CardView`具有内置视图阴影，它可以轻松地为你要添加到你的应用的 visual 深度。 以下屏幕截图显示三个面向文本的示例的`CardView`:

[![RecyclerView 使用卡片视图-基于项的应用的示例屏幕快照](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

在上面的示例的卡的每个包含`TextView`; 通过设置背景色`cardBackgroundColor`属性。

有关详细信息`CardView`，请参阅[卡片视图-](~/android/user-interface/controls/card-view.md)。


## <a name="enhanced-notifications"></a>增强的通知

Android 5.0 中的通知系统已显著更新具有新的可视化格式和新功能。 通知具有 Android 5.0 中的新面貌。 例如，Android 5.0 中的通知现在使用深文本通过浅色背景：

![未展开的 Android 5.0 通知的示例](lollipop-images/expanded-notification-contracted.png)

当大图标显示在通知 （如上面的示例中所示） 时，Android 5.0 显示徽章，并且具有通过大图标的小图标。 

在 Android 5.0，通知也可以显示在设备锁屏上。
例如，下面是使用一条通知锁屏示例屏幕快照：

[![在锁定屏幕上显示通知的屏幕截图](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

用户可以双击锁屏解锁设备，并跳转到的应用程序产生该通知上的通知或轻扫以取消通知。 通知有一个新*可见性*设置确定可以在锁屏上显示多少内容。 用户可以选择是否允许在锁屏通知中显示的敏感内容。

Android 5.0 引入了新的高优先级通知演示文稿格式称为*提醒*。 提醒通知几秒钟滑从屏幕顶部，然后返回到在屏幕顶部的通知明暗度撤回。 提醒通知使系统 UI，以将呈现给用户的重要信息放而不会中断当前运行的活动。 下面的示例阐释了显示在应用程序之上的简单提醒通知：

[![Heads-up 通知的示例](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

提醒通知通常用于以下事件：

-   新的下一条消息

-   传入的电话呼叫

-   电池电量不足指示

-   警报

仅当它具有高或最大优先级设置时，android 5.0 提醒格式显示一条通知。

在 Android 5.0 中，你可以提供通知元数据来帮助 Android 进行排序和更智能地显示通知。 Android 5.0 组织根据优先级、 可见性和类别的通知。
通知类别用于筛选时在设备处于可以看到哪些通知*不打扰*模式。

有关创建和启动通知的最新的 Android 5.0 功能的详细信息，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。


## <a name="new-apis"></a>新 API

除了前面所述的新外观和功能，Android 5.0 添加新扩展功能的现有多媒体、 存储和无线/连接功能的 Api。 此外，Android 5.0 包含新的 Api，可为新的作业计划程序功能提供支持。

### <a name="camera"></a>照相机

Android 5.0 增强的相机功能提供了几个新的 Api。 新`Android.Hardware.Camera2`命名空间包括功能访问单个照相机设备连接到 Android 设备。 此外，`Android.Hardware.Camera2`模型作为管道的每个照相机设备： 它接受捕获请求、 捕获映像，然后将结果输出。 此方法使应用程序队列到照相机设备的多个捕获请求。

以下 Api 使这些新功能可能：

-   `CameraManager.GetCameraIdList` &ndash; 可帮助你以编程方式访问相机设备; 请参阅你使用`CameraManager.OpenCamera`连接到特定的照相机设备。

-   `CameraCaptureSession` &ndash; 捕获或流式处理从照相机设备的图像。 你实现`CameraCaptureSession.CaptureListener`接口来处理新映像捕获事件。

-   `CaptureRequest` &ndash; 定义捕获参数。

-   `CaptureResult` &ndash; 提供映像的捕获操作的结果。

有关详细信息的新照相机 Android 5.0 中的 Api，请参阅[媒体](http://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="audio-playback"></a>音频播放

Android 5.0 更新`AudioTrack`进行更好的音频播放的类：

-   `ENCODING_PCM_FLOAT` &ndash; 配置`AudioTrack`接受以更好的动态范围、 更大的空间，和更高的质量 （得益于增加的精度） 的浮点格式的音频数据。 此外，浮点格式可帮助避免音频剪辑。

-   `ByteBuffer` &ndash; 你现在可以提供到音频数据`AudioTrack`作为字节数组。

-   `WRITE_NON_BLOCKING` &ndash; 此选项简化了缓冲和多线程处理用于某些应用程序。

有关详细信息`AudioTrack`改进在 Android 5.0 中，请参阅[媒体](http://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="media-playback-control"></a>媒体播放控制

Android 5.0 引入了新`Android.Media.MediaController`类，哪些替换`RemoteControlClient`。 `Android.Media.MediaController` 提供了简化的传输控制 Api 并提供线程安全的播放的 UI 上下文之外的控件。 以下新的 Api 处理传输控件：

-   `Android.Media.Session.MediaSession` &ndash; 处理多个控制器媒体控制会话。 你调用`MediaSession.GetSessionToken`以请求令牌供你的应用程序用来与会话交互。

-   `MediaController.TransportControls` &ndash; 句柄传输等命令**播放**，**停止**，和**跳过**。

此外，你可以使用新`Android.App.Notification.MediaStyle`要与富通知内容 （如提取和显示唱片集画面） 关联的媒体会话类。

有关 Android 5.0 中的新媒体播放控件功能的详细信息，请参阅[媒体](http://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="storage"></a>存储

Android 5.0 更新存储访问框架以简化应用程序来处理目录和文档：

-   若要选择目录子树，可以生成并发送`Android.Intent.Action.OPEN_DOCUMENT_TREE`意图。 此方法会导致系统显示支持子树选择; 的所有提供程序实例然后，用户浏览并选择一个目录。

-   若要创建和管理新文档或任何位置下子树的目录，你使用新`CreateDocument`， `RenameDocument`，和`DeleteDocument`方法`DocumentsContract`。

-   若要获取所有共享的存储设备上的媒体目录的路径，请调用新`Android.Content.Context.GetExternalMediaDirs`方法。

有关新的存储空间 Android 5.0 中的 Api 的详细信息，请参阅[存储](http://developer.android.com/preview/api-overview.html#Storage)。

### <a name="wireless--connectivity"></a>无线和连接

Android 5.0 添加无线和连接的以下 API 增强功能：

-   新*多网络*使应用程序以查找并选择具有特定功能的网络进行连接之前的 Api。

-   使 Android 5.0 设备，使其作为低能量蓝牙外围设备的蓝牙广播功能。

-   NFC 增强功能，它可以更轻松地与其他设备共享数据使用近场通信功能。

新的无线和连接 Android 5.0 中的 Api 的详细信息，请参阅[无线和连接](http://developer.android.com/preview/api-overview.html#Wireless)。

### <a name="job-scheduling"></a>作业计划

Android 5.0 引入了新`JobScheduler`API，可帮助用户通过计划某些任务仅在设备接通电源时运行和充电最小化电池消耗。 此作业计划程序功能还可用于计划任务以运行该任务，如当设备连接通过而不是按流量计费的网络的 Wi-fi 网络时下载较大的文件更适合条件时。

有关新的作业计划 Android 5.0 中的 Api 的详细信息，请参阅[计划作业](http://developer.android.com/preview/api-overview.html#JobScheduler)。

## <a name="summary"></a>总结

本文章 Xamarin.Android 应用程序开发人员提供 Android 5.0 中的重要新增功能的概述：

-   材料主题

-   动画

-   视图阴影和提升

-   颜色功能，如可绘制、 颜色和突出显示颜色提取

-   新`RecyclerView`和`CardView`小组件

-   通知增强功能

-   照相机、 音频播放、 媒体控件、 存储、 无线/连接和作业计划的新 Api

如果你熟悉到 Xamarin Android 开发，请阅读[设置和安装](~/android/get-started/installation/index.md)来帮助你开始使用 Xamarin.Android。
[Hello，Android](~/android/get-started/hello-android/index.md)绝佳介绍有关学习如何创建 Android 项目。



## <a name="related-links"></a>相关链接

- [Android L 开发者预览版](http://developer.android.com/preview/index.html)
- [获取 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [材料设计](http://developer.android.com/preview/material/index.html)
- [材料设计原则](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
