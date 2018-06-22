---
title: Android 磨损简介
description: 通过 Google Android 磨损引入，你将不再限制为仅手机和平板电脑时涉及到开发强大的 Android 应用程序。 对 Android 磨损 Xamarin.Android 的支持使你在你静电腕带运行 C# 代码 ！ 本简介提供 Android 磨损的基本概述，描述它的主要功能，并提供了 Android 磨损 2.0 中提供的功能的概述。 它列出了一些更受欢迎的 Android 磨损设备，并提供有关其他参考资料的基本 Google Android 磨损文档的链接。
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 0ab166bb71c23d456cb70d35a2794717110642fd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772089"
---
# <a name="introduction-to-android-wear"></a>Android 磨损简介

_通过 Google Android 磨损引入，你将不再限制为仅手机和平板电脑时涉及到开发强大的 Android 应用程序。对 Android 磨损 Xamarin.Android 的支持使你在你静电腕带运行 C# 代码 ！本简介提供 Android 磨损的基本概述，描述它的主要功能，并提供了 Android 磨损 2.0 中提供的功能的概述。它列出了一些更受欢迎的 Android 磨损设备，并提供有关其他参考资料的基本 Google Android 磨损文档的链接。_


## <a name="overview"></a>概述

Android 磨损上的各种设备，包括第一代 Motorola 360、 LG 的 G 监视和 Samsung 齿轮实时运行。 生成第二，包括 Sony 的 SmartWatch 3，还具有其他功能包括内置 GPS 和脱机音乐播放已释放。 For Android 磨损 2.0 中，Google 具有搭配使用，与 LG 以两个新监视： LG 监视运动和 LG 监视样式。

![Android 磨损 2.0 设备](intro-to-wear-images/hero-image.png "示例 Android 磨损 2.0 设备")

通过我们 Android 4.4W (API 20) 的 Xamarin.Android 5.0 及更高版本支持 Android 磨损支持和一个用于添加其他的 NuGet 程序包磨损特定 UI 控件。 Xamarin.Android 5.0 及更高版本还包括打包磨损应用的功能。 NuGet 包也已可用于 Android 磨损 2.0 稍后在本指南中所述。


## <a name="android-wear-basics"></a>Android 磨损基础知识

Android 磨损具有不同于 Android 的手持应用的用户界面范例。 第一波磨损应用旨在扩展一起提供一些方法，但开头 Android 磨损 2.0 中的手持应用、 磨损应用可以是使用的独立的。 磨损应用部署时，它是与助理手持应用一起打包。 由于大多数磨损应用取决于手持辅助应用程序，它们需要某种方式来与手持应用进行通信。 下列各节介绍这些使用方案，并概述 Android 磨损的基本功能。 



### <a name="usage-scenarios"></a>使用方案

Android 磨损的第一个版本是主要侧重扩展使用增强的通知的当前手持应用程序和手持应用和 wearable 应用程序之间同步数据。 因此，这些方案都相对比较简单实现。


#### <a name="wearable-notifications"></a>Wearable 通知

支持 Android 磨损的最简单方法是利用通知手持设备和 wearable 设备之间的共享性质。 通过使用支持 v4 通知 API 和`WearableExtender`类 (位于[Xamarin Android 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v4/))，你可以点击到平台，如收件箱样式卡的本机功能或语音的输入。 [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/)示例还提供代码示例演示如何向 Android 磨损设备发送通知的列表。 



#### <a name="companion-applications"></a>助理应用程序

另一种策略是创建一个完整的应用程序在 wearable 设备上本机运行并对使用助理手持应用商店应用。 此方法的一个很好示例是[测验](https://developer.xamarin.com/samples/monodroid/wear/Quiz/)示例应用程序，演示如何创建测验手持设备上运行并在 wearable 设备上要求测验问题。 



### <a name="user-interface"></a>用户界面

磨损的主导航模式是垂直排列的卡的一系列。 每个这些卡可以具有关联的同一行进行分层出的操作。 `GridViewPager`类提供此功能，它遵循相同的适配器概念`ListView`。 通常将关联`GridViewPager`与`FragmentGridPagerAdaptor`(或`GridPagerAdaptor`)，可让你表示每个行和列的单元格作为`Fragment`: 

[![带导航](intro-to-wear-images/2d-picker-sml.png "磨损导航")](intro-to-wear-images/2d-picker.png#lightbox)

带还使组成大的操作按钮的使用着色包含其下 （如上面所述） 的小说明文本圆圈，圆圈。  [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)示例演示如何使用`GridViewPager`和`GridPagerAdapter`磨损应用中。

Android 磨损 2.0 将导航抽屉、 操作抽屉和内联操作按钮添加到磨损用户界面中。 有关 Android 磨损 2.0 用户界面元素有关的详细信息，请参阅 Android [Anatomy](https://www.google.com/design/spec-wear/system-overview/anatomy.html)主题。 



### <a name="communications"></a>通信

Android 磨损提供两个不同的通信 Api，以便于 wearable 应用和助理手持应用之间的通信： 

**数据 API** &ndash;此 API 是类似于 wearable 设备和手持设备之间的同步的数据存储区。 Android 负责最佳若要这样做时传播 wearable 和手持设备之间的更改。 超出范围可穿戴时，则将排队以后的同步。 此 API 的主入口点是`WearableClass.DataApi`。 有关此 API 的详细信息，请参阅 Android[同步数据项](https://developer.android.com/training/wearables/data-layer/data-items.html)主题。 

**消息 API** &ndash;此 API 使您能够使用较低级别的通信路径： 小负载发送单向而不进行手持和 wearable 应用之间的同步。
此 API 的主入口点是`WearableClass.MessageApi`。
有关此 API 的详细信息，请参阅 Android[发送和接收消息](https://developer.android.com/training/wearables/data-layer/messages.html)主题。

你可以选择注册以接收这些消息通过每个 API 侦听器接口的回调或，或者，派生自的应用程序中实现的服务`WearableListenerService`。
此服务将自动通过 Android 磨损实例化。
[FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/)示例演示如何实现`WearableListenerService`。



### <a name="deployment"></a>部署

每个 wearable 应用不会随嵌入主应用程序 APK 自己 APK 文件部署。 此打包 Xamarin.Android 5.0 及更高版本，会自动处理，但必须手动执行为早于版本 5.0 Xamarin.Android 的版本。 
[使用打包](~/android/wear/deploy-test/packaging.md)说明详细的部署。 



## <a name="going-further"></a>继续 

要熟悉 Android 磨损的最佳方法是生成和测试你的第一个应用。 以下列表提供了建议的阅读顺序，以帮助你快速掌握：

1.  [安装程序 （&) 安装](~/android/wear/get-started/installation.md)提供有关安装和配置你的开发环境，用于构建应用的 Xamarin.Android 磨损详细的说明。 

2.  你已安装所需的包和配置的仿真程序或设备后，请参阅[Hello，带](~/android/wear/get-started/hello-wear.md)的分步说明，解释如何创建该句柄按钮的小 Android 磨损项目单击，显示单击磨损设备上的计数器。 

3.  [部署和测试](~/android/wear/deploy-test/index.md)提供更详细的有关配置和部署到仿真程序和设备，包括有关如何将您的应用程序部署到磨损设备通过蓝牙的信息。

4.  [使用屏幕大小](~/android/wear/screen-sizes.md)说明了预览和优化你的用户界面针对磨损设备上各种可用的屏幕大小。 

5.  [使用打包](~/android/wear/deploy-test/packaging.md)介绍手动打包磨损适用于 Google Play 上的分发的应用的步骤。

创建第一个磨损应用后，你可能希望尝试生成针对 Android 带自定义手表表盘。 
[创建手表表盘](~/android/wear/platform/creating-a-watchface.md)开发去除下数字监视表面服务后, 跟增强到具有额外功能模拟样式手表表盘的更多代码提供了分步说明和示例代码。 



## <a name="android-wear-20"></a>Android Wear 2.0

Android 磨损 2.0 引入了大量新特性和功能，如*复杂性*，弯曲布局、 导航和操作抽屉和扩展的通知。 此外，带 2.0 使你可以生成独立手持应用于工作的独立应用。 新*静电腕带手势*功能使你的应用程序与错交互。 以下部分突出显示这些功能，并提供链接，以帮助你开始使用应用程序中使用它们。



### <a name="install-wear-20-packages"></a>安装带 2.0 包

若要生成具有 Xamarin.Android 的磨损 2.0 应用，你必须添加**Xamarin.Android.Wear v2.0**包到你的项目 (单击**浏览选项卡**):

[![Xamarin.Android.Wear v2.0](intro-to-wear-images/wear-nuget-2.0-sml.png "安装 Xamarin.Android.Wear v2.0 NuGet")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

此 NuGet 程序包包含绑定的 Android 支持 Wearable 和磨损 Compat 库。

除了**Xamarin.Android.Wear**，我们建议你安装**Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "安装 Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>磨损 2.0 的主要功能

Android 磨损 2.0 是自 2014 年初始推出到 Android 磨损的最大更新。 下列各节突出显示的 Android 磨损 2.0 中，主要功能并提供链接，以帮助你开始在你的应用程序中使用这些新功能。 


#### <a name="complications"></a>协调的并发数据

*复杂性*是表面小组件，你可以一眼看而无需轻扫手表表盘的小监视。 复杂性是类似于桌面样式仪表板小组件;它们显示的信息如天气、 的电池使用时间、 日历事件和适用性应用统计信息： 

![复杂性示例](intro-to-wear-images/complications.png "复杂性示例")

有关详细信息的并发数据，请参阅 Android[监视表面复杂性](https://developer.android.com/wear/preview/features/complications.html)主题。 



#### <a name="navigation-and-action-drawers"></a>导航和操作抽屉 

带 2.0 中包含两个新的抽屉。 *导航抽屉*，后者在屏幕上，顶部显示允许用户 （如左侧下面所示） 应用程序视图之间进行导航。 *操作抽屉*，后者显示在屏幕底部的 （如所示右侧），允许用户从操作的列表中选择。 

![导航和操作抽屉](intro-to-wear-images/drawers.png "导航和操作抽屉")

有关这些两个新的交互式抽屉的详细信息，请参阅 Android[磨损导航和操作](https://developer.android.com/wear/preview/features/ui-nav-actions.html)主题。 



#### <a name="curved-layouts"></a>曲线的布局 

磨损 2.0 引入了用于显示曲线的布局舍入磨损设备上的新功能。 具体而言，新`WearableRecyclerView`类非常适合在舍入显示上显示的垂直项的列表： 

![曲线布局示例](intro-to-wear-images/curved-layout.png "弯曲的布局示例")

`WearableRecyclerView` 扩展`RecyclerView`类，以支持曲线的布局和循环滚动笔势。 有关详细信息，请参阅 Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) API 文档。 



#### <a name="standalone-apps"></a>独立应用程序 

Android 磨损 2.0 应用程序可以独立手持应用于工作。 这意味着，例如，智能监视可以继续提供完整功能，即使伴侣手持设备已关闭或远从 wearable 设备。 有关此功能的详细信息，请参阅 Android[独立应用](https://developer.android.com/wear/preview/features/standalone-apps.html)主题。



#### <a name="wrist-gestures"></a>静电腕带手势 

静电腕带手势使用户与你的应用程序交互而无需使用触摸屏&ndash;用户可以对应用程序与单个手的形状作出响应。 支持两个静电腕带手势： 

-   笔势静电腕带出
-   中的笔势静电腕带

有关详细信息，请参阅 Android[静电腕带手势](https://developer.android.com/wear/preview/features/gestures.html)主题。 


有许多详细的磨损 2.0 功能，例如内联操作、 智能答复、 远程输入、 扩展的通知和通知新桥接模式。 有关新的磨损 2.0 功能的详细信息，请参阅 Android [API 概述](https://developer.android.com/wear/preview/api-overview.html)。 



## <a name="devices"></a>设备

下面是可以运行 Android 磨损的设备的一些示例：

* [Motorola 360](https://moto360.motorola.com/)
* [LG G 监视](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G Watch R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Samsung 齿轮实时](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>其他阅读材料

请查看 Google Android 磨损文档：

* [有关 Android 磨损](http://www.android.com/wear/)
* [Android 磨损应用设计](https://developer.android.com/design/wear/index.html)
* [android.support.wearable 库 ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android 磨损 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>总结

本简介提供 Android 磨损的概述。 它概述 Android 磨损的基本功能，并包含在 Android 磨损 2.0 中引入的功能的概述。 它提供基本的读取，可帮助开发人员入门 Xamarin.Android 磨损开发的链接，它列出一些当前市场上的 Android 磨损设备的示例。


## <a name="related-links"></a>相关链接

- [安装和设置](~/android/wear/get-started/installation.md)
- [入门](~/android/wear/get-started/index.md)
