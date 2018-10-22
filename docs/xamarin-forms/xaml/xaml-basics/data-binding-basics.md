---
title: 第 4 部分。 数据绑定基础知识
description: 数据绑定都允许这两个对象链接，以便其中一个中的更改导致更改另一部分中的属性。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 117ddd033faedda871c33ba10c246739309e2e86
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245945"
---
# <a name="part-4-data-binding-basics"></a>第 4 部分。 数据绑定基础知识

_数据绑定都允许这两个对象链接，以便其中一个中的更改导致更改另一部分中的属性。这是一个非常有价值的工具，并可以完全在代码中定义数据绑定，而 XAML 提供快捷方式和方便起见。因此，Xamarin.Forms 中最重要的标记扩展之一绑定。_

## <a name="data-bindings"></a>数据绑定

数据绑定连接属性的两个对象，调用*源*和*目标*。 在代码中，两个步骤是必需的：`BindingContext`目标对象的属性必须设置为源对象和`SetBinding`方法 (通常与结合使用`Binding`类) 必须在要将该属性绑定的目标对象上调用源对象的属性的对象。

目标属性必须是可绑定的属性，这意味着目标对象必须派生自`BindableObject`。 联机 Xamarin.Forms 文档该值指示哪些属性是可绑定属性。 一个属性`Label`如`Text`与可绑定属性相关联`TextProperty`。

在标记中，你必须执行相同的两个步骤所需在代码中，只不过`Binding`标记扩展代替`SetBinding`调用和`Binding`类。

但是，当在 XAML 中定义数据绑定时，有多种方法来设置`BindingContext`目标对象。 有时从代码隐藏文件中，有时使用设置`StaticResource`或`x:Static`标记扩展，并且有时会作为内容`BindingContext`属性元素标记。

使用绑定最常与基础数据模型，通常在 MVVM （模型-视图-视图模型） 的应用程序体系结构、 实现连接程序的视觉对象中所述[第 5 部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)，但可能会出现其他情形。

## <a name="view-to-view-bindings"></a>视图-视图绑定

你可以定义数据绑定，以链接的同一页面上的两个视图的属性。 在这种情况下，设置`BindingContext`的目标对象使用`x:Reference`标记扩展。

下面是包含的 XAML 文件`Slider`和两个`Label`视图，其中之一旋转`Slider`值，后者将显示另一个`Slider`值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider`包含`x:Name`由两个引用的属性`Label`视图使用`x:Reference`标记扩展。

`x:Reference`绑定扩展定义名为的属性`Name`以这种情况下设置为所引用的元素的名称`slider`。 但是，`ReferenceExtension`定义的类`x:Reference`标记扩展还定义`ContentProperty`属性，则为`Name`，这意味着，不明确要求。 仅针对各种，第一个`x:Reference`包括"名称 ="，但第二个不：

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

`Binding`本身的标记扩展可以有多个属性，就像`BindingBase`和`Binding`类。 `ContentProperty`为`Binding`是`Path`，但"路径 ="如果路径中的第一个项，则可以省略标记扩展的一部分`Binding`标记扩展。 第一个示例具有"路径 ="但第二个示例省略它：

```csharp
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

属性可以是在一个行上或分隔为多行：

```csharp
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

执行任何方便。

请注意`StringFormat`在第二个属性`Binding`标记扩展。 Xamarin.Forms 中的绑定不执行任何隐式类型转换，并且如果需要非字符串对象显示为一个字符串，您必须提供的类型转换器或使用`StringFormat`。 在后台，静态`String.Format`方法用于实现`StringFormat`。 这可能是问题，因为.NET 格式规范涉及大括号，还用来分隔标记扩展。 这将创建令人费解 XAML 分析器的风险。 若要避免这种情况，请用单引号引起来将整个的格式设置字符串：

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

下面是正在运行的程序：

[![](data-binding-basics-images/sliderbinding.png "视图-视图绑定")](data-binding-basics-images/sliderbinding-large.png#lightbox "视图-视图绑定 ")

## <a name="the-binding-mode"></a>绑定模式

单一视图中其几个属性可以有数据绑定。 但是，每个视图只能有一个`BindingContext`，因此在该视图上的多个数据绑定必须所有引用同一对象的属性。

向此服务器和其他问题的解决方案涉及`Mode`属性，它被设置为的成员`BindingMode`枚举：

- `Default`
- `OneWay` -值从源传输到目标
- `OneWayToSource` -值从目标传输到源
- `TwoWay` -值之间的传输这两种方式源和目标

下面的程序演示的一个常见用途`OneWayToSource`和`TwoWay`的绑定模式。 四个`Slider`视图旨在控制`Scale`， `Rotate`， `RotateX`，和`RotateY`属性`Label`。 首先，它看起来就像的这四个属性`Label`应为数据绑定目标，因为每个设置了`Slider`。 但是，`BindingContext`的`Label`可以是仅一个对象，并且有四个不同的滑块。

因此，所有绑定中都设置看似向后方式：`BindingContext`的每四个滑块都设置为`Label`，绑定上都设置和`Value`滑块的属性。 通过使用`OneWayToSource`和`TwoWay`模式下，这些`Value`属性可以设置的源属性中，它们是`Scale`， `Rotate`， `RotateX`，和`RotateY`属性`Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

对其中三个绑定`Slider`视图是`OneWayToSource`，这意味着，`Slider`值的属性中将导致更改其`BindingContext`，即`Label`名为`label`。 这三种`Slider`视图导致对更改`Rotate`， `RotateX`，和`RotateY`属性`Label`。

但是，对于绑定`Scale`属性是`TwoWay`。 这是因为`Scale`属性具有默认值为 1，并且使用`TwoWay`绑定原因`Slider`初始价值，若要设置在 1 而不是 0。 如果该绑定`OneWayToSource`、`Scale`最初会属性设置为从 0`Slider`默认值。 `Label`将不可见，并且这可能会导致某些混淆给用户。

 [![](data-binding-basics-images/slidertransforms.png "向后绑定")](data-binding-basics-images/slidertransforms-large.png#lightbox "前后绑定")

## <a name="bindings-and-collections"></a>绑定和集合

执行任何操作演示如何将的 XAML 和数据绑定优于模板化`ListView`。

`ListView` 定义`ItemsSource`类型的属性`IEnumerable`，而且它将显示该集合项。 这些项可以是任何类型的对象。 默认情况下，`ListView`使用`ToString`的每个项可显示该项目的方法。 有时这是只是你想得到，但在许多情况下，`ToString`返回仅对象的完全限定类名。

但是中的项`ListView`集合可以显示任何所需的使用方式*模板*，涉及到派生自的类`Cell`。 模板克隆的每个项`ListView`，并将设置要在模板的数据绑定被传输到单独的克隆。

通常，你将需要创建使用这些项目的自定义单元格`ViewCell`类。 此过程是在代码中，有点混乱，但在 XAML 中它将成为非常简单。

包括在 XamlSamples 项目是一个名为类`NamedColor`。 每个`NamedColor`对象具有`Name`和`FriendlyName`类型的属性`string`，和一个`Color`类型的属性`Color`。 此外，`NamedColor`具有 141 静态只读字段的类型`Color`Xamarin.Forms 中定义的颜色对应`Color`类。 静态构造函数创建`IEnumerable<NamedColor>`集合，其中包含`NamedColor`对象对应于这些静态字段，并将其分配给其公共静态`All`属性。

设置静态`NamedColor.All`属性`ItemsSource`的`ListView`轻松使用`x:Static`标记扩展：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

结果显示建立的项是真正的类型`XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "绑定到集合")](data-binding-basics-images/listview1-large.png#lightbox "绑定到集合")

它不是多的信息，但`ListView`是可滚动和可选择。

若要定义个项的模板，你将需要细分`ItemTemplate`属性作为一个属性元素，并将其设置为`DataTemplate`，后者然后引用`ViewCell`。 到`View`属性`ViewCell`可以定义一个或多个视图，以显示每个项的布局。 下面是一个简单的示例：

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

`Label`元素设置为`View`属性`ViewCell`。 (`ViewCell.View`不需要标记，因为`View`属性是内容的属性`ViewCell`。)此标记显示`FriendlyName`每个属性`NamedColor`对象：

[![](data-binding-basics-images/listview2.png "绑定到的集合带有 DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "绑定到与 DataTemplate 集合")

好多了。 现在所有所需是修饰项模板的详细信息和实际的颜色。 若要支持此模板，某些值和对象具有已定义该页面的资源字典中：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

请注意，使用`OnPlatform`来定义的大小`BoxView`和高度`ListView`行。 尽管所有三个平台的值相同，标记无法轻松地进行适配为其他值，以微调显示。

## <a name="binding-value-converters"></a>绑定值转换器

上一个**ListView 演示**XAML 文件显示单个`R`， `G`，和`B`Xamarin.Forms 属性`Color`结构。 这些属性属于类型`double`和 0 到 1 范围。 如果你想要显示的十六进制值，则无法只需使用`StringFormat`与格式规范"X2"。 仅适用于整数和，除了`double`乘以 255 所需要的值。

此小问题已解决与*值转换器*，也称为*绑定转换器*。 这是一个类以实现`IValueConverter`接口，这意味着它具有名为的两种方法`Convert`和`ConvertBack`。 `Convert`当一个值从源传输到目标; 调用方法`ConvertBack`调用方法时传输从目标到源中`OneWayToSource`或`TwoWay`绑定：

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

`ConvertBack`因为绑定仅单向从源到目标方法无法在此程序中播放角色。

绑定引用使用的绑定转换器`Converter`属性。 绑定转换器，也可以接受具有指定的参数`ConverterParameter`属性。 对于某些更加通用，这是如何指定乘数。 绑定转换器检查是否存在有效的转换器参数`double`值。

转换器是实例化资源字典中，因此它可以在多个绑定之间共享：

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

三个数据绑定引用此单一实例。 请注意，`Binding`标记扩展包含嵌入`StaticResource`标记扩展：

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

下面是结果：

[![](data-binding-basics-images/listview3.png "绑定到的集合带有 DataTemplate 和转换器")](data-binding-basics-images/listview3-large.png#lightbox "绑定到的集合带有 DataTemplate 和转换器")

`ListView`非常完善在处理动态如果你执行某些步骤，在基础数据，但仅中可能发生更改。 如果项的集合分配给`ItemsSource`属性`ListView`过程运行时中的更改 — 的是，如果可以将项目添加到或从集合中移除-使用`ObservableCollection`这些项的类。 `ObservableCollection` 实现`INotifyCollectionChanged`接口，和`ListView`将安装的处理程序`CollectionChanged`事件。

如果在运行时，更改项本身的属性，则集合中的项应实现`INotifyPropertyChanged`对使用的属性值的接口和信号更改`PropertyChanged`事件。 说明了这一点在下一部分中的这一系列，[第 5 部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>总结

数据绑定提供了用于链接在一页内的两个对象之间或视觉对象之间的属性和基础数据的强大机制。 但是，当应用程序开始使用数据源，常用的应用程序体系结构模式开始作为有用范例中显现出来。 这一点在[第 5 部分。从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。



## <a name="related-links"></a>相关链接

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：开始使用 XAML （示例）](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 语法 （示例）](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 标记扩展 （示例）](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 5 部分：从数据绑定到 MVVM （示例）](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
