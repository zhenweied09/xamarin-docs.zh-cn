---
title: 分层导航
description: 本文演示如何使用 NavigationPage 类在堆栈的后进先出 (LIFO) 页中执行导航。
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: a0a58cf05c97221a73cd0784b7859bb9c84cef86
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "38994671"
---
# <a name="hierarchical-navigation"></a>分层导航

_NavigationPage 类提供了可以向前和向后，根据需要通过页导航用户所在的分层导航体验。类实现导航作为后进先出 (LIFO) 堆栈的页对象。本文演示如何使用 NavigationPage 类在大量页面中执行导航。_

若要从一个页面移动到另一个，应用程序会将新页面推送到导航堆栈中，其中它将成为活动页，如以下关系图中所示：

![](hierarchical-images/pushing.png "将页面推送到导航堆栈")

若要返回到上一页，应用程序将弹出当前页从导航堆栈中，并最顶层的页成为活动页，如以下关系图中所示：

![](hierarchical-images/popping.png "弹出页从导航堆栈")

导航方法公开的[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)属性上任何[ `Page` ](xref:Xamarin.Forms.Page)派生类型。 这些方法提供到导航堆栈中弹出页面推送到导航堆栈上的页面并执行堆栈操作的功能。

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>执行导航

在分层导航中，使用 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 类在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象的堆栈内进行导航。 以下屏幕截图显示的主要组件`NavigationPage`每个平台上：

![](hierarchical-images/navigationpage-components.png "NavigationPage 组件")

布局[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)取决于平台：

- 在 iOS 上，导航栏位于的页面的显示标题，并具有顶部*回*返回到上一页的按钮。
- 在 Android 上，导航栏位于顶部的标题、 一个图标，显示的页面和一个*回*返回到上一页的按钮。 在中定义的图标`[Activity]`修饰的属性`MainActivity`Android 的特定于平台的项目中的类。
- 在通用 Windows 平台上，导航栏会出现在显示一个标题页的顶部。

在所有平台的值[ `Page.Title` ](xref:Xamarin.Forms.Page.Title)属性将显示为页面标题。

> [!NOTE]
> 我们建议`NavigationPage`应填充了`ContentPage`仅限实例。

### <a name="creating-the-root-page"></a>创建根页

添加到导航堆栈中的第一页称为应用程序的根页，以下代码示例显示了实现此过程的方法：

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

这将导致`Page1Xaml` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例推送到导航堆栈中，其中它将成为活动页和应用程序的根页面。 以下屏幕截图所示：

![](hierarchical-images/mainpage.png "根页的导航堆栈")

> [!NOTE]
> [ `RootPage` ](xref:Xamarin.Forms.NavigationPage.RootPage)的属性[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)实例提供对导航堆栈中的第一页的访问。

### <a name="pushing-pages-to-the-navigation-stack"></a>将页面推送到导航堆栈

若要导航到`Page2Xaml`，必须调用[ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*)方法[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)属性的当前页上，如下面的代码示例所示：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

这会将 `Page2Xaml` 实例推送到导航堆栈中，在堆栈中，它成为活动页。 以下屏幕截图所示：

![](hierarchical-images/secondpage.png "页推送到导航堆栈上")

当[ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*)调用方法时，会发生以下事件：

- 页调用`PushAsync`具有其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)重写调用。
- 要导航到页都有其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)重写调用。
- `PushAsync`任务完成。

但是，发生这些事件的确切顺序是依赖于平台。 有关详细信息，请参阅[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)的 Charles Petzold 的 Xamarin.Forms 书籍。

> [!NOTE]
> 调用[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)并[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)重写不能被视为有保证的页面导航的迹象。 例如，在 iOS 上，`OnDisappearing`在应用程序终止时重写在活动页面上调用。

### <a name="popping-pages-from-the-navigation-stack"></a>从导航堆栈弹出页

通过设备上的返回按钮（无论是设备上的物理按钮还是屏幕按钮），可以从导航堆栈中弹出活动页。

若要以编程方式返回原始页，`Page2Xaml` 实例必须调用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法，如以下代码示例所示：

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

这会从导航堆栈中删除 `Page2Xaml` 实例，而使最顶层的页成为活动页。 当[ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync)调用方法时，会发生以下事件：

- 页调用`PopAsync`具有其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)重写调用。
- 返回到页都有其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)重写调用。
- `PopAsync`任务返回。

但是，发生这些事件的确切顺序是依赖于平台。 有关详细信息请参阅[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)的 Charles Petzold 的 Xamarin.Forms 书籍。

作为[ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*)并[ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync)方法， [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)属性的每一页还提供了[ `PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync)方法，在下面的代码示例所示：

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

此方法会弹出要求除根目录之外的所有[ `Page` ](xref:Xamarin.Forms.Page)导航堆栈中，因此，可通过应用程序的活动页面的根页。

### <a name="animating-page-transitions"></a>对页面过渡效果进行动画处理

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)属性的每一页还提供了重写的 push 和 pop 方法，包括`boolean`控制是否显示在导航窗格中，一个页面的动画，如下面的代码中所示的参数示例：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushAsync (new Page2Xaml (), false);
}

async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopAsync (false);
}

async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopToRootAsync (false);
}
```

设置`boolean`参数`false`禁用时将参数设置为的页面过渡动画`true`使页面过渡动画，前提是基础平台支持。 但是，缺少此参数的 push 和 pop 方法默认情况下启用动画。

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>导航时传递数据

有时，所需的页导航期间将数据传递给另一页。 实现此目的的两种方法将数据通过页的构造函数，并通过设置新页面的传入[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的数据。 每个将现在讨论反过来。

### <a name="passing-data-through-a-page-constructor"></a>通过 Page 构造函数传递数据

在导航过程将数据传递到另一个页面的最简单方法是通过页构造函数参数，下面的代码示例中所示：

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

此代码将创建`MainPage`实例时，传入当前日期和时间 ISO8601 格式，这包装在[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)实例。

`MainPage`实例收到的数据通过构造函数参数，如下面的代码示例中所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

数据将通过设置显示在页面[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)属性，如以下屏幕截图中所示：

![](hierarchical-images/passing-data-constructor.png "通过 Page 构造函数传递数据")

### <a name="passing-data-through-a-bindingcontext"></a>BindingContext 间传递数据

用于导航期间将数据传递到另一页一种替代方法是通过设置新页面[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)到数据，如下面的代码示例中所示：

```csharp
async void OnNavigateButtonClicked (object sender, EventArgs e)
{
  var contact = new Contact {
    Name = "Jane Doe",
    Age = 30,
    Occupation = "Developer",
    Country = "USA"
  };

  var secondPage = new SecondPage ();
  secondPage.BindingContext = contact;
  await Navigation.PushAsync (secondPage);
}
```

此代码将设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`SecondPage`实例向`Contact`实例，，然后转到`SecondPage`。

`SecondPage`然后使用数据绑定来显示`Contact`实例数据，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="PassingData.SecondPage"
             Title="Second Page">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
            ...
            <Button x:Name="navigateButton" Text="Previous Page" Clicked="OnNavigateButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

下面的代码示例演示如何在 C# 中实现数据绑定：

```csharp
public class SecondPageCS : ContentPage
{
  public SecondPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var navigateButton = new Button { Text = "Previous Page" };
    navigateButton.Clicked += OnNavigateButtonClicked;

    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        navigateButton
      }
    };
  }

  async void OnNavigateButtonClicked (object sender, EventArgs e)
  {
    await Navigation.PopAsync ();
  }
}
```

数据将显示在页面的一系列[ `Label` ](xref:Xamarin.Forms.Label)控制，如以下屏幕截图中所示：

![](hierarchical-images/passing-data-bindingcontext.png "通过 BindingContext 传递的数据")

若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>操作导航堆栈

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)属性公开[ `NavigationStack` ](xref:Xamarin.Forms.INavigation.NavigationStack)可以从中获得导航堆栈中的页的属性。 Xamarin.Forms 保留权导航堆栈中，虽然`Navigation`属性提供[ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*)并[ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*)堆栈操作的方法是插入方法页面或删除它们。

[ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*)方法之前指定一个现有页面上，在导航堆栈中插入指定的页，如以下关系图中所示：

![](hierarchical-images/insert-page-before.png "在导航堆栈中插入页面")

[ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*)方法从中删除指定的页导航堆栈中，如以下关系图中所示：

![](hierarchical-images/remove-page.png "从导航堆栈中删除页面")

这些方法启用自定义导航体验，例如登录页替换为新的页上，按照在成功登录。 下面的代码示例演示此方案：

```csharp
async void OnLoginButtonClicked (object sender, EventArgs e)
{
  ...
  var isValid = AreCredentialsCorrect (user);
  if (isValid) {
    App.IsUserLoggedIn = true;
    Navigation.InsertPageBefore (new MainPage (), this);
    await Navigation.PopAsync ();
  } else {
    // Login failed
  }
}

```

前提是用户的凭据是否正确，`MainPage`实例插入到当前页之前的导航堆栈。 [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync)方法然后会从导航堆栈中，当前页删除与`MainPage`实例成为活动页。

## <a name="displaying-views-in-the-navigation-bar"></a>在导航栏中显示视图

任何 Xamarin.Forms [ `View` ](xref:Xamarin.Forms.View)可以显示在导航栏中的[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 这可以通过设置[ `NavigationPage.TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty)附加到属性`View`。 此附加的属性可以设置任意[ `Page` ](xref:Xamarin.Forms.Page)，以及何时`Page`将被推送到`NavigationPage`，则`NavigationPage`将遵守属性的值。

以下示例摘自[标题视图示例](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/)，演示如何设置[ `NavigationPage.TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty)从 XAML 附加属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="NavigationPageTitleView.TitleViewPage">
    <NavigationPage.TitleView>
        <Slider HeightRequest="44" WidthRequest="300" />
    </NavigationPage.TitleView>
    ...
</ContentPage>
```

下面是等效的C#代码：

```csharp
public class TitleViewPage : ContentPage
{
    public TitleViewPage()
    {
        var titleView = new Slider { HeightRequest = 44, WidthRequest = 300 };
        NavigationPage.SetTitleView(this, titleView);
        ...
    }
}
```

这会导致[ `Slider` ](xref:Xamarin.Forms.Slider)上的导航栏中显示[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage):

[![滑块 TitleView](hierarchical-images/titleview-small.png "滑块 TitleView")](hierarchical-images/titleview-large.png#lightbox "滑块 TitleView")

> [!IMPORTANT]
> 很多视图不会显示在导航栏中，除非使用指定的视图的大小[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)并[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)属性。 或者，该视图可以包装在[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)与[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)并[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性设置为适当的值。

注意，因为[ `Layout` ](xref:Xamarin.Forms.Layout)类派生自[ `View` ](xref:Xamarin.Forms.View)类[ `TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty)可以设置附加的属性来显示布局包含多个视图的类。 在 iOS 和通用 Windows 平台 (UWP) 上，不能更改导航栏的高度，并因此会发生剪切，如果在导航栏中显示的视图大小大于默认大小的导航栏。 但是，在 Android 上，导航栏的高度可通过设置来更改[ `NavigationPage.BarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty)可绑定属性设置为`double`表示新的高度。 有关详细信息，请参阅[NavigationPage 上设置导航栏高度](~/xamarin-forms/platform/platform-specifics/consuming/android.md#navigationpage-barheight)。

或者，可以通过将某些内容在导航栏中，而另一些在视图中放置在您的颜色匹配到导航栏的页面内容的顶部建议扩展的导航栏。 此外，在 iOS 上的分隔线和位于导航栏底部的卷影可以删除通过设置[ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty)可绑定属性设置为`true`。 有关详细信息，请参阅[隐藏导航栏分隔符在 NavigationPage](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#navigationpage-hideseparatorbar)。

> [!NOTE]
> [ `BackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty)， [ `Title` ](xref:Xamarin.Forms.Page.Title)， [ `TitleIcon` ](xref:Xamarin.Forms.NavigationPage.TitleIconProperty)，并[ `TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty)都可以定义属性占用空间，在导航栏上的值。 虽然导航栏大小因平台和屏幕大小而异，设置所有这些属性会由于可用的有限空间冲突。 而不是尝试使用这些属性的组合，你可能会发现可以更好地实现您所需的导航栏的设计通过仅设置`TitleView`属性。

### <a name="limitations"></a>限制

有许多需要注意的显示时的限制[ `View` ](xref:Xamarin.Forms.View)中的导航栏[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage):

- 在 iOS 上，视图放在导航栏中的`NavigationPage`在不同的位置，具体取决于是否启用了大标题中显示。 有关启用大标题的详细信息，请参阅[显示大标题](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title)。
- 在 Android 上，将视图放置在导航栏中的`NavigationPage`只能在使用应用程序兼容性的应用来完成。
- 我们不建议将大型和复杂的视图，如放置[ `ListView` ](xref:Xamarin.Forms.ListView)并[ `TableView` ](xref:Xamarin.Forms.TableView)，在导航栏中的`NavigationPage`。

## <a name="related-links"></a>相关链接

- [页面导航](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [分层 （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [TitleView （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/)
- [如何在 Xamarin.Forms （Xamarin University 视频） 示例中的屏幕流中创建一个符号](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [如何在 Xamarin.Forms （Xamarin University 视频） 中的屏幕流中创建一个符号](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
