---
title: "KitKat 功能"
description: "Android 4.4 (KitKat) 装具有大量用户和开发人员的功能。 本指南重点介绍了这些功能的几个，并提供代码示例和实现详细信息以帮助你充分利用 KitKat 做出。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: ae6b89e48005ca028db5d13f1a55f237888ae08b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="kitkat-features"></a>KitKat 功能

_Android 4.4 (KitKat) 装具有大量用户和开发人员的功能。本指南重点介绍了这些功能的几个，并提供代码示例和实现详细信息以帮助你充分利用 KitKat 做出。_

## <a name="overview"></a>概述

Android 4.4 (API 级别 19)，也称为"KitKat"，在后期 2013年中发布。 KitKat 提供了各种各样的新功能和改进，包括：

-  [用户体验](#user_experience)&ndash;转换 framework、 半透明的状态和导航栏，与全屏幕沉浸式模式的简单动画帮助创建用户更好的体验。

-  [用户内容](#user_content)&ndash;用户文件管理简化与存储访问 framework; 打印图片、 网站和其他内容是改进打印 Api，更容易。

-  [硬件](#hardware)&ndash;变为 NFC 卡与 NFC 基于主机的卡仿真的任何应用程序; 运行具有低功耗传感器`SensorManager`。

-  [开发人员工具](#developer_tools) &ndash; Android 调试桥接客户端，可用操作中的段视频应用程序作为 Android SDK 的一部分。


本指南提供了为 Xamarin.Android 开发人员迁移现有 Xamarin.Android 应用程序到 KitKat，以及 KitKat 的高级概述的指南。

## <a name="requirements"></a>惠?

若要开发使用 KitKat 的 Xamarin.Android 应用程序，你需要*Xamarin.Android 4.11.0*或更高版本和 Android 4.4 (API 级别 19) 安装通过 Android SDK 管理器中，如下面的屏幕截图所示：

[![在 Android SDK 管理器中选择 Android 4.4](kitkat-images/api19.png)](kitkat-images/api19.png)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>将你的应用程序迁移到 KitKat

本部分提供一些第一个响应项，以帮助将现有的应用程序转换到 Android 4.4。

### <a name="check-system-version"></a>检查系统版本

如果应用程序需要与较旧版本的 Android 兼容，请务必将任何 KitKat 特有的代码包装在系统版本检查，，如下面的代码示例所示：

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>警报批处理

Android 使用警报服务在指定的时间唤醒在后台应用程序。 KitKat 考虑这一层楼通过批处理保留电源的警报。 这意味着，而不是在不精确的时间唤醒每个应用程序，KitKat 优先组注册唤醒在相同的时间间隔内，并在同一时间唤醒它们的多个应用程序。
若要指示 Android 唤醒指定的时间间隔内的应用程序，调用`SetWindow`上[ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/)，并传入所需的最低和最大时间，以毫秒为单位，可以经过之前唤醒应用程序，以及要执行的操作在唤醒。
下面的代码提供的应用程序需要唤醒半小时和从该窗口未设置的时间一小时之间的示例：

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

若要继续在不精确的时间唤醒应用，使用`SetExact`、 传入应用程序应唤醒的确切时间并要执行的操作：

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat 不再允许你设置精确的重复警报。 应用程序使用[ `SetRepeating` ](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/)并且需要确切的警报，若要将现在需要手动触发每个警报。

### <a name="external-storage"></a>外部存储

外部存储现在已划分为两种类型的唯一的应用程序和由多个应用程序共享的数据存储中。 读取和写入外部存储中的应用程序的特定位置需要任何特殊权限。 现在与共享存储上的数据进行交互需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`权限。 可在这种分类的两种类型：

-  如果你通过在调用某个方法获取文件或目录路径`Context`-例如， [ `GetExternalFilesDir` ](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/)或 [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - 你的应用需要任何额外权限。

-  如果你通过访问属性或调用的方法来获取文件或目录路径`Environment`，如[ `GetExternalStorageDirectory` ](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/)或[ `GetExternalStoragePublicDirectory` ](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/) ，你的应用需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`权限。

> [!NOTE]
> **注意：** `WRITE_EXTERNAL_STORAGE`意味着`READ_EXTERNAL_STORAGE`权限，因此你应只需要将一个权限设置。

### <a name="sms-consolidation"></a>SMS 合并

KitKat 简化了对用户消息通过聚合中用户选定的一个默认应用程序的所有 SMS 内容。 开发人员负责使应用程序的默认消息传送应用程序，可选择并运行正常在代码中并在现实生活中如果未选择应用程序。 有关转换到 KitKat SMS 应用程序的详细信息，请参阅[前你 SMS 应用的准备工作 KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html)将来自 Google 的指南。

### <a name="webview-apps"></a>WebView 应用

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) KitKat 而陷入功能改进。 最大更改添加用于加载到的内容的安全`WebView`。 针对较旧的 API 版本的大多数应用程序应按预期方式工作的而测试应用程序使用`WebView`强烈建议类。 有关受影响的 WebView Api 的详细信息，请参阅 Android[迁移到在 Android 4.4 WebView](http://developer.android.com/guide/webapps/migrating.html)文档。

<a name="user_experience" />

## <a name="user-experience"></a>用户体验

KitKat 附带了几个新的 Api 来增强用户体验，包括新的转换框架，用于处理属性动画和主题的半透明 UI 选项。 下面介绍了这些更改。

### <a name="transition-framework"></a>转换 Framework

转换框架，可以动画更加轻松地实现。 KitKat 可以执行简单的属性动画的只是一个代码行，或自定义转换使用*场景*。

#### <a name="simple-property-animation"></a>简单属性动画

新的 Android 转换库简化了属性动画背后的代码。 该框架允许你执行具有最少的代码的简单动画。 例如，下面的代码示例使用[ `TransitionManager.BeginDelayedTransition` ](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/)动画显示和隐藏`TextView`:

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

上面的示例使用转换框架来创建自动、 默认不断变化的属性值之间的转换。 由于动画处理的一行代码中，因此你可以轻松地这与较旧版本的 Android 兼容通过包装来`BeginDelayedTransition`调用在系统版本检查。 请参阅[迁移你的应用程序到 KitKat](#Migrating_Your_App_to_KitKat)部分的详细信息。

下面的屏幕截图显示动画之前应用：

[![在动画开始前的应用屏幕快照](kitkat-images/trans-before.png)](kitkat-images/trans-before.png)

下面的屏幕截图显示的动画结束后的应用：

[![动画完成后，应用屏幕快照](kitkat-images/trans-after.png)](kitkat-images/trans-after.png)

你可以获取更好地控制与后台，在下一节中介绍的转换。

#### <a name="android-scenes"></a>Android 场景

[场景](https://developer.xamarin.com/api/type/Android.Transitions.Scene/)作为转换 framework 为开发人员提供更好地控制动画的一部分引入。 场景在 UI 中创建的动态区域： 指定容器和多个版本或"后台"，在容器中，XML 内容而 Android 不进行动画处理的场景之间的转换的工作的其余部分。 Android 场景让你在开发端上生成具有最小工作的复杂动画。

外壳动态内容的静态用户界面元素称为*容器*或*场景基*。 下面的示例使用 Android 设计器创建`RelativeLayout`调用`container`:

[![使用 Android 设计器创建 RelativeLayout 容器](kitkat-images/container.png)](kitkat-images/container.png)

示例布局还定义了一个称为按钮`sceneButton`下面`container`。 此按钮将触发转换。

在容器内的动态内容要求两个新的 Android 布局。 这些布局指定只将代码*内*容器。
下面的示例代码定义调用布局*Scene1*分别包含两个文本字段读取"工具包"和"Kat"和第二个布局调用*Scene2*包含相同的文本字段反转。 XML 是，如下所示：

 **Scene1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

上面的示例将`merge`来使视图代码较短，并简化视图层次结构。 你可以阅读更多有关`merge`布局[此处](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html)。

通过调用创建场景[ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/)，并在容器对象中，资源 ID 的场景的布局文件和当前传递`Context`，如下面的代码示例所示：

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

单击该按钮翻转之间两个后台，Android 使用默认转换值进行动画处理：

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

下面的屏幕截图演示之前动画场景：

[![应用的动画开始之前的屏幕快照](kitkat-images/trans-after.png)](kitkat-images/trans-after.png)

下面的屏幕截图演示后动画场景：

[![在动画完成后应用的屏幕快照](kitkat-images/scene.png)](kitkat-images/scene.png)


> [!NOTE]
> **注意：**没有[已知的 bug](https://code.google.com/p/android/issues/detail?id=62450)在 Android 转换会导致场景的库创建使用`GetSceneForLayout`时将用户转到活动的第二个时间中断。 Java 解决方法进行了介绍[此处](http://www.doubleencore.com/2013/11/new-transitions-framework/)。


##### <a name="custom-transitions-in-scenes"></a>场景中的自定义转换

可以在 xml 资源文件中定义自定义转换`transition`目录下`Resources`，如下面的屏幕截图所示：

[![资源/转换目录下的 transition.xml 文件的位置](kitkat-images/resources.png)](kitkat-images/resources.png)

下面的代码示例定义的 5 秒内将动画处理，并使用的转换[超过插值程序](http://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

在活动中创建转换使用[TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/)，如下面的代码所示：

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

新转换然后添加到`Go`开始动画的调用：

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>半透明 UI

KitKat 使你更好地控制主题使用可选 transclucent 状态和导航条对应用程序。 你可以更改用于定义你的 Android 主题相同的 XML 文件中的系统 UI 元素的透明度。 KitKat 引入了以下属性：

-  `windowTranslucentStatus` -当设置为 true，使顶部的状态栏半透明。

-  `windowTranslucentNavigation` -当设置为 true，可底部导航栏半透明。

-  `fitsSystemWindows` -如果设置的顶部或底部栏 transcluent 到默认情况下移动下的透明的 UI 元素的内容。 此属性设置为`true`是一种简单的方法，以防止内容与半透明的系统 UI 元素重叠。


下面的代码定义以半透明的状态和导航条主题：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

下面的屏幕截图显示了具有半透明的状态和导航栏上方主题：

[![使用半透明的状态和导航条应用程序的示例屏幕快照](kitkat-images/theme.png)](kitkat-images/theme.png)

<a name="user_content" />

## <a name="user-content"></a>用户内容

### <a name="storage-access-framework"></a>存储访问框架

存储访问 Framework (SAF) 是用户能够进行交互，其中包含诸如图像、 视频和文档的存储内容的新方法。 而不是向用户提供一个对话框以选择应用程序来处理内容，KitKat 打开新的用户界面，用户可访问其数据在一个聚合的位置。 后已被选内容，用户将返回到的应用程序请求内容，并将继续正常进行的应用程序体验。

此更改需要开发人员一端的两个操作： 首先，需要从提供程序的内容的应用需要更新为 reqesting 内容的新方法。 第二个，应用程序将数据写入`ContentProvider`需要修改为使用新的框架。 这两种方案依赖于新[ `DocumentsProvider` ](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/) API。

#### <a name="documentsprovider"></a>DocumentsProvider

在 KitKat，与交互`ContentProviders`进行抽象与`DocumentsProvider`类。 这意味着，SAF 不介意其中的数据是以物理方式，只要它是可通过访问`DocumentsProvider`API。 本地提供程序，云服务和外部存储设备所有使用相同接口，并且将被视为相同的方式，用户和开发人员提供一个位置与用户的内容进行交互。

本部分介绍如何加载和保存与存储访问框架的内容。

#### <a name="request-content-from-a-provider"></a>从提供程序的请求内容

我们可以告诉 KitKat 我们想要选取内容使用 SAF UI`ActionOpenDocument`意向，表示我们想要连接到与该设备所有内容提供程序。 你可以添加一些筛选到此方法通过指定`CategoryOpenable`，这意味着只有可以打开 （即可访问性，可用内容） 的内容将返回。 KitKat 还允许筛选内容`MimeType`。 例如，以下筛选器通过指定映像的映像结果代码`MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

调用`StartActivityForResult`启动 SAF UI 中，用户可以浏览以选择映像：

[![浏览到图像使用的存储访问框架的应用程序的示例屏幕快照](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png)

用户已选择一个映像之后,`OnActivityResult`返回`Android.Net.Uri`的所选的文件。 下面的代码示例显示用户的映像选择：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>向提供程序写入内容

除了从 SAF UI 加载内容，KitKat 还使你可以将内容保存到任何`ContentProvider`实现`DocumentProvider`API。 保存内容使用`Intent`与`ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

上面的代码示例加载 SAF UI，让用户更改的文件名称并选择一个目录来容纳新的文件：

[![在下载目录的文件名称将改为 NewDoc 的用户的屏幕截图](kitkat-images/saf-save.png)](kitkat-images/saf-save.png)

当用户按**保存**，`OnActivityResult`传递`Android.Net.Uri`的新创建的文件，可通过访问`data.Data`。 Uri 可以使用数据进行流处理中，为新文件中：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

请注意， [ `ContentResolver.OpenOutputStream(Android.Net.Uri)` ](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri))返回`System.IO.Stream`，因此可以采用.NET 编写整个流式处理过程。

有关加载的详细信息，创建和编辑内容与存储访问 Framework，请参阅[Android 文档存储访问 Framework](http://developer.android.com/guide/topics/providers/document-provider.html)。

### <a name="printing"></a>打印

打印内容中 KitKat 简化通过引入[打印服务](https://developer.xamarin.com/api/namespace/Android.PrintServices/)和`PrintManager`。 KitKat 也是第一个的 API 版本，能够充分利用[Google 云打印服务 Api](https://developers.google.com/cloud-print/)使用[Google 云打印应用程序](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint)。
自动附带 KitKat 的大多数设备下载 Google 云打印应用和[HP 打印服务插件](https://play.google.com/store/apps/details?id=com.hp.android.printservice)当它们首次连接到 WiFi。 用户可以通过导航到检查他或她的设备的打印设置**设置 > 系统 > 打印**:

[![示例的打印设置屏幕的屏幕截图](kitkat-images/printing.png)](kitkat-images/printing.png)


> [!NOTE]
> **注意：** Android 打印 Api 设置要使用 Google 云打印默认情况下，虽然仍让开发人员准备打印内容使用新的 Api，并将其发送到其他应用程序可以处理打印。



#### <a name="printing-html-content"></a>打印 HTML 内容

自动创建 KitKat [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) web 视图与`WebView.CreatePrintDocumentAdapter`。 打印 web 内容是一项协调的工作之间[ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) ，等待要加载的 HTML 内容，并允许知道在选项菜单中提供打印选项的活动和 Actvity，它会一直等待到的用户选择打印选项和调用`Print`上`PrintManager`。 本部分介绍打印屏幕上所需的基本设置 HTML 内容。

请注意，加载和打印 web 内容需要 Internet 权限：

[![在应用程序选项中设置 Internet 权限](kitkat-images/internet.png)](kitkat-images/internet.png)

##### <a name="print-menu-item"></a>打印菜单项

打印选项通常会在中显示活动的[选项菜单](http://developer.android.com/guide/topics/ui/menus.html#options-menu)。
选项菜单允许用户在活动上执行操作。 它是在该屏幕的右上角中，如下所示：

[![在屏幕的右上角中的打印菜单项全都示例屏幕快照](kitkat-images/menu.png)](kitkat-images/menu.png)


可以在定义其他菜单项*菜单*目录下*资源*。 下面的代码定义示例菜单项调用[打印](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

与在活动中的选项菜单的交互是通过`OnCreateOptionsMenu`和`OnOptionsItemSelected`方法。
`OnCreateOptionsMenu` 中，可以添加新的菜单项，如打印选项中，从*菜单*资源目录。
`OnOptionsItemSelected` 侦听用户从菜单中，选择打印选项，并开始打印：

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

上面的代码还定义了名为`dataLoaded`以跟踪的 HTML 内容的状态。 `WebViewClient`将设置此变量为 true 时加载的所有内容，因此活动知道将打印菜单项添加到选项菜单。

##### <a name="webviewclient"></a>WebViewClient

作业`WebViewClient`是确保数据在`WebView`是完全加载之前打印选项显示在菜单上，以做到这`OnPageFinished`方法。 `OnPageFinished` 侦听的 web 内容完成加载，并通知以重新创建使用其选项菜单上的活动`InvalidateOptionsMenu`:

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` 此外将设置`dataLoaded`值赋给`true`，因此`OnCreateOptionsMenu`可以重新创建使用位置中的打印选项菜单。

##### <a name="printmanager"></a>PrintManager

下面的代码示例会将内容输出`WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` 将作为自变量: ("MyWebPage"在此示例中)，该打印作业的名称[ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) ，用来从内容中，生成打印的文档和[ `PrintAttributes` ](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/) (`null`中上面示例）。 你可以指定`PrintAttributes`来帮助布置内容在打印页上，尽管默认属性应处理大多数 scenarions。

调用`Print`加载打印用户界面，列出了打印作业的选项。 如下面的屏幕截图所示，UI 向用户提供打印或保存为 pdf 格式的 HTML 内容的选项：

[![显示打印菜单 PrintHtmlActivity 的屏幕截图](kitkat-images/print1.png)](kitkat-images/print1.png)

[![显示保存为 PDF 菜单 PrintHtmlActivity 的屏幕截图](kitkat-images/print2.png)](kitkat-images/print2.png)

<a name="hardware" />

## <a name="hardware"></a>硬件

KitKat 添加了几个 Api，以容纳新的设备功能。 最值得注意的这些是基于主机的卡仿真和新`SensorManager`。

### <a name="host-based-card-emulation-in-nfc"></a>NFC 的基于主机的卡模拟机制

基于主机的卡仿真 (HCE) 允许应用程序而不依赖于承运人的专有安全元素的行为类似于 NFC 卡或 NFC 卡读卡器。 在设置 HCE 之前, 确保 HCE 是与设备上的可用`PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE 需要这两个 HCE 功能和`Nfc`与应用程序的注册权限`AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![在应用程序选项中设置 NFC 权限](kitkat-images/nfc.png)](kitkat-images/nfc.png)

若要 HCE 必须能够在后台运行且必须启动当用户进行的 NFC 事务，则即使使用 HCE 的应用程序未运行。 我们可以通过编写为 HCE 代码完成此`Service`。 HCE 服务实现`HostApduService`接口，实现以下方法：

-  *ProcessCommandApdu* -应用程序协议数据单元 (APDU) 是什么获取 NFC 读取器和 HCE 服务之间发送。 此方法使用 ADPU 从读取器，并在响应中返回一个数据单元。

-  *OnDeactivated* - `HostAdpuService` HCE 服务不再与 NFC 读取器通信时停用。


HCE 服务还需要向应用程序的清单，注册并使用正确的权限、 意向筛选器和元数据修饰。 下面的代码演示了`HostApduService`注册 Android 清单使用`Service`属性 (有关属性的详细信息，请参阅 Xamarin[使用 Android 清单](~/android/platform/android-manifest.md)指南):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("andorid.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

上面的服务提供 NFC 读取器进行交互应用程序后，一种方法，但 NFC 读取器仍具有无法知道如果此服务在模拟它需要扫描的 NFC 卡。 为了帮助标识服务 NFC 读取器，我们可以为分配服务唯一*应用程序 ID （帮助）*。 我们指定辅助手段，以及有关 HCE 服务中，其他元数据 xml 资源文件中注册与`MetaData`属性 （请参阅上面的代码示例）。 此资源文件指定一个或多个辅助设备筛选器-以十六进制格式的唯一标识符字符串对应于一个或多个 NFC 读取器设备的帮助：

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

除了帮助筛选器，xml 资源文件还提供的 HCE 服务，面向用户的说明指定的帮助组 （而不是"其他"付款应用程序），对于付款应用程序，以向用户显示 260 x 96 dp 横幅。

上面所述安装程序提供模拟 NFC 卡应用程序的基本构建基块。 NFC 本身需要几个更多步骤和进一步测试配置。 有关基于主机的卡仿真的详细信息，请参阅[Android 文档门户](https://developer.android.com/guide/topics/connectivity/nfc/hce.html)。
在 Xamarin 中使用 NFC 的详细信息，请查看[Xamarin NFC 示例](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample)。

### <a name="sensors"></a>传感器

KitKat 提供对设备的传感器通过访问[ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/)。
`SensorManager` ，操作系统就可以计划的传感器信息再到批处理中的应用程序传递保留的电池使用时间。

KitKat 还附带了两个新的传感器类型用于跟踪用户的步骤。 这些基于加速，包括：

-  *StepDetector* -应用程序通知/唤醒时用户执行一个步骤中，并检测程序提供了步骤发生时的时间值。

-  *StepCounter* -用于跟踪的用户已执行，因为传感器已注册的步骤数*直到下一步的设备重启*。

下面的屏幕截图描绘了操作中的步骤计数器：

[![显示的步骤计数器 SensorsActivity 应用的屏幕快照](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png)

你可以创建`SensorManager`通过调用`GetSystemService(SensorService)`和强制转换为结果`SensorManager`。 若要使用的步骤计数器，调用`GetDeafultSensor`上`SensorManager`。 你可以注册传感器和单步计数的帮助中的更改侦听[ `ISensorEventListener` ](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/)接口，如下面的代码示例所示：

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged` 如果单步计数更新该应用程序在前台时进行调用。 如果应用程序进入背景，或在设备处于睡眠状态，`OnSensorChanged`将不会调用; 但是，步骤将继续进行计数直到`UnregisterListener`调用。

请记住，*步骤计数值是累积的在所有应用程序注册传感器*。 这意味着，即使卸载然后重新安装应用程序，并初始化`count`变量为 0，在应用程序启动，传感器报告的值将保持已注册传感器时, 执行的步骤的总数是否通过您应用程序或另一个。 你可以阻止应用程序通过调用添加到步骤计数器`UnregisterListener`上`SensorManager`，如下面的代码所示：

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

重新启动设备，则会将单步计数重置为 0。 你的应用程序将需要额外的代码，以确保它报告应用程序，而不考虑使用传感器或设备状态的其他应用程序的准确计数。


> [!NOTE]
> **注意：**时用于步骤检测和计数与 KitKat 一起提供的 API，并非所有手机都配备传感器。 你可以检查是否可通过运行访问传感器`PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`，或检查以确保返回的值的`GetDefaultSensor`不`null`。


 <a name="developer_tools" />


## <a name="developer-tools"></a>开发人员工具

### <a name="screen-recording"></a>屏幕录制

KitKat 包括记录功能，以便开发人员可以在操作中记录应用程序的新屏幕。 屏幕录制时可通过[Android 调试桥接 (ADB)](http://developer.android.com/tools/help/adb.html)客户端，可以下载 Android SDK 的一部分。

若要录制你的屏幕，将设备连接;然后，找到你的 Android SDK 安装中，导航到**平台工具**目录并运行**adb**客户端：

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

上面的命令，将录制在 4Mbps 的默认解析的默认值 3 分钟视频。 若要编辑的长度，添加*-时间限制*标志。
若要更改分辨率，将添加*-比特率*标志。 以下命令，将录制在 8Mbps 的长时间的分钟视频：

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

你可以在你设备上找到视频-时将显示在你的库中录制时完成。

<a name="other_kitkat_additions" />

## <a name="other-kitkat-additions"></a>其他 KitKat 新增功能：

除了上面所述的更改，KitKat 可让你：

-  *使用全屏*-KitKat 引入了新[沉浸式模式](http://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int))用于浏览内容、 玩游戏，和运行其他应用程序无法得益于全屏体验。

-  *自定义通知*-获取与系统通知的更多详情[ `NotificationListenerService` ](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) 。 这样就可以在你的应用程序内的不同方式显示信息。

-  *镜像可绘制资源*-可绘制资源有一个新[ `autoMirrored` ](http://developer.android.com/reference/android/R.attr.html#autoMirrored)会通知系统的属性创建从左到右布局需要翻转的图像的镜像的版本。

-  *暂停动画*-暂停和恢复使用创建的动画[ `Animator` ](https://developer.xamarin.com/api/type/Android.Animation.Animator/)类。

-  *读取动态更改的文本*-表示动态更新使用新文本作为"实时区域"使用新的 UI 的部件[ `accesibilityLiveRegion` ](http://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)属性，因此，在可访问性模式下将自动显示新文本。

-  *增强音频体验*-请噪音跟踪与[ `LoudnessEnhancer` ](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/) ，查找高峰期和音频流使用的 RMS [ `Visualizer` ](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/)类，并从获取信息[音频时间戳](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/)有助于音频视频同步。

-  *自定义间隔同步 ContentResolver* -KitKat 将存在一些变化添加到执行同步请求的时间。 同步`ContentResolver`在自定义时间或间隔通过调用`ContentResolver.RequestSync`和传入`SyncRequest`。

-  *区分之间控制器*-在 KitKat 控制器分配可以通过设备的访问的唯一整数标识符`ControllerNumber`属性。 这样会更容易，以告知游戏中的相隔播放器。

-  *远程控制*-具有一侧的硬件和软件的一些更改，KitKat，您可以禁用设备到远程控制使用配备 IR 发送器`ConsumerIrService`，并使用新外围设备与之交互[ `RemoteController` ](https://developer.xamarin.com/api/type/Android.Media.RemoteController/) Api。

有关上述 API 更改的详细信息，请参阅 Google [Android 4.4 Api](http://developer.android.com/about/versions/android-4.4.html)概述。


## <a name="summary"></a>摘要

这篇文章引入的一些新 Api 在 Android 4.4 (API 级别 19) 中可用，并转换为 KitKat 的应用程序时涉及的最佳做法。 It 体验轮廓包围的更改影响用户的 Api，包括*转换 framework*和新选项*主题*。 接下来，它引入*存储访问 Framework*和`DocumentsProvider`类，以及新*打印 Api*。 它探讨了*NFC 基于主机的卡仿真*以及如何使用*低功耗传感器*，包括两个新传感器，跟踪用户的步骤。 最后，它演示捕获的应用程序的实时演示*屏幕录制*，并提供的 KitKat API 更改和添加的详细的列表。


## <a name="related-links"></a>相关链接

- [KitKat 示例](https://developer.xamarin.com/samples/KitKat/)
- [Android 4.4 Api](http://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](http://developer.android.com/about/versions/kitkat.html)
