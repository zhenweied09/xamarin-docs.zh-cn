---
title: Xamarin.Forms 绑定回退
description: 此文章介绍了如何通过定义绑定将失败的情况下使用的回退值使绑定更稳健。
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 2a4b29df9148ce695f8f3ca5377e5848af1b775a
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171594"
---
# <a name="xamarinforms-binding-fallbacks"></a>Xamarin.Forms 绑定回退

有时数据绑定失败，因为无法解析，绑定源，或者因为绑定成功，但返回`null`值。 虽然可以使用值转换器或其他额外的代码处理这些情况下，数据绑定可实现更稳健通过定义要使用在绑定过程失败时的回退值。 这可以通过定义来实现[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)并[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)绑定表达式中的属性。 因为这些属性位于[ `BindingBase` ](xref:Xamarin.Forms.BindingBase)类，它们可以使用绑定，已编译的绑定，以及使用`Binding`标记扩展。

> [!NOTE]
> 利用[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)并[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)绑定表达式中的属性是可选的。

## <a name="defining-a-fallback-value"></a>定义一个回退值

[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)属性，可将使用定义的回退值时绑定*源*无法解析。 设置此属性的常见方案是绑定到不同类型的绑定的集合中的所有对象可能不存在的源属性时。

**MonkeyDetail**页说明了设置[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)属性：

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

上的绑定[ `Label` ](xref:Xamarin.Forms.Label)定义[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)绑定源无法解析，则将在目标系统设置的值。 因此，定义的值`FallbackValue`将显示属性，如果`Population`属性不存在对绑定对象。 请注意，此处`FallbackValue`由单引号 （撇号） 字符分隔属性值。

而不是定义[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)内联属性值，我们建议将它们定义为中的资源[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 此方法的优点是，此类值在单个位置中，定义一次并且可以进行更轻松地本地化。 然后可以使用检索资源`StaticResource`标记扩展：

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> 不能设置`FallbackValue`使用绑定表达式的属性。

下面是运行的程序：

![传输绑定](binding-fallbacks-images/bindingunavailable-detail-cropped.png "传输绑定")

当`FallbackValue`属性未设置在绑定表达式和绑定路径或路径的一部分进行解析， [ `BindableProperty.DefaultValue` ](xref:Xamarin.Forms.BindableProperty.DefaultValue)在目标上设置。 但是，当`FallbackValue`属性设置了绑定路径或路径的一部分进行解析，值`FallbackValue`value 属性设置在目标系统上。 因此，在**MonkeyDetail**页面[ `Label` ](xref:Xamarin.Forms.Label)显示"未知的总体大小"，因为缺少所需的绑定的对象`Population`属性。

> [!IMPORTANT]
> 绑定表达式中不执行已定义的值转换器时[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)属性设置。

## <a name="defining-a-null-replacement-value"></a>定义 null 替换值

[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)属性，可替换值以定义用于将时绑定*源*得到解决，但值`null`。 设置此属性的常见方案是绑定到可能的源属性时`null`绑定集合中。

**猴**页说明了设置[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)属性：

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

上的绑定[ `Image` ](xref:Xamarin.Forms.Image)并[ `Label` ](xref:Xamarin.Forms.Label)都定义[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)值的绑定路径返回的情况下应用`null`. 因此，定义的值`TargetNullValue`属性将显示集合中的任何对象的位置`ImageUrl`和`Location`未定义属性。 请注意，此处`TargetNullValue`由单引号 （撇号） 字符分隔属性值。

而不是定义[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)内联属性值，我们建议将它们定义为中的资源[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 此方法的优点是，此类值在单个位置中，定义一次并且可以进行更轻松地本地化。 然后可以使用检索资源`StaticResource`标记扩展：

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> 不能设置`TargetNullValue`使用绑定表达式的属性。

下面是运行的程序：

[![TargetNullValue 绑定](binding-fallbacks-images/bindingunavailable-small.png "TargetNullValue 绑定")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "TargetNullValue 绑定")

时`TargetNullValue`属性未设置在绑定表达式中，源值`null`将被转换，如果定义了值转换器，如果格式化`StringFormat`定义，然后将结果设置在目标系统上。 但是，当`TargetNullValue`属性设置，源值`null`如果定义了值转换器，并且如果它仍是将转换`null`完成转换的值后`TargetNullValue`属性设置在目标系统上。

> [!IMPORTANT]
> 绑定表达式中不应用格式设置字符串时`TargetNullValue`属性设置。

## <a name="related-links"></a>相关链接

- [数据绑定演示 （示例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
