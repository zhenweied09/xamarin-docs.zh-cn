---
title: "单元格的外观"
description: "浏览更多用于同时利用 ListView 方便地显示数据的选项。"
ms.topic: article
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 551a0de8cd4965815c67a795fb5723d4261a173c
ms.sourcegitcommit: cc38757f56aab53bce200e40f873eb8d0e5393c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2018
---
# <a name="cell-appearance"></a>单元格的外观

ListView 显示可滚动列表，可以使用自定义`ViewCell`s。 `ViewCells` 可用于显示文本和图像、 指示 true/false 状态和接收用户输入。

有两种方法来获取所需从 ListView 单元格的外观：

- **[自定义内置的单元格](#Built_in_Cells)** &ndash;可以方便实施和更好的性能，代价是牺牲可定制性。
- **[创建自定义单元格](#customcells)** &ndash;更控制最终结果，但有可能成为性能问题，如果实现不正确。

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>内置的单元格
Xamarin.Forms 附带了内置的单元格适用于许多简单应用程序：

- **TextCell** &ndash;用于显示文本
- **ImageCell** &ndash;用于显示文本的映像。

两个其他单元格， [ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell)和[ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell)为可用，但它们不常用于`ListView`。 请参阅[ `TableView` ](~/xamarin-forms/user-interface/tableview.md)有关这些单元的详细信息。

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) 是用于显示文本，（可选） 与为详细信息文本第二行的单元格。

TextCells 呈现为本机控件在运行时，因此性能是非常好向自定义比较`ViewCell`。 TextCells 是可自定义，允许您设置：

- `Text` &ndash; 在第一行，用大字体显示文本。
- `Detail` &ndash; 较小的字体中的第一行下方显示的文本。
- `TextColor` &ndash; 文本颜色。
- `DetailColor` &ndash; 详细信息文本的颜色

![](customizing-cell-appearance-images/text-cell-default.png "默认 TextCell 示例")

![](customizing-cell-appearance-images/text-cell-custom.png "自定义的 TextCell 示例")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/)如`TextCell`、 可以用于显示文本和辅助的详细信息文本和它通过使用每个平台的本机控件提供优良的性能。 `ImageCell` 不同于`TextCell`在于它会向文本的左侧显示图像。

`ImageCell` 在需要以显示与 visual 方面，如联系人或电影的列表的数据的列表时，则非常有用。 ImageCells 是可自定义，允许您设置：

- `Text` &ndash; 在第一行，用大字体显示文本
- `Detail` &ndash; 显示下较小的字体中的第一行文本
- `TextColor` &ndash; 文本颜色
- `DetailColor` &ndash; 详细信息文本的颜色
- `ImageSource` &ndash; 文本旁边显示的图像

请注意，当面向 Windows Phone 8.1，`ImageCell`不会默认情况下缩放图像。 此外，请注意，Windows Phone 8.1 为默认情况下的主文本中的相同的颜色和字体是唯一的平台的详细信息显示文本。 Windows Phone 8.0 呈现`ImageCell`，如下所示：

![](customizing-cell-appearance-images/image-cell-default.png "默认 ImageCell 示例")

![](customizing-cell-appearance-images/image-cell-custom.png "自定义的 ImageCell 示例")

<a name="customcells" />

## <a name="custom-cells"></a>自定义单元格
当内置的单元格未提供必需的布局时，自定义单元格实现必需的布局。 例如，你可能想要向单元格显示两个标签都有相等的权重。 A`LabelCell`将不足因为`LabelCell`具有较小的一个标签。 大多数的单元格自定义项添加其他只读数据 （如其他标签、 图像或其他显示信息）。

所有自定义单元格必须派生自[ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，类型使用内置的单元格的所有相同的基类。

Xamarin.Forms 2 引入一个新[缓存行为](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)上`ListView`控件可对其进行设置，以提高某些类型的自定义单元格的滚动性能。

这是自定义单元格的一个示例：

![](customizing-cell-appearance-images/custom-cell.png "自定义单元格示例")

### <a name="xaml"></a>XAML
以下是要创建上述布局的 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

上面的 XAML 执行大量操作。 让我们将分解：

- 自定义单元格嵌套在`DataTemplate`，这是内部`ListView.ItemTemplate`。 这是与使用任何其他单元格相同的进程。
- `ViewCell` 是一种自定义单元格。 子`DataTemplate`元素必须是或派生自类型`ViewCell`。
- 请注意该内部`ViewCell`，由管理布局`StackLayout`。 此布局使得我们可以自定义的背景色。 请注意的任何属性`StackLayout`，它是可绑定可以绑定自定义单元格，内部，不过，未在此处显示。

### <a name="cnum"></a>C&num;

在 C# 中指定自定义单元格是有点比详细 XAML 等效项。 让我们来实际操作一下：

首先，定义自定义单元格类，`ViewCell`作为基类：

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

在你构造函数中的页`ListView`，设置的 ListView`ItemTemplate`到新的属性`DataTemplate`:

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

请注意的构造函数`DataTemplate`接受一个类型。 Typeof 运算符获取的 CLR 类型`CustomCell`。

<a name="binding-context-changes" />

### <a name="binding-context-changes"></a>绑定上下文更改

当绑定到自定义单元格类型的[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例显示的 UI 控件`BindableProperty`值应使用[ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/)替代以将设置要在中显示的数据每个单元格，而不是单元格构造函数，如下面的代码示例中所示：

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null) {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

[ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/)重写时将会调用[ `BindingContextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.BindingContextChanged/)事件激发，以响应的值[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)属性更改。 因此，当`BindingContext`发生变化，显示的 UI 控件[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)值应设置其数据。 请注意，`BindingContext`应检查是否有`null`值，因为这可以通过 Xamarin.Forms 设置，进行垃圾回收，这反过来将导致`OnBindingContextChanged`重写调用。

或者，可以 UI 控件绑定到[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)实例，以显示它们的值，因此不再需要重写`OnBindingContextChanged`方法。

> [!NOTE]
> 在重写`OnBindingContextChanged`，确保基类的`OnBindingContextChanged`方法调用，以便已注册的委托接收`BindingContextChanged`事件。

在 XAML 中，绑定到数据的自定义单元格类型可以实现下面的代码示例中所示：

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

这会将绑定`Name`， `Age`，和`Location`中的可绑定属性`CustomCell`实例，为`Name`， `Age`，和`Location`基础集合中每个对象的属性。

在 C# 中等效的绑定下面的代码示例所示：

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView {
    ItemsSource = people,
    ItemTemplate = customCell
};
```

在 iOS 和 Android，如果[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)回收元素和自定义单元格使用自定义呈现器，自定义呈现器必须正确实施属性更改通知。 当单元格会重复使用其属性值将发生更改，当绑定上下文更新到的一个可用的单元格中，与`PropertyChanged`引发的事件。 有关详细信息，请参阅[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。 有关单元格回收的详细信息，请参阅[缓存策略](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)。

## <a name="related-links"></a>相关链接

- [内置单元格 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [自定义单元格 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [绑定上下文更改 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BindingContextChanged)
