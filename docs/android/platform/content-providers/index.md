---
title: "ContentProviders 简介"
description: "Android 操作系统使用内容提供商为了便于访问共享数据，例如媒体文件、 联系人和日历信息。 本文介绍 ContentProvider 类，并提供如何使用它的两个示例。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 53408220f1bbd3b0aa97e0c54bd8f4c09847b448
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="intro-to-contentproviders"></a>ContentProviders 简介

_Android 操作系统使用内容提供商为了便于访问共享数据，例如媒体文件、 联系人和日历信息。本文介绍 ContentProvider 类，并提供如何使用它的两个示例。_


## <a name="content-providers-overview"></a>内容提供程序概述

A *ContentProvider*封装的数据存储库，并提供一个 API 来访问它。 提供程序存在的 Android 应用程序通常还提供用于显示/管理数据的用户界面的一部分。 使用内容提供程序的主要好处使其他应用程序能够轻松地访问封装的数据使用的提供程序客户端对象 (称为*ContentResolver*)。 在一起，内容提供程序和内容解析程序提供一致的应用程序间 API 进行很容易生成和使用的数据访问。 任何应用程序可以选择使用`ContentProviders`内部管理数据以及将其公开给其他应用程序。

A `ContentProvider` ，则也需要应用程序，以提供自定义搜索建议，或如果你想要提供将复杂的数据从你的应用程序，将粘贴到其他应用程序复制的功能。 本文档演示如何访问和生成`ContentProviders`与 Xamarin.Android。

本部分的结构如下所示：

- **它是如何工作**&ndash;的新增功能概述`ContentProvider`旨在为，以及如何配合工作。

- **使用内容提供商**&ndash;示例访问联系人列表。

- **使用 ContentProvider 共享数据**&ndash;编写和使用`ContentProvider`在同一应用程序。

`ContentProviders` 并对其数据的操作的光标通常用于填充 Listview。 请参阅[Listview 和适配器指南](~/android/user-interface/layouts/list-view/index.md)有关如何使用这些类的详细信息。

`ContentProviders` 公开的 Android （或其他应用程序），可以方便地在你的应用程序中包含来自其他源的数据。 它们允许你访问和显示数据，例如联系人列表、 照片或从应用程序中的日历事件，并允许用户与该数据进行交互。

自定义`ContentProviders`是一种简便的方法来打包你的数据在你自己的应用中使用或用于通过其他应用程序 （包括自定义搜索和复制/粘贴等特殊用途）。

本部分中的主题提供一些简单的示例使用和写入`ContentProvider`代码。



## <a name="related-links"></a>相关链接

- [ContactsAdapter 演示 （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [内容提供程序开发人员指南](http://developer.android.com/guide/topics/providers/content-providers.html)
- [ContentProvider 类引用](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [ContentResolver 类引用](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [ListView 类引用](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [CursorAdapter 类引用](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [UriMatcher 类引用](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [ContactsContract 类引用](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
