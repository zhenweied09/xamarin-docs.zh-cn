---
title: "提供向后与 Android 支持包的兼容性"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/12/2017
ms.openlocfilehash: 670ec465843bbe819b41a53fff71b01ab78b0059
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>提供向后与 Android 支持包的兼容性

将片段的用途有限而无需向后兼容性的预 Android 3.0 (API 级别 11) 设备。 若要提供此功能，Google 引入[支持库](http://developer.android.com/sdk/compatibility-library.html)(最初调用*Android 的兼容性库*发布时) 的 backports 一些从较新版本的 Api与旧版本的 Android android。 它是 Android 使 Android 2.3.3 到运行 Android 1.6 （API 级别 4） 的设备的支持包。 （API 级别 10）。

> [!NOTE]
> 仅`ListFragment`和`DialogFragment`通过 Android 的支持包。 无其他片段子类，例如`PreferenceFragment,`支持 Android 的支持包中。 它们不会预先 Android 3.0 的应用程序中。 


## <a name="adding-the-support-package"></a>添加支持包

Android 支持包不会自动添加到 Xamarin.Android 应用程序。 Xamarin 提供[Android 支持库 v4 NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)来简化支持库添加到 Xamarin.Android 应用程序。若要将支持包包含在应用程序包括你 Xamarin.Android [Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)组件入 Xamarin.Android 项目中，如下面的屏幕截图中所示： 

[![添加到项目的屏幕截图 Android 支持库 v4 包](providing-backwards-compatibility-images/02.png)](providing-backwards-compatibility-images/02.png#lightbox)

在执行这些步骤后，就可以使用在早期版本的 Android 片段。 片段 Api 将在这些早期版本中，有以下例外工作相同现在： 

-   **更改最低 Android 版本**&ndash;应用程序不再需要面向 Android 3.0 或更高版本，如下所示： 

    [![最低 Android 屏幕快照设置应用程序属性下的目标](providing-backwards-compatibility-images/03.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **扩展 FragmentActivity** &ndash;托管片段的活动必须现在继承自`Android.Support.V4.App.FragmentActivity`，而不是从`Android.App.Activity`。 

-   **更新命名空间**&ndash;继承的类`Android.App.Fragment`必须现在继承自`Android.Support.V4.App.Fragment`。 删除使用语句" `using Android.App;` "顶部的源代码文件，并将其替换为" `using Android.Support.V4.App` "。 

-   **使用 SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity`公开`SupportingFragmentManager`属性，必须用于获取对引用`FragmentManager`。 例如: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

与这些更改后，它将有可能在 Android 1.6 或 2.x 上以及在 Honeycomb 和冰激凌德桑威奇运行基于片段的应用程序。 


## <a name="related-links"></a>相关链接

- [Android 支持库 v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
