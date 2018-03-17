---
title: "专用的片段类"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 7ddb4b7d4867813311448258bb4fb177ae4cd175
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="specialized-fragment-classes"></a>专用的片段类

片段 API 提供其他的子类，可封装某些应用程序中找到的更常见功能。 这些子类是：

-   **ListFragment** &ndash;此片段用于显示绑定到数据源，如数组或游标的项的列表。

-   **DialogFragment** &ndash;此片段用作对话框周围的包装器。 该片段将显示在其活动之上对话框。

-   **PreferenceFragment** &ndash;此片段中用于显示作为列表的首选项对象。



## <a name="the-listfragment"></a>ListFragment

`ListFragment`的概念和功能非常类似`ListActivity`; 它是承载的包装器`ListView`片段中。 下面的图像显示`ListFragment`平板电脑和手机上运行：

[![屏幕快照的 ListFragment 平板电脑和手机上](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)


### <a name="binding-data-with-the-listadapter"></a>绑定数据和 ListAdapter

`ListFragment`类已经提供了默认的布局，使其不需要重写`OnCreateView`若要显示的内容`ListFragment`。 `ListView`通过使用绑定到数据`ListAdapter`实现。 下面的示例演示如何这可以通过使用简单字符串的数组：

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    string[] values = new[] { "Android", "iPhone", "WindowsMobile",
                "Blackberry", "WebOS", "Ubuntu", "Windows7", "Max OS X",
                "Linux", "OS/2" };
    this.ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleExpandableListItem1, values);
}
```

设置时`ListAdapter`，务必使用`ListFragment.ListAdapter`属性，而不`ListView.ListAdapter`属性。 使用`ListView.ListAdapter`将导致跳过的重要的初始化代码。



### <a name="responding-to-user-selection"></a>响应用户选择

若要响应用户选择，应用程序必须重写`OnListItemClick`方法。 下面的示例演示这种可能性：

```csharp
public override void OnListItemClick(ListView l, View v, int index, long id)
{
    // We can display everything in place with fragments.
    // Have the list highlight this item and show the data.
    ListView.SetItemChecked(index, true);

    // Check what fragment is shown, replace if needed.
    var details = FragmentManager.FindFragmentById<DetailsFragment>(Resource.Id.details);
    if (details == null || details.ShownIndex != index)
    {
        // Make new fragment to show this selection.
        details = DetailsFragment.NewInstance(index);

        // Execute a transaction, replacing any existing
        // fragment with this one inside the frame.
        var ft = FragmentManager.BeginTransaction();
        ft.Replace(Resource.Id.details, details);
        ft.SetTransition(FragmentTransit.FragmentFade);
        ft.Commit();
    }
}
```

在上面的代码，当用户选择中的项`ListFragment`，新片段将显示在托管的活动，并显示有关已选定的项的更多详细信息。



## <a name="dialogfragment"></a>DialogFragment

*DialogFragment*是用于显示的对话框对象内将浮动在活动的窗口的顶部的片段的片段。 它旨在替换托管的对话框 （Android 3.0 中启动） 的 Api。 以下屏幕截图显示的示例`DialogFragment`:

[![屏幕快照的 DialogFragment 显示添加新的车辆编辑框](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

A`DialogFragment`可确保之间片段和对话框的状态保持一致。 所有交互和控制的对话框对象都应通过`DialogFragment`API，并不会执行与直接调用对话框对象。 `DialogFragment` API 提供了与每个实例`Show()`用于显示一个片段的方法。 有两种方法，若要消除的片段：

-  调用`DialogFragment.Dismiss()`上`DialogFragment`实例。 

-  显示另一个`DialogFragment`。

若要创建`DialogFragment`，一个类继承自`Android.App.DialogFragment,`，然后替代以下两种方法之一：

- **OnCreateView** &ndash;这将创建并返回的视图。

- **OnCreateDialog** &ndash;这将创建一个自定义对话框。 它通常用于显示*AlertDialog*。 在重写此方法，不需要重写`OnCreateView`。



### <a name="a-simple-dialogfragment"></a>简单 DialogFragment

以下屏幕截图显示一个简单`DialogFragment`具有`TextView`和两个`Button`s:

[![具有 TextView 和两个按钮的示例 DialogFragment](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

`TextView`将显示的用户已单击中的一个按钮的次数`DialogFragment`，而单击其他按钮将关闭该片段。 代码`DialogFragment`是：

```csharp
public class MyDialogFragment : DialogFragment
{
    private int _clickCount;
    public override void OnCreate(Bundle savedInstanceState)
    {
        _clickCount = 0;
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState)
        
        var view = inflater.Inflate(Resource.Layout.dialog_fragment_layout, container, false);
        var textView = view.FindViewById<TextView>(Resource.Id.dialog_text_view);
            
        view.FindViewById<Button>(Resource.Id.dialog_button).Click += delegate
            {
                textView.Text = "You clicked the button " + _clickCount++ + " times.";
            };

        // Set up a handler to dismiss this DialogFragment when this button is clicked.
        view.FindViewById<Button>(Resource.Id.dismiss_dialog_button).Click += (sender, args) => Dismiss();
        return view;
        }
    }
}
```


### <a name="displaying-a-fragment"></a>显示片段

如所有片段`DialogFragment`的上下文中显示`FragmentTransaction`。

`Show()`方法`DialogFragment`采用`FragmentTransaction`和`string`作为输入。 对话框将添加到该活动，与`FragmentTransaction`提交。

下面的代码演示一种活动可以使用`Show()`方法来显示`DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```


### <a name="dismissing-a-fragment"></a>解除片段

调用`Dismiss()`实例上`DialogFragment`导致要从活动中删除片段并提交该事务。
将调用所涉及的一个片段析构标准片段生命周期方法。


### <a name="alert-dialog"></a>警报对话框

而不是重写`OnCreateView`、`DialogFragment`可能会改为替代`OnCreateDialog`。 这允许应用程序创建[AlertDialog](https://developer.xamarin.com/api/type/Android.App.AlertDialog/)由一个片段。 下面的代码是使用的示例，`AlertDialog.Builder`创建`Dialog`:

```csharp
public class AlertDialogFragment : DialogFragment
{
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        EventHandler<DialogClickEventArgs> okhandler;
        var builder = new AlertDialog.Builder(Activity)
            .SetMessage("This is my dialog.")
            .SetPositiveButton("Ok", (sender, args) =>
                                         {
                                             // Do something when this button is clicked.
                                         })
            .SetTitle("Custom Dialog");
        return builder.Create();
    }
}
```



## <a name="preferencefragment"></a>PreferenceFragment

为了帮助管理首选项，该片段 API 提供了`PreferenceFragment`子类。 `PreferenceFragment`类似于[PreferenceActivity](https://developer.xamarin.com/api/type/Android.Preferences.PreferenceActivity/) &ndash;它将在片段中显示的首选项设置为用户层次结构。 当用户交互具有首选项时，它们将自动保存到[SharedPreferences](http://developer.android.com/reference/android/content/SharedPreferences.html)。
在 Android 3.0 或更高版本的应用程序，使用`PreferenceFragment`处理应用程序中的首选项。 下图显示的示例`PreferenceFragment`:

[![使用内联、 对话框中，和启动首选项的示例 PreferencesFragment](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)


### <a name="create-a-preference-fragment-from-a-resource"></a>从资源中创建的首选项片段

片段可能通过使用放大从 XML 资源文件的首选项[PreferenceFragment.AddPreferencesFromResource](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromResource/p/System.Int32/)方法。 片段的生命周期中调用此方法的逻辑位置都会出现在`OnCreate`方法。

`PreferenceFragment`图所示更高版本由从 XML 加载资源。 资源文件是：

```xml
<?xml version="1.0" encoding="utf-8"?>

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

  <PreferenceCategory android:title="Inline Preferences">
    <CheckBoxPreference android:key="checkbox_preference"
                        android:title="Checkbox Preference Title"
                        android:summary="Checkbox Preference Summary" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Dialog Based Preferences">

    <EditTextPreference android:key="edittext_preference"
                        android:title="EditText Preference Title"
                        android:summary="EditText Preference Summary"
                        android:dialogTitle="Edit Text Preferrence Dialog Title" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Launch Preferences">

    <!-- This PreferenceScreen tag serves as a screen break (similar to page break
             in word processing). Like for other preference types, we assign a key
             here so it is able to save and restore its instance state. -->
    <PreferenceScreen android:key="screen_preference"
                      android:title="Title Screen Preferences"
                      android:summary="Summary Screen Preferences">

      <!-- You can place more preferences here that will be shown on the next screen. -->

      <CheckBoxPreference android:key="next_screen_checkbox_preference"
                          android:title="Next Screen Toggle Preference Title"
                          android:summary="Next Screen Toggle Preference Summary" />

    </PreferenceScreen>

    <PreferenceScreen android:title="Intent Preference Title"
                      android:summary="Intent Preference Summary">

      <intent android:action="android.intent.action.VIEW"
              android:data="http://www.android.com" />

    </PreferenceScreen>

  </PreferenceCategory>

</PreferenceScreen>
```

片段的首选项的代码如下所示：

```csharp
public class PrefFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        AddPreferencesFromResource(Resource.Xml.preferences);
    }
}
```



### <a name="querying-activities-to-create-a-preference-fragment"></a>查询活动，以便创建首选项片段

另一种方法创建`PreferenceFragment`涉及到查询活动。 每个活动可以使用[元数据\_密钥\_首选项](https://developer.xamarin.com/api/field/Android.Preferences.PreferenceManager.MetadataKeyPreferences/)将指向的 XML 资源文件的属性。 在 Xamarin.Android，这完成通过装饰的活动`MetaDataAttribute`，，然后指定要使用的资源文件。 `PreferenceFragment`类会提供方法[AddPreferenceFromIntent](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromIntent/p/Android.Content.Intent/)) 可用来查询用于查找此 XML 资源和放大量有关它的首选项层次结构的活动。

在以下代码段中，使用提供了此过程的一个示例`AddPreferencesFromIntent`创建`PreferenceFragment`:

```csharp
public class MyPreferenceFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        var intent = new Intent(this.Activity, typeof (MyActivityWithPreferences));
        AddPreferencesFromIntent(intent);
    }
}
```

Android 将看看类`MyActivityWithPreference`。 必须使用修饰类`MetaDataAttribute,`下面的代码段中所示：

```csharp
[Activity(Label = "My Activity with Preferences")]
[MetaData(PreferenceManager.MetadataKeyPreferences, Resource = "@xml/preference_from_intent")]
public class MyActivityWithPreferences : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        // This is deliberately blank
    }
}
```

`MetaDataAttribute`声明的 XML 资源文件`PreferenceFragment`将使用放大量首选项层次结构。 如果`MetatDataAttribute`未提供，则将在运行时引发异常。 此代码运行时，`PreferenceFragment`显示如以下屏幕截图所示：

[![显示的 PreferenceFragment 示例应用程序的屏幕截图](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
