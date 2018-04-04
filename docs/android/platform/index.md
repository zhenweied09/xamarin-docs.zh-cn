---
title: 平台功能
description: 本部分中的文档涵盖特定于 Android 的功能。 你将找到主题，如使用片段、 使用图和封装与内容提供商的数据。
ms.prod: xamarin
ms.assetid: DDE54082-6E2B-9ED9-05FB-D9C1D1B1258E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 69a70fe399bc95a9d8b24cc839382f2f3bee553e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="platform-features"></a>平台功能

_本部分中的文档涵盖特定于 Android 的功能。你将找到主题，如使用片段、 使用图和封装与内容提供商的数据。_

## <a name="android-beamandroidplatformandroid-beammd"></a>[Android 无线发送](~/android/platform/android-beam.md)

Android 无线数据交换是一项新近距离通信 (NFC) 技术，允许应用程序共享信息在近距离的 NFC Android 4。

## <a name="fingerprint-authenticationandroidplatformfingerprint-authenticationindexmd"></a>[指纹身份验证](~/android/platform/fingerprint-authentication/index.md)

本部分讨论如何使用指纹身份验证，Android 6.0 中，在首次引入到 Xamarin.Android 应用程序。


## <a name="firebase-job-dispatcherandroidplatformfirebase-job-dispatchermd"></a>[Firebase 作业调度程序](~/android/platform/firebase-job-dispatcher.md)

本指南讨论 Firebase 作业调度程序以及如何使用它来简化在 Xamarin.Android 应用程序中运行后台作业。



##  <a name="fragmentsandroidplatformfragmentsindexmd"></a>[片段](~/android/platform/fragments/index.md)

Android 3.0 引入了片段，显示如何在手机和平板电脑上找到的许多不同的屏幕大小支持更灵活的设计。 本文将介绍如何使用片段开发 Xamarin.Android 应用程序，以及如何在预 Android 3.0 (API 级别 11) 设备上支持片段。 



## <a name="app-linkingandroidplatformapp-linkingmd"></a>[App-Linking](~/android/platform/app-linking.md)

本指南将讨论如何支持 Android 6.0_应用链接_，该技术允许移动应用以响应在网站上的 Url。 它将讨论如何实现应用程序将 Android 6.0 应用程序中的链接以及如何配置网站以授予向移动应用程序的权限来处理应用程序链接域。



##  <a name="android-8-oreoandroidplatformoreomd"></a>[Android 8 Oreo](~/android/platform/oreo.md)

本文提供了 Android Oreo 中的新增功能概述介绍如何准备要 Xamarin.Android 的 Android Oreo 开发，并提供指向示例应用程序演示如何在 Xamarin.Android 应用程序中使用 Android Oreo 功能。



##  <a name="android-7-nougatandroidplatformnougatmd"></a>[Android 7 Nougat](~/android/platform/nougat.md)

本文章提供了 Android 7.0 Nougat 中引入的新功能的高级别概述。




##  <a name="android-6-marshmallowandroidplatformmarshmallowmd"></a>[Android 6 Marshmallow](~/android/platform/marshmallow.md)

本文章提供了 Android 6.0 Marshmallow 中引入的新功能的高级别概述。




##  <a name="android-5-lollipopandroidplatformlollipopmd"></a>[Android 5 Lollipop](~/android/platform/lollipop.md)

本指南提供的新的 Android 5.0 棒糖形功能，例如材料主题、 卡片视图-、 RecyclerView 和磁头向上通知的概述和链接供深入文章，可帮助你在应用中使用这些新功能。 



##  <a name="android-44-kitkatandroidplatformkitkatmd"></a>[Android 4.4 KitKat](~/android/platform/kitkat.md)

Android 4.4 (KitKat) 装具有大量用户和开发人员的功能。 本指南重点介绍了这些功能的几个，并提供代码示例和实现详细信息以帮助你充分利用 KitKat 做出。 




##  <a name="android-41-jelly-beanandroidplatformjelly-beanmd"></a>[Android 4.1 Jelly Bean](~/android/platform/jelly-bean.md)

本文档将为 Android 4.1 中引入的开发人员提供新功能的高级别的概述。 这些功能包括： 增强型通知，到 Android 无线发送共享大文件，对多媒体、 对等网络发现、 动画、 新的权限更新的更新。 



##  <a name="android-40-ice-cream-sandwichandroidplatformice-cream-sandwichmd"></a>[Android 4.0 Ice Cream Sandwich](~/android/platform/ice-cream-sandwich.md)

本指南介绍了几个可供使用的应用程序开发人员的新功能*Android 4 API-冰激凌德桑威奇*。 它介绍几种新的用户界面技术，然后检查的各种 Android 4 提供的数据应用程序之间和设备之间共享的新功能。 


##  <a name="working-with-the-android-manifestandroid-manifestmd"></a>[使用 Android 清单](android-manifest.md)

本文介绍 introducts AndroidManifest.xml 文件中，和如何它可能是用于控制功能以及描述 Android 应用程序的 Mono 的要求。


##  <a name="introduction-to-content-providersandroidplatformcontent-providersindexmd"></a>[内容提供商简介](~/android/platform/content-providers/index.md)

ContentProvider 封装的数据存储库，并提供一个 API 来访问它。 提供程序存在的 Android 应用程序通常还提供用于显示/管理数据的用户界面的一部分。 使用内容提供程序的主要好处使其他应用程序能够轻松地访问封装的数据使用提供程序客户端对象 （称为 ContentResolver）。 在一起的内容提供程序和内容解析程序提供一致的应用程序间 API 进行很容易生成和使用的数据访问。 本文档演示如何访问和生成与 Xamarin.Android ContentProviders。 



##  <a name="maps-and-locationandroidplatformmaps-and-locationindexmd"></a>[地图和位置](~/android/platform/maps-and-location/index.md)

本部分讨论如何使用 Xamarin.Android 的地图和位置。 它涵盖从利用内置地图应用程序使用的所有内容[Google 映射 Android API v2](https://developers.google.com/maps/documentation/android/)直接。 此外，它说明了如何使用单个 API 以便使用位置服务，使用移动电话三边转换以允许应用程序来获取位置修补程序、 Wi-fi 位置和 GPS。 



## <a name="android-speechandroidplatformspeechmd"></a>[Android 语音](~/android/platform/speech.md)

本部分讨论如何使用 Android 文本到语音转换和语音到文本设施。 它还介绍了安装语言包和解释说到设备的文本。 


##  <a name="binding-a-java-librarybinding-java-libraryindexmd"></a>[绑定 Java 库](binding-java-library/index.md)

本指南说明如何通过创建绑定库将 Java 库合并到 Xamarin.Android 应用程序。

##  <a name="java-integrationjava-integrationindexmd"></a>[Java 集成](java-integration/index.md)

本文概述了开发人员可以重复使用在 Xamarin.Android 应用中的现有 Java 组件的方式。

##  <a name="renderscriptrenderscriptmd"></a>[Renderscript](renderscript.md)

本指南讨论 Renderscript。