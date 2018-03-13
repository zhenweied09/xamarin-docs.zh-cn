---
title: "列表外观"
description: "自定义使用标头、 页脚、 组和变量高度单元格的 Listview。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 4e849cc034b8c77b84d1be8cb31cc1206f203ef6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="list-appearance"></a>列表外观

`ListView` 包含用于控制表示法的总体列表中，除了基础选项`ViewCell`s。 选项包括：

- [**分组**](#Grouping) &ndash; ListView 中更易于导航和改进的组织的项进行分组。
- [**页眉和页脚**](#Headers_and_Footers) &ndash;开头和末尾具有其他项滚动视图中显示的信息。
- [**行分隔符**](#Row_Separators) &ndash;显示或隐藏项之间的分隔线。
- [**变量高度行**](#Row_Heights) &ndash;默认情况下的所有行都都高度相同，但这可以更改为允许与不同的高度，要显示的行。

<a name="Grouping" />

## <a name="grouping"></a>分组
通常情况下，大型数据集可能变得难以操作时不断滚动列表中显示。 启用分组可以更好地组织内容和激活特定于平台的控件，轻松导航数据通过提高在这些情况下的用户体验。

当激活分组`ListView`，为每个组添加一个标题行。

若要启用分组：

- 创建列表的列表 （正在的元素列表的每个组中的组的列表）。
- 设置`ListView`的`ItemsSource`到该列表。
- 设置`IsGroupingEnabled`为 true。
- 设置[ `GroupDisplayBinding` ](http://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupDisplayBinding/)要绑定到正在为组的标题使用的组的属性。
- [可选]设置[ `GroupShortNameBinding` ](http://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupShortNameBinding/)要绑定到作为组的短名称正在使用的组的属性。 短名称用于跳转列表 （iOS、 Windows Phone 上的磁贴网格上的 rigt 端列）。

首先创建组的类：

```csharp
public class PageTypeGroup : List<PageModel>
    {
        public string Title { get; set; }
        public string ShortName { get; set; } //will be used for jump lists
        public string Subtitle { get; set; }
        private PageTypeGroup(string title, string shortName)
        {
            Title = title;
            ShortName = shortName;
        }

        public static IList<PageTypeGroup> All { private set; get; }
    }
```

在上面的代码中，`All`是将赋予我们 ListView 用作绑定源的列表。 `Title` 和`ShortName`是将用于组标题的属性。

在此阶段，`All`为空列表。 添加静态构造函数，以便在程序启动时将填充列表：

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alfa", "A"){
                new PageModel("Amelia", "Cedar", new switchCellPage(),""),
                new PageModel("Alfie", "Spruce", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ava", "Pine", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Archie", "Maple", new switchCellPage(), "grapefruit.jpg")
            },
            new PageTypeGroup ("Bravo", "B"){
                new PageModel("Brooke", "Lumia", new switchCellPage(),""),
                new PageModel("Bobby", "Xperia", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Bella", "Desire", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ben", "Chocolate", new switchCellPage(), "grapefruit.jpg")
            }
        }
        All = Groups; //set the publicly accessible list
}
```

在上述代码中我们可以调用`Add`元素`groups`，这是类型的实例`PageTypeGroup`。 这是可能因为`PageTypeGroup`继承自`List<PageModel>`。 这是列表的一个示例的列表模式上文中记下。

下面是用于显示分组的列表 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="DemoListView.GroupingViewPage"
    <ContentPage.Content>
        <ListView  x:Name="GroupedView"
        GroupDisplayBinding="{Binding Title}"
        GroupShortNameBinding="{Binding ShortName}"
        IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                     Detail="{Binding Subtitle}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

这将产生以下结果：

![](customizing-list-appearance-images/grouping-depth.png "ListView 分组示例")

请注意，我们具有：

- 设置`GroupShortNameBinding`到`ShortName`我们组类中定义的属性
- 设置`GroupDisplayBinding`到`Title`我们组类中定义的属性
- 设置`IsGroupingEnabled`为 true
- 更改`ListView`的`ItemsSource`到分组列表

### <a name="customizing-grouping"></a>自定义分组
现在，我们已了解如何实现基本分组在 ListView 中，让我们了解如何自定义组标题的显示。

类似于如何`ListView`具有`ItemTemplate`用于定义如何显示行`ListView`具有`GroupHeaderTemplate`。 这是使用自定义的组标头模板的 ListView 从上面的示例：

![](customizing-list-appearance-images/grouping-depth.png "使用自定义 GroupHeaderTemplate 的 ListView")


下面介绍了如何完成该设计在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="DemoListView.GroupingViewPage">
    <ContentPage.Content>
        <ListView x:Name="GroupedView"
         GroupDisplayBinding="{Binding Title}"
         GroupShortNameBinding="{Binding ShortName}"
         IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                    Detail="{Binding Subtitle}"
                    TextColor="#f35e20"
                    DetailColor="#503026" />
                </DataTemplate>
            </ListView.ItemTemplate>
            <!-- Group Header Customization-->
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                    Detail="{Binding ShortName}"
                    TextColor="#f35e20"
                    DetailColor="#503026" />
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            <!-- End Group Header Customization
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

<a name="Headers_and_Footers" />

## <a name="headers-and-footers"></a>页眉和页脚
它有可能 ListView 向向下滚动页眉和页脚显示列表中的元素。 页眉和页脚可以是文本或更复杂的布局的字符串。 请注意，这是独立于[部分组](#Grouping)。

你可以设置`Header`和/或`Footer`到一个简单的字符串值，也可以将它们设置为更复杂的布局。
也有`HeaderTemplate`和`FooterTemplate`属性让你创建更复杂的页眉和页脚的布局支持数据绑定。

若要创建简单的页眉/页脚，仅将页眉或页脚属性设置为你想要显示的文本。 在代码中：

```csharp
ListView HeaderList = new ListView() {
    Header = "Header",
    Footer = "Footer"
    };
```

在 XAML 中：

```xaml
<ListView  x:Name="HeaderList"  Header="Header" Footer="Footer"></ListView>
```

![](customizing-list-appearance-images/header-default.png "页眉和页脚的 ListView")

若要创建自定义的页眉和页脚，定义页眉和页脚视图：

```xaml
<ListView.Header>
    <StackLayout Orientation="Horizontal">
        <Label Text="Header"
        TextColor="Olive"
        BackgroundColor="Red" />
    </StackLayout>
</ListView.Header>
<ListView.Footer>
    <StackLayout Orientation="Horizontal">
        <Label Text="Footer"
        TextColor="Gray"
        BackgroundColor="Blue" />
    </StackLayout>
</ListView.Footer>
```

![](customizing-list-appearance-images/header-custom.png "自定义的页眉和页脚的 ListView")

<a name="Row_Separators" />

## <a name="row-separators"></a>行分隔符
分隔线显示之间`ListView`默认情况下，在 iOS 和 Android 的元素。 Windows Phone 不支持分隔符，每个行数该平台 UX 准则。 如果你想要隐藏在 iOS 和 Android 的分隔符行，设置`SeparatorVisibility`上你 ListView 的属性。 选项`SeparatorVisibility`是：

* **默认**-iOS 和 Android 上显示一条分隔线。
* **无**-隐藏在所有平台上的分隔符。

默认可见性：

C#：

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "带有默认行分隔符的 ListView")

None:

C#：

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "不带行分隔符的 ListView")

你还可以设置通过分隔符线条的颜色`SeparatorColor`属性：

C#：

```csharp
SepratorDemoListView.SeparatorColor = Color.Green;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "带有绿色行分隔符的 ListView")

> [!NOTE]
> 设置这些属性在 Android 上在加载后`ListView`会产生较大的性能损失。

<a name="Row_Heights" />

## <a name="row-heights"></a>行高
默认情况下，在 ListView 中的所有行都具有高度相同。 ListView 具有可用来更改该行为的两个属性：

- `HasUnevenRows` &ndash; `true`/`false` 值，如果行具有不同的高度设置为`true`。 默认为 `false`。
- `RowHeight` &ndash; 每个高度行时的设置`HasUnevenRows`是`false`。

通过设置可以设置的所有行的高度`RowHeight`属性`ListView`。

### <a name="custom-fixed-row-height"></a>自定义的固定的行高

C#：

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "使用固定的行高度的 ListView")


### <a name="uneven-rows"></a>不均匀的行

如果你想要单独行具有不同的高度，则可以设置`HasUnevenRows`属性`true`。
请注意，行高无需手动设置一次`HasUnevenRows`已设置为`true`，这是因为高度将自动计算通过 Xamarin.Forms。


C#：

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "不均匀的行的 ListView")

### <a name="runtime-resizing-of-rows"></a>运行时调整大小的行

单个`ListView`行可以以编程方式调整大小以在运行时，条件是`HasUnevenRows`属性设置为`true`。 [ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/)方法更新单元格的大小，即使它不当前可见的如下面的代码示例中所示：

```csharp
void OnImageTapped (object sender, EventArgs args)
{
    var image = sender as Image;
    var viewCell = image.Parent.Parent as ViewCell;

    if (image.HeightRequest < 250) {
        image.HeightRequest = image.Height + 100;
        viewCell.ForceUpdateSize ();
    }
}
```

`OnImageTapped`事件处理程序执行以响应[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)在单元格中点击，并会增加的大小`Image`，以便轻松地查看显示的单元格中。

![](customizing-list-appearance-images/dynamic-row-resizing.png "运行时行大小调整的 ListView")

请注意是否过度使用此功能没有强可能存在性能下降。



## <a name="related-links"></a>相关链接

- [分组 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [自定义呈现器视图 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [动态调整大小的行 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/DynamicUnevenListCells/)
- [1.4 的发行说明](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 的发行说明](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
