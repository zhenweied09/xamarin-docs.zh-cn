---
title: 第 2 部分。 基本 XAML 语法
description: XAML 主要设计用于实例化和初始化对象。 但通常情况下，属性必须设置为不能轻松地表示为 XML 字符串的复杂对象，并有时必须在子类上设置一个类定义的属性。 这些两个需求需要属性元素和附加的属性的基本 XAML 语法特征。
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: d0129ec9872d8e5270ed8f0072cff0035d4f5255
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="part-2-essential-xaml-syntax"></a>第 2 部分。 基本 XAML 语法

_XAML 主要设计用于实例化和初始化对象。但通常情况下，属性必须设置为不能轻松地表示为 XML 字符串的复杂对象，并有时必须在子类上设置一个类定义的属性。这些两个需求需要属性元素和附加的属性的基本 XAML 语法特征。_

## <a name="property-elements"></a>属性元素

在 XAML 中，作为 XML 属性通常设置的类属性：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

但是，没有一种替代方式，在 XAML 中设置一个属性。 若要试用此替代方式与`TextColor`，首先删除现有`TextColor`设置：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

打开空元素`Label`分隔到开始和结束标记的标记：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

在这些标记中，添加组成类名称与句点分隔的属性名称的开始和结束标记：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

将属性值设置为这些新的标记，如下内容：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

若要指定这两种方式`TextColor`属性在功能上等效，但不使用的两种方法为相同属性因为这将有效地为设置属性两次，并可能会不明确。

此新语法中，可以引入一些有用的术语：

-  `Label` 是*对象元素*。 它是 Xamarin.Forms 对象表示为一个 XML 元素。
-  `Text``VerticalOptions`，`FontAttributes`和`FontSize`是*属性特性*。 它们是 Xamarin.Forms 属性表示为 XML 特性。
-  在该最终段中，`TextColor`已变得*属性元素*。 它是 Xamarin.Forms 属性，但它现在是一个 XML 元素。


在可能的元素的属性的定义首先似乎会违反 XML 语法，但它不是。 期间必须在 XML 中没有特殊含义。 为 XML 解码器，`Label.TextColor`是只需一个正常的子元素。

但是，在 XAML 中，此语法是非常特殊的。 属性元素的规则之一是，没有其他任何内容可以出现在`Label.TextColor`标记。 属性的值始终定义为属性元素的开始和结束标记之间的内容。

你可以使用多个属性上的属性元素语法：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

或者你可以使用的所有属性的属性元素语法：

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

首先，属性元素语法可能看起来不必要的长替换内容相对较非常简单，并在这些示例中这的确是这种情况。

但是，属性元素语法一点非常重要的属性的值太复杂，表示为一个简单的字符串时。 在属性元素标记中可以实例化另一个对象，并设置其属性。 例如，你可以显式设置一个属性如`VerticalOptions`到`LayoutOptions`具有属性设置的值：

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

另一个示例：`Grid`具有两个属性名为`RowDefinitions`和`ColumnDefinitions`。 这两个属性属于类型`RowDefinitionCollection`和`ColumnDefinitionCollection`，这是集合的`RowDefinition`和`ColumnDefinition`对象。 你必须使用属性元素语法来设置这些集合。

下面是为 XAML 文件的开头`GridDemoPage`类，显示的属性元素标记`RowDefinitions`和`ColumnDefinitions`集合：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

请注意定义大小自动调整单元格的像素宽度和高度，以及星型设置单元格的缩写的语法。

## <a name="attached-properties"></a>附加属性

你刚刚已经看到`Grid`需要的属性元素`RowDefinitions`和`ColumnDefinitions`集合来定义的行和列。 但是，还必须对程序员来说，以指示行和列的某种方式其中的每个子`Grid`驻留。

中的每个子级的标记`Grid`你指定的行和列的子使用下列属性：

-  `Grid.Row`
-  `Grid.Column`

这些属性的默认值为 0。 您还可以指示子是否跨多个行或列具有这些属性：

-  `Grid.RowSpan`
-  `Grid.ColumnSpan`

这两个属性具有默认值为 1。

下面是完整的 GridDemoPage.xaml 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

`Grid.Row`和`Grid.Column`为 0 的设置不是必需的但通常会包括是为了清楚起见。

下面是如下所示在所有三个平台上：

[![](essential-xaml-syntax-images/griddemo.png "网格布局")](essential-xaml-syntax-images/griddemo-large.png#lightbox "网格布局")

仅所用的语法，判断这些`Grid.Row`， `Grid.Column`， `Grid.RowSpan`，和`Grid.ColumnSpan`属性显示为静态字段或属性的`Grid`，但有趣的是，`Grid`未定义任何名为`Row`， `Column`， `RowSpan`，或`ColumnSpan`。

相反，`Grid`定义名为的四个可绑定属性`RowProperty`， `ColumnProperty`， `RowSpanProperty`，和`ColumnSpanProperty`。 这些是特殊类型的可绑定的属性称为*附加属性*。 它们由定义`Grid`类但设置的子级`Grid`。

当您希望使用这些附加属性在代码中，`Grid`类提供静态方法名为`SetRow`， `GetColumn`，依次类推。 但在 XAML 中，这些附加的属性被设置为的子级中的特性`Grid`使用简单的属性名称。

附加的属性始终是可以识别在 XAML 文件中作为属性包含一个类和句点分隔的属性名称。 它们分别名*附加属性*因为它们由一个类定义 (在这种情况下， `Grid`)、 但连接到其他对象 (在此情况下的子级`Grid`)。 在布局，`Grid`可以询问这些附加的属性，以了解每个子级的放置位置的值。

`AbsoluteLayout`类定义两个附加的属性名为`LayoutBounds`和`LayoutFlags`。 下面是实现使用的成比例定位呈现为棋盘样式和调整大小功能`AbsoluteLayout`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

和其内容如下：

[![](essential-xaml-syntax-images/absolutedemo-large.png "绝对布局")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "绝对布局")

有关所示，你可能问题使用 XAML 的经验。 当然，重复和的规律性`LayoutBounds`矩形提供的建议，它可能更好地实现在代码中。

这的确是合法的问题，并且没有定义用户界面时平衡代码和标记的使用没有问题。 很容易地在 XAML 中定义一些视觉对象，然后使用代码隐藏文件的构造函数添加一些可能会更好地生成在循环中的多个视觉对象。

## <a name="content-properties"></a>内容属性

在前面的示例中， `StackLayout`， `Grid`，和`AbsoluteLayout`对象设置为`Content`属性`ContentPage`，且这些布局的子级是实际中的项`Children`集合。 但这些`Content`和`Children`属性是无处 XAML 文件中。

你当然可以包括`Content`和`Children`属性作为属性的元素，如 in **XamlPlusCode**示例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

真正的问题是： 为何这些属性元素*不*所需的 XAML 文件？

允许在 XAML 中使用 Xamarin.Forms 中定义的元素中被标记了一个属性`ContentProperty`类中的属性。 如果你查找`ContentPage`类在联机 Xamarin.Forms 文档中，你将看到此属性：

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

这意味着，`Content`属性元素标记不是必需的。 显示的开始和结束之间的任何 XML 内容`ContentPage`标记假定要分配给`Content`属性。

 `StackLayout``Grid`， `AbsoluteLayout`，和`RelativeLayout`都派生自`Layout<View>`，并且如果你查找`Layout<T>`在 Xamarin.Forms 文档中，你将看到另一个`ContentProperty`属性：

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

允许内容的布局，以将自动添加到`Children`集合而无需显式`Children`属性元素标记。

其他类也有`ContentProperty`属性定义。 例如，内容属性的`Label`是`Text`。 查看其他人的 API 文档。

## <a name="platform-differences-with-onplatform"></a>使用 OnPlatform 的平台的差异

在单页面应用程序很常见设置`Padding`页后，可以避免覆盖 iOS 状态栏上的属性。 在代码中，你可以使用`Device.RuntimePlatform`属性用于此目的：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

你还可以执行类似在 XAML 中使用`OnPlatform`和`On`类。 首次包括的属性元素`Padding`页面顶部附近的属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

在这些标记，包括`OnPlatform`标记。 `OnPlatform` 是一个泛型类。 你需要指定泛型类型参数，在这种情况下， `Thickness`，这是一种`Padding`属性。 幸运的是，没有专门用于定义调用的泛型自变量的 XAML 属性`x:TypeArguments`。 这应与你要设置的属性的类型匹配：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform` 有一个名为`Platforms`即`IList`的`On`对象。 使用该属性的属性元素标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

现在添加`On`元素。 对于每个 onem 设置`Platform`属性和`Value`属性标记`Thickness`属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

可以简化此标记。 内容属性`OnPlatform`是`Platforms`，因此可以删除这些属性元素标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`Platform`属性`On`属于类型`IList<string>`，因此如果值相同，你可以将包含多个平台：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

因为 Android 和 UWP 设置的默认值为`Padding`，可以删除标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

这是设置依赖平台的标准方式`Padding`在 XAML 中的属性。 如果`Value`设置不能由单个字符串，您可以为其定义属性元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

## <a name="summary"></a>总结

与属性元素和附加的属性的基本 XAML 语法大部分已建立了。 但是，有时你需要将属性设置为对象以间接方式，例如，从资源字典。 此方法会在下一部分中，一部分介绍[3。XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)。



## <a name="related-links"></a>相关链接

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 3 部分：XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
