---
title: Xamarin.Forms 绑定路径
description: 此文章介绍了如何使用 Xamarin.Forms 数据绑定来访问子属性和集合成员的绑定类的路径属性。
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 5ffc167b1e5695663dff6005f3d7e0ba0ea958db
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172101"
---
# <a name="xamarinforms-binding-path"></a>Xamarin.Forms 绑定路径

在所有以前的数据绑定示例中， [ `Path` ](xref:Xamarin.Forms.Binding.Path)的属性`Binding`类 (或[ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path)属性`Binding`标记扩展) 已设置向单个属性。 实际上可能会设置`Path`到*子属性*（的属性的属性），或者与集合的成员。

例如，假设您的页面包含`TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

`Time`的属性`TimePicker`属于类型`TimeSpan`，但可能是你想要创建数据绑定引用`TotalSeconds`属性的`TimeSpan`值。 下面是数据绑定：

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

`Time`属性属于类型`TimeSpan`，其中包含`TotalSeconds`属性。 `Time`和`TotalSeconds`属性是只需连接的周期。 中的项`Path`字符串始终引用属性而不属于这些属性的类型。

在显示的示例和其他几种**路径变体**页：

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
                              StringFormat='The second Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

在第二个`Label`，绑定源是页面本身。 `Content`属性属于类型`StackLayout`，其中包含`Children`类型的属性`IList<View>`，其中包含`Count`属性，指示的子级的个数。

## <a name="paths-with-indexers"></a>使用索引器的路径

在第三个绑定`Label`中**路径变体**页的引用[ `CultureInfo` ](xref:System.Globalization.CultureInfo)类`System.Globalization`命名空间：

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

源设置为静态`CultureInfo.CurrentCulture`属性，它是类型的对象`CultureInfo`。 类定义一个名为属性`DateTimeFormat`类型的[ `DateTimeFormatInfo` ](xref:System.Globalization.DateTimeFormatInfo) ，其中包含`DayNames`集合。 索引选择第四个项。

第四个`Label`执行类似但区域性与法国相关联。 `Source`绑定的属性设置为`CultureInfo`对象和构造函数：

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

请参阅[传递构造函数参数](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)有关在 XAML 中指定构造函数自变量的详细信息。

最后，最后一个示例是类似于第二个，不同之处在于它所引用的一个子级的`StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

该子级是`Label`，其中包含`Text`类型的属性`String`，其中包含`Length`属性。 第一个`Label`报表`TimeSpan`集中`TimePicker`，因此该文本更改时，最终`Label`也将发生更改。

下面是运行的程序：

[![路径变体](binding-path-images/pathvariations-small.png "路径变体")](binding-path-images/pathvariations-large.png#lightbox "路径变体")

## <a name="debugging-complex-paths"></a>调试复杂的路径

复杂路径定义可能很难构造： 您需要知道每个子属性的类型或要正确地添加下一个子属性的集合中的项的类型，但这些类型本身并不显示在路径中。 一个好方法是以增量方式生成路径和查看的中间结果。 对于最后一个示例，可以开始否`Path`根本的定义：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

显示绑定源的类型或`DataBindingDemos.PathVariationsPage`。 您知道`PathVariationsPage`派生自`ContentPage`，因此它有`Content`属性：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

类型`Content`属性现在显示为`Xamarin.Forms.StackLayout`。 添加`Children`属性设置为`Path`且类型为`Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`，Xamarin.Forms 中，但显然是集合类型的内部的类。 将索引添加到该且类型为`Xamarin.Forms.Label`。 继续以这种方式。

在 Xamarin.Forms 处理绑定路径，它将安装`PropertyChanged`实现的路径中的任何对象上的处理程序`INotifyPropertyChanged`接口。 最后一个绑定例如，在第一个更改做出反应`Label`因为`Text`属性更改。

如果绑定路径中的属性不实现`INotifyPropertyChanged`，对该属性的任何更改将被忽略。 某些更改可能完全使指针无效的绑定路径，因此仅当属性和子属性的字符串永远不会变为无效时，应使用此方法。



## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [数据绑定 Xamarin.Forms 书籍章节](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
