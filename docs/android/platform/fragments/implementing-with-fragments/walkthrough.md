---
title: 演练
ms.prod: xamarin
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: cc5c05fce9b9c3dd974afe027cc7cbb60085c621
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough"></a>演练

在以下步骤中，基本应用程序的创建，其片段。 第一步是创建新的 Xamarin.Android 针对 Android 项目。

## <a name="1-create-a-project"></a>1.创建项目

创建一个名为的新的 Xamarin.Android 项目**FragmentSample**。 **最低 Android**版本应设置到 Android 3.1 或更高版本，如下面的图像中所示：

[![最低 Android 版本设置](walkthrough-images/00.png)](walkthrough-images/00.png#lightbox)


## <a name="2-create-the-mainactivity"></a>2.创建 MainActivity

接下来，我们需要创建`MainActivity`类。 这是应用程序的启动活动。 此活动将承载一个或两个片段，具体取决于屏幕大小。 `MainActivity` 将可执行此操作通过适用于设备的布局文件加载。

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```

> [!NOTE]
> `Note:` 片段子类必须具有公共的默认无参数构造函数。

## <a name="3-create-the-layout-files"></a>3.创建布局文件

两个不同的屏幕大小需要两个不同的布局文件。 因此，让我们创建一个新的文件夹，**资源/布局大型**，并创建新的布局，调用**activity_main.axml**。 我们还将重命名默认布局文件作为**Resources/Layout/activity_main.axml**。 这些更改后的布局文件夹应类似于下面的屏幕快照：

[![在 IDE 中的布局文件夹的屏幕截图](walkthrough-images/01.png)](walkthrough-images/01.png#lightbox)


所有设备将加载和使用中的布局文件**资源/布局**。
它是只是显示了非常简单布局`TitlesFragment`:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_width="fill_parent"
           android:layout_height="fill_parent" />
</LinearLayout>
```

对于具有大屏幕设备，Android 将负载中的布局文件**资源/布局大型**。 平板电脑的布局的内容如下所示：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_weight="1"
           android:layout_width="0px"
           android:layout_height="match_parent" />
 <FrameLayout android:id="@+id/details"
              android:layout_weight="1"
              android:layout_width="0px"
              android:layout_height="match_parent" />
</LinearLayout>
```

较大的屏幕的布局文件是略有不同。 不只是`TitlesFragment`显示在此布局文件，但`FrameLayout`添加段的旁边。 更大的屏幕上`DetailsFragment`以编程方式添加到`MainActivity`当用户选择 play。 更高版本上，我们将介绍更详细地如何做到这一点。

Android 3.2 引入了指定屏幕布局的新方法。 这些新限定符指定需要你的布局，而不是屏幕的大小的空间量。 如果此应用程序旨在仅在 Android 3.2 或更高版本运行，我们将创建**资源/布局 sw600dp**文件夹 (而不是文件夹**资源/布局大型**) 的布局文件**activity_main.axml**。 具有 600 密度无关的像素的最小屏幕宽度的所有设备将都加载此资源文件。 但是，为此应用程序的设置为目标 Android 3.1 或更高版本，它使用较旧的资源限定符。

## <a name="4-create-the-titlesfragment"></a>4.创建 TitlesFragment

`TitlesFragment` 将显示各种起到的标题，因此，让我们添加到项目的新片段调用`TitlesFragment`:

[![将新片段添加到 TitlesFragment 项目](walkthrough-images/02.png)](walkthrough-images/02.png#lightbox)

后`TitlesFragment`已添加，我们必须更改类，以便它继承自`Android.App.ListFragment`。 `ListFragment` 是专用的片段类型，包括列表功能。
`TitlesFragment` 此外将重写`OnActivityCreated`（另一种片段生命周期方法），并提供`Adapter`，`ListFragment`将用于填充列表：

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
   base.OnActivityCreated(savedInstanceState);
   var adapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemChecked, Shakespeare.Titles);
   ListAdapter = adapter;
   if (savedInstanceState != null)
   {
       _currentPlayId = savedInstanceState.GetInt("current_play_id", 0);
   }
   var detailsFrame = Activity.FindViewById<View>(Resource.Id.details);
   _isDualPane = detailsFrame != null && detailsFrame.Visibility == ViewStates.Visible;
   if (_isDualPane)
   {
       ListView.ChoiceMode = (int) ChoiceMode.Single;
       ShowDetails(_currentPlayId);
   }
}
```

如前文所述，我们的应用程序具有两个布局`MainActivity`。 中的代码`OnActivityCreated`检测是否存在`FrameLayout`并确定已加载的布局文件。 如果`FrameLayout`存在在布局中，则`_isDualPane`标志设置为`true`。 `_isDualPane`标志的其他位置使用在活动中中, 专门由`ShowDetails`方法。 `ShowDetails`方法将在下面更详细地介绍。

`TitlesFragment` 是一个列表，并必须响应用户选择列表中。 若要这样做，`TitlesFragment`将重写方法`OnListItemClick`。 内部`OnListItemClick`，新`DetailsFragment`将创建并显示在`FrameLayout`、 以编程方式。 内的相关代码`TitlesFragment`是：

```csharp
public override void OnListItemClick(ListView l, View v, int position, long id)
{
   ShowDetails(position);
}
private void ShowDetails(int playId)
{
   _currentPlayId = playId;
   if (_isDualPane)
   {
       // We can display everything in place with fragments.
       // Have the list highlight this item and show the data.
       ListView.SetItemChecked(playId, true);
       // Check what fragment is shown, replace if needed.
       var details = FragmentManager.FindFragmentById(Resource.Id.details) as DetailsFragment;
       if (details == null || details.ShownPlayId != playId)
       {
           // Make new fragment to show this selection.
           details = DetailsFragment.NewInstance(playId);
           // Execute a transaction, replacing any existing
           // fragment with this one inside the frame.
           var ft = FragmentManager.BeginTransaction();
           ft.Replace(Resource.Id.details, details);
           ft.SetTransition(FragmentTransaction.TransitFragmentFade);
           ft.Commit();
       }
   }
   else
   {
       // Otherwise we need to launch a new Activity to display
       // the dialog fragment with selected text.
       var intent = new Intent();
       intent.SetClass(Activity, typeof (DetailsActivity));
       intent.PutExtra("current_play_id", playId);
       StartActivity(intent);
   }
}
```

代码从该设备将确定如何格式化并显示从所选 play 引号。 对于平板电脑，`_isDualPane`标志将设置为`true`，并因此将旁边显示引号`TitlesFragment`。 如果所选的 play`id`尚未显示，然后新`DetailsFragment`时创建，并加载到`FrameLayout`活动上。 对于不具有大型显示其他设备&ndash;手机，例如&ndash;`isDualPane`将设置为`false`使新`DetailsActivity`将启动。


## <a name="5-create-the-detailsactivity"></a>5.创建 DetailsActivity

`DetailsActivity`显示`DetailsFragment`为较小的设备。 若要查看此，首先我们将新活动向项目添加名为`DetailsActivity`。 `DetailsActivity` 是一个非常简单的活动。 它将创建，然后将新`DetailsFragment`回放`id`，已发送：

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("current_play_id", 0);

       var details = DetailsFragment.NewInstance(index); // DetailsFragment.NewInstance is a factory method to create a Details Fragment
       var fragmentTransaction = FragmentManager.BeginTransaction();
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

请注意，没有布局文件加载为`DetailsActivity`。 相反，`DetailsFragment`将会加载到活动的根视图。 此根视图具有特殊 ID `Android.Resource.Id.Content`。 一个新`DetailFragment`已创建并随后添加到此根视图内`FragmentTransaction`，它由活动的创建`FragmentManager`。


## <a name="6-create-the-detailsfragment"></a>6.创建 DetailsFragment

现在，让我们添加到指定的应用程序的另一个片段`DetailsFragment`。 此代码段将显示从所选 play 引号。 下面的代码演示完整`DetailsFragment`:

```csharp
internal class DetailsFragment : Fragment
{
   public static DetailsFragment NewInstance(int playId)
   {
       var detailsFrag = new DetailsFragment {Arguments = new Bundle()};
       detailsFrag.Arguments.PutInt("current_play_id", playId);
       return detailsFrag;
   }
   public int ShownPlayId
   {
       get { return Arguments.GetInt("current_play_id", 0); }
   }
   public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
   {
       if (container == null)
       {
           // Currently in a layout without a container, so no reason to create our view.
           return null;
       }
       var scroller = new ScrollView(Activity);
       var text = new TextView(Activity);
       var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
       text.SetPadding(padding, padding, padding, padding);
       text.TextSize = 24;
       text.Text = Shakespeare.Dialogue[ShownPlayId];
       scroller.AddView(text);
       return scroller;
   }
}
```

为了使`DetailsFragment`若要正常运行，则必须在中选择 play 的索引`TitlesFragment`。 有多种方法来提供此值与`DetailsFragment`; 在此示例中，play`Id`放入包和包存储到的实例的自变量属性`DetailsFragment`。 属性`ShownPlayId`为方便而提供&ndash;的实例将使用`DetailsFragment`从捆绑中检索该值。

`OnCreateView` 片段需要绘制其用户界面，应返回时，将调用`Android.Views.View`对象。 在大多数情况下，这是`View`放大从现有的布局文件。 在上面的示例中，该片段将以编程方式生成将用于显示的视图。

祝贺你！ 你现在已创建的应用程序使用片段简化开发跨窗体因素。

在[下一节](supporting-pre-honeycomb.md)，我们将扩展此应用程序，以便它将在预 Android 3.0 设备上运行。

