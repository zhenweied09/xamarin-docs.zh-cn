---
title: 分层导航
description: 本文演示如何使用 NavigationPage 类在堆栈的后进先出 (LIFO) 页中执行导航。
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: f8f8f9b4e5755e8b1707178fef633321b64e4e94
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994671"
---
# <a name="hierarchical-navigation"></a>分层导航

_NavigationPage 类提供了可以向前和向后，根据需要通过页导航用户所在的分层导航体验。类实现导航作为后进先出 (LIFO) 堆栈的页对象。本文演示如何使用 NavigationPage 类在大量页面中执行导航。_

本文讨论以下主题：

- [执行导航](#Performing_Navigation)– 创建根页、 将页面推送到导航堆栈、 弹出导航堆栈中的页面和页面转换进行动画处理。
- [将数据传递时导航](#Passing_Data_when_Navigating)– 将数据页的构造函数，通过和传递`BindingContext`。
- [操作导航堆栈](#Manipulating_the_Navigation_Stack)– 通过插入或删除页操作堆栈。

## <a name="overview"></a>概述

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

## <a name="summary"></a>总结

本文演示了如何使用[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)类来执行大量页面中导航。 此类提供分层导航体验，用户是可以向前和向后，根据需要通过页进行导航。 此类将导航实现为 [`Page`](xref:Xamarin.Forms.Page) 对象的后进先出 (LIFO) 堆栈。


## <a name="related-links"></a>相关链接

- [页面导航](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [分层 （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [如何在 Xamarin.Forms （Xamarin University 视频） 示例中的屏幕流中创建一个符号](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [如何在 Xamarin.Forms （Xamarin University 视频） 中的屏幕流中创建一个符号](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
