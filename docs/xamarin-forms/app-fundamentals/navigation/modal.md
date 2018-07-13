---
title: Xamarin.Forms 模式页
description: Xamarin.Forms 支持模式页面。 模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。 本文演示如何导航到模式页面。
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 44aee8500c7de2ae56b59049368d6025ec49cc5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994808"
---
# <a name="xamarinforms-modal-pages"></a>Xamarin.Forms 模式页

_Xamarin.forms 支持模式页面。模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。本文演示如何导航到模式页面。_

本文讨论以下主题：

- [执行导航](#Performing_Navigation)– 将页面推送到模式堆栈，模式堆栈中弹出页面，禁用后退按钮，并对页面过渡效果进行动画处理。
- [将数据传递时导航](#Passing_Data_when_Navigating)– 将数据页的构造函数，通过和传递`BindingContext`。

## <a name="overview"></a>概述

模式页面可以是任一[页](~/xamarin-forms/user-interface/controls/pages.md)Xamarin.Forms 支持的类型。 若要显示模式页面应用程序会将其推送到了模式堆栈，其中它将成为活动页，如以下关系图中所示：

![](modal-images/pushing.png "将页面推送到模式堆栈")

若要返回到以前的页面应用程序会弹出当前页从模式堆栈，而最顶层的页成为活动页，如以下关系图中所示：

![](modal-images/popping.png "弹出模式堆栈中的一页")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>执行导航

可以由任何 [`Page`](xref:Xamarin.Forms.Page) 派生类型上的 [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) 属性公开模式导航方法。 这些方法提供的功能[推送模式页面](#Pushing_Pages_to_the_Modal_Stack)到模式堆栈上，并[弹出模式页面](#Popping_Pages_from_the_Modal_Stack)从模式堆栈。

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)属性也公开[ `ModalStack` ](xref:Xamarin.Forms.INavigation.ModalStack)属性可以从中获取模式堆栈中的模式页面。 但是，在模式导航中没有执行模式堆栈操作或弹出到根页的概念。 这是因为基础平台普遍都不支持这些操作。

> [!NOTE]
> 执行模式页面导航无需具有 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 实例。

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>将页面推送到模式堆栈

若要导航到`ModalPage`需要调用[ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*)方法[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)属性的当前页上，如下面的代码示例所示：

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    ...
    await Navigation.PushModalAsync (detailPage);
  }
}
```

这将导致`ModalPage`实例可以被推送到了模式堆栈，其中它成为活动页，提供已中选择一项[ `ListView` ](xref:Xamarin.Forms.ListView)上`MainPage`实例。 `ModalPage`实例下面的屏幕截图中所示：

![](modal-images/modalpage.png "模式页示例：")

当[ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*)调用时，会发生以下事件：

- 页调用`PushModalAsync`具有其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)重写调用，前提是基础平台不是 Android。
- 要导航到页都有其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)重写调用。
- `PushAsync`任务完成。

但是，这些事件发生的确切顺序是依赖于平台。 有关详细信息，请参阅[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)的 Charles Petzold 的 Xamarin.Forms 书籍。

> [!NOTE]
> 调用[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)并[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)重写不能被视为有保证的页面导航的迹象。 例如，在 iOS 上，`OnDisappearing`在应用程序终止时重写在活动页面上调用。

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>模式堆栈中弹出页面

活动页面可以从堆栈中弹出模式通过按*回*按钮在设备上，而不考虑这是否在设备上的物理按钮或屏幕按钮。

若要以编程方式返回原始页，`ModalPage` 实例必须调用 [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) 方法，如以下代码示例所示：

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

这将导致`ModalPage`模式堆栈中，从最顶层的页成为活动页与要删除的实例。 当[ `PopModalAsync` ](xref:Xamarin.Forms.INavigation.PopModalAsync)调用时，会发生以下事件：

- 页调用`PopModalAsync`具有其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)重写调用。
- 返回到页都有其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)重写调用，前提是基础平台不是 Android。
- `PopModalAsync`任务返回。

但是，这些事件发生的确切顺序是依赖于平台。 有关详细信息，请参阅[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)的 Charles Petzold 的 Xamarin.Forms 书籍。

### <a name="disabling-the-back-button"></a>禁用后退按钮

在 Android 上，用户可以始终通过返回到前一页按标准*回*在设备上的按钮。 如果模式页面需要用户离开页面之前完成独立的任务，必须禁用该应用程序*回*按钮。 这可以通过重写[ `Page.OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed)模式页面上的方法。 有关详细信息请参阅[第 24 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)的 Charles Petzold 的 Xamarin.Forms 书籍。

### <a name="animating-page-transitions"></a>对页面过渡效果进行动画处理

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)属性的每一页还提供了重写的 push 和 pop 方法，包括`boolean`控制是否显示在导航窗格中，一个页面的动画，如下面的代码中所示的参数示例：

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushModalAsync (new DetailPage (), false);
}

async void OnDismissButtonClicked (object sender, EventArgs args)
{
  // Page appearance not animated
  await Navigation.PopModalAsync (false);
}
```

设置`boolean`参数`false`禁用时将参数设置为的页面过渡动画`true`使页面过渡动画，前提是基础平台支持。 但是，缺少此参数的 push 和 pop 方法默认情况下启用动画。

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>导航时传递数据

有时，所需的页导航期间将数据传递给另一页。 实现此目的的两种方法是按页构造函数中，通过传递数据以及通过设置新页面[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的数据。 每个将现在讨论反过来。

### <a name="passing-data-through-a-page-constructor"></a>通过 Page 构造函数传递数据

在导航过程将数据传递到另一个页面的最简单方法是通过页构造函数参数，下面的代码示例中所示：

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

此代码将创建`MainPage`实例，并传入当前日期和时间 ISO8601 格式中。

`MainPage`实例收到的数据通过构造函数参数，如下面的代码示例中所示：

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

数据将通过设置显示在页面[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)属性。

### <a name="passing-data-through-a-bindingcontext"></a>BindingContext 间传递数据

用于导航期间将数据传递到另一页一种替代方法是通过设置新页面[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)到数据，如下面的代码示例中所示：

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    detailPage.BindingContext = e.SelectedItem as Contact;
    listView.SelectedItem = null;
    await Navigation.PushModalAsync (detailPage);
  }
}
```

此代码将设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`DetailPage`实例向`Contact`实例，，然后转到`DetailPage`。

`DetailPage`然后使用数据绑定来显示`Contact`实例数据，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ModalNavigation.DetailPage">
    <ContentPage.Padding>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,40,0,0" />
      </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" FontSize="Medium" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
              ...
            <Button x:Name="dismissButton" Text="Dismiss" Clicked="OnDismissButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

下面的代码示例演示如何在 C# 中实现数据绑定：

```csharp
public class DetailPageCS : ContentPage
{
  public DetailPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var dismissButton = new Button { Text = "Dismiss" };
    dismissButton.Clicked += OnDismissButtonClicked;

    Thickness padding;
    switch (Device.RuntimePlatform)
    {
        case Device.iOS:
            padding = new Thickness(0, 40, 0, 0);
            break;
        default:
            padding = new Thickness();
            break;
    }

    Padding = padding;
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
        dismissButton
      }
    };
  }

  async void OnDismissButtonClicked (object sender, EventArgs args)
  {
    await Navigation.PopModalAsync ();
  }
}
```

数据将显示在页面的一系列[ `Label` ](xref:Xamarin.Forms.Label)控件。

若要深入了解数据绑定，请参阅[数据绑定基本知识](~/xamarin-forms/xaml/xaml-basics/index.md)。

## <a name="summary"></a>总结

本文演示了如何导航到模式页面。 模式页面鼓励用户完成独立任务，在完成或取消该任务之前，不允许导航离开该任务。


## <a name="related-links"></a>相关链接

- [页面导航](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [模式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Modal/)
- [PassingData （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
