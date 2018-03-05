---
title: "活动生命周期"
description: "活动是的 Android 应用程序的基本构建基块，并且可以存在于多个不同状态。 活动生命周期开头实例化和析构，结尾，并且在此期间包括许多的状态。 当活动更改状态时，会调用相应的生命周期事件方法，以便通知即将发生的状态更改的活动，并使其能够执行的代码以适应所做的更改。 本文分析的活动的生命周期，并解释了负责该活动具有这些状态更改的功能良好的可靠应用程序的一部分的每一阶段。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: ccd55d4d7f1aea55110e109bed1fbd4ebc90b93f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="activity-lifecycle"></a>活动生命周期

_活动是的 Android 应用程序的基本构建基块，并且可以存在于多个不同状态。活动生命周期开头实例化和析构，结尾，并且在此期间包括许多的状态。当活动更改状态时，会调用相应的生命周期事件方法，以便通知即将发生的状态更改的活动，并使其能够执行的代码以适应所做的更改。本文分析的活动的生命周期，并解释了负责该活动具有这些状态更改的功能良好的可靠应用程序的一部分的每一阶段。_

## <a name="activity-lifecycle-overview"></a>活动生命周期概述

活动是一个特定于 Android 的异常编程概念。 在传统的应用程序开发通常会有静态的 main 方法，执行，以启动应用程序。 与 Android，但是的事项为不同的;Android 应用程序可以通过应用程序中的任何已注册活动启动。 在实践中，大多数应用程序将只能具有指定为应用程序入口点的特定活动。 但是，如果应用程序崩溃，或终止操作系统，操作系统可尝试重新启动应用程序在打开的最后一个活动或以前的活动堆栈中的其他任何位置。
此外，操作系统可能暂停等活动时它们并不处于活动状态，并且回收它们，如果它是内存不足。 必须进行仔细考虑将允许应用程序以正确还原其状态的事件中活动重新启动时，尤其是在活动依赖于以前的活动中的数据。

活动生命周期被实现为整个活动生命周期方法操作系统调用的集合。 这些方法允许开发人员实现的功能所必需满足他们的应用程序的状态和资源管理要求。

它对于而言非常重要应用程序开发人员分析每个活动的要求，以确定需要实现由活动的生命周期公开的方法。 如果不这样做可能导致应用程序不稳定、 崩溃、 资源膨胀和甚至基础的操作系统不稳定。

本章检查活动的生命周期中的详细信息，包括：

-  活动状态
-  生命周期方法
-  保留应用程序状态


本部分还包括[演练](~/android/app-fundamentals/activity-lifecycle/saving-state.md)实用示例提供有关如何有效地活动生命周期中保存状态。 结束时的这一章中，你应会活动生命周期和如何支持 Android 应用程序中的了解。

## <a name="activity-lifecycle"></a>活动生命周期

Android 活动生命周期包括在活动类公开的方法，以提供一个资源管理框架，开发人员的集合。 此框架允许开发人员可以满足应用程序中的每个活动的唯一的状态管理要求和正确处理资源管理。

### <a name="activity-states"></a>活动状态

Android OS 仲裁基于其状态的活动。 这有助于识别不再使用，允许操作系统回收内存和资源的活动的 Android。 下图说明了活动可能在其生存期内要经历的状态：

[ ![活动状态图](images/image1-sml.png)](images/image1.png)

这些状态可分为 4 个主要组，如下所示：

1.  *活动或正在运行*&ndash;活动是被视为处于活动状态或运行它们是否在前台，也称为活动堆栈的顶部。 这被视为在 Android 中，最高优先级的活动，这种情况下将仅在终止在极端情况下操作系统，如如同活动尝试使用的内存量大于是设备上的可用这可能会导致 UI 以变得不响应。

1.  *暂停*&ndash;时设备进入睡眠状态，或活动是仍可见但部分隐藏由新的、 非全尺寸或透明的活动，该活动才被视为已暂停。 暂停的活动是仍处于活动状态，也就是说，它们保持所有的状态和成员信息，并且保持附加到的窗口管理器。 这被视为第二个如果终止此活动将满足保持稳定且高度可响应的活动/正在运行活动所需的资源要求最高优先级的活动在 Android 和，因此，将仅由操作系统在终止。

1.  *停止/Backgrounded* &ndash;完全遮盖由另一个活动的活动被视为已停止或在后台。
    停止的活动仍尝试保留其状态和成员信息，只要可行的但已停止活动被视为三种状态的最低优先级，并且在这种情况下，操作系统将终止此状态，首先以满足资源中的活动更高的优先级活动的要求。

1.  *重新启动*&ndash;所以有可能是从任意位置的活动暂停为停止在要从内存中移除的 Android 的生命周期。 如果用户向后定位到活动必须重新启动它，将还原到以前保存的状态，然后向用户显示。


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>活动重新创建以响应配置更改

若要使更复杂的事情，Android，则会引发一个详细扳手调用配置更改的组合。 配置更改会快速活动析构/重新 creation 周期时发生的活动的配置更改，例如设备时[rotated](~/android/app-fundamentals/handling-rotation.md) （和该活动需要在横向或纵向中获取重新生成模式下），当显示键盘 （和活动提供有机会调整自身大小），或当设备处于停靠、 等。

配置更改仍可使相同的活动状态更改将在停止并重新启动活动过程中发生。 但是，为了确保应用程序中的外观保持响应状态，并且也在配置更改过程中执行，非常重要，它们将尽可能快地处理。 因此，Android 有一个特定的 API，可用于在配置更改过程中保留状态。
我们将介绍这更高版本中[管理生命整个状态周期](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle)部分。

### <a name="activity-lifecycle-methods"></a>活动生命周期方法

Android SDK，并通过扩展的 Xamarin.Android framework 所提供的用于管理应用程序内的活动状态的一个功能强大的模型。 当活动的状态将更改时，该活动将通知操作系统，在活动上调用特定方法。 下图说明了与活动生命周期关系中的这些方法：

[ ![活动生命周期流程图](images/image2-sml.png)](images/image2.png)

作为开发人员，你可以通过重写这些方法在活动中的处理状态更改。 请务必注意，但是，所有的生命周期方法在 UI 线程上调用，并且将阻止执行的 UI 工作，如隐藏当前的活动中，下一步部分操作系统显示一个新活动，等等。在这种情况下，这些方法中的代码应尽可能简单使感到很好地执行应用程序。 应在后台线程上执行任何长时间运行的任务。

让我们检查这些生命周期方法，其使用的每个：

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)是第一种方法来创建活动时调用。
`OnCreate` 始终重写以执行任何启动初始化，则可能需要由某个活动如：

-  创建视图
-  初始化变量
-  静态数据绑定到列表


`OnCreate` 采用[捆绑](https://developer.xamarin.com/api/type/Android.OS.Bundle/)参数，这是用于存储和如果捆绑不为 null 的活动之间传递状态信息和对象的字典，这表示该活动正在重新启动，并且它应还原从其状态在前一个实例。 下面的代码演示如何从捆绑中检索值：

```csharp
protected override void OnCreate(Bundle bundle)
{
   base.OnCreate(bundle);

   string intentString;
   bool intentBool;

   if (bundle != null)
   {
      intentString = bundle.GetString("myString");
      intentBool = bundle.GetBoolean("myBool");
   }

   // Set our view from the "main" layout resource
   SetContentView(Resource.Layout.Main);
}
```

一次`OnCreate`已完成，则将调用 Android `OnStart`。

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/)始终在调用后系统`OnCreate`完成。 活动可以重写此方法，如果他们需要活动变得可见如刷新的活动中的视图的当前值之前执行特定任务的任何权利。 Android 将调用`OnResume`紧跟此方法。

#### <a name="onresume"></a>OnResume

系统调用[OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/)活动时准备好开始与用户交互。
活动应重写此方法以执行如下任务：

-  掌握帧速率 （游戏构建中常见的任务）
-  启动动画
-  侦听 GPS 更新
-  显示任何相关的警报或对话框
-  将外部事件处理程序


例如，下面的代码段演示如何初始化照相机：

```csharp
public void OnResume()
{
    base.OnResume(); // Always call the superclass first.

    if (_camera==null)
    {
        // Do camera initializations here
    }
}
```

`OnResume` 为重要，因为这是任何操作中完成`OnPause`应为在未完成`OnResume`，因为它是可保证执行之后的唯一生命周期方法`OnPause`时会活动回生命。

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/)将要放置活动到背景或活动变得遮盖系统时调用。 如果需要则活动应重写此方法：

-   未保存的更改提交到持久性数据

-   销毁或清理消耗资源的其他对象

-   向下帧速率和暂停的动画的负载增加

-   取消注册通知处理程序 （即，绑定到服务） 或外部的事件处理程序。 这必须为了防止活动内存泄漏。

-   同样，如果活动具有显示任何对话框或警报，它们必须先清除与`.Dismiss()`方法。

例如，下面的代码段将释放相机，如活动不能进行使用它在暂停时：

```csharp
public void OnPause()
{
    base.OnPause(); // Always call the superclass first

    // Release the camera as other activities might need it
    if (_camera != null)
    {
        _camera.Release();
        _camera = null;
    }
}
```

有两种可能的生命周期方法将调用后`OnPause`:

1.  `OnResume` 如果活动是要返回前台，将被调用。
1.  `OnStop` 如果将活动放入后台将调用。


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/)不再对用户可见的活动时调用。 发生以下某种情况时，将发生这种情况：

-  新的活动启动，并覆盖此活动。
-  是要将现有的活动置于前台。
-  活动正被损坏。


`OnStop` 可能不会始终会调用在内存不足的情况下，例如当 Android 缺少资源，以及无法正确后台活动。 为此，最好是不依赖于`OnStop`销毁准备活动时调用。 下一步后它将不能调用的生命周期方法`OnDestroy`活动将消失，如果或`OnRestart`如果活动返回与用户进行交互。

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/)是它已销毁，并从内存中完全删除之前一个活动的实例, 调用的最后一个方法。 在极端情况下 Android 可能终止活动，这将导致托管的应用程序进程`OnDestroy`不调用。 大多数活动将不会实现此方法，因为大多数清理并完成了关闭`OnPause`和`OnStop`方法。 `OnDestroy`方法通常被重写以进行清除长时间运行的资源可能泄漏资源。 此示例可能是在启动的后台线程`OnCreate`。

将调用该活动已销毁之后进行任何生命周期方法。

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/)后已停止你的活动，在它重新启动之前调用。 当用户在应用程序中按在活动上的主页按钮是一个很好的示例。 当发生这种情况`OnPause`然后`OnStop`方法调用，和活动移动到后台，但不是会被销毁。 如果用户是使用任务管理器或类似的应用程序还原应用程序，Android 将调用`OnRestart`活动的方法。

有何种逻辑应在中实现通用准则`OnRestart`。 这是因为`OnStart`始终调用而不考虑是否正在创建活动或正在重新启动，因此应在初始化所需的活动的任何资源`OnStart`，而不是`OnRestart`。

下一个生命周期方法之后调用`OnRestart`将`OnStart`。

### <a name="back-vs-home"></a>回 vs。主页

许多 Android 设备没有两个不同的按钮:"上一步"按钮和"主页"按钮。 可以在为 Android 4.0.3 下面的屏幕截图中看到举例说明：

[ ![上一页和主页按钮](images/image4-sml.png)](images/image4.png)

即使它们看起来具有相同的效果将应用程序放在后台，没有两个按钮，之间略有不同。 当用户单击后退按钮时，它们在告诉 Android 与活动完成。 Android 将销毁活动。 与此相反，当用户单击主页按钮活动只是放入后台&ndash;Android 将终止活动。

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>管理整个生命周期的状态

停止活动或将其销毁时系统提供了一个机会保存更高版本 rehydration 的活动的状态。
这已保存状态被称为实例状态。 Android 提供活动生命周期中存储实例状态的三个选项：

1. 存储中的基元值`Dictionary`称为[捆绑](https://developer.xamarin.com/api/type/Android.OS.Bundle/)Android 将用于保存状态。

1. 创建自定义类，将保存复杂值，例如位图。 Android 将使用此自定义类来保存状态。

1. 也可以避开配置更改生命周期和假设已完成负责维护在活动的状态。


本指南介绍前两个选项。

 <a name="Bundle_State" />


### <a name="bundle-state"></a>捆绑包状态

保存实例状态的主选项是使用键/值字典对象称为[捆绑](https://developer.xamarin.com/api/type/Android.OS.Bundle/)。
回想一下，当创建一个活动`OnCreate`方法作为参数传递捆绑时，此包可用于还原的实例状态。 不建议用于捆绑更复杂或的数据不会快速轻松地序列化以键/值对 （如位图）;相反，它应该用于简单的值，如字符串。

活动提供了可帮助保存和检索捆绑中的实例状态的方法：

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash;这在销毁活动时调用 android。 活动可以实现此方法，如果它们需要保存任何键/值状态项。

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash;后，这会调用`OnCreate`方法完成之后，并提供用于在初始化完成后恢复其状态的活动的另一个有机会。

下图说明了如何使用这些方法：

[ ![捆绑包状态流程图](images/image3-sml.png)](images/image3.png)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/)将调用，如正在停止该活动。 它将接收活动可以存储在其状态的捆绑包参数。 当设备遇到配置更改时，可以使用活动`Bundle`传入的要通过重写保留的活动状态的对象`OnSaveInstanceState`。 例如，考虑以下代码：

```csharp
int c;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  this.SetContentView (Resource.Layout.SimpleStateView);

  var output = this.FindViewById<TextView> (Resource.Id.outputText);

  if (bundle != null) {
    c = bundle.GetInt ("counter", -1);
  } else {
    c = -1;
  }

  output.Text = c.ToString ();

  var incrementCounter = this.FindViewById<Button> (Resource.Id.incrementCounter);

  incrementCounter.Click += (s,e) => {
    output.Text = (++c).ToString();
  };
}
```

上面的代码递增整数名为`c`名为的按钮时`incrementCounter`显示中的结果的单击`TextView`名为`output`。 发生配置更改-例如，当将设备旋转-上面的代码都将丢失的值`c`因为`bundle`将`null`下, 图中所示：

[ ![显示不会显示以前的值](images/07-sml.png)](images/07.png)

若要保留的值`c`在此示例中，活动可以重写`OnSaveInstanceState`，保存为捆绑中的值，如下所示：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

现在当设备旋转至新的方向的整数部分为捆绑中保存和检索的行：

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> **注意：**很重要始终调用基实现的`OnSaveInstanceState`以便查看层次结构的状态也可能保存。


<a name="View_State" />

##### <a name="view-state"></a>视图状态

重写`OnSaveInstanceState`是用于在活动中保存暂时性数据，跨方向更改，如上面的示例中的计数器适当机制。 但是的默认实现`OnSaveInstanceState`将会负责处理的每个视图中，UI 中保存暂时性数据处理程序，但前提是每个视图都有分配的 ID。 例如，假设应用程序具有`EditText`，如下所示在 XML 中定义的元素：

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

由于`EditText`控件具有`id`分配，当用户输入一些数据，并旋转设备时，数据仍显示，如下所示：

[ ![在横向模式中保留数据](images/08-sml.png)](images/08.png)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/)将之后调用`OnStart`。 它使活动能够还原以前已保存到捆绑在上一个过程的任何状态`OnSaveInstanceState`。 这是提供给同一捆绑`OnCreate`，但是。

下面的代码演示如何在还原状态`OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

此方法存在是为了提供了解决一些灵活性时应还原状态。 有时很等待，直到还原实例状态之前完成所有初始化更为合适。 此外，从实例状态还原某些值可能仅希望将现有的活动的一个子类。 在许多情况下，它不需要重写`OnRestoreInstanceState`，因为大多数活动可以还原使用提供给包的状态`OnCreate`。

有关的保存状态使用示例`Bundle`，请参阅[演练-保存活动状态](saving-state.md)。


<a name="Bundle_Limitations" />

#### <a name="bundle-limitations"></a>捆绑限制

尽管`OnSaveInstanceState`更加轻松地保存暂时性数据，它具有一些限制：

-   它不是在所有情况下调用。 例如，按**主页**或**回**退出活动不会导致`OnSaveInstanceState`调用。

-   捆绑包传递到`OnSaveInstanceState`不是对于大型对象，如图像。 对于大型对象，保存该对象从[OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/)非常可取，如下文所述。

-   通过使用捆绑包来保存的数据进行序列化，这可能会导致延迟。

捆绑包状态是适用于不使用的内存多的简单数据，而*非配置实例数据*用于更复杂的数据或将占用大量资源，若要检索的数据，例如，由于 web 服务调用或一项复杂数据库查询。 根据需要在对象中获取保存非配置实例数据。 下一节介绍`OnRetainNonConfigurationInstance`作为保留通过配置更改的更复杂数据类型的一种方法。

<a name="Persisting_Complex_Data" />

### <a name="persisting-complex-data"></a>将复杂数据永久保存

除了捆绑中的持久保存数据，Android 还支持将数据保存通过重写[OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/)并返回的实例`Java.Lang.Object`，其中包含要保持的数据。 有两个主要好处使用`OnRetainNonConfigurationInstance`保存状态：

-   从返回的对象`OnRetainNonConfigurationInstance`性能也具有更大、 更复杂的数据类型，因为内存保留此对象。

-   `OnRetainNonConfigurationInstance`方法是按需、 被调用，而且仅在需要时才。 这是比使用手动缓存更经济。

使用`OnRetainNonConfigurationInstance`是适用于需要高开销检索多个时间的数据的情况，如 web 服务调用。 例如，考虑下面的代码搜索 Twitter:

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);
    SearchTwitter ("xamarin");
  }

  public void SearchTwitter (string text)
  {
    string searchUrl = String.Format("http://search.twitter.com/search.json?" + "q={0}&rpp=10&include_entities=false&" + "result_type=mixed", text);

    var httpReq = (HttpWebRequest)HttpWebRequest.Create (new Uri (searchUrl));
    httpReq.BeginGetResponse (new AsyncCallback (ResponseCallback), httpReq);
  }

  void ResponseCallback (IAsyncResult ar)
  {
    var httpReq = (HttpWebRequest)ar.AsyncState;

    using (var httpRes = (HttpWebResponse)httpReq.EndGetResponse (ar)) {
      ParseResults (httpRes);
    }
  }

  void ParseResults (HttpWebResponse httpRes)
  {
    var s = httpRes.GetResponseStream ();
    var j = (JsonObject)JsonObject.Load (s);

    var results = (from result in (JsonArray)j ["results"] let jResult = result as JsonObject select jResult ["text"].ToString ()).ToArray ();

    RunOnUiThread (() => {
      PopulateTweetList (results);
    });
  }

  void PopulateTweetList (string[] results)
  {
    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
  }
}
```

此代码从格式化为 JSON web 中检索结果，分析它们，然后在列表中，显示的结果，如下面的屏幕截图中所示：

[ ![在屏幕上显示的结果](images/06-sml.png)](images/06.png)

配置发生更改时-例如，当设备旋转的代码将重复该过程。 若要重用最初检索的结果并不会导致不必要的冗余网络调用，我们可以使用`OnRetainNonconfigurationInstance`来保存结果，如下所示：

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  TweetListWrapper _savedInstance;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    var tweetsWrapper = LastNonConfigurationInstance as TweetListWrapper;

    if (tweetsWrapper != null) {
      PopulateTweetList (tweetsWrapper.Tweets);
    } else {
      SearchTwitter ("xamarin");
    }

    public override Java.Lang.Object OnRetainNonConfigurationInstance ()
    {
      base.OnRetainNonConfigurationInstance ();
      return _savedInstance;
    }

    ...

    void PopulateTweetList (string[] results)
    {
      ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
      _savedInstance = new TweetListWrapper{Tweets=results};
    }
}
```

现在，当将设备旋转，原始搜索结果将检索从`LastNonConfiguartionInstance`属性。 在此示例中，结果组成`string[]`包含推文。 由于`OnRetainNonConfigurationInstance`要求`Java.Lang.Object`返回，`string[]`类中，该子类包装`Java.Lang.Object`，如下所示：

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

例如，尝试使用`TextView`从返回的对象作为`OnRetainNonConfigurationInstance`将泄漏活动，如下面的代码所示：

```csharp
TextView _textView;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  var tv = LastNonConfigurationInstance as TextViewWrapper;

  if(tv != null) {
    _textView = tv;
    var parent = _textView.Parent as FrameLayout;
    parent.RemoveView(_textView);
  } else {
    _textView = new TextView (this);
    _textView.Text = "This will leak.";
  }

  SetContentView (_textView);
}

public override Java.Lang.Object OnRetainNonConfigurationInstance ()
{
  base.OnRetainNonConfigurationInstance ();
  return _textView;
}
```

在本部分中，我们已了解如何保留与简单状态数据`Bundle`，而且将保留更复杂的数据类型与`OnRetainNonConfigurationInstance`。

## <a name="summary"></a>摘要

Android 活动生命周期提供功能强大的框架，用于应用程序内的活动的状态管理，但它可能会很棘手，若要了解和实施。 本章介绍了活动可能要通过在其生存期内，以及与这些状态关联的生命周期方法过程的不同状态。 接下来，指南提供有关哪种逻辑应在每种方法中执行。


## <a name="related-links"></a>相关链接

- [处理旋转](~/android/app-fundamentals/handling-rotation.md)
- [Android 活动](https://developer.xamarin.com/api/type/Android.App.Activity/)
