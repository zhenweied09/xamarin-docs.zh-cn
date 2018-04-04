---
title: 如何在内容提供程序的效果
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b4c674176be5af09d6b780d79923737a364d1591
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="how-content-providers-work"></a>如何在内容提供程序的效果

有两个类参与`ContentProvider`交互：

- **ContentProvider** &ndash;实现以标准方式公开的数据集的 API。 主要方法为查询、 插入、 更新和删除。

- **ContentResolver** &ndash;与进行通信的静态代理`ContentProvider`能够访问其数据，无论从同一个应用程序内或从另一个应用程序。

内容提供程序通常由一个 SQLite 数据库，但 API 意味着，使用代码不需要知道有关基础 SQL 的任何信息。 查询都是通过 Uri 使用常量引用列名称 （以减少对基础数据结构的依赖关系），和`ICursor`返回要使用的代码来循环访问。


## <a name="consuming-a-contentprovider"></a>使用 ContentProvider

`ContentProviders` 公开其功能通过在中注册的 Uri **AndroidManifest.xml**的应用程序将数据发布。 是的约定 Uri 和公开的数据列应在其中可用作为常量，以便可以方便地绑定到数据。 Android 的内置`ContentProviders`都提供方便类具有引用中的数据结构的常量[ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/)命名空间。



### <a name="built-in-providers"></a>内置提供程序

Android 提供对各种系统和用户数据使用访问`ContentProviders`:

- *浏览器*&ndash;书签和浏览器历史记录 (需要权限`READ_HISTORY_BOOKMARKS`和/或`WRITE_HISTORY_BOOKMARKS`)。

- *CallLog* &ndash;还是与设备接收了新的调用。

- *联系人*&ndash;详细从用户的联系人列表中，包括人员、 手机、 照片和组的信息。

- *MediaStore* &ndash;用户的设备的内容： 音频专辑、 艺术家、 风格 （播放列表）、 （包括缩略图） 的图像和视频。

- *设置*&ndash;系统范围内的设备设置和首选项。

- *UserDictionary* &ndash;用于预测的文本输入用户定义的字典的内容。

- *语音邮件*&ndash;的语音邮件消息的历史记录。



## <a name="classes-overview"></a>类概述

在使用时使用的主类`ContentProvider`如下所示：

[![内容提供程序应用程序和 Consuming 应用程序交互的类图](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

在此图中，`ContentProvider`实现查询并注册 URI 的其他应用程序用来查找数据。 `ContentResolver`起到代理的作用`ContentProvider`（查询、 插入、 更新和删除方法）。 `SQLiteOpenHelper`包含使用的数据`ContentProvider`，但不是直接公开给使用应用。
`CursorAdapter`传递光标返回`ContentResolver`要在中显示`ListView`。 `UriMatcher`是处理查询时就会分析 Uri 的帮助器类。

下面描述了每个类的用途：

- **ContentProvider** &ndash;实现此抽象类的方法来公开数据。 API 可供其他类和通过向类定义添加的 Uri 特性的应用程序。

- **SQLiteOpenHelper** &ndash;可帮助实现公开的 SQLite 数据存储`ContentProvider`。

- **UriMatcher** &ndash;使用`UriMatcher`中你`ContentProvider`实现以帮助管理用于查询的内容的 Uri。

- **ContentResolver** &ndash;使用代码使用`ContentResolver`访问`ContentProvider`实例。 两个类一起负责进程间通信问题，从而允许数据可轻松地应用程序之间共享。 使用代码永远不会创建`ContentProvider`类显式; 相反，创建一个游标，该游标根据公开的 Uri 访问的数据`ContentProvider`应用程序。

- **CursorAdapter** &ndash;使用`CursorAdapter`或`SimpleCursorAdapter`以显示数据通过访问`ContentProvider`。

`ContentProvider` API 允许使用者执行各种操作的数据，如：

-  查询返回列表或单个记录数据的数据。
-  修改各个记录。
-  添加新的记录。
-  删除记录。

本文档包含使用系统提供的示例， `ContentProvider`，以及一个简单的只读的示例实现一个自定义`ContentProvider`。

