---
title: 创建 XAML 标记扩展
description: 定义你自己的自定义 XAML 标记扩展
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: 1a484aa4a19473c5a4f60b3d7bab78af7a20eecd
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848247"
---
# <a name="creating-xaml-markup-extensions"></a>创建 XAML 标记扩展

在以编程方式的级别中，XAML 标记扩展是一个类，实现[ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/)或[ `IMarkupExtension<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension%3CT%3E/)接口。 你可以浏览中如下所述的标准的标记扩展的源代码[ **MarkupExtensions**目录](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)Xamarin.Forms GitHub 存储库。 

还有可能由派生自定义你自己自定义的 XAML 标记扩展`IMarkupExtension`或`IMarkupExtension<T>`。 如果标记扩展获取特定类型的值，请使用普通表单。 这是与 Xamarin.Forms 标记扩展的几个这种情况：

- `TypeExtension` 派生自 `IMarkupExtension<Type>`
- `ArrayExtension` 派生自 `IMarkupExtension<Array>`
- `DynamicResourceExtension` 派生自 `IMarkupExtension<DynamicResource>`
- `BindingExtension` 派生自 `IMarkupExtension<BindingBase>`
- `ConstraintExpression` 派生自 `IMarkupExtension<Constraint>`

这两个`IMarkupExtension`接口定义每个，只有一个方法名为`ProvideValue`: 

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

由于`IMarkupExtension<T>`派生自`IMarkupExtension`并包括`new`关键字`ProvideValue`，它包含`ProvideValue`方法。

通常，XAML 标记扩展的返回值定义参与的属性。 (明显的例外是`NullExtension`，在其中`ProvideValue`只返回`null`。)`ProvideValue`方法具有类型的单个参数`IServiceProvider`，将更高版本在本文中讨论。

## <a name="a-markup-extension-for-specifying-color"></a>用于指定颜色标记扩展

下面的 XAML 标记扩展使你可以构造`Color`值使用色调、 饱和度和亮度组件。 它定义的颜色，包括初始化为 1 的 alpha 分量的四个组件的四个属性。 类派生自`IMarkupExtension<Color>`以指示`Color`返回值：

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

因为`IMarkupExtension<T>`派生自`IMarkupExtension`，类必须包含两个`ProvideValue`方法、 返回的一个`Color`，另一个返回`object`，但第二种方法可以只需调用第一种方法。

**HSL 颜色演示**页显示的各种不同的方式`HslColorExtension`可以出现在 XAML 文件，以指定的颜色`BoxView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    
    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

请注意，当`HslColorExtension`XML 的标记，四个属性被设置为属性，但当出现大括号之间时，由不带引号的逗号分隔的四个属性。 默认值`H`， `S`，和`L`为 0 和的默认值`A`为 1，因此可以省略这些属性，如果你希望它们设置为默认值。 最后一个示例演示的示例，亮度是 0，它通常会导致黑色，但 alpha 通道为 0.5，因此它是半双工透明的将显示灰色页上的白色背景：

[![HSL 颜色演示](creating-images/hslcolordemo-small.png "HSL 颜色演示")](creating-images/hslcolordemo-large.png#lightbox "HSL 颜色演示")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>用于访问位图标记扩展

自变量`ProvideValue`是一个对象，实现[ `IServiceProvider` ](https://developer.xamarin.com/api/type/System.IServiceProvider/)接口，在.NET 中定义`System`命名空间。 此接口具有一个成员，一个名为方法`GetService`与`Type`自变量。 

`ImageResourceExtension`如下所示的类演示一种可能使用`IServiceProvider`和`GetService`获取`IXmlLineInfoProvider`可以提供，该值指示在检测到特定错误的行和字符信息的对象。 在这种情况下，将引发异常时`Source`未设置属性：

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;

        return ImageSource.FromResource(assemblyName + "." + Source);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` 如果 XAML 文件需要访问图像文件存储为在.NET 标准的类库项目中嵌入的资源，非常有用。 它使用`Source`属性来调用静态`ImageSource.FromResource`方法。 此方法需要一个完全限定的资源名，它包含程序集名称、 文件夹的名称，以及用句点分隔的文件名。 `ImageResourceExtension`不需要的程序集名称部分因为它获取使用反射的程序集名称，并且将添加到前`Source`属性。 无论如何，`ImageSource.FromResource`必须从包含位图，这意味着此 XAML 的资源扩展不能是外部的库的一部分，除非的映像也是在库中的程序集进行调用。 (请参阅[**嵌入图像**](~/xamarin-forms/user-interface/images.md#embedded_images)访问位图存储为嵌入的资源的详细信息的文章。) 

尽管`ImageResourceExtension`需要`Source`要设置属性`Source`属性在属性中指定为类的内容的属性。 这意味着，`Source=`在大括号中的表达式的一部分，则可以省略。 在**映像资源演示**页上，`Image`元素提取的文件夹名称以及用句点分隔的文件名使用的两个映像：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        
        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

下面是在所有三个平台上运行的程序：

[![图像资源演示](creating-images/imageresourcedemo-small.png "图像资源演示")](creating-images/imageresourcedemo-large.png#lightbox "图像资源演示")

## <a name="service-providers"></a>服务提供商

通过使用`IServiceProvider`参数`ProvideValue`，XAML 标记扩展可以有权访问在其中它们正在使用的 XAML 文件有关的有用信息。 但若要使用`IServiceProvider`参数成功，你需要了解哪种类型的服务是在特定上下文中可用。 了解此功能的最佳方法是通过研究中现有的 XAML 标记扩展的源代码[ **MarkupExtensions**文件夹](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)Xamarin.Forms 储存库中的 GitHub 上。 请注意，某些类型的服务都是内部类到 Xamarin.Forms。

在某些 XAML 标记扩展中，此服务可能会很有用：

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

`IProvideValueTarget`接口定义两个属性，`TargetObject`和`TargetProperty`。 当在获取此信息`ImageResourceExtension`类，`TargetObject`是`Image`和`TargetProperty`是`BindableProperty`对象`Source`属性`Image`。 这是在其设置 XAML 标记扩展的属性。

`GetService`用自变量调用`typeof(IProvideValueTarget)`实际返回类型的对象`SimpleValueTargetProvider`中, 定义`Xamarin.Forms.Xaml.Internals`命名空间。 如果强制转换的返回值`GetService`为该类型，也可以访问`ParentObjects`属性，它是一个数组，包含`Image`元素，`Grid`父，和`ImageResourceDemoPage`的父级`Grid`。

## <a name="conclusion"></a>结束语

XAML 标记扩展通过扩展从各种源中设置属性的能力，在 XAML 中扮演重要角色。 此外，如果现有的 XAML 标记扩展不提供完全需要你还可以编写你自己。 


## <a name="related-links"></a>相关链接

- [标记扩展 （示例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [从 Xamarin.Forms 簿 XAML 标记扩展章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
