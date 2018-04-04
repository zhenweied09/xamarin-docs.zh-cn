---
title: 导航栏
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: 476e73906e4fbe01847740f90a8da701768b29db
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="navigation-bar"></a>导航栏

Android 4 引入名的新系统用户界面功能*导航栏*，它提供在不包含有关的硬件按钮的设备上的导航控件**主页**，**返回**，和**菜单**。
以下屏幕截图显示导航栏从 Nexus 质数设备：

 [![Android 导航栏的示例](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

可以使用几个新标记用于控制导航栏和其控件的可见性以及在 Android 3 中引入的系统栏的可见性。 在中定义的标志`Android.View.View`类，如下所示：

-   `SystemUiFlagVisible` &ndash; 使导航栏可见。 
-   `SystemUiFlagLowProfile` &ndash; 在导航栏中的控件的变暗。 
-   `SystemUiFlagHideNavigation` &ndash; 隐藏导航栏。 


这些标志可以通过设置应用于的任意视图中查看层次结构`SystemUiVisibility`属性。 如果多个视图有此属性设置，系统将它们合并与或运算，并将其应用处理程序，但前提是在其中设置了标志窗口保留焦点。 当您删除视图时，已将它设置任何标志也将被删除。

下面的示例演示一个简单的应用程序，单击任一按钮更改`SystemUiVisibility`:

 [![演示可见、 低分析和隐藏 SystemUiVisibility 的屏幕截图](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

若要更改的代码`SystemUiVisibility`上设置的属性`TextView`从每个按钮的单击事件处理程序如下所示：

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);
           
lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};
           
hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};
           
visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

此外，`SystemUiVisibility`更改引发`SystemUiVisibilityChange`事件。 就像设置`SystemUiVisibility`属性、 的处理程序`SystemUiVisibilityChange`层次结构中的任何视图注册的事件。 例如，以下代码使用`TextView`实例以注册事件：

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>相关链接

- [SystemUIVisibilityDemo （示例）](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [引入冰激凌德桑威奇](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
