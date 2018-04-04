---
title: 资源字典
description: XAML 资源是可以多次使用的对象定义。 ResourceDictionary 允许定义在一个位置，并在 Xamarin.Forms 应用程序中重新使用的资源。 此文章介绍了如何创建和使用 ResourceDictionary，以及如何合并资源字典。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/17/2017
ms.openlocfilehash: 37e38f2c4297d5acd148a7e6d1bec5569fe8c51c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="resource-dictionaries"></a>资源字典

_XAML 资源是可以多次使用的对象定义。ResourceDictionary 允许定义在一个位置，并在 Xamarin.Forms 应用程序中重新使用的资源。此文章介绍了如何创建和使用 ResourceDictionary，以及如何合并资源字典。_

## <a name="overview"></a>概述

A [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)是 Xamarin.Forms 应用程序使用的资源的存储库。 存储中的典型资源`ResourceDictionary`包括[样式](~/xamarin-forms/user-interface/styles/index.md)，[控件模板](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)，[数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)，颜色、 和转换器。

在 XAML 中，资源中定义[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)然后检索并使用应用于元素`StaticResource`标记扩展。 在 C# 中，资源中定义`ResourceDictionary`然后检索并通过使用基于字符串的索引器应用于的元素。 但是，没有什么好处使用`ResourceDictionary`C# 中作为资源可以轻松地直接分配到的可视元素的属性而无需首先检索从`ResourceDictionary`。

## <a name="creating-and-consuming-a-resourcedictionary"></a>创建和使用 ResourceDictionary

可以在定义资源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)附加到[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)组页面或控件，或到[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/)集合应用程序。 选择定义的位置[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)可以使用它的影响：

- 中的资源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定义在控件级别只能应用到控件和及其子级。
- 中的资源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定义在页级别可以仅应用到页和及其子级。
- 中的资源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定义的应用程序可以在整个应用程序应用级别。

下面的 XAML 代码示例演示应用程序级别中定义的资源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

这[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定义三个[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)资源和[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)资源。 有关创建 XAML`App`类，请参阅[App 类](~/xamarin-forms/app-fundamentals/application-class.md)。

每个资源都有一个使用指定的密钥`x:Key`属性，其在提供描述性密钥`ResourceDictionary`。 密钥用于检索从资源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)通过`StaticResource`标记扩展，如下面的 XAML 代码示例演示在控件中定义的其他资源级别中所示`ResourceDictionary`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

第一个[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例检索和使用`LabelPageHeadingStyle`在应用程序级别中定义的资源[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，与第二个`Label`实例检索和使用`LabelNormalStyle`控件级别中定义资源`ResourceDictionary`。 同样， [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)实例检索和使用`NormalTextColor`在应用程序级别中定义的资源`ResourceDictionary`，和`MediumBoldText`控件级别中定义资源`ResourceDictionary`。 这将导致以下屏幕截图中所示的外观：

[![](resource-dictionaries-images/screenshots-sml.png "消耗 ResourceDictionary 资源")](resource-dictionaries-images/screenshots.png#lightbox "消耗 ResourceDictionary 资源")

> [!NOTE]
> 特定于单个页面的资源不应包含应用程序级别资源字典中，在这种资源将然后分析在而不是应用程序启动时所需的页。 有关详细信息，请参阅[减少应用程序的资源字典大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="overriding-resources"></a>重写资源

当[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)资源共享`x:Key`属性值，在查看层次结构中更低时定义的资源将优先于那些定义更高版本上。 例如，设置`PageBackgroundColor`资源`Blue`在应用程序级别将被替代的页级别`PageBackgroundColor`资源设置为`Yellow`。 同样，页面级别`PageBackgroundColor`资源将被替代的控制级别`PageBackgroundColor`资源。 下面的 XAML 代码示例演示了此优先：

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

原始`PageBackgroundColor`和`NormalTextColor`情况下，应用程序级别定义重写了`PageBackgroundColor`和`NormalTextColor`页级别定义的实例。 因此，页背景色变为蓝色，并且在页上的文本变为黄色，如以下屏幕截图中所示：

[![](resource-dictionaries-images/overridding-screenshots-sml.png "重写 ResourceDictionary 资源")](resource-dictionaries-images/overridding-screenshots.png#lightbox "重写 ResourceDictionary 资源")

但请注意的后台栏[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)仍为黄色，因为[ `BarBackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/)属性设置为的值`PageBackgroundColor`应用程序中定义的资源级别[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。

## <a name="merged-resource-dictionaries"></a>合并资源字典

合并的资源字典合并一个或多[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)到另一个实例。 这通过设置实现`ResourceDictionary.MergedDictionaries`属性设置为一个或多个将合并到应用程序、 页上，或控件级别的资源字典`ResourceDictionary`。

> [!IMPORTANT]
> [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)类型还具有[ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/)属性，可以用于合并单个`ResourceDictionary`到另一个，与`ResourceDictionary`指定为该值`MergedWith`属性合并到当前`ResourceDictionary`实例。 通过合并时`MergedWith`属性、 当前中的任何资源`ResourceDictionary`该共享`x:Key`属性中的资源的值，则`ResourceDictionary`要合并，将被替换。 但是， `MergedWith` Xamarin.Forms 的未来版本中还将弃用属性。 因此，建议你先使用`MergedDictionaries`属性来合并`ResourceDictionary`实例。

下面的 XAML 代码示例演示[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)名为`MyResourceDictionary`包含单个资源：

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

`MyResourceDictionary` 可以合并到任何应用程序、 页上，或控件级别`ResourceDictionary`。 下面的 XAML 代码示例演示其合并为一个页级别`ResourceDictionary`使用`MergedDictionaries`属性：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

合并时[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)资源共享相同`x:Key`属性值，Xamarin.Forms 使用以下资源优先级：

1. 本地的资源字典的资源。
1. 通过合并的资源字典中包含的资源[ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/)属性。
1. 已通过合并的资源字典中包含的资源`MergedDictionaries`中列出的顺序集合`MergedDictionaries`属性。

> [!NOTE]
> 搜索资源字典可以是计算密集型任务，如果应用程序包含多个较大的资源字典。 因此，确保应用程序中的每一页仅使用适用于页上，以避免不必要的搜索的资源字典。

## <a name="summary"></a>总结

本文介绍了如何创建和使用[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，以及如何合并资源字典。 A`ResourceDictionary`允许定义在一个位置，并在 Xamarin.Forms 应用程序中重新使用的资源。


## <a name="related-links"></a>相关链接

- [资源字典 （示例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
