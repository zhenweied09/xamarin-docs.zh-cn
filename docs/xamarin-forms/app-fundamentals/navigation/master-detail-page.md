---
title: "主-详细信息页"
description: "Xamarin.Forms MasterDetailPage 是信息的管理两个相关页 – 主控页显示项和在主页上显示项目的详细信息的详细信息页的页。 此文章介绍了如何使用 MasterDetailPage 和信息其页面之间导航。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 9d774870a541630d8c6519f9dfeaeb21cacb98e8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="master-detail-page"></a>主-详细信息页

_Xamarin.Forms MasterDetailPage 是信息的管理两个相关页 – 主控页显示项和在主页上显示项目的详细信息的详细信息页的页。此文章介绍了如何使用 MasterDetailPage 和信息其页面之间导航。_

## <a name="overview"></a>概述

母版页通常显示项的列表，如以下屏幕截图中所示：

[![](master-detail-page-images/masterpage-components.png "Master 页组件")](master-detail-page-images/masterpage-components-large.png#lightbox "Master 页组件")

项的列表的位置是在每个平台上完全相同，并选择某一项将导航到相应的详细信息页。 此外，主控页还提供了导航栏包含可用于导航到的活动的详细信息页的按钮：

- 在 iOS 上的导航栏在页面顶部，并具有按钮，导航到的详细信息页。 此外，还可以母版页向左轻扫到导航的活动的详细信息页。
- 在 Android 上，导航栏在页面顶部，并为的详细信息页中显示标题、 图标和导航按钮。 在中定义的图标`[Activity]`修饰的属性`MainActivity`Android 的特定于平台的项目中的类。 此外，该活动的详细信息页面可以导航到下轻扫左侧母版页点击屏幕上，最右端的详细信息页，通过点击*回*在屏幕底部的按钮。
- 在通用 Windows 平台 (UWP)，导航栏在页面顶部，并具有按钮，导航到的详细信息页。

页上，在主机上选择对应于的项的详细信息页上显示数据，并在以下屏幕截图中显示的详细信息页的主要组件：

![](master-detail-page-images/detailpage-components.png "详细信息页组件")

详细信息页包含导航栏中，其内容是与平台相关：

- 在 iOS 上的导航栏将在页面顶部和显示标题，并具有按钮，返回到主页面中，假设的详细信息页实例包装在[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)实例。 此外，还可以轻扫到右的详细信息页到返回母版页。
- 在 Android 上，导航栏在页面顶部，并显示标题、 图标和一个按钮，返回到主页。 在中定义的图标`[Activity]`修饰的属性`MainActivity`Android 的特定于平台的项目中的类。
- 在 UWP，导航栏在页面顶部和显示标题，并具有按钮，返回到主页。

### <a name="navigation-behavior"></a>导航行为

Master 和详细信息页面之间的导航体验的行为是取决于平台：

- 在 iOS 的详细信息页上*幻灯片*从左侧和的详细信息的左侧的部分作为母版页幻灯片右侧页仍是可见的。
- 在 Android 上，详细信息和主页面是*均*相互。
- 详细信息和主页面个上 UWP，*交换*。

只不过在 iOS 和 Android 母版页母版页纵向模式下，作为具有类似的宽度，因此更多的详细信息页将可见，则将在横向模式中观察到类似的行为。

有关控制导航行为的信息，请参阅[控制详细信息页显示行为](#Controlling_the_Detail_Page_Display_Behavior)。

## <a name="creating-a-masterdetailpage"></a>创建 MasterDetailPage

A [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)包含[ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)存在以下两种类型的属性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，它用于获取和分别设置的 master 和详细信息页。

> [!IMPORTANT]
> A [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)旨在成为根页中，并使用它，因为其他页面类型中的子页可能导致意外和不一致的行为。 此外，它具有建议的母版页[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)应始终为[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)实例和的详细信息页应仅在使用填充[ `TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)， [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)，和`ContentPage`实例。 这将有助于确保在所有平台之间一致的用户体验。

下面的 XAML 代码示例演示[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)设置[ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)属性：

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

下面的代码示例演示的等效[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) C# 创建的：

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

[ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)属性设置为[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)实例。 [ `MasterDetailPage.Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)属性设置为[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)包含`ContentPage`实例。

### <a name="creating-the-master-page"></a>创建 Master 页

下面的 XAML 代码示例显示的声明`MasterPage`对象，通过引用[ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

页组成[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) ，通过设置用在 XAML 中的数据填充其[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)属性的数组`MasterPageItem`实例。 每个`MasterPageItem`定义`Title`， `IconSource`，和`TargetType`属性。

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)分配给[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/)属性，以显示每个`MasterPageItem`。 `DataTemplate`包含[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)组成， [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)和[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)。 [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)显示`IconSource`属性值和[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)显示`Title`属性值，每个`MasterPageItem`。

页都有其[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/)和[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)属性集。 假设的详细信息页具有标题栏，在详细信息页上，将显示图标。 这必须在 iOS 上启用，通过包装中的详细信息页实例来[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)实例。

> [!NOTE]
> [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)页必须具有其[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/)设置属性，或将发生异常。

下面的代码示例演示在 C# 中创建的等效页：

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
        var grid = new Grid { Padding = new Thickness(5, 10) };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

        var image = new Image();
        image.SetBinding(Image.SourceProperty, "IconSource");
        var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
        label.SetBinding(Label.TextProperty, "Title");

        grid.Children.Add(image);
        grid.Children.Add(label, 1, 0);

        return new ViewCell { View = grid };
      }),
      SeparatorVisibility = SeparatorVisibility.None
    };

    Icon = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

以下屏幕快照显示每个平台上的主控页：

![](master-detail-page-images/masterpage.png "Master 页示例")

### <a name="creating-and-displaying-the-detail-page"></a>创建并显示的详细信息页

`MasterPage`实例包含`ListView`属性公开其[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)实例以便`MainPage` [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)实例可以注册事件处理程序来处理[ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/)事件。 这使`MainPage`实例设置[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)属性为表示所选的页`ListView`项。 下面的代码示例显示的事件处理程序：

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.ListView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.ListView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

`OnItemSelected`方法执行下列操作：

- 它检索[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/)从[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)实例，并提供不是`null`，到中存储的页类型的新实例设置的详细信息页`TargetType`属性`MasterPageItem`。 页类型包装在[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)实例以确保通过引用的图标[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)属性`MasterPage`在 iOS 中的详细信息页上显示。
- 中的选定的项[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)设置为`null`以确保没有任何`ListView`选择在项目中的下一次`MasterPage`显示。
- 通过设置向用户显示的详细信息页[ `MasterDetailPage.IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/)属性`false`。 此属性控制是否将显示出 master 或详细信息页。 它应设置为`true`以显示主页上，并对其`false`以显示详细信息页。

下面的屏幕截图演示`ContactPage`后它已被选中在主页上显示的详细信息页：

![](master-detail-page-images/detailpage.png "详细信息页示例")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>控制的详细信息页显示行为

如何[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)管理主页和详细信息页取决于在手机或平板电脑、 设备的方向和的值上运行该应用程序是否[ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/)属性。 此属性确定将如何显示的详细信息页。 可能值有：

- **默认**– 使用平台默认显示的页。
- **Popover** – 的详细信息页涵盖，或部分覆盖母版页。
- **拆分**– 主控页显示在左边和的详细信息页位于右侧。
- **SplitOnLandscape** – 时在设备处于横向使用拆分屏幕。
- **SplitOnPortrait** – 如果设备纵向，则会使用拆分屏幕。

下面的 XAML 代码示例演示如何设置[ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/)属性[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

下面的代码示例演示的等效[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) C# 创建的：

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

但是，值[ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/)属性仅会影响在平板电脑或桌面上运行的应用程序。 始终在手机上运行的应用程序具有*Popover*行为。

## <a name="summary"></a>摘要

这篇文章演示了如何使用[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)和信息其页面之间导航。 Xamarin.Forms`MasterDetailPage`是管理两个页的相关信息 – 主控页显示项和在主页上显示项目的详细信息的详细信息页的页。


## <a name="related-links"></a>相关链接

- [页类型](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)
