---
title: Xamarin.Forms 选项卡式的页面
description: Xamarin.Forms TabbedPage 包含一系列选项卡和较大的详细信息区域，与每个选项卡加载到详细信息区域的内容。 本文演示如何使用您不要将 TabbedPage 页面的集合中导航。
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 3eb978780222da2050fc91dfa41c68ef4bd3b6f4
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996290"
---
# <a name="xamarinforms-tabbed-page"></a>Xamarin.Forms 选项卡式的页面

_Xamarin.Forms TabbedPage 包含一系列选项卡和较大的详细信息区域，与每个选项卡加载到详细信息区域的内容。本文演示如何使用您不要将 TabbedPage 页面的集合中导航。_

## <a name="overview"></a>概述

下面的屏幕截图演示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)每个平台上：

![](tabbed-page-images/tab1.png "TabbedPage 示例")

下面的屏幕截图重点介绍每个平台上的选项卡格式：

![](tabbed-page-images/tabbedpage-components.png "TabbedPage 选项卡组件")

布局[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，其选项卡，并依赖于该平台：

- 在 iOS 上，选项卡的列表显示在屏幕的底部，在详细信息区域上方。 每个选项卡还包含图标图像应是 30 倍的正常解析透明度具有 30 PNG、 高分辨率的 60 x 60 和 90 x 90 适用于 iPhone 6 Plus 解决方法。 如果有五个选项卡*详细*选项卡将出现，这可用于访问其他选项卡。 有关加载 Xamarin.Forms 应用程序中的映像的详细信息，请参阅[处理图像](~/xamarin-forms/user-interface/images.md)。 有关图标要求的详细信息，请参阅[创建选项卡式应用程序](~/ios/user-interface/controls/creating-tabbed-applications.md)。

    > [!NOTE]
  > 请注意， `TabbedRenderer` for iOS 的可重写`GetIcon`方法，可以用来从指定的数据源加载选项卡图标。 此重写就可以使用 SVG 图像以图标形式在`TabbedPage`。 此外，可以提供选定和未选定版本的图标。

- 在 Android 上，在屏幕顶部显示默认情况下的选项卡的列表和详细信息区域低于。 但是，选项卡列表可以移动到具有平台特定屏幕的底部。 有关详细信息，请参阅[设置 TabbedPage 工具栏位置和颜色](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar)。

    > [!NOTE]
  > 请注意，当在 Android 上使用 AppCompat，每个选项卡还将显示一个图标。 此外， `TabbedPageRenderer` for Android AppCompat 的可重写`SetTabIcon`方法，可以用来加载的自定义选项卡图标`Drawable`。 此重写就可以使用 SVG 图像以图标形式在`TabbedPage`。

- 在 Windows 平板电脑外形规格，选项卡始终不可见，并且用户需要对向轻扫取 （或右键单击，如果它们的附加是鼠标） 若要查看中的选项卡`TabbedPage`（如下所示）。

![](tabbed-page-images/windows-tabs.png "在 Windows 上的 TabbedPage 选项卡")

## <a name="creating-a-tabbedpage"></a>创建您不要将 TabbedPage

两种方法可用于创建[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

- [填充](#Populating_a_TabbedPage_with_a_Page_Collection) [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)的子集合[ `Page` ](xref:Xamarin.Forms.Page)对象，如集合[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例。
- [将分配](#Populating_a_TabbedPage_with_a_Template)收藏[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)属性和分配[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)到[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)属性返回的页集合中的对象。

这两种方法[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)在用户选择每个选项卡将显示每一页。

> [!NOTE]
> 我们建议[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)应填充了[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)并[ `ContentPage`](xref:Xamarin.Forms.ContentPage)仅限实例。 这将有助于确保在所有平台上一致的用户体验。

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>填充您不要将 TabbedPage 页面集合

以下 XAML 代码示例所示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)构造通过填充了一系列子[ `Page` ](xref:Xamarin.Forms.Page)对象：

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

下面的代码示例显示等效[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) C# 创建的：

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

[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)具有两个子填充[ `Page` ](xref:Xamarin.Forms.Page)对象。 第一个子级是[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例和第二个选项卡是[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)包含`ContentPage`实例。

> [!NOTE]
> [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)不支持 UI 虚拟化。 因此，性能可能会影响如果`TabbedPage`包含太多的子元素。

下面的屏幕截图演示`TodayPage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例，而会显示在*今天*选项卡：

![](tabbed-page-images/today-page.png "在您不要将 TabbedPage ContentPage")

选择*计划*选项卡显示`SchedulePage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例，包装在[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)实例，并在所示以下屏幕截图：

![](tabbed-page-images/schedule-page.png "在您不要将 TabbedPage NavigationPage")

有关布局的信息[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，请参阅[执行导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

> [!NOTE]
> 而是可以接受放置[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)到[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，不建议将`TabbedPage`到`NavigationPage`。 这是因为，在 iOS 上，`UITabBarController`始终充当的包装器`UINavigationController`。 有关详细信息，请参阅[结合使用视图控制器接口](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html)iOS 开发人员库中。

#### <a name="navigation-inside-a-tab"></a>选项卡内的导航

可以通过调用从第二个选项卡执行导航[ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*)方法[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)属性的[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例，下面的代码示例中所示：

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

这会将 `UpcomingAppointmentsPage` 实例推送到导航堆栈中，在堆栈中，它成为活动页。 以下屏幕截图所示：

![](tabbed-page-images/navigationpage.png "选项卡内的导航")

有关执行导航使用详细信息[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)类，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>填充您不要将 TabbedPage 使用模板

以下 XAML 代码示例所示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)构建的分配[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)到[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)属性返回的页集合中的对象：

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

[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)通过设置使用数据填充[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)代码隐藏文件的构造函数中的属性：

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

下面的代码示例显示等效[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) C# 创建的：

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

每个选项卡显示[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)使用一系列[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)并[ `Label` ](xref:Xamarin.Forms.Label)实例显示为选项卡的数据。以下屏幕截图显示的内容*Tamarin*选项卡：

![](tabbed-page-images/tab3.png "填充您不要将 TabbedPage 使用模板")

然后选择另一个选项卡将显示该选项卡的内容。

> [!NOTE]
> [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)不支持 UI 虚拟化。 因此，性能可能会影响如果`TabbedPage`包含太多的子元素。

有关详细信息[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，请参阅[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)的 Charles Petzold 的 Xamarin.Forms 书籍。

## <a name="summary"></a>总结

本文演示了如何使用您不要将 TabbedPage 页面的集合中导航。 Xamarin.Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)包含具有内容载入的详细信息区域的每个选项卡的选项卡和较大的详细信息区域的列表。


## <a name="related-links"></a>相关链接

- [页类型](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
