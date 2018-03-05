---
title: "第 3 部分。 XAML 标记扩展"
description: "XAML 标记扩展构成允许将属性设置为对象或从其他源间接引用的值的 XAML 中的重要功能。 XAML 标记扩展是对于共享对象，并引用在应用程序，使用常量尤为重要，但它们在数据绑定中找到其最大的实用程序。"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 7aea7b1536efc952378c6a1df63640af191f1ebe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="part-3-xaml-markup-extensions"></a>第 3 部分。 XAML 标记扩展

_XAML 标记扩展构成允许将属性设置为对象或从其他源间接引用的值的 XAML 中的重要功能。XAML 标记扩展是对于共享对象，并引用在应用程序，使用常量尤为重要，但它们在数据绑定中找到其最大的实用程序。_

## <a name="xaml-markup-extensions"></a>XAML 标记扩展

一般情况下，你可以使用 XAML 将对象的属性设置为显式值，如字符串、 数字、 枚举成员或转换为在幕后值的字符串。

有时，但是，属性必须改为引用其他，某一位置定义的值，或通过在运行时的代码可能需要很少的处理。 出于这些目的，XAML*标记扩展*可用。

这些 XAML 标记扩展不是扩展的 XML。 XAML 是完全合法的 XML。 它们在称为"扩展"，因为它们提供支持的类中实现的代码`IMarkupExtension`。 你可以编写自己的自定义标记扩展。

在许多情况下，XAML 标记扩展是 XAML 文件中立即可识别因为它们显示由大括号分隔的特性设置为: {和}，但有时标记扩展会出现在标记作为传统的元素。

## <a name="shared-resources"></a>共享的资源

某些 XAML 页属性设置为相同的值与包含多个视图。 例如，有许多属性设置这些`Button`对象是否相同：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="Large" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="Large" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="Large" />

    </StackLayout>
</ContentPage>
```

如果其中一个属性需要更改，你可能希望以使更改只需一次，而不是三次。 如果这是代码，你将很可能会使用常数和静态只读对象为了使此类值保持一致且轻松地修改。

在 XAML 中，一个常用来存储此类值或解决方案中的对象*资源字典*。 `VisualElement`类定义一个名为属性`Resources`类型的`ResourceDictionary`，即具有键的类型的字典`string`和类型的值`object`。 可以放入此字典的对象，然后从标记中，所有 XAML 中引用它们。

若要在页面上使用的资源字典，包含一对`Resources`属性元素标记。 它是最方便的方法将这些页的顶部：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

它也是需要显式包含`ResourceDictionary`标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

现在对象和各种类型的值可以添加到资源字典。 这些类型必须是可实例化。 它们不能是抽象类，例如。 这些类型还必须具有公共无参数构造函数。 每个项，要求使用指定的字典项`x:Key`属性。 例如:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

这两项是结构类型的值`LayoutOptions`，和每个具有唯一键以及一个或两个属性设置。 在代码和标记，它是更常见的是使用的静态字段`LayoutOptions`，但还有更方便地设置的属性。

现在需要设置`HorizontalOptions`和`VerticalOptions`对这些资源，这些按钮的属性和完成与`StaticResource`XAML 标记扩展：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="Large" />
```

`StaticResource`标记扩展始终用大括号分隔，并包含字典键。

名称`StaticResource`使其有别于`DynamicResource`，Xamarin.Forms 还支持。 `DynamicResource` 适用于在运行时，可能会更改的值与关联的字典键时`StaticResource`只后当构造页上元素的从字典访问元素。

有关`BorderWidth`属性，它是必需的双精度值存储在字典中。 XAML 方便地定义了标记的常见数据类型，如`x:Double`和`x:Int32`:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

你不需要将其放在三个行上。 上移一行仅采用此旋转角度此字典条目：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

可以在相同的方式中引用这些两个资源`LayoutOptions`值：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="Large" />
```

资源类型的`Color`，你可以使用直接将这些类型的属性的分配时，你使用的相同的字符串表示形式。 创建资源时调用的类型转换器。 下面是类型的资源`Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

`FontSize`属性显示的一个小问题。 属性定义的类型为`double`。 当将属性设置为的成员`NamedSize`如枚举`Large`、`FontSizeConverter`类在后台以将其转换为平台相关值使用的工作原理`Device.GetNamedSized`方法。

但是，不能定义字体大小作为资源`double`并将值设置为"大型"。 在 XAML 分析器处理资源时，它不知道的值将用作字体大小。 

解决方法是定义为资源`string`使用`x:String`类型：

```xaml
<x:String x:Key="fontSize">Large</x:String>
```

现在所有属性除外`Text`由资源设置定义：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

还有可能要使用`OnPlatform`要定义的平台的不同值的资源字典中。 下面是如何`OnPlatform`对象可以是不同的文本颜色资源字典中的一部分：

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

请注意，`OnPlatform`获取同时`x:Key`特性，因为它是在字典中的对象和`x:TypeArguments`特性，因为它是一个泛型类。 `iOS`， `Android`，和`UWP`属性转换为`Color`值在初始化对象时。

下面是最终完成 XAML 访问六个共享的值的三个按钮文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:String x:Key="fontSize">Large</x:String>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize"{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

屏幕截图验证一致样式和平台相关样式：

[ ![](xaml-markup-extensions-images/sharedresources.png "风格的控件")](xaml-markup-extensions-images/sharedresources-large.png "风格的控件")

尽管它是最常见的定义`Resources`集合顶部的页上，请记住，`Resources`属性定义由`VisualElement`，并且你可以提供`Resources`上页面上的其他元素的集合。 例如，尝试将添加到一个`StackLayout`在此示例中：

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

您会发现这些按钮的文本颜色现在为蓝色。 基本上，每当 XAML 分析器遇到`StaticResource`标记扩展，它将向上的可视化树搜索，并使用第一个`ResourceDictionary`遇到包含该注册表项。

最常见的资源字典中存储的对象类型的一个方法是 Xamarin.Forms `Style`，后者定义属性设置的集合。 文中讨论了样式[样式](~/xamarin-forms/user-interface/styles/index.md)。

开发人员熟悉 XAML 它们可以如将可视元素想要知道的有时`Label`或`Button`中`ResourceDictionary`。 虽然肯定可能，但它不太大意义。 用途`ResourceDictionary`是共享对象。 可视元素不能共享。 同一个实例不能出现两次在单个页。

## <a name="the-xstatic-markup-extension"></a>X:static 标记扩展

尽管其名称，这种相似性`x:Static`和`StaticResource`有很大不同。 `StaticResource` 从资源字典时返回一个对象`x:Static`访问以下项之一：

- 公共静态字段
- 公共静态属性
- 公共常量字段 
- 枚举成员。 

`StaticResource`标记扩展支持的 XAML 实现定义使用的资源字典时`x:Static`属于内部 XAML 中，作为`x`前缀显示。

下面是几个示例，演示如何`x:Static`可以显式引用静态字段和枚举成员：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

到目前为止，这不是非常令人印象深刻的。 但`x:Static`标记扩展还可以引用静态字段或属性从你自己的代码。 例如，下面是`AppConstants`包含某些字段可能想要在应用程序的多个页上使用的静态字段的类：

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;
                    
                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

若要引用的 XAML 文件中此类的静态字段，你将需要某种方式，以指示在此文件所在的 XAML 文件内。 使用 XML 命名空间声明来执行此操作。

回想一下，作为标准的 Xamarin.Forms XAML 模板的一部分创建的 XAML 文件包含两个 XML 命名空间声明： 一个用于访问 Xamarin.Forms 类和另一个用于引用标记和特性到使用 XAML 内部函数：

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

你将需要其他的 XML 命名空间声明，若要访问其他类。 每个其他的 XML 命名空间声明定义新的前缀。 若要访问类 PCL 中，共享的应用程序的本地如`AppConstants`，XAML 程序员通常使用前缀`local`。 命名空间声明必须指示 CLR （公共语言运行时） 命名空间名称，也称为.NET 命名空间名称，是在 C# 中显示的名称`namespace`定义中或在`using`指令：

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

此外可以在 PCL 中引用任何程序集中定义.NET 命名空间的 XML 命名空间的声明。 例如，下面是`sys`前缀标准.NET`System`命名空间，该对话框位于**mscorlib**程序集，其中一次尤为"的 Microsoft 常见对象运行时库，"，但现在意味着"多语言标准常见对象运行时库。" 由于这是另一个程序集，你还必须指定程序集名称，在这种情况下**mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

请注意，关键字`clr-namespace`后跟一个冒号，然后.NET 命名空间名称后, 跟一个分号，关键字`assembly`，一个等号和程序集名称。

是，加上冒号`clr-namespace`但等号遵循`assembly`。 语法已定义在此方式故意： 最 XML 命名空间声明引用一个 URI，如开始 URI 方案名称`http`，这始终后跟一个冒号。 `clr-namespace`此字符串的一部分用于模拟这种约定。

这两个以下命名空间声明中包含**StaticConstantsPage**示例。 请注意，`BoxView`维度设置为`Math.PI`和`Math.E`，但通过 100 的比例因子缩放：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

所产生的大小`BoxView`相对于屏幕是与平台相关：

 [ ![](xaml-markup-extensions-images/staticconstants.png "使用 X:static 标记扩展的控件")](xaml-markup-extensions-images/staticconstants-large.png "控件使用 X:static 标记扩展")

## <a name="other-standard-markup-extensions"></a>其他标准的标记扩展

几个标记扩展是固有的 XAML 和 Xamarin.Forms XAML 文件中支持。 其中一些未非常频繁使用，但在需要时非常重要：

-  如果属性具有非`null`值由默认，但你想要将其设置为`null`，将其设置为`{x:Null}`标记扩展。
-  如果属性的类型是`Type`，你可以将其分配给`Type`对象使用标记扩展`{x:Type someClass}`。
-  你可以在 XAML 中使用定义数组`x:Array`标记扩展。 此标记扩展有必需的特性名为`Type`，该值指示数组中元素的类型。
- `Binding`标记扩展已在[第 4 部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

## <a name="the-constraintexpression-markup-extension"></a>ConstraintExpression 标记扩展

标记扩展可以有属性，但它们不将设置 XML 属性等。 在标记扩展中，用逗号，分隔属性设置和没有引号出现在大括号内。

这可以用名为 Xamarin.Forms 标记扩展说明`ConstraintExpression`，与用于`RelativeLayout`类。 作为常量，或相对于父或其他已命名的视图，可以指定的位置或大小的子视图。 语法`ConstraintExpression`允许您设置的位置或大小的视图使用`Factor`属性的另一个视图，加上一次`Constant`。 需要代码比这更复杂的任何内容。

以下是一个示例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

与此示例，应执行的最重要经验可能是标记扩展的语法： 没有引号必须出现在标记扩展的大括号内。 在 XAML 文件中键入标记扩展，很自然会想要用引号引起来的属性的值。 反对倾向做法 ！

下面是运行的程序：

[ ![](xaml-markup-extensions-images/relativelayout.png "使用约束的相对布局")](xaml-markup-extensions-images/relativelayout-large.png "相对布局使用约束")

## <a name="summary"></a>摘要

此处显示的 XAML 标记扩展的 XAML 文件提供了重要的支持。 但是而言最有价值的 XAML 标记扩展的或许是`Binding`，此内容进行介绍在下一部分中的这一系列，[第 4 部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。



## <a name="related-links"></a>相关链接

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
