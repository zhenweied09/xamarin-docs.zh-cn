---
title: "演练-保存活动状态"
description: "我们已介绍的理论基础活动的生命周期指南; 中保存状态现在，让我们演练一下示例。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: d8b44fb7f0e60db407271fd84899489bf8e65694
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="walkthrough---saving-the-activity-state"></a>演练-保存活动状态

_我们已介绍的理论基础活动的生命周期指南; 中保存状态现在，让我们演练一下示例。_

## <a name="activity-state-walkthrough"></a>活动状态演练

让我们打开**ActivityLifecycle_Start**项目 (在[ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)示例)，生成它，并运行它。 这是一个非常简单的项目具有两个活动，以演示活动生命周期和如何调用各种的生命周期方法。 当启动应用程序的屏幕`MainActivity`显示： 

[![活动 A 屏幕](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>查看状态转换

在此示例中的每个方法将写入到 IDE 应用程序输出窗口，以指示活动状态。 (若要在 Visual Studio 中打开输出窗口，键入**CTRL ALT O**; 若要将用于 Mac 打开 Visual Studio 中的输出窗口中，单击**视图 > 填充 > 应用程序输出**。)

输出窗口首次启动应用程序时, 显示的状态更改*活动 A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

当我们单击**启动活动 B**按钮，我们看到*活动 A*暂停和停止时*活动 B*经历其状态更改： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

因此，*活动 B*已启动并显示代替了*活动 A*: 

[![活动 B 屏幕](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

当我们单击**回**按钮，*活动 B*销毁和*活动 A*恢复： 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>添加单击计数器

接下来，我们将更改应用程序，以便我们具有计数和显示的单击的次数的按钮。 首先，让我们添加`_counter`实例变量`MainActivity`:

```csharp
int _counter = 0;
```

接下来，让我们编辑**Resource/layout/Main.axml**布局文件，并添加一个新`clickButton`显示的用户单击按钮的次数。 生成**main.axml**应如下所示： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

让我们将以下代码添加到末尾[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)中的方法`MainActivity`&ndash;此代码处理单击事件从`clickButton`:

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

当我们生成并再次运行该应用，一个新按钮，则会出现，递增和显示的值`_counter`上每次单击：

[![添加触摸计数](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

但当我们在旋转设备连接到横向模式，便会丢失此次数：

[![轮换为横向回零设置计数](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

检查应用程序输出，我们看到*活动 A*已暂停、 停止、 销毁、 重新创建、 重新启动，然后从纵向为横向模式在轮换恢复： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

因为*活动 A*销毁该对象并将设备旋转时，再次重新创建其实例状态将丢失。 接下来，我们将添加代码来保存并还原实例状态。

### <a name="adding-code-to-preserve-instance-state"></a>将代码添加到保留实例状态

让我们将方法添加到`MainActivity`保存实例状态。 之前*活动 A*是被销毁，Android 自动调用[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/)并且将传入[捆绑](https://developer.xamarin.com/api/type/Android.OS.Bundle/)，我们可以使用存储我们实例状态。 让我们使用它来将我们单击计数另存为一个整数值：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

当*活动 A*恢复，并重新创建 Android 将此传递`Bundle`回我们`OnCreate`方法。 让我们将代码添加到`OnCreate`还原`_counter`从传入的值`Bundle`。 添加以下代码行之前只是其中`clickbutton`定义： 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

生成并再次运行应用程序，然后单击几次的第二个按钮。 当我们在旋转设备连接到横向模式时，将被保留计数 ！

[![轮换屏幕显示的四个保留的计数](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


让我们看看输出窗口以查看发生了什么情况：
    
```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
``` 

之前[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/)调用方法，我们新`OnSaveInstanceState`调用了方法，以保存`_counter`中的值`Bundle`。 Android 传递此`Bundle`回我们调用时我们`OnCreate`方法，并且我们曾经使用它来还原`_counter`到我们的地方开始的值。


## <a name="summary"></a>摘要

在本演练中，我们使用了我们的活动的生命周期的知识来保留状态数据。 



## <a name="related-links"></a>相关链接

- [ActivityLifecycle （示例）](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Android 活动](https://developer.xamarin.com/api/type/Android.App.Activity/)
