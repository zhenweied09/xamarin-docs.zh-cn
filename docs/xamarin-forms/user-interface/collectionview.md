---
title: Xamarin.Forms 之间导航
description: CollectionView 是灵活且高性能视图来显示数据使用不同的布局规范的列表。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2018
ms.openlocfilehash: 46ab8169b31624e3862cf14f477bcd6cf8c8f3a3
ms.sourcegitcommit: 408b78dd6eded4696469e316af7922a5991f2211
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53246303"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms 之间导航

![预览](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` 用于呈现数据的列表视图使用不同的布局规范。 它旨在提供更灵活和高性能替代方式`ListView`。 虽然`CollectionView`和`ListView`Api 类似，有一些明显的差异：

- `CollectionView` 有没有单元格的概念。 相反，数据模板用于在列表中定义的数据的每个项的外观。
- `CollectionView` 减少的 API 面`ListView`。 很多属性和事件`ListView`中不存在`CollectionView`。
- `CollectionView` 提供了灵活的布局模式，允许列表或网格中显示垂直或水平地数据。

一个`CollectionView`使用通过设置其`ItemsSource`属性设置为任何实现的集合`IEnumerable`，并将其`ItemTemplate`属性设置为`DataTemplate`，用于定义每个列表项外观。 此外，其`ItemsLayout`属性必须设置为`ItemsLayout`类中，以定义列表的布局规范。

> [!IMPORTANT]
> `CollectionView`当前是早期预览版，并且缺少众多其计划的功能。 此外，API 将更改为实现已完成。

`CollectionView` 现已推出 Xamarin.Forms 4.0 pre1。 但是，它是当前处于实验阶段并仅可以通过添加以下代码行来使用你`AppDelegate`类在 iOS 上，或为你`MainActivity`类在 Android 上，然后才能调用`Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

## <a name="populating-a-collectionview-with-data"></a>填充数据 CollectionView

一个`CollectionView`通过设置使用数据填充其`ItemSource`属性设置为任何实现的集合`IEnumerable`。 项可以在 XAML 中通过初始化中添加`ItemsSource`属性从项的数组：

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> 请注意，`x:Array`元素需要`Type`属性，指示数组中的项的类型。

此外，`CollectionView`通过使用数据绑定来绑定可以使用数据填充其`ItemsSource`属性设置为`IEnumerable`集合。 在 XAML 中此，可以使用`Binding`标记扩展：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

在此示例中，`ItemsSource`将属性数据绑定到`Monkeys`连接的视图模型，它将返回属性`IList<Monkey>`集合。 下面的代码示例演示`Monkey`类，该类包含四个属性：

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

## <a name="providing-a-view-for-the-empty-state"></a>提供空状态视图

当`CollectionView`不含任何数据，若要显示，可以向用户显示合适的消息。 这可以通过设置`CollectionView.EmptyView`属性设置为`object`，将显示由指定的集合`ItemSource`属性为空：

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}">
    <CollectionView.EmptyView>
        <Label Text="No items to display" />
    </CollectionView.EmptyView>
</CollectionView>
```

`EmptyView`属性可以设置为`string`、 绑定或视图中，因此，如果需要，可以包括交互式内容。

此外，`EmptyViewTemplate`属性可以设置为`DataTemplate`将用于设置格式`EmptyView`。

## <a name="defining-list-item-appearance"></a>定义列表项外观

中每一项数据的外观`CollectionView`可以通过设置定义`CollectionView.ItemTemplate`属性设置为`DataTemplate`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
    ...
</CollectionView>
```

在此示例中，`DataTemplate`包含`Grid`与`Image`，并将两个`Label`实例，所有绑定到的属性`Monkey`对象。

有关数据模板的详细信息，请参阅[数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="specifying-a-layout"></a>指定布局

默认情况下，`CollectionView`将各个项显示垂直列表中。 但是，可以使用任何以下布局规范：

- 垂直列表 – 垂直增长时添加新项目时的单个列列表。
- 水平列表-水平变宽，添加新项目时的单个行列表。
- 垂直网格 – 垂直增长时添加新项目时的多列网格。
- 水平网格 – 水平变宽，添加新项目时的多行网格。

可以通过设置指定这些布局`CollectionView.ItemsLayout`属性设置为`ItemsLayout`类，与`ListItemsLayout`类，用于提供列表布局和`GridItemsLayout`类，用于提供一种网格布局。

`ItemsLayout`类定义`Orientation`类型的属性， `ItemsLayoutOrientation`。 `ItemsLayoutOrientation`枚举定义`Vertical`和`Horizontal`成员值。

`ListItemsLayout`类继承自`ItemsLayout`类，并定义静态`VerticalList`和`HorizontalList`成员。 这些成员可用于分别创建垂直或水平列表。 或者，`ListItemsLayout`可以创建实例，指定`ItemsLayoutOrientation`作为自变量的枚举成员。

`GridItemsLayout`类继承自`ItemsLayout`类，并定义`Span`类型的属性， `int`，它表示要在网格中显示的行或列数。 默认值`Span`属性为 1，且其值必须始终为大于或等于 1。

### <a name="vertical-list"></a>垂直列表

默认情况下，`CollectionView`将各个项显示垂直列表布局中。 因此，不需要设置`ItemsLayout`属性，以使用此布局：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

但是，出于完整性的考虑，`CollectionView`可以设置为各个项显示垂直列表中，通过设置其`ItemsLayout`对静态`ListItemsLayout.VerticalList`成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.VerticalList}">
    ...
</CollectionView>
```

或者，也可以通过设置`ItemsLayout`指向的实例的属性`ListItemsLayout`类中，指定`Vertical``ItemsLayoutOrientation`作为自变量的枚举成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

这会导致单个列列表，如添加新项垂直增长时：

[![CollectionView 垂直列表布局](collectionview-images/vertical-list.png "CollectionView 垂直列表布局")](collectionview-images/vertical-list-large.png#lightbox)

### <a name="horizontal-list"></a>水平列表

一个`CollectionView`可以通过设置水平列表中显示其项及其`ItemsLayout`属性设置为静态`ListItemsLayout.HorizontalList`成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.HorizontalList}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

或者，也可以通过设置`ItemsLayout`指向的实例的属性`ListItemsLayout`类中，指定`Horizontal``ItemsLayoutOrientation`作为自变量的枚举成员：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Horizontal</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

这会导致单个行列表，水平变宽，如添加新项：

[![CollectionView 水平列表布局](collectionview-images/horizontal-list.png "CollectionView 水平列表布局")](collectionview-images/horizontal-list-large.png#lightbox)

### <a name="vertical-grid"></a>垂直网格

一个`CollectionView`可以通过设置垂直网格中显示其项及其`ItemsLayout`属性设置为`GridItemsLayout`实例，它的`Orientation`属性设置为`Vertical`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

默认情况下，垂直`GridItemsLayout`将在单个列中显示项。 但是，此示例设置`GridItemsLayout.Span`属性设置为 2。 这会导致两个列网格，如添加新项垂直增长时：

[![CollectionView 垂直网格布局](collectionview-images/vertical-grid.png "CollectionView 垂直网格布局")](collectionview-images/vertical-grid-large.png#lightbox)

### <a name="horizontal-grid"></a>水平网格

一个`CollectionView`可以通过设置水平网格中显示其项及其`ItemsLayout`属性设置为`GridItemsLayout`实例，它的`Orientation`属性设置为`Horizontal`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

默认情况下，水平`GridItemsLayout`将在单个行中显示项。 但是，此示例设置`GridItemsLayout.Span`属性设置为 4。 这会导致水平变宽，添加新项目时的四行网格：

[![CollectionView 水平网格布局](collectionview-images/horizontal-grid.png "CollectionView 水平网格布局")](collectionview-images/horizontal-grid-large.png#lightbox)

## <a name="scrolling"></a>滚动

`CollectionView` 可以向请求的项滚动到视图`ScrollTo`方法。 此方法将滚动到视图的指定索引处的项：

```csharp
_collectionView.ScrollTo(12);
```

或者，此方法的重载可用于指定的项滚动到视图：

```csharp
var viewModel = BindingContext as MonkeysViewModel;
var monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
_collectionView.ScrollTo(monkey);
```

这两个重载允许其他参数来指定，可指示该项所在的组、 项滚动后已完成 （开始、 center、 结束），以及是否要进行动画处理滚动的准确位置。

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
