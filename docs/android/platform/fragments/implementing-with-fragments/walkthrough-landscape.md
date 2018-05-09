---
title: 片段演练-第 2 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 58291388d375a4fd9273c8e0cd46db3799966766
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="fragments-walkthrough-ndash-landscape"></a>片段演练&ndash;横向

[片段演练&ndash;第 1 部分](./walkthrough.md)演示了如何创建和使用片段中的 Android 应用程序面向 phone 上较小的屏幕。 本演练的下一步是修改应用程序以利用平板电脑上的额外水平空间&ndash;将始终会起到列表的一个活动 ( `TitlesFragment`) 和`PlayQuoteFragment`将动态添加到中对用户进行的选定内容的响应的活动：

[![在平板电脑上运行的应用程序](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

在横向模式下运行的手机将是有益的此增强功能：

[![在横向模式中 Android 手机上运行应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>更新应用程序来处理横向

以下修改之处，将依赖在中完成的工作[片段演练-电话](./walkthrough.md)

1. 创建备用的布局，以同时显示`TitlesFragment`和`PlayQuoteFragment`。
1. 更新`TitlesFragment`以检测设备如果正在同时显示两个片段并相应地更改行为。
1. 更新`PlayQuoteActivity`以关闭该设备时在横向模式中。

## <a name="1-create-an-alternate-layout"></a>1.创建备用布局

Android 设备上创建主活动时, Android 将决定哪种布局加载基于设备的方向。 默认情况下，将提供 Android **Resources/layout/activity_main.axml**布局文件。 对于在横向模式中加载的设备提供 Android 将**Resources/layout-land/activity_main.axml**布局文件。 上的参考线[Android 资源](/xamarin/android/app-fundamentals/resources-in-android)包含 Android 如何判断哪些资源时要为应用程序加载的文件的详细信息。

创建备用布局面向**横向**中所述的步骤的方向[备用布局](/xamarin/android/user-interface/android-designer/alternative-layout-views)指南。 这应将新的布局资源文件添加到项目中， **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在解决方案资源管理器的备用布局](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在本解决方案中的备用布局](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

创建备用版式后，编辑文件的来源**Resources/layout-land/activity_main.axml** ，使其匹配此 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

活动的根视图给定的资源 ID`two_fragments_layout`和具有两个子视图，`fragment`和`FrameLayout`。 虽然`fragment`以静态方式加载，`FrameLayout`充当将被替换为在运行时由一个"占位符" `PlayQuoteFragment`。 每次在中选择新 play `TitlesFragment`、`playquote_container`将使用的新实例的更新`PlayQuoteFragment`。

每个子视图占用它们的父级的最大高度。 由控制每个子视图的宽度`android:layout_weight`和`android:layout_width`属性。 在此示例中，每个子视图将占用 50%的宽度提供由父级。 请参阅[LinearLayout 上的 Google 文档](https://developer.android.com/guide/topics/ui/layout/linear.html)有关的详细信息_布局权重_。

## <a name="2-changes-to-titlesfragment"></a>2.对 TitlesFragment 更改

一旦创建备用布局后，有必要更新`TitlesFragment`。 当应用程序显示两个片段上一个活动，然后`TitlesFragment`应加载`PlayQuoteFragment`父活动中。 否则为`TitlesFragment`应启动`PlayQuoteActivity`哪台主机`PlayQuoteFragment`。 一个布尔型标志将帮助`TitlesFragment`确定应使用哪种行为。 将在中初始化此标志`OnActivityCreated`方法。

首先，在顶部添加一个实例变量`TitlesFragment`类：

```csharp
bool showingTwoFragments;
```

然后，将添加到下面的代码段`OnActivityCreated`初始化该变量： 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

如果设备正在运行在横向模式中，则`FrameLayout`具有资源 ID`playquote_container`将显示在屏幕上，因此`showingTwoFragments`将初始化为`true`。 如果设备是纵向模式下运行，然后`playquote_container`将不会在屏幕上，因此`showingTwoFragments`将`false`。

`ShowPlayQuote`方法将需要更改显示引号方式&ndash;放在片段或启动新的活动。  更新`ShowPlayQuote`方法以加载片段时显示两个片段，否则，它应启动活动：

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

如果用户具有选择不同于当前显示在 play `PlayQuoteFragment`，然后新`PlayQuoteFragment`创建，将替换内容`playquote_container`的上下文中`FragmentTransaction`。

### <a name="complete-code-for-titlesfragment"></a>TitlesFragment 的完整代码

完成对所有以前的更改后`TitlesFragment`，完整的类应匹配此代码：

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }


        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3.对 PlayQuoteActivity 更改

没有一个最终的详细信息，来处理无足轻重的：`PlayQuoteActivity`时在设备处于横向模式不需要。 如果设备不在横向模式中`PlayQuoteActivity`应该是不可见。 更新`OnCreate`方法`PlayQuoteActivity`，以便它将关闭本身。 此代码是的最终版本`PlayQuoteActivity.OnCreate`:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

此修改添加一项检查的设备方向。 如果是，在横向模式中，则`PlayQuoteActivity`将关闭本身。

## <a name="4-run-the-application"></a>4.运行此应用程序

完成这些更改后，运行应用时，旋转设备以横向模式 （如有必要），，然后选择播放。 引号应显示在同一屏幕作为起到的列表：

[![在横向模式中 Android 手机上运行应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![在 Android 平板电脑上运行的应用程序](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
