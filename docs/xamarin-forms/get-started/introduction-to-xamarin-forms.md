---
title: Xamarin.Forms 简介
description: Xamarin.Forms 是本机支持的跨平台抽象 UI 工具包，让开发人员能够轻松创建可在 Android、iOS、Windows 和 Windows Phone 之间共享的用户界面。 使用目标平台的本机控件即可呈现用户界面，从而让 Xamarin.Forms 应用程序为每个平台保留相应的界面外观。 本文介绍了 Xamarin.Forms 以及如何开始使用它编写应用程序。
ms.prod: xamarin
ms.assetid: f619595f-3ee7-439b-a1bc-d13e5106e6e9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 6428f1658245ec5ecf47e474bc5ffd5d49663bf2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="an-introduction-to-xamarinforms"></a>Xamarin.Forms 简介

_Xamarin.Forms 是本机支持的跨平台抽象 UI 工具包，让开发人员能够轻松创建可在 Android、iOS、Windows 和 Windows Phone 之间共享的用户界面。使用目标平台的本机控件即可呈现用户界面，从而让 Xamarin.Forms 应用程序为每个平台保留相应的界面外观。本文介绍了 Xamarin.Forms 以及如何开始使用它编写应用程序。_

<a name="Overview" />

## <a name="overview"></a>概述

Xamarin.Forms 框架让开发人员能够快速创建跨平台用户界面。 它为用户界面提供自己的抽象，用户界面可使用 iOS、Android、Windows 或 Windows Phone 上的本机控件呈现。 这意味着应用程序可以共享大部分用户界面代码，同时保留目标平台的本机外观。

Xamarin.Forms 能让原型应用程序随时间推移迅速演变为复杂应用程序。 由于 Xamarin.Forms 应用程序是本机应用程序，因此没有其他工具包（如浏览器沙盒、API 限制或性能不佳）的限制。 使用 Xamarin.Forms 编写的应用程序能够利用基础平台的任何 API 或功能，包括（但不限于） iOS 上的 CoreMotion、PassKit 和 StoreKit；Android 上的 NFC 和 Google Play Services；以及 Windows 上的磁贴。 此外，创建应用程序时，还可以使用 Xamarin.Forms 创建应用程序用户界面的一部分，使用本机 UI 工具包创建应用程序用户界面的其他部分。

Xamarin.Forms 应用程序采用与传统跨平台应用程序相同的构建方式。 最常用的方法是使用[可移植库](~/cross-platform/app-fundamentals/pcl.md)或[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)来存放共享代码，并创建会使用共享代码的特定于平台的应用程序。

在 Xamarin.Forms 中创建用户界面可以采用两种方法。 第一种方法是完全使用 C# 源代码创建 UI。 第二种方法是使用 *Extensible Application Markup Language* (XAML)（一种声明性标记语言）来描述用户界面。 有关 XAML 的详细信息，请参阅 [XAML 基础](~/xamarin-forms/xaml/xaml-basics/index.md)。

本文讨论 Xamarin.Forms 框架的基础知识，包括以下主题：

-  [检查 Xamarin.Forms 应用程序](#Examining_A_Xamarin.Forms_Application)。
-  [如何使用 Xamarin.Forms 页面和控件](#Views_and_Layouts)。
-  [如何显示数据列表](#Lists_in_Xamarin.Forms)。
-  [如何设置数据绑定](#Data_Binding)。
-  [如何在页面之间导航](#Navigation)。
-  [后续步骤](#Next_Steps)。

<a name="Examining_A_Xamarin_Forms_Application" />

### <a name="examining-a-xamarinforms-application"></a>检查 Xamarin.Forms 应用程序

在 Visual Studio for Mac 和 Visual Studio 中，可以使用默认的 Xamarin.Forms 应用程序模板创建最简单的 Xamarin.Forms 解决方案，并向用户显示文本。 如果运行该应用程序，它应类似于以下屏幕截图：

[![](introduction-to-xamarin-forms-images/image05-sml.png "默认 Xamarin.Forms 应用程序")](introduction-to-xamarin-forms-images/image05.png#lightbox "Default Xamarin.Forms Application")

屏幕截图中的每个屏幕对应于 Xamarin.Forms 中的一个页面。 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 在 Android 表示为一个活动，在 iOS 中表示为一个视图控制器，在 Windows 通用平台 (UWP) 中则表示为一个页面。 以上屏幕截图中的示例实例化 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 对象，并使用该对象显示 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)。

为了最大限度重用启动代码，Xamarin.Forms 应用程序有一个名为 `App` 的单个类，该类负责实例化第一个将要显示的 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)。 `App` 类的示例可以在以下代码中看到：

```csharp
public class App : Application
{
  public App ()
  {
    MainPage = new ContentPage {
      Content =  new Label
      {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
      }
    };
  }
}
```

此代码实例化一个新的 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 对象，该对象将在页面上垂直和水平居中显示单个 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)。

<a name="Launching_the_Initial_Xamarin_Forms_Page_on_Each_Platform" />

### <a name="launching-the-initial-xamarinforms-page-on-each-platform"></a>在每个平台上启动 Xamarin.Forms 初始页面

若要在应用程序内部使用此 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，每个平台应用程序启动时必须初始化 Xamarin.Forms 框架并提供 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 的一个实例。 初始化步骤因平台而异，此内容将在以下部分讨论。

<a name="Launching_in_iOS" />

#### <a name="ios"></a>iOS

若要在 iOS 中启动 Xamarin.Forms 初始页面，平台项目应包括 `AppDelegate` 类（该类从 `Xamarin.Forms.Platform.iOS.FormsApplicationDelegate` 类继承），如以下代码示例所示：

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
      global::Xamarin.Forms.Forms.Init ();
      LoadApplication (new App ());
      return base.FinishedLaunching (app, options);
    }
}
```

通过调用 `Init` 方法，`FinishedLoading` 替代初始化 Xamarin.Forms 框架。 这会导致在调用将根视图控制器设置为 `LoadApplication` 方法之前，将特定于 iOS 的 Xamarin.Forms 实现加载到应用程序。

<a name="Launching_in_Android" />

#### <a name="android"></a>Android

若要在 Android 中启动 Xamarin.Forms 初始页面，平台项目应包括可使用 `MainLauncher` 属性创建 `Activity` 的代码，且具有从 `FormsApplicationActivity` 类继承的活动，如以下代码示例所示：

```csharp
namespace HelloXamarinFormsWorld.Android
{
    [Activity(Label = "HelloXamarinFormsWorld", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication (new App ());
        }
    }
}
```

通过调用 `Init` 方法，`OnCreate` 替代初始化 Xamarin.Forms 框架。 这会导致在加载 Xamarin.Forms 应用程序之前，将特定于 Android 的 Xamarin.Forms 实现加载到应用程序。

<a name="Launching_in_Windows_Phone" />

#### <a name="windows-phone-81-winrt"></a>Windows Phone 8.1 (WinRT)

在 Windows 运行时应用程序中，可从 `App` 类调用初始化 Xamarin.Forms 框架的 `Init` 方法：

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

这会将特定于 Windows Phone 的 Xamarin.Forms 实现加载到应用程序中。 可通过 `MainPage` 类启动 Xamarin.Forms 初始页面，如以下代码示例所示：

```csharp
public partial class MainPage
{
    public MainPage()
    {
      this.InitializeComponent();
      this.NavigationCacheMode = NavigationCacheMode.Required;
      this.LoadApplication(new HelloXamarinFormsWorld.App());
    }
}
```

可通过 `LoadApplication` 方法加载 Xamarin.Forms 应用程序。

Xamarin.Forms 还支持 Windows 8.1。 有关如何配置此项目类型的信息，请参阅[设置 Windows 项目](~/xamarin-forms/platform/windows/installation/index.md)。

#### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 应用程序中，可从 `App` 类调用初始化 Xamarin.Forms 框架的 `Init` 方法：

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

这会将特定于 UWP 的 Xamarin.Forms 实现加载到应用程序。 可通过 `MainPage` 类启动 Xamarin.Forms 初始页面，如以下代码示例所示：

```csharp
public partial class MainPage
{
    public MainPage()
    {
      this.InitializeComponent();
      this.LoadApplication(new HelloXamarinFormsWorld.App());
    }
}
```

可通过 `LoadApplication` 方法加载 Xamarin.Forms 应用程序。

<a name="Views_and_Layouts" />

### <a name="views-and-layouts"></a>视图和布局

可使用 4 个主要控件组创建 Xamarin.Forms 应用程序的用户界面。

1. **页面** - Xamarin.Forms 页呈现跨平台移动应用程序屏幕。 有关页面的详细信息，请参阅 [Xamarin.Forms 页面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **布局** - Xamarin.Forms 布局是用于将视图组合到逻辑结构的容器。 有关布局的详细信息，请参阅 [Xamarin.Forms 布局](~/xamarin-forms/user-interface/controls/layouts.md)。
1. **视图** - Xamarin.Forms 视图是显示在用户界面上的控件，如标签、按钮和文本输入框。 有关视图的详细信息，请参阅 [Xamarin.Forms 视图](~/xamarin-forms/user-interface/controls/views.md)。
1. **单元格** - Xamarin.Forms 单元格是专门用于列表中的项的元素，描述列表中每个项的绘制方式。 有关单元格的详细信息，请参阅 [Xamarin.Forms 单元格](~/xamarin-forms/user-interface/controls/cells.md)。

在运行时，每个控件都会映射到其本身的本机等效项（即呈现的内容）。

控件在布局内部进行托管。 现在检查 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 类，该类实现常用的布局。

<a name="StackLayout" />

#### <a name="stacklayout"></a>StackLayout

[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 在屏幕上自动排列控件而不考虑屏幕大小，从而简化了跨平台应用程序开发。 根据添加顺序，以垂直方式或水平方式逐个放置每个子元素。 `StackLayout` 使用的空间大小取决于 [`HorizontalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) 和 [`VerticalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) 属性的设置方式，但默认情况下，`StackLayout` 尝试使用整个屏幕。

以下 XAML 代码举例说明了如何使用 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 排列三个 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 控件：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample1" Padding="20">
  <StackLayout Spacing="10">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

以下代码示例显示相应的 C# 代码：

```csharp
public class StackLayoutExample : ContentPage
{
    public StackLayoutExample()
    {
        Padding = new Thickness(20);
        var red = new Label
        {
            Text = "Stop", BackgroundColor = Color.Red, FontSize = 20
        };
        var yellow = new Label
        {
            Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20
        };
        var green = new Label
        {
            Text = "Go", BackgroundColor = Color.Green, FontSize = 20
        };

        Content = new StackLayout
        {
            Spacing = 10,
            Children = { red, yellow, green }
        };
    }
}
```

默认情况下，[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 采用垂直方向，如以下屏幕截图所示：

[![](introduction-to-xamarin-forms-images/image09-sml.png "垂直 StackLayout")](introduction-to-xamarin-forms-images/image09.png#lightbox "Vertical StackLayout")

可以将 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 更改为水平方向，如以下 XAML 代码示例所示：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample2" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

以下代码示例显示相应的 C# 代码：

```csharp
public class StackLayoutExample: ContentPage
{
    public StackLayoutExample()
    {
        // Code that creates labels removed for clarity
        Content = new StackLayout
        {
            Spacing = 10,
            VerticalOptions = LayoutOptions.End,
            Orientation = StackOrientation.Horizontal,
            HorizontalOptions = LayoutOptions.Start,
            Children = { red, yellow, green }
        };
    }
}
```

以下屏幕截图显示布局结果：

[![](introduction-to-xamarin-forms-images/image10-sml.png "水平 StackLayout")](introduction-to-xamarin-forms-images/image10.png#lightbox "Horizontal StackLayout")

可通过 `HeightRequest` 和 `WidthRequest` 属性设置控件大小，如以下 XAML 代码示例所示：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample3" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" WidthRequest="100" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" WidthRequest="100" />
    <Label Text="Go" BackgroundColor="Green" Font="20" WidthRequest="200" />
  </StackLayout>
</ContentPage>
```

以下代码示例显示相应的 C# 代码：

```csharp
var red = new Label
{
    Text = "Stop", BackgroundColor = Color.Red, FontSize = 20, WidthRequest = 100
};
var yellow = new Label
{
    Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20, WidthRequest = 100
};
var green = new Label
{
    Text = "Go", BackgroundColor = Color.Green, FontSize = 20, WidthRequest = 200
};

Content = new StackLayout
{
    Spacing = 10,
    VerticalOptions = LayoutOptions.End,
    Orientation = StackOrientation.Horizontal,
    HorizontalOptions = LayoutOptions.Start,
    Children = { red, yellow, green }
};
```

以下屏幕截图显示布局结果：

[![](introduction-to-xamarin-forms-images/image11-sml.png "带 LayoutOptions 的水平 StackLayout")](introduction-to-xamarin-forms-images/image11.png#lightbox "Horizontal StackLayout with LayoutOptions")

有关 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 类的详细信息，请参阅 [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)。

<a name="Lists_in_Xamarin_Forms" />

## <a name="lists-in-xamarinforms"></a>Xamarin.Forms 中的列表

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控件负责在屏幕上显示项集合 - `ListView` 中的每一个项都包含在单个单元格中。 默认情况下，`ListView` 使用内置 [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) 模板并呈现单行文本。

以下代码示例演示一个简单的 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 示例：

```csharp
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = new string []
{
    "Buy pears", "Buy oranges", "Buy mangos", "Buy apples", "Buy bananas"
};
Content = new StackLayout
{
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = { listView }
};
```

以下屏幕截图显示生成的 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)：

 ![](introduction-to-xamarin-forms-images/image13.png "ListView")

有关 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控件的详细信息，请参阅 [ListView](~/xamarin-forms/user-interface/listview/index.md)。

<a name="Binding_to_a_Custom_Class" />

### <a name="binding-to-a-custom-class"></a>绑定到自定义类

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控件还可以通过默认的 [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) 模板显示自定义对象。

以下代码示例演示 `TodoItem` 类：

```csharp
public class TodoItem
{
    public string Name { get; set; }
    public bool Done { get; set; }
}
```

可按如下代码示例所示填充 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控件：

```csharp
listView.ItemsSource = new TodoItem [] {
    new TodoItem { Name = "Buy pears" },
    new TodoItem { Name = "Buy oranges", Done=true} ,
    new TodoItem { Name = "Buy mangos" },
    new TodoItem { Name = "Buy apples", Done=true },
    new TodoItem { Name = "Buy bananas", Done=true }
};
```

可以创建一个绑定以设置 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 要显示的 `TodoItem` 属性类型，如以下代码示例所示：

```csharp
listView.ItemTemplate = new DataTemplate(typeof(TextCell));
listView.ItemTemplate.SetBinding(TextCell.TextProperty, "Name");
```

这会创建一个绑定，指定 `TodoItem.Name` 属性的路径，并生成前面显示的屏幕截图。

有关绑定到自定义类的详细信息，请参阅 [ListView 数据源](~/xamarin-forms/user-interface/listview/data-and-databinding.md)。

<a name="Selecting_an_Item_in_a_ListView" />

### <a name="selecting-an-item-in-a-listview"></a>选择 ListView 中的项

为响应用户触摸 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 中的单元格，应处理 [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) 事件，如以下代码示例所示：

```csharp
listView.ItemSelected += async (sender, e) => {
    await DisplayAlert("Tapped!", e.SelectedItem + " was tapped.", "OK");
};
```

当包含在 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 中时，[`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) 方法可用来打开具有内置后退导航的新页面。 [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) 事件可以访问通过 [`e.SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem/) 属性与单元格关联的对象，将其绑定到新页面并使用 `PushAsync` 显示新页面，如以下代码示例所示：

```csharp
listView.ItemSelected += async (sender, e) => {
    var todoItem = (TodoItem)e.SelectedItem;
    var todoPage = new TodoItemPage(todoItem); // so the new page shows correct data
    await Navigation.PushAsync(todoPage);
};
```

每个平台实现内置后退导航的方法都各不相同。 有关详细信息，请参阅[导航](#Navigation)。

有关 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 选择的详细信息，请参阅 [ListView 交互性](~/xamarin-forms/user-interface/listview/interactivity.md)。

<a name="Customizing_the_appearance_of_a_cell" />

### <a name="customizing-the-appearance-of-a-cell"></a>自定义单元格的外观

通过子类化 [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) 类，并将该类的类型设置为 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 的 [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) 属性，可以自定义单元格的外观。

以下屏幕截图所示的单元格由一个 [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) 和两个 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 控件组成：

 ![](introduction-to-xamarin-forms-images/image14.png "ListView 自定义单元格外观")

若要创建此自定义布局，应子类化 [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) 类，如以下代码示例所示：

```csharp
class EmployeeCell : ViewCell
{
    public EmployeeCell()
    {
        var image = new Image
        {
            HorizontalOptions = LayoutOptions.Start
        };
        image.SetBinding(Image.SourceProperty, new Binding("ImageUri"));
        image.WidthRequest = image.HeightRequest = 40;

        var nameLayout = CreateNameLayout();
        var viewLayout = new StackLayout()
        {
           Orientation = StackOrientation.Horizontal,
           Children = { image, nameLayout }
        };
        View = viewLayout;
    }

    static StackLayout CreateNameLayout()
    {
        var nameLabel = new Label
        {
            HorizontalOptions= LayoutOptions.FillAndExpand
        };
        nameLabel.SetBinding(Label.TextProperty, "DisplayName");

        var twitterLabel = new Label
        {
           HorizontalOptions = LayoutOptions.FillAndExpand,
           Font = Fonts.Twitter
        };
        twitterLabel.SetBinding(Label.TextProperty, "Twitter");

        var nameLayout = new StackLayout()
        {
           HorizontalOptions = LayoutOptions.StartAndExpand,
           Orientation = StackOrientation.Vertical,
           Children = { nameLabel, twitterLabel }
        };
        return nameLayout;
    }
}
```

此段代码执行下列任务：

-  添加 [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) 控件并将其绑定到 `Employee` 对象的 `ImageUri` 属性。 若要深入了解数据绑定，请参阅[数据绑定](#Data_Binding)。
-  创建垂直方向的 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 来存放两个 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 控件。 `Label` 控件被绑定到 `DisplayName` 和 `Employee` 对象的 `Twitter` 属性。
-  创建 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)用于托管现有的 [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) 和 `StackLayout`。 使用水平方向排列其子级。

创建好自定义单元格后，可以通过将其包装在 [`DataTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) 中由 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控件使用，如以下代码示例所示：

```csharp
List<Employee> myListOfEmployeeObjects = GetAListOfAllEmployees();
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = myListOfEmployeeObjects;
listView.ItemTemplate = new DataTemplate(typeof(EmployeeCell));
```

此代码为 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 提供 `Employee` 的 `List`。 可使用 `EmployeeCell` 类呈现每个单元格。 `ListView` 将 `Employee` 对象作为其 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 传递给 `EmployeeCell`。

有关自定义单元格的外观的详细信息，请参阅[单元格的外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="Using_XAML_to_Create_and_Customize_A_List" />

### <a name="using-xaml-to-create-and-customize-a-list"></a>使用 XAML 创建和自定义列表

以下代码示例演示上一部分中 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 的 XAML 等效项：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:XamarinFormsXamlSample;assembly=XamarinFormsXamlSample"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="listView" IsVisible="false" ItemsSource="{x:Static local:App.Employees}" ItemSelected="EmployeeListOnItemSelected">
    <ListView.ItemTemplate>
      <DataTemplate>
        <ViewCell>
          <ViewCell.View>
            <StackLayout Orientation="Horizontal">
              <Image Source="{Binding ImageUri}" WidthRequest="40" HeightRequest="40" />
              <StackLayout Orientation="Vertical" HorizontalOptions="StartAndExpand">
                <Label Text="{Binding DisplayName}" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Twitter}" Font="{x:Static constants:Fonts.Twitter}"/>
              </StackLayout>
            </StackLayout>
          </ViewCell.View>
        </ViewCell>
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

此 XAML 定义包含 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 的 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)。 可通过 [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) 属性设置 `ListView` 的数据源。 在 [`ListView.ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) 元素内定义 `ItemsSource` 中每一行的布局。

<a name="Data_Binding" />

## <a name="data-binding"></a>数据绑定

数据绑定连接两个对象，即源和目标。 源对象提供数据。 目标对象使用（并经常显示）来自源对象的数据。 例如，[`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)（目标对象）通常会将其 [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) 属性绑定到源对象中的公共 `string` 属性。 下图说明了这种绑定关系：

![](introduction-to-xamarin-forms-images/data-binding.png "数据绑定")

数据绑定的主要优点是让你无需再担心视图和数据源之间的数据同步。 幕后的绑定框架源会将源对象中的更改自动推送到目标对象，且目标对象中的更改可选择性地推送回源对象。

创建数据绑定只需两个步骤：

- 目标对象的 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 属性必须设置为源。
- 必须在目标和源之间建立绑定。 在 XAML 中，此过程可通过使用 [`Binding`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) 标记扩展实现。 在 C# 中，此过程可通过 [`SetBinding`](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) 方法实现。

若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

### <a name="xaml"></a>XAML

以下代码示例说明如何在 XAML 中执行数据绑定：

```xaml
<Entry Text="{Binding FirstName}" ... />
```

在 [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) 属性和源 对象的 `FirstName` 属性之间建立绑定。 `Entry` 控件中所做的更改将自动传播到 `employeeToDisplay` 对象。 同样，如果更改了 `employeeToDisplay.FirstName` 属性，Xamarin.Forms 绑定引擎也会更新 `Entry` 控件的内容。 这称为双向绑定。 为了使双向绑定发挥作用，模型类必须实现 `INotifyPropertyChanged` 接口。

尽管可以在 XAML 中设置 `EmployeeDetailPage` 类的 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 属性，但此处在代码隐藏中设置 `Employee` 对象的实例：

```csharp
public EmployeeDetailPage(Employee employee)
{
    InitializeComponent();
    this.BindingContext = employee;
}
```

虽然可以分别设置每个目标对象的 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 属性，但没有必要。 `BindingContext` 是特殊属性，其所有子级都会继承该属性。 因此，当 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 上的 `BindingContext` 设置为 `Employee` 实例时，`ContentPage` 的所有子级都具有相同的 `BindingContext`，并且都可绑定到 `Employee` 对象的公共属性。

### <a name="c35"></a>C&#35;

以下代码示例说明如何在 C# 中执行数据绑定：

```csharp
public EmployeeDetailPage(Employee employeeToDisplay)
{
    this.BindingContext = employeeToDisplay;
    var firstName = new Entry()
    {
        HorizontalOptions = LayoutOptions.FillAndExpand
    };
    firstName.SetBinding(Entry.TextProperty, "FirstName");
    ...
}
```

向 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 构造函数传递 `Employee` 对象的一个实例，并将 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 设置为要绑定到的对象。 实例化 [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 控件，并在 [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) 属性和源对象的 `FirstName` 属性之间设置绑定。 `Entry` 控件中所做的更改将自动传播到 `employeeToDisplay` 对象。 同样，如果更改了 `employeeToDisplay.FirstName` 属性，Xamarin.Forms 绑定引擎也会更新 `Entry` 控件的内容。 这称为双向绑定。 为了使双向绑定发挥作用，模型类必须实现 `INotifyPropertyChanged` 接口。

`SetBinding` 方法采用两个参数。 第一个参数指定绑定类型的信息。 第二个参数提供绑定内容或绑定方式的信息。 大多数情况下，第二个参数只是一个字符串，持有 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 上的属性名称。 使用下列语法可直接绑定到 `BindingContext`：

```csharp
someLabel.SetBinding(Label.TextProperty, new Binding("."));
```

使用点语法指示 Xamarin.Forms 使用 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)（而非 `BindingContext` 上的属性）作为数据源。 如果 `BindingContext` 是简单类型（例如 `string` 或 `int`），此语法非常有用。

<a name="INotifyPropertyChanged" />

### <a name="property-change-notification"></a>属性更改通知

创建绑定后，默认情况下目标对象只接收源对象的值。 若要使 UI 与数据源同步，当源对象发生更改时，必须通过一种方法来通知目标对象。 `INotifyPropertyChanged` 接口就提供了这种机制。 当基础属性值发生更改时，实现此接口可以通知任何数据绑定控件。

当它的某个属性更新为新值时，实现 `INotifyPropertyChanged` 的对象必须引发 `PropertyChanged` 事件，如以下代码示例所示：

```csharp
public class MyObject : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    string _firstName;
    public string FirstName
    {
        get { return _firstName; }
        set
        {
            if (value.Equals(_firstName, StringComparison.Ordinal))
            {
                // Nothing to do - the value hasn't changed;
                return;
            }
            _firstName = value;
            OnPropertyChanged();

        }
    }

    void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        var handler = PropertyChanged;
        if (handler != null)
        {
            handler(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

当 `MyObject.FirstName` 属性发生更改时，会调用 `OnPropertyChanged` 法，从而引发 `PropertyChanged` 事件。 为了避免不必要的事件触发，如果属性值没有发生更改，则不引发 `PropertyChanged` 事件。

请注意，在 `OnPropertyChanged` 方法中，`propertyName` 参数标有 `CallerMemberName` 属性。 这可确保当使用 `null` 值调用 `OnPropertyChanged` 方法时，`CallerMemberName` 属性提供调用 `OnPropertyChanged` 的方法的名称。

<a name="Navigation" />

## <a name="navigation"></a>导航

Xamarin.Forms 提供多种不同的页导航体验，具体取决于使用的 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 类型。 对于 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 实例，提供两种导航体验：

- [分层导航](#Hierarchical_Navigation)
- [模式导航](#Modal_Navigation)

[`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)[`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) 和 [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) 类提供替代导航体验。 有关详细信息，请参阅[导航](~/xamarin-forms/app-fundamentals/navigation/index.md)。

<a name="Hierarchical_Navigation" />

### <a name="hierarchical-navigation"></a>分层导航

[`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 类提供分层导航体验，用户可以随心所欲地向前或向后导航页面。 此类将导航实现为 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 对象的后进先出 (LIFO) 堆栈。

在分层导航中，使用 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 类在 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 对象的堆栈内进行导航。 若要从一页移动到另一页，应用程序会将新页推送到导航堆栈中，在堆栈中，该页会变为活动页。 若要返回到前一页，应用程序会从导航堆栈弹出当前页，而使最顶层的页成为活动页。

添加到导航堆栈中的第一页称为应用程序的根页，以下代码示例显示了实现此过程的方法：

```csharp
public App ()
{
    MainPage = new NavigationPage(new EmployeeListPage());
}
```

若要导航到 `LoginPage`，需要调用当前页的 [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 属性上的 [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) 方法，如以下代码示例所示：

```csharp
await Navigation.PushAsync(new LoginPage());
```

这会将新的 `LoginPage` 对象推送到导航堆栈中，在堆栈中，它成为活动页。

通过设备上的返回按钮（无论是设备上的物理按钮还是屏幕按钮），可以从导航堆栈中弹出活动页。 若要以编程方式返回到前一页，`LoginPage` 实例必须调用 [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) 方法，如以下代码示例所示：

```csharp
await Navigation.PopAsync();
```

有关分层导航的详细信息，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

<a name="Modal_Navigation" />

### <a name="modal-navigation"></a>模式导航

Xamarin.Forms 支持模式页面。 模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。

模式页面可以是 Xamarin.Forms 支持的任何 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 类型。 若要显示模式页面，应用程序会将页面推送到导航堆栈中，在堆栈中，该页会变为活动页。 若要返到回前一页，应用程序会从导航堆栈弹出当前页面，而使最顶层的页成为活动页。

可以由任何 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 派生类型上的 [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 属性公开模式导航方法。 也可使用 [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 属性公开 [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) 属性，并从中获得导航堆栈中的模式页面。 但是，在模式导航中没有执行模式堆栈操作或弹出到根页的概念。 这是因为基础平台普遍都不支持这些操作。

> [!NOTE]
> 执行模式页面导航无需具有 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 实例。

若要以模式方式导航到 `LoginPage`，需要调用当前页的 [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 属性上的 [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) 方法，如以下代码示例所示：

```csharp
await Navigation.PushModalAsync(new LoginPage());
```

这会将 `LoginPage` 实例推送到导航堆栈中，在堆栈中，它成为活动页。

通过设备上的返回按钮（无论是设备上的物理按钮还是屏幕按钮），可以从导航堆栈中弹出活动页。 若要以编程方式返回原始页，`LoginPage` 实例必须调用 [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/) 方法，如以下代码示例所示：

```csharp
await Navigation.PopModalAsync();
```

这会从导航堆栈中删除 `LoginPage` 实例，而使最顶层的页成为活动页。

有关模式导航的详细信息，请参阅[模式页面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

<a name="Next_Steps" />

## <a name="next-steps"></a>后续步骤

本文介绍了如何开始编写 Xamarin.Forms 应用程序。 建议的后续步骤包括了解以下功能：

- 控件模板让你在运行时能够轻松设计或重新设计应用程序页面的主题。 有关详细信息，请参阅[控件模板](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。
- 数据模板让你可以在支持的控件上定义数据表示形式。 有关详细信息，请参阅[数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。
- 共享代码可通过 [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) 类访问本机功能。 有关详细信息，请参阅[通过 DependencyService 访问本机功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。
- Xamarin.Forms 具有简单的消息传送服务，用于发送和接收消息，减少两个类之间的耦合。 有关详细信息，请参阅[通过 MessagingCenter 发布和订阅](~/xamarin-forms/app-fundamentals/messaging-center.md)。
- 通过 `Renderer` 类可以在每个平台上以不同方式呈现每个页面、布局和控件，反过来又可以创建本机控件，在屏幕上排列该控件，并添加共享代码中指定的行为。 开发人员可以实现自定义 `Renderer` 类，以自定义控件的外观和/或行为。 有关详细信息，请参阅[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。
- 还可以自定义每个平台上的本机控件的效果。 通过子类化 [`PlatformEffect`](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/) 控件在特定于平台的项目中创建效果，并将其附加到相应的 Xamarin.Forms 控件中使用。 有关详细信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

此外，也可以阅读 Charles Petzold 撰写的 Creating Mobile Apps with Xamarin.Forms（使用 Xamarin.Forms 创建移动应用），了解有关 Xamarin.Forms 的详细信息。 有关详细信息，请参阅[使用 Xamarin.Forms 创建移动应用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)。

## <a name="summary"></a>总结

本文介绍了 Xamarin.Forms 以及如何开始使用它编写应用程序。 Xamarin.Forms 是本机支持的跨平台抽象 UI 工具包，让开发人员能够轻松创建可在 Android、iOS、Windows 和 Windows Phone 之间共享的用户界面。 使用目标平台的本机控件即可呈现用户界面，从而让 Xamarin.Forms 应用程序为每个平台保留相应的界面外观。


## <a name="related-links"></a>相关链接

- [XAML 基础知识](~/xamarin-forms/xaml/xaml-basics/index.md)
- [控件引用](~/xamarin-forms/user-interface/controls/index.md)
- [用户界面](~/xamarin-forms/user-interface/index.md)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [入门示例](https://developer.xamarin.com/samples/xamarin-forms/GettingStarted/)
- [Xamarin.Forms](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
- [免费自学教程（视频）](https://university.xamarin.com/self-guided)
- [Hello，Xamarin.Forms iOS 工作簿](https://developer.xamarin.com/workbooks/xamarin-forms/getting-started/GettingStartedWithXamarinForms-ios.workbook)
