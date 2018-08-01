---
title: 冰激凌德桑威奇功能
description: 本文介绍一些可供使用 Android 4 API-冰激凌三明治的应用程序开发人员的新功能。 它介绍几种新的用户界面技术，然后检查的各种 Android 4 提供的数据应用程序之间和设备之间共享的新功能。
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 3c5412629f6dcd31fb0a82634ef530569eef459a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764926"
---
# <a name="ice-cream-sandwich-features"></a>冰激凌德桑威奇功能

_本文介绍一些可供使用 Android 4 API-冰激凌三明治的应用程序开发人员的新功能。它介绍几种新的用户界面技术，然后检查的各种 Android 4 提供的数据应用程序之间和设备之间共享的新功能。_

## <a name="overview"></a>概述

Android OS 版本 4.0 (API 级别 14) 表示主要的改编 Android 操作系统的并且包括大量的重要更改和升级，包括：

-   **更新用户界面**– 几个新的用户界面功能提供开发人员更电源和时他们创建应用程序用户的灵活性接口。 这些新功能包括： `GridLayout` ， `PopupMenu` ，`Switch`小组件中，和`TextureView`。 
-   **更好的硬件加速**– 二维现在呈现所有 Android 控件在 GPU 上的发生。 此外，硬件加速处于打开状态，默认情况下，在为 Android 4.0 开发的所有应用程序。 
-   **新的数据 Api** – 没有新访问无法以前正式访问，如日历数据和设备所有者的用户配置文件的数据。 
-   **应用程序数据共享**– 共享应用程序和设备之间的数据，则现在比以往通过技术如`ShareActionProvider`，这样就可以轻松从操作栏中，创建共享操作和*Android 无线发送*有关*近距离通信 (NFC)* ，这使得变得很容易地在彼此的近距离的设备之间共享数据。 


在本文中，我们将浏览这些功能和其他 Android 4.0 API，对所做的更改和我们将介绍如何通过 Xamarin.Android 使用每个功能。

## <a name="user-interface-features"></a>用户界面功能

新的用户界面技术的各种是适用于 Android 4，包括：

-   **[GridLayout](~/android/user-interface/layouts/grid-layout.md)**  – 支持的控件的二维网格布局。 
-   **[切换小组件](~/android/user-interface/controls/switch.md)** – 允许之间切换 ON 或 OFF。 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  – 使视频和 OpenGL 的视图中的内容。 
-   **[导航栏](~/android/user-interface/controls/navigation-bar.md)** – 包含虚拟按钮后，家庭和多任务。 


此外，其他 UI 元素得到了增强，如`<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`，其值为现在更轻松地使用，且选项卡上，其中包含具有多优美的外观。

## <a name="sharing-features"></a>共享功能

Android 4 包括让我们跨设备和应用程序间共享数据的几种新技术。 它还提供对各种类型的未以前提供，如日历信息和设备所有者的用户配置文件的数据访问。 在本节中我们将检查了各种各样的功能提供 Android 4 该地址这些领域包括：

-  **[Android 无线发送](~/android/platform/android-beam.md)** – 允许数据通过 NFC 共享。
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  – 创建的提供程序允许开发人员指定操作栏中的共享操作。 
-   **[用户配置文件](~/android/user-interface/user-profile.md)** – 提供对设备所有者的配置文件数据的访问。 
-   **[日历 API](~/android/user-interface/controls/calendar.md)**  – 从日历提供程序提供对日历数据的访问权限。 

## <a name="x86-emulators"></a>x86 仿真程序

ICS 尚不支持开发与 x86 仿真程序。 x86 仿真程序仅支持 Android 2.3.3，API 级别 10。 请参阅[配置 x86 仿真程序](~/android/get-started/installation/android-emulator/index.md)有关详细信息。

## <a name="summary"></a>总结

本文介绍了各种现适用于 Android 4 的新技术。 我们回顾了新的用户界面功能如*GridLayout*， *PopupMenu*，和*交换机*小组件。 我们还了解了一些用于控制系统用户界面，以及如何才能使用新的支持*TextureView*。 然后，我们讨论了多种新共享技术。 我们如何涵盖*Android 无线发送*让我们使用的设备之间共享信息*NFC*，讨论新*日历 API*，还介绍了如何使用内置的和*ShareActionProvider*。
最后，我们探讨了如何使用*ContactsContract*访问用户配置文件数据提供程序。



## <a name="related-links"></a>相关链接

- [冰激凌德桑威奇示例](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo （示例）](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo （示例）](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [选项卡布局教程](~/android/user-interface/layouts/tab-layout/index.md)
- [冰激凌德桑威奇](http://developer.android.com/about/versions/android-4.0-highlights.html)
- [Android 4.0 平台](http://developer.android.com/about/versions/android-4.0.html)
