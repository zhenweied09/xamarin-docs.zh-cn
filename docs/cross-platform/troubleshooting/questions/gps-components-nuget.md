---
title: 将 Google Play Services 组件和 NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.openlocfilehash: cfd417f4fc01b07b4334259c45472eb24b73abd8
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="unifying-google-play-services-components-and-nuget"></a>将 Google Play Services 组件和 NuGet

### <a name="history"></a>历史记录

存在用于将多个 Google Play Services 组件和 NuGet 包：

-   Google Play 服务 (Froyo)
-   Google Play 服务 （小玩偶）
-   Google Play 服务 (ICS)
-   Google Play 服务 (JellyBean)
-   Google Play 服务 (KitKat)

Google 实际上仅附带两个.jar 文件 Google Play 服务：

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

差异存在转换，因为未正确告诉我们工具`aapt.exe`要用于给定应用的最大资源 API 级别已。 这意味着，我们收到编译错误，如果我们尝试在较低的 API 级别，如小玩偶上使用 Google Play 服务 (KitKat) 绑定。

### <a name="unifying-google-play-services"></a>将 Google Play Services

在 Xamarin.Android 的较新版本中，我们现在告诉`aapt.exe`最大资源版本使用，以便此问题将为我们的消失。

这意味着，没有任何单独的包对小玩偶/ICS/JellyBean/KitKat （但是我们仍单独绑定对需要 Froyo 因为它完全是不同的.jar 文件） 的真正原因。

为了简化操作的开发人员，我们已现在统一我们的组件和 NuGet 包复制到以下两个：

-   Google Play 服务 (Froyo) (将绑定`google-play-services-froyo.jar`)
-   Google Play 服务 (将绑定`google-play-services.jar`)

### <a name="which-one-should-be-used"></a>应使用哪一个？

在几乎所有情况下，应使用 Google Play 服务。 若要使用 (Froyo) 包的唯一原因是如果你主动面向 Froyo。 此单独.jar 文件存在将来自 Google 的唯一原因是由于 Froyo 启用的设备的此类一小部分，其本身已决定停止支持，因此此.jar 文件的 Google Play 服务冻结、 不受支持的快照。

### <a name="note-about-gingerbread"></a>请注意有关小玩偶

小玩偶没有默认情况下，支持的片段，因此，某些在绑定中的类将无法使用在运行时在小玩偶设备上的应用程序。 如类`MapFragment`上小玩偶，将无法工作，并且应改为使用其支持变体`SupportMapFragment`。 由开发人员知道要使用它。 此不兼容性 google 记录在其 Google Play 服务文档中。

### <a name="what-happens-to-the-old-componentsnugets"></a>旧组件/NuGet 会发生什么情况？

由于不再需要它们，我们具有禁用/Delisted 以下组件 NuGets:

-   Google Play 服务 （小玩偶）
-   Google Play 服务 (JellyBean)
-   Google Play 服务 (KitKat)

现有_Google Play 服务 (ICS)_ 组件/Nuget 已更名为_Google Play 服务_和将保留最新今后。 引用的禁用/Delisted 包之一的所有项目应都更新为使用此一个。

已禁用的组件仍然存在，因此应为它们中仍然引用，以避免破坏这些项目可还原。 同样 delisted 的 NuGet 包仍然存在，可以还原。 它们将不会更新今后。
