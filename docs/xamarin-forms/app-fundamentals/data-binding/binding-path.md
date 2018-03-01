---
title: "绑定路径"
description: "使用访问子属性和集合成员的数据绑定"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 39d326714a6fee1abe242a7256888647784cdec3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="binding-path"></a>绑定路径

在所有前面数据绑定的示例， [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)属性`Binding`类 (或[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Path/)属性`Binding`标记扩展) 已设置向单个属性。 实际上可以设置`Path`到*子属性*（的属性的属性），或者与集合的成员。

例如，假定你的页面包含`TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

`Time`属性`TimePicker`属于类型`TimeSpan`，但你可能想要创建数据绑定引用`TotalSeconds`属性，`TimeSpan`值。 下面是数据绑定：

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```
         
`Time`属性属于类型`TimeSpan`，它具有`TotalSeconds`属性。 `Time`和`TotalSeconds`属性是简单地连接有一个句点。 中的项`Path`字符串始终引用属性而不属于这些属性的类型。

示例和几个其他都显示在**路径变体**页：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=mscorlib"
             x:Class="DataBindingDemos.PathVariationsPage"
             Title="Path Variations"
             x:Name="page">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="HorizontalTextAlignment" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    
    <StackLayout Margin="10, 0">
        <TimePicker x:Name="timePicker" />

        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time.TotalSeconds,
                              StringFormat='{0} total seconds'}" />

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children.Count,
                              StringFormat='There are {0} children in this StackLayout'}" />
        
        <Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                              Path=DateTimeFormat.DayNames[3],
                              StringFormat='The middle day of the week is {0}'}" />

        <Label>
            <Label.Text>
                <Binding Path="DateTimeFormat.DayNames[3]"
                         StringFormat="The middle day of the week in France is {0}">
                    <Binding.Source>
                        <globe:CultureInfo>
                            <x:Arguments>
                                <x:String>fr-FR</x:String>
                            </x:Arguments>
                        </globe:CultureInfo>
                    </Binding.Source>
                </Binding>
            </Label.Text>
        </Label>

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children[1].Text.Length,
                              StringFormat='The first Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

在第二个`Label`，绑定源是本身的页。 `Content`属性属于类型`StackLayout`，它具有`Children`类型的属性`IList<View>`，它具有`Count`属性，该值指示的子级的个数。

## <a name="paths-with-indexers"></a>路径替换为索引器

在第三个绑定`Label`中**路径变体**页引用[ `CultureInfo` ](https://developer.xamarin.com/api/type/System.Globalization.CultureInfo/)类`System.Globalization`命名空间：

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

源设置为静态`CultureInfo.CurrentCulture`属性，它是类型的对象`CultureInfo`。 类定义一个名为属性`DateTimeFormat`类型的[ `DateTimeFormatInfo` ](https://developer.xamarin.com/api/type/System.Globalization.DateTimeFormatInfo/)包含`DayNames`集合。 索引选择第四个项。

第四个`Label`将执行类似但区域性与法国关联。 `Source`绑定属性设置为`CultureInfo`用构造函数的对象：

```xaml
<Label>
    <Label.Text>
        <Binding Path="DateTimeFormat.DayNames[3]"
                 StringFormat="The middle day of the week in France is {0}">
            <Binding.Source>
                <globe:CultureInfo>
                    <x:Arguments>
                        <x:String>fr-FR</x:String>
                    </x:Arguments>
                </globe:CultureInfo>
            </Binding.Source>
        </Binding>
    </Label.Text>
</Label>
```

请参阅[传递构造函数自变量](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)有关在 XAML 中指定构造函数自变量的详细信息。

最后，最后一个示例是类似于第二个，只不过它引用了一个的子级`StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

该子级则`Label`，它具有`Text`类型的属性`String`，它具有`Length`属性。 第一个`Label`报表`TimeSpan`中设置`TimePicker`，因此当该文本更改时，最终`Label`也将发生更改。

下面是在所有三个平台上运行的程序：

[![路径变体](binding-path-images/pathvariations-small.png "路径变体")](binding-path-images/pathvariations-large.png "路径变体")

## <a name="debugging-complex-paths"></a>调试复杂的路径

复杂的路径定义可能很难构造： 你需要知道每个子属性的类型或要正确添加的下一步的子属性的集合中的项的类型，但这些类型本身不会出现在路径中。 一个好方法是以增量方式生成路径并查看中间结果。 对于该最后一个示例中，你无法开始，没有`Path`根本的定义：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

显示绑定源的类型或`DataBindingDemos.PathVariationsPage`。 你知道`PathVariationsPage`派生自`ContentPage`，因此它有`Content`属性：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

一种`Content`属性现在显示为`Xamarin.Forms.StackLayout`。 添加`Children`属性`Path`和该类型是`Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`，它是 Xamarin.Forms，但很明显是集合类型的内部类。 将索引添加到该和该类型是`Xamarin.Forms.Label`。 继续以这种方式。

Xamarin.Forms 处理的绑定路径，它将安装`PropertyChanged`上实现的路径中的任何对象的处理程序`INotifyPropertyChanged`接口。 例如，最终绑定响应中第一个更改`Label`因为`Text`属性更改。 

中的绑定路径的属性不实现`INotifyPropertyChanged`，对该属性的任何更改将被忽略。 某些更改完全无法使无效的绑定路径，因此仅当属性和子属性的字符串永远不会变为无效时，应使用此方法。



## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [从 Xamarin.Forms 簿的数据绑定章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
