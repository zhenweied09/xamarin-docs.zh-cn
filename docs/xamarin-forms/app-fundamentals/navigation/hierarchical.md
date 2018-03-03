---
title: "分层导航"
description: "NavigationPage 类提供能够向前和向后，根据需要浏览页中，用户所在的层次结构导航体验。 类实现作为后进先出 (LIFO) 堆栈 Page 对象中的导航。 本文演示如何使用 NavigationPage 类来执行导航堆栈中的页。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 95fc958beb71cba8f4d575eaa96d0612aa458966
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="hierarchical-navigation"></a>分层导航

_NavigationPage 类提供能够向前和向后，根据需要浏览页中，用户所在的层次结构导航体验。类实现作为后进先出 (LIFO) 堆栈 Page 对象中的导航。本文演示如何使用 NavigationPage 类来执行导航堆栈中的页。_

本文讨论以下主题：

- [执行导航](#Performing_Navigation)– 创建根页、 将页推送到导航堆栈、 弹出页导航堆栈中，从和对该页可为进行动画处理。
- [将数据传递导航时](#Passing_Data_when_Navigating)– 将数据通过页的构造函数，并通过传递`BindingContext`。
- [操作导航堆栈](#Manipulating_the_Navigation_Stack)– 通过插入或删除页操作堆栈。

## <a name="overview"></a>概述

若要从一个页面移动到另一个，应用程序会将推送到导航堆栈中，一个新页其中它将成为活动页面，如下面的关系图中所示：

![](hierarchical-images/pushing.png "将页面推送到导航堆栈")

若要返回回前一页，应用程序将弹出导航堆栈中，从当前页和新的最顶层页成为活动的页面中，如下面的关系图中所示：

![](hierarchical-images/popping.png "弹出导航堆栈中的页面")

浏览方法公开的[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)属性上任何[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)派生类型。 这些方法提供对 pop 页导航堆栈中，从推送到导航堆栈中的页，以便执行堆栈操作的能力。

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>执行导航

在分层导航中，使用 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 类在 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 对象的堆栈内进行导航。 以下屏幕快照显示的主要组件`NavigationPage`每个平台上：

![](hierarchical-images/navigationpage-components.png "NavigationPage 组件")

布局[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)依赖平台上：

- 在 iOS 上导航栏位于顶部的页显示标题，并具有*回*返回到以前的页面的按钮。
- 在 Android 上，导航栏位于顶部显示标题，一个图标，图标的页和*回*返回到以前的页面的按钮。 在中定义的图标`[Activity]`修饰的属性`MainActivity`Android 的特定于平台的项目中的类。
- 在 Windows Phone 导航栏是页的存在在顶部显示标题。 Windows Phone 缺少*回*按钮导航栏上，因为屏幕上*回*按钮位于屏幕的底部。

在所有平台的值上[ `Page.Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/)属性将显示为页面标题。

> [!NOTE]
> 具有建议`NavigationPage`应使用填充`ContentPage`仅限实例。

### <a name="creating-the-root-page"></a>创建根页

添加到导航堆栈中的第一页称为应用程序的根页，以下代码示例显示了实现此过程的方法：

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

这将导致`Page1Xaml` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)实例为要推送到导航堆栈中，其中它将成为活动网页和应用程序的根页。 以下屏幕截图所示：

![](hierarchical-images/mainpage.png "根页的导航堆栈")

> [!NOTE]
> [ `RootPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.RootPage/)属性[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)实例提供对导航堆栈中的第一页的访问。

### <a name="pushing-pages-to-the-navigation-stack"></a>将页推送到导航堆栈

若要导航到`Page2Xaml`，需要调用[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/)方法[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)属性的当前页，详见下面的代码示例：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

这会将 `Page2Xaml` 实例推送到导航堆栈中，在堆栈中，它成为活动页。 以下屏幕截图所示：

![](hierarchical-images/secondpage.png "页强制压到导航堆栈")

当[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/)调用方法时，会发生以下事件：

- 页调用`PushAsync`具有其[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/)重写调用。
- 要导航到页都有其[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/)重写调用。
- `PushAsync`任务完成。

但是，在其中发生这些事件的确切顺序由平台决定。 有关详细信息，请参阅[章 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Charles Petzold Xamarin.Forms 书籍。

> [!NOTE]
> 调用[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/)和[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/)替代不能被视为有保证的页导航的迹象。 例如，在 iOS 上`OnDisappearing`重写调用在活动页面上，当应用程序终止时。

### <a name="popping-pages-from-the-navigation-stack"></a>从导航堆栈砰声页

通过设备上的返回按钮（无论是设备上的物理按钮还是屏幕按钮），可以从导航堆栈中弹出活动页。

若要以编程方式返回原始页，`Page2Xaml` 实例必须调用 [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) 方法，如以下代码示例所示：

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

这会从导航堆栈中删除 `Page2Xaml` 实例，而使最顶层的页成为活动页。 当[ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/)调用方法时，会发生以下事件：

- 页调用`PopAsync`具有其[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/)重写调用。
- 返回到页都有其[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/)重写调用。
- `PopAsync`任务返回。

但是，在其中发生这些事件的确切顺序由平台决定。 有关详细信息请参阅[章 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) Charles Petzold Xamarin.Forms 书籍。

以及[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/)和[ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/)方法， [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)属性的每一页还提供了[ `PopToRootAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopToRootAsync()/)方法，下面的代码示例中所示：

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

此方法弹出除根[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)从导航堆栈中，因此使得应用程序的活动页面的根页。

### <a name="animating-page-transitions"></a>该页可为进行动画处理

[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)属性的每一页还提供了重写的推送和包括的 pop 方法`boolean`控制是否显示在导航窗格中，页动画，如下面的代码中所示的参数示例：

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

设置`boolean`参数`false`禁用时将参数设置为的网页过渡动画`true`使网页过渡动画，前提是它支持由基础平台。 但是，缺少此参数的推送和弹出方法默认情况下启用动画。

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>将数据传递导航时

有时很必要页导航过程将数据传递给另一页。 实现此目的的两种技术进行双向数据通过页的构造函数，并设置新页的传递[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)的数据。 每个将现在讨论反过来。

### <a name="passing-data-through-a-page-constructor"></a>将数据传递通过页构造函数

在导航过程将数据传递到另一页的最简单方法是通过页构造函数参数，下面的代码示例中所示：

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

此代码将创建`MainPage`实例时，传入的当前日期和时间 ISO8601 格式，这包装在[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)实例。

`MainPage`实例收到的数据通过构造函数参数，如下面的代码示例中所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

数据然后通过设置显示在页面[ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)属性，如以下屏幕截图中所示：

![](hierarchical-images/passing-data-constructor.png "页构造函数传递的数据")

### <a name="passing-data-through-a-bindingcontext"></a>BindingContext 间传递数据

在导航过程将数据传递到另一页的替代方法是通过设置新页[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)到的数据，如下面的代码示例中所示：

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

此代码将设置[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)的`SecondPage`到实例`Contact`实例，然后导航到`SecondPage`。

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

下面的代码示例演示如何在 C# 中完成的数据绑定：

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

数据然后页面上显示的一系列[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控件，如以下屏幕截图中所示：

![](hierarchical-images/passing-data-bindingcontext.png "通过 BindingContext 传递的数据")

若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>操作导航堆栈

[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)属性可公开[ `NavigationStack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/)可以从中获取导航堆栈中的页的属性。 虽然 Xamarin.Forms 维持导航堆栈中，对访问`Navigation`属性提供[ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/)和[ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/)堆栈操作的方法是插入的方法页或删除它们。

[ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/)方法之前指定一个现有页面上，导航堆栈中插入指定的页，如下面的关系图中所示：

![](hierarchical-images/insert-page-before.png "在导航堆栈中插入页面")

[ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/)方法在下图中所示从在导航堆栈中移除指定的页：

![](hierarchical-images/remove-page.png "从导航堆栈中删除页面")

这些方法启用自定义导航体验，如将替换为新的页上，以下成功登录的登录页。 下面的代码示例演示此方案：

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

假设用户的凭据是否正确，`MainPage`实例插入到当前页之前导航堆栈。 [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/)方法然后删除导航堆栈中，从当前页与`MainPage`成为活动页面上的实例。

## <a name="summary"></a>摘要

这篇文章演示了如何使用[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)类来执行导航堆栈中的页。 此类提供能够向前和向后，根据需要浏览页中，用户所在的层次结构导航体验。 此类将导航实现为 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 对象的后进先出 (LIFO) 堆栈。


## <a name="related-links"></a>相关链接

- [页导航](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [分层 （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [如何在 Xamarin.Forms （Xamarin 大学视频） 示例中的屏幕流中创建一个符号](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [如何在 Xamarin.Forms （Xamarin 大学视频） 中的屏幕流中创建一个符号](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)
