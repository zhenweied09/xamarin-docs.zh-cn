---
title: 使用支持包的支持预 Honeycomb Android
ms.prod: xamarin
ms.assetid: DACD0C14-5DDF-7BDE-6844-80550D301307
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: 75e12821d96b98037c568fb5dac69ba53a507670
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="supporting-pre-honeycomb-android-using-support-packages"></a>使用支持包的支持预 Honeycomb Android

*Android 支持包*包含回端口一些新 API 的库&ndash;如片段&ndash;与旧版本的 Android。 因此通过添加 Android 的支持包，我们可以运行我们的应用程序 Android 2.3 设备上的以下屏幕所示：

[![片段演练和详细信息活动的屏幕截图](supporting-pre-honeycomb-images/01-sml.png)](supporting-pre-honeycomb-images/01.png#lightbox)

## <a name="adding-the-support-package"></a>添加支持包

Android 支持包不会自动添加到 Xamarin.Android 应用程序。 Xamarin 提供[Android 支持库 v4 NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)来简化支持库添加到 Xamarin.Android 应用程序。
若要将支持包包含在应用程序包括你 Xamarin.Android [Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)组件入 Xamarin.Android 项目中，如下面的屏幕截图中所示：

[![添加 Android 支持库 v4 包](supporting-pre-honeycomb-images/02-sml.png)](supporting-pre-honeycomb-images/02.png#lightbox)

已添加包后，更改为 Android 2.2 或更高的目标框架：

[![更改目标框架 API 级别的屏幕截图](supporting-pre-honeycomb-images/03-sml.png)](supporting-pre-honeycomb-images/03.png#lightbox)

此外，确保最低 Android 版本面向相同的 API 级别：

[![最低 Android 版本设置的屏幕截图](supporting-pre-honeycomb-images/04-sml.png)](supporting-pre-honeycomb-images/04.png#lightbox)

### <a name="change-mainactivity-to-derive-from-fragmentactivity"></a>更改 MainActivity 从 FragmentActivity 派生

任何使用片段活动必须继承自`Xamarin.Support.V4.App.FragmentActivity`。 此类是 android 的必需的部分的支持包，因为它允许的片段以托管活动，而不考虑版本。 `MainActivity` 需要仅一个小更改 — 它必须现在继承自`Android.Support.V4.App.FragmentActivity`:

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```


### <a name="change-detailsactivity-to-derive-from-fragmentactivity"></a>更改 DetailsActivity 从 FragmentActivity 派生

`DetailsActivity` 此外必须从更改`Activity`到`FragmentActivity`。 作为`FragmentManager`是与预 Honeycomb 版本的 Android 不兼容，Android 的支持包包括一个包装类， `SupportFragmentManager`，提供向后兼容性。 每个`FragmentActivity`具有`SupportFragmentManager`属性，和`DetailsActivity`更改为使用`SupportFragmentManager`而不是`FragmentManager`:

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("index", 0);
       var details = DetailsFragment.NewInstance(index);
       var fragmentTransaction = SupportFragmentManager.BeginTransaction(); // Notice the change from FragmentManager to SupportFragmentManager
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

我们完成这些更改后，我们现在有应用程序可以对运行在 Android 1.6 和更高版本，并使用片段调整到我们的目标设备的大小我们 UI。


## <a name="related-links"></a>相关链接

- [Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4)
