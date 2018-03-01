---
title: "动态样式"
description: "样式，不要响应属性更改和应用程序的持续时间内保持不变。 例如，分配到可视元素，如果其中一个 Setter 实例修改、 删除或添加的新 Setter 实例的一种样式后, 所做的更改不会应用到的可视元素。 但是，应用程序可以响应在运行时动态的样式更改通过使用动态资源。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 2088ae055fb18b3b00712f063d2178f759021088
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="dynamic-styles"></a>动态样式

_样式，不要响应属性更改和应用程序的持续时间内保持不变。例如，分配到可视元素，如果其中一个 Setter 实例修改、 删除或添加的新 Setter 实例的一种样式后, 所做的更改不会应用到的可视元素。但是，应用程序可以响应在运行时动态的样式更改通过使用动态资源。_

`DynamicResource`标记扩展是类似于`StaticResource`它们都使用字典键来提取中的一个值中的标记扩展[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 但是，尽管`StaticResource`执行单个字典查找，`DynamicResource`维护字典键的链接。 因此，如果替换与键关联的字典条目，更改将应用到的可视元素。 这使运行时将在应用程序中进行的样式更改。

下面的代码示例演示*动态*XAML 页中的样式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)实例使用`DynamicResource`要引用的标记扩展[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)名为`searchBarStyle`，这不能在 XAML 中进行定义。 但是，因为[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性`SearchBar`实例被设置使用`DynamicResource`，缺失的字典键不会导致引发异常。

相反，在代码隐藏文件中，构造函数创建[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)具有键项`searchBarStyle`，下面的代码示例中所示：

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

当`OnButtonClicked`执行事件处理程序，`searchBarStyle`将切换`blueSearchBarStyle`和`greenSearchBarStyle`。 这将导致以下屏幕截图中所示的外观：

[![](dynamic-images/dynamic-style-blue.png "蓝色动态样式示例")](dynamic-images/dynamic-style-blue-large.png "蓝色动态样式示例")
[![](dynamic-images/dynamic-style-green.png "绿色动态样式示例")](dynamic-images/dynamic-style-green-large.png "绿色动态样式示例")

下面的代码示例演示 C# 中的等效页：

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button  }
        };
    }
    ...
}
```

在 C# 中， [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)实例使用[ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/)方法引用`searchBarStyle`。 `OnButtonClicked`事件处理程序代码等同于 XAML 示例中，并在执行时，`searchBarStyle`将切换`blueSearchBarStyle`和`greenSearchBarStyle`。

<a name="dynamic-style-inheritance">

## <a name="dynamic-style-inheritance"></a>动态样式继承

派生自动态样式的样式不能使用实现[ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/)属性。 相反， [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)类包括[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)属性，可以将其值设置为字典键可能会动态更改。

下面的代码示例演示*动态*在 XAML 页面中的样式继承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)实例使用`StaticResource`要引用的标记扩展[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)名为`tealSearchBarStyle`。 这`Style`设置某些其他属性，并使用[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)属性来引用`searchBarStyle`。 `DynamicResource`标记扩展不需要，因为`tealSearchBarStyle`不会改变，除`Style`它派生自。 因此，`tealSearchBarStyle`维护的链接`searchBarStyle`和基样式更改时更改。

在代码隐藏文件中，构造函数创建[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)具有键项`searchBarStyle`，每个前面的示例演示动态样式。 当`OnButtonClicked`执行事件处理程序，`searchBarStyle`将切换`blueSearchBarStyle`和`greenSearchBarStyle`。 这将导致以下屏幕截图中所示的外观：

[![](dynamic-images/dynamic-style-inheritance-blue.png "蓝色动态样式继承示例")](dynamic-images/dynamic-style-inheritance-blue-large.png "蓝色动态样式继承示例")
[![](dynamic-images/dynamic-style-inheritance-green.png "绿色动态样式继承的示例")](dynamic-images/dynamic-style-inheritance-green-large.png "绿色动态样式继承的示例")

下面的代码示例演示 C# 中的等效页：

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

`tealSearchBarStyle`直接分配[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性[ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)实例。 这`Style`设置某些其他属性，并使用[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)属性来引用`searchBarStyle`。 [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/)方法并不是必需此处因为`tealSearchBarStyle`不会改变，除`Style`它派生自。 因此，`tealSearchBarStyle`维护的链接`searchBarStyle`和基样式更改时更改。

## <a name="summary"></a>摘要

样式，不要响应属性更改和应用程序的持续时间内保持不变。 但是，应用程序可以响应在运行时动态的样式更改通过使用动态资源。 此外，*动态*样式可以使用派生自[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)属性。



## <a name="related-links"></a>相关链接

- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [动态样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [使用样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [样式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
