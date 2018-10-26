---
title: Xamarin.Forms 编译绑定
description: 本文介绍如何使用已编译的绑定来提高 Xamarin.Forms 应用程序中的数据绑定性能。
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2018
ms.openlocfilehash: 0b350082c834076a1d69427644259087d64bf26a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111484"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin.Forms 编译绑定

_比经典绑定，从而可以提高 Xamarin.Forms 应用程序中的数据绑定性能更快地解决已编译的绑定。_

数据绑定具有两个主要问题：

1. 没有任何编译时验证的绑定表达式。 相反，绑定是在运行时解析。 因此，任何无效的绑定不是运行时之前或时检测到应用程序不会像预期会显示错误消息。
1. 它们并不经济高效。 绑定解析在运行时使用常规用途对象检查 （反射），执行此操作的开销会不同平台的。

已编译的绑定通过解析在编译时而不是在运行时绑定表达式来提高 Xamarin.Forms 应用程序中的数据绑定性能。 此外，此的绑定表达式的编译时验证可实现更好的开发人员故障排除体验，因为作为生成错误报告无效的绑定。

使用已编译的绑定的过程是：

1. 启用 XAML 编译。 有关 XAML 编译的详细信息，请参阅[XAML 编译](~/xamarin-forms/xaml/xamlc.md)。
1. 设置`x:DataType`特性，可以在[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)到的对象类型的`VisualElement`，及其子级将绑定到。 请注意，可以查看层次结构中的任何位置重新定义此属性。

> [!NOTE]
> 我们建议设置`x:DataType`作为视图层次结构中同一级别的特性[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)设置。

在 XAML 编译时，将报告任何无效的绑定表达式作为生成错误。 但是，XAML 编译器仅将报告遇到的第一个无效的绑定表达式的生成错误。 未定义任何有效的绑定表达式`VisualElement`或其子将已编译，无论[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) XAML 或代码中设置。 编译绑定表达式生成将在从属性获取一个值的已编译的代码*源*，并将其上设置的属性上*目标*中指定的标记。 此外，具体取决于绑定表达式，生成的代码可能会观察到的值中的更改*源*属性并刷新*目标*属性，并可能会将更改推从*目标*回*源*。

> [!IMPORTANT]
> 已编译的绑定当前已禁用定义任何绑定表达式[ `Source` ](xref:Xamarin.Forms.Binding.Source)属性。 这是因为`Source`属性始终设置使用`x:Reference`标记扩展，在编译时无法解析。

## <a name="using-compiled-bindings"></a>使用已编译的绑定

**编译的颜色选择器**页演示了如何使用 Xamarin.Forms 视图和 ViewModel 属性之间的已编译的绑定：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />
            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>    
</ContentPage>
```

根[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)实例化`HslColorViewModel`并初始化`Color`属性的属性元素标记内[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)属性。 此根`StackLayout`还定义了`x:DataType`属性为 ViewModel 类型，该值指示根中的任何绑定表达式`StackLayout`将编译视图层次结构。 这可以通过更改绑定表达式绑定到不存在 ViewModel 属性，这将导致生成错误的任何验证。

> [!IMPORTANT]
> `x:DataType`属性可以是视图层次结构中的任何时候重新定义。

[ `BoxView` ](xref:Xamarin.Forms.BoxView)， [ `Label` ](xref:Xamarin.Forms.Label)元素，以及[ `Slider` ](xref:Xamarin.Forms.Slider)视图继承从绑定上下文[ `StackLayout`](xref:Xamarin.Forms.StackLayout). 这些视图是引用 ViewModel 中的源属性的所有绑定目标。 有关[ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color)属性，并[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)属性，数据绑定是`OneWay`– 从 ViewModel 中的属性设置视图中的属性。 但是， [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)属性使用`TwoWay`绑定。 这样，每个`Slider`若要从 ViewModel 中，以及若要设置从每个 viewmodel 设置`Slider`。

当首次运行应用程序时， [ `BoxView` ](xref:Xamarin.Forms.BoxView)， [ `Label` ](xref:Xamarin.Forms.Label)元素，以及[ `Slider` ](xref:Xamarin.Forms.Slider)元素是从基于 ViewModel 的所有组初始`Color`时实例化 ViewModel 设置的属性。 以下屏幕截图所示：

[![编译颜色选择器](compiled-bindings-images/compiledcolorselector-small.png "编译颜色选择器")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "编译颜色选择器")

滑块操作，如[ `BoxView` ](xref:Xamarin.Forms.BoxView)并[ `Label` ](xref:Xamarin.Forms.Label)相应地更新元素。

有关此颜色选择器的详细信息，请参阅[Viewmodel 和属性更改通知](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications)。

## <a name="using-compiled-bindings-in-a-datatemplate"></a>在 DataTemplate 中使用已编译的绑定

中的绑定[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)要模板化的对象的上下文中解释。 因此，使用编译时中的绑定`DataTemplate`，则`DataTemplate`需要使用其数据对象的类型声明`x:DataType`属性。

**编译的颜色列表**页上演示了如何使用中的已编译的绑定[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

[ `ListView.ItemsSource` ](xref:Xamarin.Forms.ListView)属性设置为静态`NamedColor.All`属性。 `NamedColor`类使用.NET 反射来枚举中的所有静态公共字段[ `Color` ](xref:Xamarin.Forms.Color)结构，并将其存储在可从静态访问集合中其名称与`All`属性。 因此，`ListView`的所有填充`NamedColor`实例。 中每一项`ListView`，该项目的绑定上下文设置为`NamedColor`对象。 [ `BoxView` ](xref:Xamarin.Forms.BoxView)并[ `Label` ](xref:Xamarin.Forms.Label)中的元素[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)绑定到`NamedColor`属性。

请注意， [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)定义`x:DataType`属性不存在`NamedColor`的任何指示的类型绑定表达式中的`DataTemplate`将编译视图层次结构。 这可以通过更改绑定表达式来绑定到不存在的任何验证`NamedColor`属性，这将导致生成错误。

当首次运行应用程序时， [ `ListView` ](xref:Xamarin.Forms.ListView)填入`NamedColor`实例。 中的项后`ListView`已选中[ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color)属性设置为中的选定项的颜色`ListView`:

[![编译颜色列表](compiled-bindings-images/compiledcolorlist-small.png "编译颜色列表]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

选择其中的其他项[ `ListView` ](xref:Xamarin.Forms.BoxView)更新的颜色[ `BoxView` ](xref:Xamarin.Forms.BoxView)。

## <a name="combining-compiled-bindings-with-classic-bindings"></a>组合编译使用经典的绑定的绑定

绑定表达式仅编译为视图层次结构的`x:DataType`上定义属性。 相反，任何视图层次结构中的依据`x:DataType`属性将使用经典的绑定。 因此，它是将已编译的绑定和在页面上的经典绑定。 例如，在以前部分中的视图[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)使用已编译的绑定，而[ `BoxView` ](xref:Xamarin.Forms.BoxView)设置为在选定的颜色[ `ListView`](xref:Xamarin.Forms.ListView)却没有。

仔细构造的`x:DataType`属性可能因此会导致使用已编译和经典模型绑定的页。 或者，`x:DataType`特性可以重新定义为视图层次结构中的任何时候`null`使用`x:Null`标记扩展。 执行此操作表示视图层次结构中的任何绑定表达式将使用经典的绑定。 *混合绑定*页展示这种方法：

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

根[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)设置`x:DataType`属性不存在`HslColorViewModel`的任何指示的类型绑定表达式的根目录中`StackLayout`将编译视图层次结构。 但是，内部`StackLayout`重新定义`x:DataType`归于`null`与`x:Null`标记表达式。 因此，绑定表达式中内部`StackLayout`使用经典的绑定。 仅[ `BoxView` ](xref:Xamarin.Forms.BoxView)，在根目录`StackLayout`查看层次结构中，使用编译的绑定。

有关详细信息`x:Null`标记表达式，请参阅[X:null 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#null)。

## <a name="performance"></a>性能

已编译的绑定来提高数据绑定性能，具有性能优势不同。 单元测试将显示的：

- 使用属性更改通知的已编译的绑定 (即`OneWay`， `OneWayToSource`，或`TwoWay`绑定) 解析比经典绑定要快大约 8 次。
- 已编译的绑定不使用属性更改通知 (即`OneTime`绑定) 解析比经典绑定要快大约 20 倍。
- 设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)在已编译的绑定使用属性更改通知 (即`OneWay`， `OneWayToSource`，或`TwoWay`绑定) 比大约 5 次设置`BindingContext`经典绑定上。
- 设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)在已编译的绑定不会使用属性的更改通知 (即`OneTime`绑定) 比大约 7 次设置`BindingContext`经典绑定上。

这些性能差异可以正在使用的操作系统和应用程序正在其运行的设备版本放大取决于正在使用的平台的移动设备上。

## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
