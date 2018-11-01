---
title: Xamarin.Forms 应用程序类
description: 此文章介绍了功能的默认应用程序类，其中包含要设置为应用程序的初始页的属性，并为永久性字典在生命周期状态更改存储简单的值。
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 9acd1b8f25696267578f5cc269eb1b0c738be571
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675089"
---
# <a name="xamarinforms-app-class"></a>Xamarin.Forms 应用程序类

`Application`基类提供以下功能，它们在默认的项目中公开`App`子类：

* 一个`MainPage`属性，它是在何处设置应用的初始页。
* 持久[`Properties`字典](#Properties_Dictionary)在生命周期状态更改存储简单值。
* 静态`Current`属性，其中包含对当前应用程序对象的引用。

它还公开[生命周期方法](~/xamarin-forms/app-fundamentals/app-lifecycle.md)如`OnStart`， `OnSleep`，和`OnResume`以及模式导航的事件。

具体的模板取决于您选择，`App`类可以定义在两种方式之一：

* **C#**，或
* **XAML 和 C#**

若要创建**应用程序**类使用 XAML，默认**应用**类必须替换 XAML**应用**类和关联的代码隐藏，如下面的代码示例中所示：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

下面的代码示例显示了关联的代码隐藏：

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

设置以及[ `MainPage` ](xref:Xamarin.Forms.Application.MainPage)属性，还必须调用代码隐藏`InitializeComponent`方法来加载和分析相关联的 XAML。

## <a name="mainpage-property"></a>MainPage 属性

`MainPage`属性上的`Application`类设置的根页的应用程序。

例如，可以创建在逻辑应用`App`类，以显示不同的页，具体取决于是否用户登录或未。

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

`Application`子类具有静态`Properties`可以使用字典来存储数据，特别是在中使用`OnStart`， `OnSleep`，和`OnResume`方法。 这可以从访问，你的代码使用 Xamarin.Forms 中的任意位置`Application.Current.Properties`。

`Properties` Dictionary 使用`string`密钥和存储`object`值。

例如，您可以设置永久`"id"`在代码中的任意位置的属性 (当选择项，在页面的`OnDisappearing`方法，或在`OnSleep`方法) 如下所示：

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

在中`OnStart`或`OnResume`方法可以使用此值以重新创建以某种方式的用户的体验。 `Properties`字典存储`object`s，因此您需要其值强制转换之前使用它。

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

始终检查存在的密钥才能访问它以防止出现意外的错误。

> [!NOTE]
> `Properties`字典可以仅序列化的存储基元类型。 尝试存储的其他类型 (如`List<string>`) 可能会以静默方式失败。

<!-- bugzilla 28657 -->

### <a name="persistence"></a>持久性

`Properties`字典自动保存到设备。
当应用程序将返回从后台或甚至重新启动之后，将可添加到字典中的数据。

Xamarin.Forms 1.4 上引入的其他方法`Application`类的`SavePropertiesAsync()`-这可以调用以进行主动保留`Properties`字典。 这将允许您保存后的重要更新的属性，而不是它们不获取序列化出由于故障或丢失的 os 的风险。

您可以查找对使用的引用`Properties`字典中的**使用 Xamarin.Forms 创建移动应用**书籍章节[6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf)， [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf)，和[20](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)，与关联[示例](https://github.com/xamarin/xamarin-forms-book-preview-2)。



## <a name="the-application-class"></a>Application 类

完整`Application`类的实现如下所示的引用：

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

然后在每个特定于平台的项目中实例化并传递给此类`LoadApplication`这是 where 方法`MainPage`加载并显示给用户。
以下各节中显示为每个平台的代码。 最新的 Xamarin.Forms 解决方案模板已包含所有这些代码，为您的应用程序预先配置的。


### <a name="ios-project"></a>iOS 项目

IOS`AppDelegate`类继承自`FormsApplicationDelegate`。 它应当：

* 调用`LoadApplication`的实例与`App`类。

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

Android`MainActivity`继承`FormsAppCompatActivity`。 在中`OnCreate`重写`LoadApplication`使用的实例调用方法`App`类。

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="universal-windows-project-uwp-for-windows-10"></a>适用于 Windows 10 的通用 Windows 项目 (UWP)

请参阅[安装 Windows 项目](~/xamarin-forms/platform/windows/installation/index.md)有关 Xamarin.Forms 中的 UWP 支持信息。

在 UWP 项目的主页应继承自`WindowsPage`:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

C#代码隐藏构造必须调用`LoadApplication`创建一个实例在 Xamarin.Forms `App`。 请注意，它是显式使用应用程序的命名空间限定的好办法`App`因为 UWP 应用程序也有其自己`App`与 Xamarin.Forms 不相关的类。

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

请注意，`Forms.Init()`必须调用**App.xaml.cs**围绕第 63 行。
