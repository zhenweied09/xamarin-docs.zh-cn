---
title: "时间选取器"
description: "选择使用 TimePickerDialog 和 DialogFragment 时间"
ms.topic: article
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 93a2effd42432d13767dad05a47548aebc9a0b93
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="time-picker"></a>时间选取器

若要提供用户选择的时间的方法，可以使用[TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)。 Android 应用通常使用`TimePicker`与[TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)选择时间值&ndash;这有助于确保跨设备和应用程序一致的接口。 `TimePicker` 允许用户选择一天的时间在 24 小时或 12 小时制 AM/PM 模式下。
`TimePickerDialog` 是一个帮助器类，封装`TimePicker`在对话框中。

[![示例的操作中的时间选取器对话框的屏幕截图](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>概述

现代的 Android 应用程序显示`TimePickerDialog`中[DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)。 这使得应用程序以显示`TimePicker`作为弹出式对话框中或将其嵌入在活动中。 此外，`DialogFragment`管理的生命周期和显示的对话框中，可以减少必须实现的代码。

本指南演示如何使用`TimePickerDialog`、 在已包装`DialogFragment`。 示例应用程序将显示`TimePickerDialog`为模式对话框当用户单击活动上的按钮。 当时间由用户设置时，对话框退出和处理程序更新`TextView`与所选的时间活动屏幕上。

## <a name="requirements"></a>惠?

本指南的示例应用程序面向 Android 4.1 （API 级别
16) 或更高版本，但可以用于 Android 3.0 （API 级别为 11 或更高版本）。 它是 android 的可以支持较旧版本项目和一些代码更改 Android 支持库 v4 添加。

## <a name="using-the-timepicker"></a>使用 TimePicker

本示例将扩展`DialogFragment`; 的子类实现`DialogFragment`(调用`TimePickerFragment`下面) 承载并显示`TimePickerDialog`。 当第一次启动示例应用程序时，它会显示**选取时间**上面按钮`TextView`将用于显示所选的时间：

[![初始示例应用程序屏幕](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

当你单击**选取时间**按钮，示例应用程序启动`TimePickerDialog`此屏幕截图中所示：

[![应用显示的默认时间选取器对话框的屏幕截图](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

在`TimePickerDialog`，选择一个时间，然后单击**确定**按钮原因`TimePickerDialog`来调用的方法[IOnTimeSetListener.OnTimeSet](https://developer.xamarin.com/api/member/Android.App.TimePickerDialog+IOnTimeSetListener.OnTimeSet/p/Android.Widget.TimePicker/System.Int32/System.Int32/System.Int32/)。
此接口由宿主实现`DialogFragment`(`TimePickerFragment`、 下面所述)。 单击**取消**按钮会导致片段并以可撤消的对话框。

`DialogFragment` 在以下三种方式承载 Actvity 将返回所选的时间：

1. **调用方法或设置属性**&ndash;活动可以提供的属性或针对将此值设置的方法。

2. **引发事件** &ndash; `DialogFragment`可以定义将一个事件时引发`OnTimeSet`调用。

3. **使用`Action`**  &ndash; `DialogFragment`可以调用`Action<DateTime>`来显示活动中的时间。 活动将提供`Action<DateTime`实例化时`DialogFragment`。 

此示例将使用第三个方法，此操作需要活动提供`Action<DateTime>`处理程序`DialogFragment`。



## <a name="start-an-app-project"></a>启动应用程序项目

启动新的 Android 项目调用**TimePickerDemo** (如果你不熟悉创建 Xamarin.Android 项目，请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)若要了解如何创建新项目)。

编辑**Resources/layout/Main.axml**并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:padding="16dp">
    <Button
        android:id="@+id/select_button"
        android:paddingLeft="24dp"
        android:paddingRight="24dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:textSize="20dp" />
    <TextView
        android:id="@+id/time_display"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:paddingTop="22dp"
        android:text="Picked time will be displayed here"
        android:textSize="24dp" />
</LinearLayout>
```

这是一个基本[LinearLayout](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)与[TextView](https://developer.xamarin.com/api/type/Android.Widget.TextView/)显示的时间和[按钮](https://developer.xamarin.com/api/type/Android.Widget.Button/)，这将打开`TimePickerDialog`。 请注意此布局，使用硬编码字符串和维度将应用程序更简单且更易于理解&ndash;生产应用通常使用这些值进行的资源 (如可以中所示[DatePicker](https://github.com/xamarin/recipes/blob/master/android/controls/datepicker/select_a_date/Resources/layout/Main.axml)的代码示例)。

编辑**MainActivity.cs**并将其内容替换为以下代码：

```csharp
using Android.App;
using Android.Widget;
using Android.OS;
using System;
using Android.Util;
using Android.Text.Format;

namespace TimePickerDemo
{
    [Activity(Label = "TimePickerDemo", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        TextView timeDisplay;
        Button timeSelectButton;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            SetContentView(Resource.Layout.Main);
            timeDisplay = FindViewById<TextView>(Resource.Id.time_display);
            timeSelectButton = FindViewById<Button>(Resource.Id.select_button);
        }
    }
}
```

当生成和运行此示例中时，你应看到类似于下面的屏幕快照的初始屏幕：

[![初始应用程序屏幕](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

单击**选取时间**按钮不执行任何操作因为`DialogFragment`具有尚未实现以显示`TimePicker`。
下一步是创建此`DialogFragment`。



## <a name="extending-dialogfragment"></a>扩展 DialogFragment

若要扩展`DialogFragment`用于`TimePicker`，需要创建一个派生自的子类`DialogFragment`并实现`TimePickerDialog.IOnTimeSetListener`。 添加到以下类**MainActivity.cs**:

```csharp
public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
{
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };

    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }

    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }

    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
        Log.Debug(TAG, selectedTime.ToLongTimeString());
        timeSelectedHandler (selectedTime);
    }
}
```

这`TimePickerFragment`类是分解为较小的部分，并在下一部分中所述。


### <a name="dialogfragment-implementation"></a>DialogFragment 实现

`TimePickerFragment` 实现几种方法： 工厂方法时，对话框实例化方法，与`OnTimeSet`所需的处理程序方法`TimePickerDialog.IOnTimeSetListener`。

-   `TimePickerFragment` 是的一个子类`DialogFragment`。 它还实现`TimePickerDialog.IOnTimeSetListener`接口 (即，它会提供所需`OnTimeSet`方法):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG` 初始化用于日志记录 (*MyTimePickerFragment*可以更改为任何字符串你想要使用)。 `timeSelectedHandler`操作初始化为一个空的委托，以防止 null 引用异常：

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   `NewInstance`调用工厂方法来实例化一个新`TimePickerFragment`。 此方法采用`Action<DateTime>`当用户单击调用处理程序**确定**按钮`TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   当该片段是要显示时，Android 调用`DialogFragment`方法[OnCreateDialog](https://developer.xamarin.com/api/member/Android.App.DialogFragment.OnCreateDialog/p/Android.OS.Bundle/)。 
    此方法创建一个新`TimePickerDialog`对象并初始化与活动的回调对象 (即的当前实例`TimePickerFragment`)，和当前时间：

    ```csharp
    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }
    ```

-   当用户更改中的时间设置`TimePicker`对话框中，`OnTimeSet`调用方法。 `OnTimeSet` 创建`DateTime`对象使用当前日期和时间 （小时和分钟） 中的合并数由用户选择：

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   这`DateTime`对象传递给`timeSelectedHandler`注册到`TimePickerFragment`在创建时的对象。 `OnTimeSet` 调用此处理程序进行更新到选定的时间 （在下一节中实现此处理程序） 的活动的时间显示：

    ```csharp
    timeSelectedHandler (selectedTime);
    ```



## <a name="displaying-the-timepickerfragment"></a>显示 TimePickerFragment

现在，`DialogFragment`已实现，是时候实例化`DialogFragment`使用`NewInstance`工厂方法并将其显示通过调用[DialogFragment.Show](https://developer.xamarin.com/api/member/Android.App.DialogFragment.Show/p/Android.App.FragmentManager/System.String/):

添加以下方法`MainActivity`:

```csharp
void TimeSelectOnClick (object sender, EventArgs eventArgs)
{
    TimePickerFragment frag = TimePickerFragment.NewInstance (
        delegate (DateTime time)
        {
            timeDisplay.Text = time.ToShortTimeString();
        });

    frag.Show(FragmentManager, TimePickerFragment.TAG);
}
```

后`TimeSelectOnClick`实例化`TimePickerFragment`，它会创建并传递在委托中使用传入的时间值更新活动的时间显示的匿名方法。 最后，它将启动`TimePicker`对话框片段 (通过`DialogFragment.Show`) 以显示`TimePicker`给用户。

在结束`OnCreate`方法，添加以下行以附加到事件处理程序**选取时间**启动对话框的按钮：

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

当**选取时间**单击按钮时，`TimeSelectOnClick`将调用以显示`TimePicker`对话框向用户的片段。



## <a name="try-it"></a>尝试一下！

生成并运行应用。 当你单击**选取时间**按钮，`TimePickerDialog`显示在默认的时间格式的活动 （在此示例中为 12 小时 AM/PM 模式下）：

[![AM/PM 模式下显示时间对话框](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
当你单击**确定**中`TimePicker`对话框中，该处理程序更新活动的`TextView`与选择的时间，然后退出：

[![A/M 时间显示在活动 TextView](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

接下来，添加以下代码的行`OnCreateDialog`后立即`is24HourFormat`声明和初始化：

```csharp
is24HourFormat = true;
```

此更改强制标志传递给`TimePickerDialog`构造函数要`true`因此该 24 小时模式下使用而不是托管活动的时间格式。 生成并再次运行该应用，请单击**选取时间**按钮，`TimePicker`对话框现在将显示在 24 小时格式：

[![采用 24 小时格式 TimePicker 对话框](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

因为该处理程序调用[DateTime.ToShortTimeString](https://msdn.microsoft.com/en-us/library/system.datetime.toshortdatestring%28v=vs.110%29.aspx)要打印的活动的时间`TextView`，时间仍打印默认 12 小时 AM/PM 格式。



## <a name="summary"></a>摘要

本文介绍了如何显示`TimePicker`为弹出窗口模式对话框从 Android 活动的小组件。 它提供一个示例`DialogFragment`实现对其进行讨论`IOnTimeSetListener`接口。 此示例还演示了如何`DialogFragment`可与主机活动来显示选定的时间交互。


## <a name="related-links"></a>相关链接

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)
- [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)
- [TimePickerDialog.IOnTimeSetListener](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog+IOnTimeSetListener/)
- [TimePickerDemo (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
