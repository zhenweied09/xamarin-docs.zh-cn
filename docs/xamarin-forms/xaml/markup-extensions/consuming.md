---
title: 使用 XAML 标记扩展
description: 使用 Xamarin.Forms 中可用的 XAML 标记扩展
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: 25eada483e8bd2ce95cb3101dfe873ea38b283ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-xaml-markup-extensions"></a>使用 XAML 标记扩展

XAML 标记扩展帮助增强的功能和灵活性的 XAML 通过允许要设置从各种源的元素属性。 几个 XAML 标记扩展是 XAML 2009 规范的一部分。 它们出现在与通常的 XAML 文件`x`命名空间前缀，并通常与此前缀称为。 以下各节描述了这些：

- [`x:Static`](#static) &ndash; 引用静态属性、 字段或枚举成员。
- [`x:Reference`](#reference) &ndash; 名为页面上的元素的引用。
- [`x:Type`](#type) &ndash; 将属性设置为`System.Type`对象。
- [`x:Array`](#array) &ndash; 构造特定类型的对象的数组。
- [`x:Null`](#null) &ndash; 将属性设置为`null`值。

三个其他 XAML 标记扩展从历史上看其他 XAML 实现中，通过受支持和 Xamarin.Forms 也支持。 描述了这些内容更完全其他文章中：

- `StaticResource` &ndash; 从资源字典中，引用对象，如文所述[**资源字典**](~/xamarin-forms/xaml/resource-dictionaries.md)。
- `DynamicResource` &ndash; 响应中使用的资源字典中的对象的更改，如本文中所述[**动态样式**](~/xamarin-forms/user-interface/styles/dynamic.md)。
- `Binding` &ndash; 建立两个对象的属性之间的链接，如下文所述[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- `TemplateBinding` &ndash; 文章中所述从控件模板，执行数据绑定 [**从控件模板绑定**] / 参考线/xamarin-窗体/应用程序的基础知识/模板/控件的模板/模板的绑定 /)

[ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)布局的自定义标记扩展使用[ `ConstraintExpression` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/)。 文章中介绍了此标记扩展[ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md)。

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static 标记扩展

`x:Static`标记扩展支持的[ `StaticExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/)类。 此类具有名为的单个属性[ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/)类型的`string`你将设置为公共的常量、 静态属性、 静态字段或枚举成员的名称。

一种常用方式使用`x:Static`是第一次定义的类具有一些常量或静态变量，如这小`AppConstants`类[ **MarkupExtensions** ](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)程序：

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

**X:static 演示**页演示几种方式使用`x:Static`标记扩展。 最详细的方法实例化`StaticExtension`类之间`Label.FontSize`属性元素标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

XAML 分析器还允许`StaticExtension`类缩写为`x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

可简化此过程甚至可更进一步，但更改，将引入某些新的语法： 它包含将放`StaticExtension`类和设置在大括号中的成员。 生成的表达式将直接为`FontSize`属性：

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

请注意，有*没有*的大括号内的引号引起来。 `Member`属性`StaticExtension`不再是一个 XML 属性。 相反，它是用于标记扩展的表达式的一部分。

就像你可以将缩写`x:StaticExtension`到`x:Static`当作为对象元素中使用它，你可以还将其缩写在大括号内的表达式：

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

`StaticExtension`类具有`ContentProperty`引用属性的属性`Member`，这会将此属性作为类的默认内容属性。 对于用大括号的 XAML 标记扩展，可以消除`Member=`表达式的一部分：

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

这是最常见的形式`x:Static`标记扩展。

**静态演示**页包含两个其他示例。 XAML 文件的根标记包含 XML 命名空间声明为.NET`System`命名空间：

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

这允许`Label`字体大小设置为静态字段`Math.PI`。 那样会导致而是小文本，因此`Scale`属性设置为`Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

最后一个示例显示`Device.RuntimePlatform`值。 `Environment.NewLine`静态属性用于插入新行字符这两者之间`Span`对象：

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

下面是在所有三个平台上运行示例：

[![X:static 演示](consuming-images/staticdemo-small.png "X:static 演示")](consuming-images/staticdemo-large.png#lightbox "X:static 演示")

<a name="reference" />

## <a name="xreference-markup-extension"></a>x:Reference 标记扩展

`x:Reference`标记扩展支持的[ `ReferenceExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/)类。 此类具有名为的单个属性[ `Name` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.ReferenceExtension.Name/)类型的`string`你将设置为已被赋予的名称与页上的元素的名称`x:Name`。 这`Name`属性是内容的属性`ReferenceExtension`，因此`Name=`时不需要`x:Reference`将显示在大括号。

`x:Reference`标记扩展以独占方式用于数据绑定，文章中的更详细地描述了这些[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

**X:reference 演示**页显示的两种用法`x:Reference`使用数据绑定，它用于设置的第一个`Source`属性`Binding`对象，并且它用于设置第二个`BindingContext`对于两个数据绑定的属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

同时`x:Reference`表达式使用的缩写的形式`ReferenceExtension`类名称和消除`Name=`表达式的一部分。 在第一个示例中，`x:Reference`中嵌入标记扩展`Binding`标记扩展。 请注意，`Source`和`StringFormat`设置用逗号分隔。 下面是在所有三个平台上运行的程序：

[![X:reference 演示](consuming-images/referencedemo-small.png "X:reference 演示")](consuming-images/referencedemo-large.png#lightbox "X:reference 演示")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type 标记扩展

`x:Type`标记扩展是 XAML 等效的 C# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/)关键字。 它受[ `TypeExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/)类，该类定义名为的一个属性[ `TypeName` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.TypeExtension.TypeName/)类型的`string`是否设置为类或结构的名称。 `x:Type`标记扩展返回[ `System.Type` ](https://developer.xamarin.com/api/type/System.Type/)类或结构的对象。 `TypeName` 作为内容属性的`TypeExtension`，因此`TypeName=`时不需要`x:Type`显示，其中包含大括号。

在 Xamarin.Forms，有几个属性具有类型自变量`Type`。 示例包括[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)属性`Style`，和[X:typearguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments)属性用于指定泛型类中的自变量。 但是，XAML 分析器不会执行`typeof`操作自动与`x:Type`在这些情况下不使用标记扩展。

一个位置其中`x:Type`*是*所需并且`x:Array`中所述的标记扩展[下一节](#array)。

`x:Type`构造一个菜单，其中每个菜单项对应于特定类型的对象时，可能也是有用标记扩展。 你可以将关联`Type`对象每个菜单项，然后选择菜单项时实例化对象。

这是如何中的导航菜单`MainPage`中**标记扩展**程序工作原理。 **MainPage.xaml**文件包含`TableView`与每个`TextCell`对应于程序中的特定页：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

下面是的打开主页**标记扩展**:

[![Main 页](consuming-images/mainpage-small.png "Main 页")](consuming-images/mainpage-large.png#lightbox "Main 页")

每个`CommandParameter`属性设置为`x:Type`引用了一个其他页面的标记扩展。 `Command`属性绑定到名为的属性`NavigateCommand`。 此属性定义中`MainPage`代码隐藏文件：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

`NavigateCommand`属性是`Command`实现用类型自变量执行命令的对象`Type`&mdash;的值`CommandParameter`。 该方法使用`Activator.CreateInstance`来实例化页，然后导航到它。 构造函数通过设置到结束`BindingContext`到其自身的页面，从而使`Binding`上`Command`工作。 请参阅[**数据绑定**](~/xamarin-forms/app-fundamentals/data-binding/index.md)文章和特别[ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)文章，以这种类型的代码有关的更多详细信息。

**X:type 演示**页使用了类似的方法来实例化 Xamarin.Forms 元素并将其添加到`StackLayout`。 XAML 文件最初由三个`Button`元素使用其`Command`属性设置为`Binding`和`CommandParameter`属性设置为三个 Xamarin.Forms 视图的类型：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

代码隐藏文件定义和初始化`CreateCommand`属性：

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

方法时执行`Button`按下创建的自变量的新实例，设置其`VerticalOptions`属性，并将其添加到`StackLayout`。 这三种`Button`元素然后共享页具有动态创建的视图：

[![x:Type Demo](consuming-images/typedemo-small.png "x:Type Demo")](consuming-images/typedemo-large.png#lightbox "x:Type Demo")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array 标记扩展

`x:Array`标记扩展，可在标记中定义一个数组。 它受[ `ArrayExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/)类，该类定义两个属性：

- `Type` 类型的`Type`，指示数组中元素的类型。
- `Items` 类型的`IList`，它是项本身的集合。 这是的内容属性`ArrayExtension`。

`x:Array`标记扩展本身永远不会显示在大括号中。 相反，`x:Array`开始和结束标记分隔的项的列表。 设置`Type`属性`x:Type`标记扩展。

**X:array 演示**页显示如何使用`x:Array`将项添加到`ListView`通过设置`ItemsSource`到一个数组的属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

`ViewCell`创建一个简单`BoxView`对于每个颜色条目：

[![x:Array Demo](consuming-images/arraydemo-small.png "x:Array Demo")](consuming-images/arraydemo-large.png#lightbox "x:Array Demo")

有几种方法来指定单个`Color`此数组中的项。 你可以使用`x:Static`标记扩展：

```xaml
<x:Static Member="Color.Blue" />
```

或者，你可以使用`StaticResource`从资源字典中检索一种颜色：

```xaml
<StaticResource Key="myColor" />
```

本文末尾，你将看到的自定义的 XAML 标记扩展，还会创建一个新的颜色值：

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

在定义的公共类型，如字符串或数字数组时，使用中列出的标记[**传递构造函数自变量**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)文章来分隔的值。

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null 标记扩展

`x:Null`标记扩展支持的[ `NullExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/)类。 它没有属性，并且只需 XAML 等效于 C# [ `null` ](/dotnet/csharp/language-reference/keywords/null/)关键字。

`x:Null`很少需要数据并标记扩展很少使用，但如果你发现需要它，您会很高兴它存在。

**X:null 演示**页演示一种情况时`x:Null`可能很方便。 假设你定义的一种隐式`Style`为`Label`包括`Setter`设置`FontFamily`属性依赖于平台的系列名称：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

然后你会发现之一`Label`元素，你需要所有的属性设置中的隐式`Style`除`FontFamily`，你想要作为默认值。 你可以定义另一个`Style`对于该目的，但更简单的方法是只需设置`FontFamily`的特定属性`Label`到`x:Null`、 中心中所示`Label`。

此处是三个平台上运行的程序：

[![X:null 演示](consuming-images/nulldemo-small.png "X:null 演示")](consuming-images/nulldemo-large.png#lightbox "X:null 演示")

通知该四个的`Label`元素具有 serif 字体，但 center`Label`具有默认 san serif 字体。

## <a name="define-your-own-markup-extensions"></a>定义你自己的标记扩展

如果遇到在 Xamarin.Forms 中不可用的 XAML 标记扩展需要，你可以[创建你自己](creating.md)。


## <a name="related-links"></a>相关链接

- [标记扩展 （示例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [从 Xamarin.Forms 簿 XAML 标记扩展章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [动态样式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
