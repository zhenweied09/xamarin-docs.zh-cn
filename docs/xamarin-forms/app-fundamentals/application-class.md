---
title: "应用程序类"
description: "默认应用程序类，该类可以是 C# 或 XAML 的功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: d7965c5d4d65dd6bf7aa4128f467acd3e2d39e60
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="app-class"></a>应用程序类

`Application`基类提供了以下功能，你的项目默认情况下公开`App`子类：

* A`MainPage`属性，它是在何处设置应用程序的初始页。
* 持久的[`Properties`字典](#Properties_Dictionary)跨越生命周期状态更改存储简单值。
* 静态`Current`属性，它包含对当前的应用程序对象的引用。

如果还公开[生命周期方法](~/xamarin-forms/app-fundamentals/app-lifecycle.md)如`OnStart`， `OnSleep`，和`OnResume`以及模式导航事件。

具体取决于哪个模板，你选择，`App`无法在两种方式之一中定义类：

* **C#**，或
* **XAML & C#**

若要创建**应用**类使用 XAML，默认值**应用**类必须替换 XAML**应用**类和关联的代码隐藏，如下面的代码示例中所示：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

下面的代码示例显示关联的代码隐藏：

```csharp
public partial class App : Application
{
    public App ()
    {
        InitializeComponent ();
        MainPage = new HomePage ();
    }
    ...
}
```

设置以及[ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/)属性，还必须调用代码隐藏`InitializeComponent`加载并分析关联的 XAML 方法。

## <a name="mainpage-property"></a>MainPage 属性

`MainPage`属性`Application`类设置的应用程序的根页。

例如，你可以在其中创建中的逻辑你`App`类来显示具体取决于是否用户或不记录在不同的页。

`MainPage`属性应设置`App`构造函数，

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

<a name="Properties_Dictionary" />

## <a name="properties-dictionary"></a>属性字典

`Application`子类具有静态`Properties`可以用于存储数据，尤其是在中使用的字典`OnStart`， `OnSleep`，和`OnResume`方法。 这可从任何位置中访问 Xamarin.Forms 代码使用`Application.Current.Properties`。

`Properties`字典使用`string`密钥和存储`object`值。

例如，你可以设置持久性`"id"`属性在代码中的任意位置 (当选择项，在页的`OnDisappearing`方法，或在`OnSleep`方法) 如下所示：

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

在`OnStart`或`OnResume`然后可以使用此值以重新创建以某种方式的用户的体验的方法。 `Properties`字典存储`object`s，因此你需要其值强制转换然后再使用它。

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

始终检查存在的键然后才能访问它以防止出现意外的错误。

> [!NOTE]
> **注意：** `Properties`字典只能序列化的存储基元类型。 尝试存储其他类型 (如`List<string>`) 以无提示方式可能会失败。

<!-- bugzilla 28657 -->

### <a name="persistence"></a>持久性

`Properties`字典自动保存到设备。
当从后台返回应用程序时，或甚至重新启动之后，将可向字典中添加的数据。

Xamarin.Forms 1.4 上引入了其他方法`Application`类- `SavePropertiesAsync()` -其可以调用以主动保留`Properties`字典。 这是为了允许你保存重要更新后的属性，而不是它们不获取序列化出由于故障或正在终止操作系统的风险。

你可以查找对使用的引用`Properties`字典中的**具有 Xamarin.Forms 创建移动应用**书籍章节[6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf)， [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf)，和[20](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)，与关联[示例](https://github.com/xamarin/xamarin-forms-book-preview-2)。



## <a name="the-application-class"></a>Application 类

完整`Application`类实现的引用如下所示：

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }

    protected override void OnStart()
    {
        // Handle when your app starts
        Debug.WriteLine ("OnStart");
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Debug.WriteLine ("OnSleep");
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
        Debug.WriteLine ("OnResume");
    }
}

```

然后，此类是在每个特定于平台的项目中实例化并传递给`LoadApplication`where 方法`MainPage`加载并且会向用户显示。
下列部分中显示了为每个平台的代码。 最新的 Xamarin.Forms 解决方案模板已经包含所有此代码中，为你的应用程序预先配置的。


### <a name="ios-project"></a>iOS 项目

IOS`AppDelegate`类现在继承自`FormsApplicationDelegate`。 它应当：

* 调用`LoadApplication`与实例`App`类。

* 始终返回`base.FinishedLaunching (app, options);`。

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="android-project"></a>Android 项目

Android`MainActivity`现在继承自`FormsApplicationActivity`。 在`OnCreate`重写`LoadApplication`与的实例调用方法`App`类。

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

> [!NOTE]
> **注意：**没有更新[ `FormsAppCompatActivity` ](~/xamarin-forms/platform/android/appcompat.md)基类可以用于更好地支持 Android 材料设计。
> 这将在将来成为默认 Android 模板，但您可以按照[这些说明](~/xamarin-forms/platform/android/appcompat.md)更新你的现有 Android 应用。


### <a name="windows-phone-project"></a>Windows Phone 项目

Windows Phone （基于 Silverlight 的） 项目中的主页应继承自`FormsApplicationPage`。 这意味着 XAML 和 C# 为`MainPage`引用`FormsApplicationPage`类所示。

XAML 使用自定义的命名空间，以便使根元素反映`FormsApplicationPage`类：

```xaml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

C# 继承自`FormsApplicationPage`类，并调用`LoadApplication`若要创建的你 Xamarin.Forms 实例`App`。 请注意，它是好的做法显式使用应用程序命名空间来限定`App`因为 Windows Phone 应用程序还具有自己`App`与 Xamarin.Forms 无关的类。

```csharp
public partial class MainPage :
    global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3, use the correct namespace
    }
 }
```

### <a name="windows-81-project"></a>Windows 8.1 项目

在主页[（基于 WinRT） 的 Windows 8.1](~/xamarin-forms/platform/windows/installation/tablet.md)项目应现在继承自`WindowsPage`。 这意味着为 XAML`MainPage`引用`WindowsPage`类所示：

XAML 使用自定义的命名空间，以便使根元素反映`FormsApplicationPage`类：

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
   ...>
</forms:WindowsPage>
```

必须调用的 C# 代码隐藏的构造`LoadApplication`若要创建的你 Xamarin.Forms 实例`App`。 请注意，它是好的做法显式使用应用程序命名空间来限定`App`因为 UWP 应用程序还具有自己`App`与 Xamarin.Forms 无关的类。

```csharp
public partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();
        LoadApplication(new YOUR_APP_NAMESPACE.App());
    }
 }
```

请注意，`Forms.Init()`必须调用**App.xaml.cs**围绕行 65。

### <a name="universal-windows-project-uwp-for-windows-10"></a>适用于 Windows 10 的通用 Windows 项目 (UWP)

[通用 Windows 项目](~/xamarin-forms/platform/windows/installation/universal.md)Xamarin.Forms 中的支持目前处于预览状态。

对 UWP 项目中的主页应继承自`WindowsPage`。 这意味着 XAML 和 C# 为`MainPage`引用`FormsApplicationPage`类所示。

XAML 使用自定义的命名空间，以便使根元素反映`FormsApplicationPage`类：

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

必须调用的 C# 代码隐藏的构造`LoadApplication`若要创建的你 Xamarin.Forms 实例`App`。 请注意，它是好的做法显式使用应用程序命名空间来限定`App`因为 UWP 应用程序还具有自己`App`与 Xamarin.Forms 无关的类。

```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();

        LoadApplication(new YOUR_NAMESPACE.App());
    }
 }
```

请注意，`Forms.Init()`必须调用**App.xaml.cs**围绕行 63。
