---
title: 使用其中的选项卡式的布局
description: 本指南介绍，并说明如何使用其中 Api 在 Xamarin.Android 应用程序中创建选项卡式的用户界面。
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 3e96ce2064391d585943f4d79453f8b4f8c6f583
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
ms.locfileid: "32020122"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>使用其中的选项卡式的布局

_本指南介绍，并说明如何使用其中 Api 在 Xamarin.Android 应用程序中创建选项卡式的用户界面。_


## <a name="overview"></a>概述

操作栏是用于为主要功能，如选项卡、 应用程序标识、 菜单和搜索提供一致的用户界面的 Android UI 模式。 在 Android 3.0 （API 级别 11） 中，Google 向 Android 平台的引入其中 Api。 其中 Api 引入用户界面主题，以提供一致的外观和用于提供选项卡式的用户接口的类。 本指南介绍如何将操作栏选项卡添加到 Xamarin.Android 应用程序。 它还介绍了如何使用 Android 支持库 v7 到向后移植操作栏选项卡添加到面向 Android 2.3 到 Android 2.1 的 Xamarin.Android 应用程序。 

请注意，`Toolbar`是你应使用而不是一个更高版本和更通用操作栏组件`ActionBar`(`Toolbar`旨在替换`ActionBar`)。 有关详细信息，请参阅[工具栏](~/android/user-interface/controls/tool-bar/index.md)。 



## <a name="requirements"></a>要求

任何 Xamarin.Android 应用程序面向 API 级别 11 (Android 3.0) 或更高版本作为本机 Android Api 的一部分中有权访问其中 Api。 

一些操作栏 Api 返回移植到 API 级别 7 (Android 2.1)，但不可通过[V7 AppCompat 库](http://developer.android.com/tools/support-library/features.html#v7-appcompat)，这将提供给 Xamarin.Android 应用程序通过[Xamarin Android 支持库-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)包。



## <a name="introducing-tabs-in-the-actionbar"></a>引入操作栏中的选项卡

操作栏尝试同时显示所有其选项卡，并使所有选项卡的大小基于最宽的选项卡标签的宽度相等。 这是由下面的屏幕截图所示： 

![其中的所有大小相等的多选项卡显示的示例屏幕截图](with-action-bar-images/image1.png)

当其中无法显示所有选项卡时，它将设置的水平可滚动视图中的选项卡。 左侧或右侧以查看剩余的选项卡，用户可能往下轻扫。 从 Google Play 此屏幕截图显示了此示例： 

![示例中的水平可滚动视图选项卡的屏幕截图](with-action-bar-images/image2.png)

在操作栏中的每个选项卡应关联[*片段*](~/android/platform/fragments/index.md)。 当用户选择一个选项卡时，应用程序将显示与选项卡关联的片段。其中不负责向用户显示相应的片段。 相反，其中将通知有关通过实现 ActionBar.ITabListener 接口的类的选项卡中的状态更改应用程序。 此接口提供三种 Android 选项卡的状态更改时将调用的回调方法： 

-  **OnTabSelected** -当用户选择选项卡时调用此方法。它应该会显示该片段。

-  **OnTabReselected** -当选项卡已被选择，但由用户再次选择时调用此方法。 通常使用此回调以刷新/更新显示的片段。

-  **OnTabUnselected** -当用户选择另一个选项卡时调用此方法。使用此回调以将状态保存在显示的片段中之前就会消失。

Xamarin.Android 包装`ActionBar.ITabListener`上的事件与`ActionBar.Tab`类。 应用程序可以将事件处理程序分配给一个或多个这些事件。 有三个事件 (一个用于在每个方法`ActionBar.ITabListener`) 操作栏选项卡将引发： 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>向其中添加选项卡

其中是本机到 Android 3.0 （API 级别 11） 和更高版本，可供任何面向此 API 与最小值的 Xamarin.Android 应用程序。 

以下步骤说明了如何将操作栏选项卡添加到 Android 活动： 

1. 在`OnCreate`一个活动的方法&ndash;*之前初始化任何 UI 小组件*&ndash;应用程序必须设置`NavigationMode`上`ActionBar`到`ActionBar.NavigationModeTabs`此代码中所示代码段：

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. 创建新的选项卡上使用`ActionBar.NewTab()`。

3. 分配事件处理程序或提供自定义`ActionBar.ITabListener`将响应用户交互操作栏选项卡时引发的事件的实现。

4. 添加到上一步中创建该选项卡`ActionBar`。


下面的代码是使用以下步骤将选项卡添加到使用事件处理程序的状态更改进行响应的应用程序的一个示例： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```


#### <a name="event-handlers-vs-actionbaritablistener"></a>事件处理程序与 ActionBar.ITabListener

应用程序应使用事件处理程序和`ActionBar.ITabListener`针对不同的方案。 事件处理程序确实提供了一定数量的语法上的便利;他们无需创建类并实现保存你`ActionBar.ITabListener`。 这种便利未是需要付出代价&ndash;Xamarin.Android 执行此转换为你创建一个类和实现`ActionBar.ITabListener`为你。 当应用程序具有有限的数量的选项卡时，这是正常的。 

许多选项卡上，在处理时共享之间操作栏选项卡的常见功能，也可以在内存和性能，以创建一个自定义类，实现方面更加高效`ActionBar.ITabListener`，和共享的类的单个实例。 这会减少 GREF 的 Xamarin.Android 应用程序正在使用的数量。 



### <a name="backwards-compatibility-for-older-devices"></a>向后兼容性较旧的设备

[Android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)后端口操作栏选项卡添加到 Android 2.1 （API 级别 7）。 此组件添加到项目后，选项卡是在 Xamarin.Android 应用程序中可访问。

若要使用其中，活动必须子类`ActionBarActivity`并使用 AppCompat 主题，如下面的代码段中所示：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

活动可能会获得对从其操作栏的引用`ActionBarActivity.SupportingActionBar`属性。 下面的代码段演示了在活动中操作栏设置的示例：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```


## <a name="summary"></a>总结

在本指南中我们将讨论如何在使用其中 Xamarin.Android 中创建选项卡式的用户界面。 我们介绍了如何向其中添加选项卡和活动与通过选项卡事件的交互方式`ActionBar.ITabListener`接口。 我们还了解了 Android 支持库 v7 AppCompat 包 backports 操作栏与旧版本的 Android 的选项卡。 


## <a name="related-links"></a>相关链接

- [ActionBarTabs （示例）](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [工具栏](~/android/user-interface/controls/tool-bar/index.md)
- [片段](~/android/platform/fragments/index.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](http://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [操作栏模式](http://developer.android.com/design/patterns/actionbar.html)
- [Android v7 AppCompat](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Xamarin.Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
