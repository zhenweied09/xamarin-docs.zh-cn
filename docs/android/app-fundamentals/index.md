---
title: "应用程序基础知识"
description: "应用程序的核心概念"
ms.topic: article
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 4abb8c823c62bc62fd2e6f717cc1b5bde9057e4e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="application-fundamentals"></a>应用程序基础知识

本部分提供的指南上的一些较常见的操作任务或开发人员需要时应注意的开发 Android 应用程序的概念。

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[辅助功能](~/android/app-fundamentals/accessibility.md)

本页介绍如何使用 Android 的可访问性 Api 来生成应用根据[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)

本指南介绍了 Android 如何使用 API 级别来管理跨不同版本的 Android 应用程序兼容性，并介绍如何配置要部署这些应用程序中的 API 级别的 Xamarin.Android 项目设置。 此外，本指南介绍如何编写涉及到不同的 API 级别的运行时代码，并提供所有 Android API 级别、 版本号 （例如 Android 8.0)、 Android 代码名称 （例如 Oreo) 和生成版本代码的引用列表。



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Android 中的资源](~/android/app-fundamentals/resources-in-android/index.md)

本文介绍的概念 Xamarin.Android 和文档中的 Android 资源如何使用它们。 它介绍如何在 Android 应用程序中使用资源以支持应用程序本地化和多个设备，包括不同的屏幕大小和密度。




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[活动的生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)

活动是的 Android 应用程序的基本构建基块，并且可以存在于多个不同状态。 活动生命周期开头实例化和析构，结尾，并且在此期间包括许多的状态。 当活动更改状态时，会调用相应的生命周期事件方法，以便通知即将发生的状态更改的活动，并使其能够执行的代码以适应所做的更改。 本文分析的活动的生命周期，并解释了负责该活动具有这些状态更改的功能良好的可靠应用程序的一部分的每一阶段。

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[本地化](~/android/app-fundamentals/localization.md)

此文章介绍了如何通过转换字符串和提供备用映像将 Xamarin.Android 本地化为其他语言。

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[服务](~/android/app-fundamentals/services/index.md)

本文介绍如何 Android 服务，允许在后台进行工作的 Android 组件。 它说明服务适合的不同方案，并同时包括这两者并提供用于远程过程调用的接口执行长时间运行后台任务以及演示如何实现它们。

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[广播的接收方](~/android/app-fundamentals/broadcast-receivers.md)

本指南介绍如何创建和使用广播接收方，响应系统级广播，在 Xamarin.Android 的 Android 组件。



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[权限](~/android/app-fundamentals/permissions.md)

可以使用内置于适用于 Mac 的 Visual Studio 或 Visual Studio 的工具支持创建并将权限添加到 Android 清单。 本文档介绍如何在 Visual Studio 和 Xamarin Studio 中添加权限。



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[图形和动画](~/android/app-fundamentals/graphics-and-animation.md)

Android 支持二维图形和动画提供了非常丰富、 不同的框架。 本文档介绍这些框架，并讨论如何创建自定义图形和动画并在 Xamarin.Android 应用程序中使用它们。


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[CPU 体系结构](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android 支持多个 CPU 体系结构，包括 32 位和 64 位设备。 此文章介绍了如何应用定位到一个或多个 Android 支持 CPU 体系结构。




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[处理旋转](~/android/app-fundamentals/handling-rotation.md)

本文介绍如何处理在 Xamarin.Android 设备方向更改。 它介绍如何使用 Android 资源系统自动加载资源的特定设备方向以及如何以编程方式处理方向更改。 然后，它描述技术来旋转设备时保持状态。



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android 音频](~/android/app-fundamentals/android-audio.md)

Android OS 为多媒体，包含音频和视频提供广泛支持。 本指南重点介绍在 Android 中的音频，并介绍如何播放和记录音频使用内置的音频播放器和记录器类，以及低级别的音频 API。 此外将介绍使用其他应用程序，广播的音频事件，从而使开发人员能够生成良好的应用程序。




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[通知](~/android/app-fundamentals/notifications/index.md)

本部分介绍如何在 Xamarin.Android 中实现本地和远程通知。 它介绍 Android 通知的各种 UI 元素，并介绍 API 的涉及创建和显示一条通知。 要使远程通知，同时 Google Cloud Messaging 和 Firebase Cloud Messaging 进行说明。 包括的分步演练和代码示例。



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[触摸](~/android/app-fundamentals/touch/index.md)

本部分介绍的概念和详细信息实现触摸手势在 Android 上。 触摸 Api 是引入并且说明了跟笔势识别器浏览。



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[HttpClient 堆栈和 SSL/TLS](~/android/app-fundamentals/http-stack.md)

本部分说明适用于 Android 的 HttpClient 堆栈和 SSL/TLS 实现的选择器。 这些设置将确定将由您的 Xamarin.Android 应用程序的 HttpClient 和 SSL/TLS 实现。


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[编写响应的应用程序](writing-responsive-apps.md)

本文讨论如何使用线程处理来使 Xamarin.Android 应用程序保持响应状态通过移动到后台线程长时间运行任务。