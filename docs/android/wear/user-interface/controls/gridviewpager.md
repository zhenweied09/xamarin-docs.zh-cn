---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: 3a0b1ec9359b1c6067c253b4d04126dbdd726cc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="gridviewpager"></a>GridViewPager

[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)示例演示如何实现 Android 磨损的二维选取器导航模式。

![GridViewPager 上正方形显示示例屏幕快照](gridviewpager-images/gridviewpager.png)

首次添加[Xamarin Android 磨损支持](http://www.nuget.org/packages/Xamarin.Android.Wear/)到你的项目的 NuGet 包。

布局 XML 如下所示：

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

创建[ `GridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html) (或子类如[ `FragmentGridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)提供视图以显示当用户导航。

[示例适配器](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs)演示如何实现所需的方法，包括替代`RowCount`， `GetColumnCount`， `GetBackground`，和 `GetFragment`

将适配器所示：

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>相关链接

- [Google 的二维选取器文档](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [android.support.wearable 文档](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager （示例）](https://developer.xamarin.com/samples/GridViewPager/)
