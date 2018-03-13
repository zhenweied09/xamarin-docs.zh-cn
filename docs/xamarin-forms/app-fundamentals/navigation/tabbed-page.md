---
title: "选项卡式的页面"
description: "Xamarin.Forms TabbedPage 包含的选项卡和更大的详细信息区域，列表带有入详细信息区域加载内容的每个选项卡。 本文演示如何使用 TabbedPage 若要浏览的页的集合。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 2ec783b6963fc4ae14166ebf1e56bf8a802ba8b4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="tabbed-page"></a>选项卡式的页面

_Xamarin.Forms TabbedPage 包含的选项卡和更大的详细信息区域，列表带有入详细信息区域加载内容的每个选项卡。本文演示如何使用 TabbedPage 若要浏览的页的集合。_

## <a name="overview"></a>概述

下面的屏幕截图演示[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)每个平台上：

![](tabbed-page-images/tab1.png "TabbedPage 示例")

以下屏幕截图重点介绍每个平台上的选项卡格式：

![](tabbed-page-images/tabbedpage-components.png "TabbedPage 标签组件")

布局[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)，和其选项卡，依赖于该平台：

- 在 iOS 上的选项卡列表显示在该屏幕的底部和详细信息区域高于。 每个卡还包含了图标图像应为 x 的正常解析的透明度 30 PNG 30、 60 x 60 作为高分辨率和 90 x 90 iPhone 6 Plus 解析。 如果有五个以上的选项卡，*详细*选项卡将出现，这可以用于访问其他选项卡。 有关加载在 Xamarin.Forms 应用程序中的映像的详细信息，请参阅[处理映像](~/xamarin-forms/user-interface/images.md)。 有关图标要求的详细信息，请参阅[创建选项卡式应用程序](~/ios/user-interface/controls/creating-tabbed-applications.md)。

    > [!NOTE]
  > 请注意， `TabbedRenderer` iOS 具有可重写`GetIcon`方法，可以用来从指定的源加载选项卡图标。 此替代使能够上以图标形式使用 SVG 图像`TabbedPage`。 此外，可以提供选定和未选定版本的一个图标。

- 在 Android 上，该屏幕的顶部显示的选项卡列表和详细信息区域低于。 选项卡名称自动首字母大写，并且如果没有过多，无法容纳在一个屏幕上，用户可以向上滚动选项卡的集合。

    > [!NOTE]
  > 请注意，当在 Android 上使用 AppCompat，每个选项卡也将显示一个图标。 此外， `TabbedPageRenderer` Android AppCompat 具有可重写`SetTabIcon`方法，可以用来加载来自自定义选项卡图标`Drawable`。 此替代使能够上以图标形式使用 SVG 图像`TabbedPage`。

- 在 Windows Phone 上的选项卡列表将显示在该屏幕的顶部和低于以下值的详细信息区域。 如果有过多，无法容纳在一个屏幕上，名称自动转换为小写，和用户选项卡可以滚动选项卡的集合。
- Windows 平板电脑窗体因素的影响，在选项卡并不总是可见，并且用户需要对轻扫向 （或右键单击，如果它们的附加是鼠标） 若要查看的选项卡的`TabbedPage`（如下所示）。

![](tabbed-page-images/windows-tabs.png "在 Windows 上的 TabbedPage 选项卡")

## <a name="creating-a-tabbedpage"></a>创建 TabbedPage

两种方法可以用于创建[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/):

- [填充](#Populating_a_TabbedPage_with_a_Page_Collection) [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)的子集合[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)对象，如一套[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)实例。
- [分配](#Populating_a_TabbedPage_with_a_Template)集合与[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/)属性和分配[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)到[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/)属性以返回页集合中的对象。

这两种方法， [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)在用户选择每个选项卡将显示每个页面。

> [!NOTE]
> 具有建议[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)应使用填充[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)和[ `ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)仅限实例。 这将有助于确保在所有平台之间一致的用户体验。

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>填充 TabbedPage 与页面集合

下面的 XAML 代码示例演示[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)通过填充的子集合构造[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)对象：

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" Icon="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

下面的代码示例演示的等效[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) C# 创建的：

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    var navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.Icon = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)填入两个子[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)对象。 第一个子级是[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)实例，，第二个选项卡是[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)包含`ContentPage`实例。

> [!NOTE]
> [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)不支持 UI 虚拟化。 因此，性能可能会影响如果`TabbedPage`包含太多的子元素。

下面的屏幕截图演示`TodayPage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)实例，而会显示在*今天*选项卡：

![](tabbed-page-images/today-page.png "在 TabbedPage 内容页")

选择*计划*选项卡显示`SchedulePage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)实例，包装在[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)实例，和中所示以下屏幕快照：

![](tabbed-page-images/schedule-page.png "在 TabbedPage NavigationPage")

有关的布局信息[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)，请参阅[执行导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

> [!NOTE]
> 可接受放置时[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)到[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)，不建议将`TabbedPage`到`NavigationPage`。 这是因为，在 iOS 上`UITabBarController`始终作为包装`UINavigationController`。 有关详细信息，请参阅[组合视图控制器界面](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html)iOS 开发人员库中。

#### <a name="navigation-inside-a-tab"></a>选项卡内的导航

可通过调用从第二个选项卡上执行导航[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/)方法[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)属性[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)实例，如下面的代码示例中所示：

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

这会将 `UpcomingAppointmentsPage` 实例推送到导航堆栈中，在堆栈中，它成为活动页。 以下屏幕截图所示：

![](tabbed-page-images/navigationpage.png "选项卡内的导航")

有关执行导航使用[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)类，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>填充使用模板 TabbedPage

下面的 XAML 代码示例演示[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)构造通过分配[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)到[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/)属性以返回页集合中的对象：

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage">
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" Icon="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)通过设置情况下用数据填充[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/)的代码隐藏文件中的构造函数中的属性：

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

下面的代码示例演示的等效[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) C# 创建的：

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() => {
      var nameLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage {
        Icon = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children = {
            nameLabel,
            image,
            new StackLayout {
              Padding = new Thickness (50, 10),
              Children = {
                new StackLayout {
                  Orientation = StackOrientation.Horizontal,
                  Children = {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

每个选项卡显示[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)使用一系列[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)和[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例以显示选项卡的数据。以下屏幕快照显示的内容*Tamarin*选项卡：

![](tabbed-page-images/tab3.png "填充使用模板 TabbedPage")

然后选择另一个选项卡将显示该选项卡的内容。

> [!NOTE]
> [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)不支持 UI 虚拟化。 因此，性能可能会影响如果`TabbedPage`包含太多的子元素。

有关详细信息[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)，请参阅[章 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) Charles Petzold Xamarin.Forms 书籍。

## <a name="summary"></a>摘要

这篇文章演示了如何使用 TabbedPage 浏览的页的集合。 Xamarin.Forms [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)的选项卡和更大的详细信息区域，列表包含带有加载到详细信息区域的内容每个选项卡。


## <a name="related-links"></a>相关链接

- [页类型](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)
