---
title: "演练-使用 TabHost 创建选项卡式的 UI"
description: "本文将引导完成在 Xamarin.Android 使用 TabHost API 中创建选项卡式的 UI。"
ms.topic: article
ms.prod: xamarin
ms.assetid: AD6E2173-974E-477C-940F-0CAB5E53326D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 2dd397e824ce7735be4421c3f258852de3f77ecb
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="walkthrough---creating-a-tabbed-ui-with-tabhost"></a>演练-使用 TabHost 创建选项卡式的 UI

_本文将引导完成在 Xamarin.Android 使用 TabHost API 中创建选项卡式的 UI。_

> [!NOTE]
> `TabHost` 是已否决 Google 的旧 API。 若要生成使用的选项卡式应用程序，鼓励开发人员[其中](~/android/user-interface/controls/action-bar.md)。 `ActionBar`在所有版本的 Android 中可用。 它首先 Android 3.0 （API 级别 11） 中引入并返回已移植到 Android 2.2 （API 级别 8） 和 Android 2.3 （API 级别 10） 中[V7 AppCompat 库](http://developer.android.com/tools/support-library/features.html#v7-appcompat)，可供通过 Xamarin.Android [XamarinAndroid 支持库-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)包。

本文将指导完成使用在 Xamarin.Android 中创建选项卡式的 UI `TabHost` API。 这是 android 的可在所有版本中的较旧 API。 此示例将创建与三个选项卡，在活动中正在封装每个选项卡的逻辑的应用程序。
下面的屏幕截图是应用的我们将创建程序的一个示例：

![示例的应用程序与多个选项卡的屏幕截图](creating-a-tabbed-ui-images/image02.png)


## <a name="creating-the-application"></a>创建应用程序

下载并解压缩[TabHostWalkthrough](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/)。
此项目用作我们的应用程序的起始点，并包含某些图像。 如果您检查此项目，你将看到，我们已创建的选项卡图标可绘制的资源。

第一个让我们更新布局文件**Resources/Layout/Main.axml**将承载选项卡。 编辑**Resources/Layout/Main.axml**和插入以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
         android:id="@android:id/tabhost"
         android:layout_width="fill_parent"
         android:layout_height="fill_parent">
    <LinearLayout
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:padding="5dp">
        <TabWidget
                android:id="@android:id/tabs"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content" />
        <FrameLayout
                android:id="@android:id/tabcontent"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:padding="5dp" />
    </LinearLayout>
</TabHost>
```

下面的屏幕截图显示了 Xamarin 设计器中的布局：

[![在 Xamarin 设计器中的 TabHost 布局的屏幕截图](creating-a-tabbed-ui-images/image04-sml.png)](creating-a-tabbed-ui-images/image04.png#lightbox)

TabHost 必须具有其内部的两个子视图：`TabWidget`和`FrameLayout`。 为位置位置`TabWidget`和`FrameLayout`垂直内部`TabHost`、`LinearLayout`使用。 FrameLayout 是每个选项卡的内容的位置，它是空因为`TabHost`会自动嵌入在运行时的每个活动。 有多个创建选项卡式的用户界面的布局时，必须遵守的规则：

-   `TabHost`必须具有 id `@android:id/tabhost`。

-   `TabWidget`必须具有 id `@android:id/tabs`。

-   `FrameLayout`必须具有 id `@android:id/tabcontent`。

-   `TabHost` 需要它管理要从其继承的任何活动`TabActivity`。 因此，很重要子类化`TabActivity`此处&ndash;不起作用常规活动。

编辑文件**MainActivity.cs**以便类`MainActivity`子类`TabActivity`下面的代码段中所示：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/ic_launcher")]
public class MainActivity : TabActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
    }
}
```

在你的项目中创建四个单独的活动类： `MyScheduleActivity`， `SessionsActivity`， `SpeakersActivity`，和`WhatsOnActivity`。 每个活动将构成了一个选项卡的 UI。现在这些活动将显示一个存根`TextView`简单的消息。 编辑每个活动以包含以下中的代码`OnCreate`实现：

```csharp
[Activity]
public class MyScheduleActivity : Activity
{
    protected override void OnCreate (Bundle savedInstanceState)
    {
        base.OnCreate (savedInstanceState);
        TextView textview = new TextView (this);
        textview.Text = "This is the My Schedule tab";
        SetContentView (textview);
    }
}
```

请注意，上面的代码不使用布局文件。 它只需创建`TextView`与一些文本，并将其设置`TextView`作为内容的视图。 每个剩余的三个活动重复这。

接下来我们将分配到每个选项卡的图标。每个选项卡需要两个图标&ndash;另一个用于所选的状态和一个用于未选中状态。 可以在下面的两个图像 （此应用程序的必需图标已添加到示例项目） 中看到举例说明这些两个不同的图标：

![所选的状态和未选定的状态图标的屏幕截图](creating-a-tabbed-ui-images/tab-icons.png)


我们会将可绘制资源分配给图标选项卡时，通过定义*状态列表 Drawable*。 状态列表 drawables 是特殊的绘制资源，在 XML 中定义和允许你指定特定于该项的状态的不同映像。 在此示例中没有选中一个选项卡时，使用的一个映像，另一个未选中选项卡时使用。 保存时，有必要的状态列表 drawables 已添加到项目。 以下列表显示了文件和每个 XML 包含：

-   `ic_tab_my_schedule.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

-   `ic_tab_sessions.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_sessions_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_sessions_unselected"/>
    </selector>
    ```

-   `ic_tab_speakers.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_speakers_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_speakers_unselected"/>
    </selector>
    ```

-   `ic_tab_whats_on.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

选项卡添加到`TabHost`以编程方式，这是非常重复任务。 若要帮助实现这一点，请将以下方法添加到类`MainActivity`:

```csharp
private void CreateTab(Type activityType, string tag, string label, int drawableId )
{
    var intent = new Intent(this, activityType);
    intent.AddFlags(ActivityFlags.NewTask);

    var spec = TabHost.NewTabSpec(tag);
    var drawableIcon = Resources.GetDrawable(drawableId);
    spec.SetIndicator(label, drawableIcon);
    spec.SetContent(intent);

    TabHost.AddTab(spec);
}
```

在每个选项卡`TabHost`的实例所表示的`TabHost.TabSpec`类。 此实例包含元数据需要呈现选项卡上，具体而言：

-   **文本和图标**&ndash;中显示`TabWidget`。

-   **选项卡内容**&ndash;这可能是`Activity`或`View`并选择选项卡会显示。

-   **唯一标记**&ndash;每个选项卡必须具有分配给它的唯一标记。

我们必须添加`TabHost.TabSpec`我们的应用程序中的每个选项卡的实例。 允许在下一步执行该操作。

更新方法`OnCreate`中`MainActivity`，使它类似于下面的代码：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateTab(typeof(WhatsOnActivity), "whats_on", "What's On", Resource.Drawable.ic_tab_whats_on);
    CreateTab(typeof(SpeakersActivity), "speakers", "Speakers", Resource.Drawable.ic_tab_speakers);
    CreateTab(typeof(SessionsActivity), "sessions", "Sessions", Resource.Drawable.ic_tab_sessions);
    CreateTab(typeof(MyScheduleActivity), "my_schedule", "My Schedule", Resource.Drawable.ic_tab_my_schedule);
}
```

运行该应用程序。 你的应用程序应如下所示的屏幕截图显示此演练的开头。

就这么简单！ 我们已创建的应用程序的不同部分使用户轻松导航选项卡式应用程序。



## <a name="summary"></a>摘要

这一章讨论选项卡式的布局，并指导你完成创建选项卡式应用程序的过程。 本演练演示了如何使用`TabActivity`放大量布局文件该承载`TabHost`和`TabWidget`。 `TabHost`的集合，然后填充`TabHost.TabSpec`对象将由使用`TabHost`在运行时实例化将在每个选项卡中使用的活动。



## <a name="related-links"></a>相关链接

- [TabHostWalkthrough （示例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 库](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
