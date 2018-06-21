---
title: 资源字典
description: XAML 资源是可以共享和重复使用在整个 Xamarin.Forms 应用程序对象。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c2e6a5624baba251061bcd324fcb849e3d95ebfd
ms.sourcegitcommit: c2d1249cb67b877ee0d9cb8d095ec66fd51d8c31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36291030"
---
# <a name="resource-dictionaries"></a>资源字典

_XAML 资源是可以共享和重复使用在整个 Xamarin.Forms 应用程序对象的定义。_

这些资源对象存储在资源字典。 本文介绍如何创建和使用的资源字典，以及如何合并资源字典。

## <a name="overview"></a>概述

A [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)是 Xamarin.Forms 应用程序使用的资源的存储库。 存储中的典型资源`ResourceDictionary`包括[样式](~/xamarin-forms/user-interface/styles/index.md)，[控件模板](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)，[数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)，颜色、 和转换器。

在 XAML 中，资源存储在`ResourceDictionary`然后可以检索和使用应用于元素`StaticResource`标记扩展。 在 C# 中，资源也可以定义在`ResourceDictionary`然后检索并通过使用基于字符串的索引器应用于的元素。 但是，没有什么好处使用`ResourceDictionary`在 C# 中，因为共享的对象只可以存储为字段或属性，并不直接访问无需编写第一个检索它们从一个字典。

## <a name="creating-and-consuming-a-resourcedictionary"></a>创建和使用 ResourceDictionary

资源定义中[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)即然后设置为以下项之一`Resources`属性：

- [ `Resources` ](xref:Xamarin.Forms.Application.Resources)任何派生自的类的属性 [`Application`](xref:Xamarin.Forms.Application)
- [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)任何派生自的类的属性 [`VisualElement`](xref:Xamarin.Forms.Application)

Xamarin.Forms 程序包含只有一个派生自的类`Application`但通常使用派生自的许多类`VisualElement`，包括页、 布局和控件。 任何这些对象可以具有其`Resources`属性设置为`ResourceDictionary`。 选择在何处放置特定`ResourceDictionary`其中，可以用资源的影响：

- 中的资源`ResourceDictionary`如附加到视图`Button`或`Label`只能应用于该特定的对象，因此这不是非常有用。
- 中的资源`ResourceDictionary`附加到的布局如`StackLayout`或`Grid`可应用于布局和该布局的所有子级。
- 中的资源`ResourceDictionary`定义的页级别可向页以及所有子项。
- 中的资源`ResourceDictionary`定义的应用程序可以在整个应用程序应用级别。

下面的 XAML 演示应用程序级别中定义的资源`ResourceDictionary`中**App.xaml**标准 Xamarin.Forms 程序的一部分创建的文件：

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

这`ResourceDictionary`定义三个[ `Color` ](xref:Xamarin.Forms.Color)资源和[ `Style` ](xref:Xamarin.Forms.Style)资源。 有关详细信息`App`类，请参阅[App 类](~/xamarin-forms/app-fundamentals/application-class.md)。

Xamarin.Forms 从 3.0 开始，显式`ResourceDictionary`标记不是必需的。 `ResourceDictionary`对象自动创建的并且你可以直接之间插入资源`Resources`属性元素标记：

```xaml
<Application ...>
    <Application.Resources>
        <Color x:Key="PageBackgroundColor">Yellow</Color>
        <Color x:Key="HeadingTextColor">Black</Color>
        <Color x:Key="NormalTextColor">Blue</Color>
        <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
            <Setter Property="FontAttributes" Value="Bold" />
            <Setter Property="HorizontalOptions" Value="Center" />
            <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
        </Style>
    </Application.Resources>
</Application>
```

每个资源都有一个使用指定的密钥`x:Key`属性，则该命令将其字典中的键`ResourceDictionary`。 密钥用于检索从资源`ResourceDictionary`通过[ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension)标记扩展，如下面显示中定义的其他资源的 XAML 代码示例中所示`StackLayout`:

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

第一个[ `Label` ](xref:Xamarin.Forms.Label)实例检索和使用`LabelPageHeadingStyle`在应用程序级别中定义的资源`ResourceDictionary`，与第二个`Label`实例检索和使用`LabelNormalStyle`控件级别中定义资源`ResourceDictionary`。 同样， [ `Button` ](xref:Xamarin.Forms.Button)实例检索和使用`NormalTextColor`在应用程序级别中定义的资源`ResourceDictionary`，和`MediumBoldText`控件级别中定义资源`ResourceDictionary`。 这将导致以下屏幕截图中所示的外观：

[![](resource-dictionaries-images/screenshots-sml.png "消耗 ResourceDictionary 资源")](resource-dictionaries-images/screenshots.png#lightbox "消耗 ResourceDictionary 资源")

> [!NOTE]
> 特定于单个页面的资源不应包含应用程序级别资源字典中，在这种资源将然后分析在而不是应用程序启动时所需的页。 有关详细信息，请参阅[减少应用程序的资源字典大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="overriding-resources"></a>重写资源

当`ResourceDictionary`资源共享`x:Key`属性值，在查看层次结构中更低时定义的资源将优先于那些定义更高版本上。 例如，设置`PageBackgroundColor`资源`Blue`在应用程序级别将被替代的页级别`PageBackgroundColor`资源设置为`Yellow`。 同样，页面级别`PageBackgroundColor`资源将被替代的控制级别`PageBackgroundColor`资源。 下面的 XAML 代码示例演示了此优先：

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

但请注意的后台栏[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)仍为黄色，因为[ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)属性设置为的值`PageBackgroundColor`应用程序中定义的资源级别`ResourceDictionary`。

下面是另一种方法要考虑`ResourceDictionary`优先级： 当 XAML 分析器遇到`StaticResource`，它通过在可视化树旅行向上搜索匹配的键，它使用第一个匹配项查找。 如果此搜索结束在页上，且仍未找到键，XAML 分析器搜索`ResourceDictionary`附加到`App`对象。 如果仍未找到项，则引发异常。

## <a name="stand-alone-resource-dictionaries"></a>独立的资源字典

从派生的类`ResourceDictionary`也可以在单独的独立文件。 (更确切地说，派生自的类`ResourceDictionary`通常需要_对_的文件因为资源定义中的 XAML 文件但具有的代码隐藏文件`InitializeComponent`调用，还有必要。)然后可以在应用程序之间共享生成的文件。

若要创建此类文件，添加一个新**内容视图**或**内容页**项与项目 (但不是**内容视图**或**内容页**与只有一个 C# 文件）。 在 XAML 文件和 C# 文件中，将从基类的名称更改`ContentView`或`ContentPage`到`ResourceDictionary`。 在 XAML 文件中，基本类的名称是顶级元素。

下面的 XAML 示例演示[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)名为`MyResourceDictionary`:

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

这`ResourceDictionary`包含单个资源，它是一个对象类型`DataTemplate`。

可以实例化`MyResourceDictionary`之间的一对置于`Resources`属性元素标记，例如，在`ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

实例`MyResourceDictionary`设置为`Resources`属性`ContentPage`对象。

但是，此方法有一些限制：`Resources`属性`ContentPage`引用了仅此一个`ResourceDictionary`。 在大多数情况下，所需的选项包括其他`ResourceDictionary`实例和可能是其他资源以及。

此任务需要合并的资源字典。

## <a name="merged-resource-dictionaries"></a>合并资源字典

合并的资源字典合并一个或多`ResourceDictionary`到另一个实例`ResourceDictionary`。 你可以执行此 XAML 文件中通过设置[ `MergedDictionaries` ](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)属性设置为一个或多个将合并到应用程序、 页上，或控件级别的资源字典`ResourceDictionary`。

> [!IMPORTANT]
> `ResourceDictionary` 此外定义[ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith)属性。 未使用此属性;它已弃用截至 Xamarin.Forms 3.0。

与类的实例`MyResourceDictionary`可合并到任何应用程序、 页上，或控件级别`ResourceDictionary`。 下面的 XAML 代码示例演示其合并为一个页级别`ResourceDictionary`使用`MergedDictionaries`属性：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

该标记显示仅的实例`MyResourceDictionary`添加到`ResourceDictionary`但你还可以引用其他`ResourceDictionary`实例内`MergedDictionary`属性元素标记，并在这些标记之外的其他资源：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary.MergedDictionaries>

                <!-- Add more resource dictionaries here -->

                <local:MyResourceDictionary />

                <!-- Add more resource dictionaries here -->

            </ResourceDictionary.MergedDictionaries>

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

可能只有一个`MergedDictionaries`主题中`ResourceDictionary`，但你可以放置任意多个`ResourceDictionary`根据需要，在这里实例。

合并时[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)资源共享相同`x:Key`属性值，Xamarin.Forms 使用以下资源优先级：

1. 本地的资源字典的资源。
1. 合并的资源字典中包含的资源通过不推荐使用[ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith)属性。
1. 已通过合并的资源字典中包含的资源`MergedDictionaries`集合中, 列出的相反顺序`MergedDictionaries`属性。

> [!NOTE]
> 搜索资源字典可以是计算密集型任务，如果应用程序包含多个较大的资源字典。 因此，若要避免不必要的搜索，你应确保应用程序中的每一页仅使用适用于页的资源字典。

## <a name="merging-dictionaries-in-xamarinforms-30"></a>Xamarin.Forms 3.0 中的合并字典

以 Xamarin.Forms 3.0，合并进程开头[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)实例已变得某种程度上更容易且更灵活。 `MergedDictionaries`属性元素标记不再需要。 相反，你将添加到资源字典另一个`ResourceDictionary`与新的标记[ `Source` ](xref:Xamarin.Forms.ResourceDictionary.Source)属性设置为与资源的 XAML 文件的文件名：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary Source="MyResourceDictionary.xaml" />

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

因为 Xamarin.Forms 3.0 自动实例化`ResourceDictionary`，其中两个外部`ResourceDictionary`标记是否不再需要：

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Add more resources here -->

        <ResourceDictionary Source="MyResourceDictionary.xaml" />

        <!-- Add more resources here -->

    </ContentPage.Resources>
    ...
</ContentPage>
```

此新语法未_不_实例化`MyResourceDictionary`类。 相反，它引用的 XAML 文件。 原因代码隐藏文件 (**MyResourceDictionary.xaml.cs**) 不再需要。 您还可以删除`x:Class`从根标记的属性**MyResourceDictionary.xaml**文件。

## <a name="summary"></a>总结

本文介绍了如何创建和使用[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，以及如何合并资源字典。 A`ResourceDictionary`允许定义在一个位置，并在 Xamarin.Forms 应用程序中重新使用的资源。

## <a name="related-links"></a>相关链接

- [资源字典 （示例）](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
