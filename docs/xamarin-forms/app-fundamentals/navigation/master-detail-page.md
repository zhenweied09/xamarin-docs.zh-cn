---
title: Xamarin.Forms 母版-详细信息页
description: Xamarin.Forms MasterDetailPage 是信息的管理两个相关的页面 – 主页面，其中的项，并显示主页面的项目的详细信息的详细信息页的页。 本文介绍如何使用 MasterDetailPage 和其信息的页面之间导航。
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: a3d0edbd933339ee8b8a0a277a4f2493cc8dc70e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997460"
---
# <a name="xamarinforms-master-detail-page"></a>Xamarin.Forms 母版-详细信息页

_Xamarin.Forms MasterDetailPage 是信息的管理两个相关的页面 – 主页面，其中的项，并显示主页面的项目的详细信息的详细信息页的页。本文介绍如何使用 MasterDetailPage 和其信息的页面之间导航。_

## <a name="overview"></a>概述

主页面通常会显示一系列项，如以下屏幕截图中所示：

[![](master-detail-page-images/masterpage-components.png "主机页组件")](master-detail-page-images/masterpage-components-large.png#lightbox "主机页组件")

在每个平台上完全相同的项列表的位置，选择某一项将导航到相应的详细信息页。 此外，主页还提供包含一个按钮，可用于导航到活动详细信息页的导航条：

- 在 iOS 上，导航栏位于页面顶部，并有一个按钮，导航到详细信息页。 此外，还可以母版页向左轻扫到导航活动详细信息页。
- 在 Android 上，导航栏位于页面顶部，并将标题、 图标和导航的按钮显示的详细信息页。 在中定义的图标`[Activity]`修饰的属性`MainActivity`Android 的特定于平台的项目中的类。 此外，活动详细信息页可以导航到母版页向左轻扫，点击屏幕上，最右侧的详细信息页和通过点击*回*屏幕底部的按钮。
- 在通用 Windows 平台 (UWP)，导航栏位于页面顶部，并有一个按钮，导航到详细信息页。

页上，在主机上选择对应于的项详细信息页上显示数据并在下面的屏幕截图中显示的详细信息页的主要组件：

![](master-detail-page-images/detailpage-components.png "详细信息页组件")

详细信息页包含导航栏中，其内容是依赖于平台的：

- 在 iOS 上，导航栏位于页面顶部显示标题，并有一个按钮，返回到主页面，前提是详细信息页实例包装在[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)实例。 此外，还可以轻扫到右侧的详细信息页向返回主页面。
- 在 Android 上，导航栏位于页面顶部，并显示一个标题、 图标和一个按钮，返回到主页面。 在中定义的图标`[Activity]`修饰的属性`MainActivity`Android 的特定于平台的项目中的类。
- 在 UWP 中，导航栏位于页面顶部显示标题，并有一个按钮，返回到主页面。

### <a name="navigation-behavior"></a>导航行为

母版和详细信息页之间导航体验的行为是依赖于平台：

- 在 iOS 上，详细信息页*幻灯片*左侧，和的详细信息的左侧的部分作为母版页幻灯片右侧页仍是可见的。
- 在 Android 上，详细信息和主页面都*重叠*相互。
- UWP 上的详细信息和主页面*交换*。

只不过 iOS 和 Android 上的母版页类似的宽度都作为在纵向模式下，母版页使更多详细信息页将显示，则将在横向模式中观察到类似的行为。

有关如何控制导航行为的信息，请参阅[控制的详细信息页显示行为](#Controlling_the_Detail_Page_Display_Behavior)。

## <a name="creating-a-masterdetailpage"></a>创建 MasterDetailPage

一个[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)包含[ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)并[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)存在以下两种类型的属性[ `Page` ](xref:Xamarin.Forms.Page)，用于获取和分别设置母版和详细信息页。

> [!IMPORTANT]
> 一个[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)旨在成为根页，并使用它，因为子页面中其他页类型可能导致意外和不一致行为。 此外，我们建议的母版页[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)始终应[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例和的详细信息页应只填充了[ `TabbedPage`](xref:Xamarin.Forms.TabbedPage)， [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，和`ContentPage`实例。 这将有助于确保在所有平台上一致的用户体验。

以下 XAML 代码示例所示[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) ，用于设置[ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)并[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)属性：

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

下面的代码示例显示等效[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) C# 创建的：

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

[ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)属性设置为[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例。 [ `MasterDetailPage.Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)属性设置为[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)包含`ContentPage`实例。

### <a name="creating-the-master-page"></a>创建主页面

以下 XAML 代码示例演示的声明`MasterPage`对象，通过引用[ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
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

页面组成[ `ListView` ](xref:Xamarin.Forms.ListView)使用 XAML 中的数据在通过设置填充其[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)属性设置为一个数组`MasterPageItem`实例。 每个`MasterPageItem`定义`Title`， `IconSource`，和`TargetType`属性。

一个[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)分配给[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)属性，以显示每个`MasterPageItem`。 `DataTemplate`包含[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)组成[ `Image` ](xref:Xamarin.Forms.Image)并[ `Label` ](xref:Xamarin.Forms.Label)。 [ `Image` ](xref:Xamarin.Forms.Image)显示`IconSource`属性值，并且[ `Label` ](xref:Xamarin.Forms.Label)显示`Title`属性值，每个`MasterPageItem`。

页都有其[ `Title` ](xref:Xamarin.Forms.Page.Title)并[ `Icon` ](xref:Xamarin.Forms.Page.Icon)属性集。 假设的详细信息页具有标题栏，在详细信息页上，将显示图标。 这必须在 iOS 上启用，通过包装中的详细信息页实例[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)实例。

> [!NOTE]
> [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)页面必须有其[ `Title` ](xref:Xamarin.Forms.Page.Title)设置属性，或将出现异常。

下面的代码示例显示了在 C# 中创建的等效页：

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

以下屏幕截图显示在母版页上每个平台上：

![](master-detail-page-images/masterpage.png "主页面示例")

### <a name="creating-and-displaying-the-detail-page"></a>创建并显示详细信息页

`MasterPage`实例包含`ListView`公开的属性及其[ `ListView` ](xref:Xamarin.Forms.ListView)实例，以便`MainPage` [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)实例可以注册事件处理程序来处理[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件。 这使得`MainPage`实例设置[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)属性设置为表示所选的页面`ListView`项。 下面的代码示例显示了事件处理程序：

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

`OnItemSelected`方法将执行以下操作：

- 它检索[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)从[ `ListView` ](xref:Xamarin.Forms.ListView)实例，并提供它不是`null`，设置为中存储的页类型的新实例的详细信息页`TargetType`属性的`MasterPageItem`。 页类型包装在[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)实例，确保通过引用的图标[ `Icon` ](xref:Xamarin.Forms.Page.Icon)属性`MasterPage`在 iOS 中的详细信息页上显示。
- 中的选定的项[ `ListView` ](xref:Xamarin.Forms.ListView)设置为`null`以确保没有任何`ListView`选择在项目中的下一次`MasterPage`显示。
- 通过设置向用户显示的详细信息页[ `MasterDetailPage.IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented)属性设置为`false`。 此属性控制是否显示 master 或详细信息页。 它应设置为`true`以显示主页上，并对其`false`以显示详细信息页。

下面的屏幕截图演示`ContactPage`详细信息页上之后它已被选中在母版页上, 所示：

![](master-detail-page-images/detailpage.png "详细信息页示例：")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>控制的详细信息页显示行为

如何[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)管理的母版和详细信息页上应用程序还是运行在手机或平板电脑，设备的方向和的值取决于[ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)属性。 此属性确定的详细信息页的显示方式。 可能值有：

- **默认**– 使用平台默认显示的页。
- **弹出框**– 的详细信息页盖住了，或部分覆盖的母版页。
- **拆分**– 主页面显示在左侧和右侧是详细信息页。
- **SplitOnLandscape** – 时在设备处于横向方向使用拆分屏幕。
- **SplitOnPortrait** – 当设备处于纵向方向时使用拆分屏幕。

下面的 XAML 代码示例演示如何设置[ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)上的属性[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

下面的代码示例显示等效[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) C# 创建的：

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

但是，值[ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)属性只影响在平板电脑或桌面上运行的应用程序。 始终在电话上运行的应用程序具有*弹出框*行为。

## <a name="summary"></a>总结

本文演示了如何使用[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)和其信息的页面之间导航。 Xamarin.Forms`MasterDetailPage`是可管理两个页面的相关信息 – 主页面，其中的项，并显示主页面的项目的详细信息的详细信息页面的页面。


## <a name="related-links"></a>相关链接

- [页类型](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage （示例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
