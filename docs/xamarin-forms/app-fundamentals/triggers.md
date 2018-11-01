---
title: Xamarin.Forms 触发器
description: 此文章介绍了如何使用 Xamarin.Forms 触发器来响应具有 XAML 用户界面更改。 触发器可以表示 XAML 中声明的方式更改的基于事件或属性更改控件外观的操作。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: e9ec9288e2b8ea991ef8d41f9b601d0897631b9d
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675206"
---
# <a name="xamarinforms-triggers"></a>Xamarin.Forms 触发器

触发器可以表示 XAML 中声明的方式更改的基于事件或属性更改控件外观的操作。

可以一个触发器将直接分配给一个控件，或将其添加到要应用于多个控件的页级别或应用程序级资源字典。

有四种类型的触发器：

* [属性触发器](#property)-上一个控件的属性设置为特定值时，会发生。

* [数据触发器](#data)-使用数据绑定到触发器基于另一个控件的属性。

* [事件触发器](#event)-当事件发生在控件上时发生。

* [多触发器](#multi)-允许多个操作发生之前要设置的触发器条件。

<a name="property" />

## <a name="property-triggers"></a>属性触发器

简单触发器可以表示完全在 XAML 中，添加`Trigger`触发回收时给控件的元素。
此示例显示了更改的触发器`Entry`接收焦点时，背景色：

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
             Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
        </Trigger>
    </Entry.Triggers>
</Entry>
```

触发器的重要部分是声明的：

* **TargetType** -触发器应用于控件类型。

* **属性**-监视在控件上的属性。

* **值**-的值，为受监视的属性时，导致触发器激活。

* **Setter** -一系列`Setter`可以添加元素，并且满足触发器条件时。 必须指定`Property`和`Value`设置。

* **EnterActions 和 ExitActions** （未显示）-在代码中编写并可以在除 （或 instead of）`Setter`元素。 它们是[如下所述](#enterexit)。

### <a name="applying-a-trigger-using-a-style"></a>应用触发器，请使用一种样式

触发器还可以添加到`Style`页上或应用程序中的控件上的声明`ResourceDictionary`。 此示例中声明的隐式样式 (ie。 没有`Key`设置) 这意味着它将适用于所有`Entry`页上的控件。

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

<a name="data" />

## <a name="data-triggers"></a>数据触发器

数据触发器使用数据绑定来监视另一个控件来导致`Setter`以调用。 而不是`Property`属性中的属性触发器，请设置`Binding`属性来监视指定的值。

下面的示例使用数据绑定语法 `{Binding Source={x:Reference entry}, Path=Text.Length}`
这是我们是如何引用另一个控件的属性。 时的长度`entry`为零，激活触发器。 在此示例中触发禁用的按钮时输入为空。

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </Button.Triggers>
</Button>
```

提示： 评估时`Path=Text.Length`始终提供的目标属性 （例如默认值。 `Text=""`) 因为否则它将为`null`和触发器不会使用起来就像您预期。

除了指定`Setter`还可以提供的 s [ `EnterActions`并`ExitActions` ](#enterexit)。

<a name="event" />

## <a name="event-triggers"></a>事件触发器

`EventTrigger`元素仅需要`Event`属性，如`"Clicked"`在下面的示例。

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

请注意，有没有`Setter`元素，但而不是对定义的类的引用`local:NumericValidationTriggerAction`需要`xmlns:local`页面中声明的 XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

实现类本身`TriggerAction`这意味着它应提供的替代`Invoke`每当触发器事件发生时调用的方法。

触发器操作实现应：

* 实现泛型`TriggerAction<T>`类，与该触发器将应用于控件类型相对应的泛型参数。 您可以使用如超类`VisualElement`编写适用于各种控件，或指定控件类型的触发器操作`Entry`。

* 重写`Invoke`方法-这称为只要满足触发器条件。

* 选择公开声明触发器时可以在 XAML 中设置属性 (如`Anchor`， `Scale`，和`Length`在此示例中)。

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

由触发器操作公开的属性可以按如下所示设置 XAML 声明中：

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

共享中的触发器时要小心`ResourceDictionary`，以便对其所有应用将配置一次的任何状态，在控件之间共享一个实例。

请注意，不支持事件触发器`EnterActions`并`ExitActions`[如下所述](#enterexit)。

<a name="multi" />

## <a name="multi-triggers"></a>多触发器

一个`MultiTrigger`看起来类似于`Trigger`或`DataTrigger`只可以有多个条件。 所有条件均都为 true，然后才能`Setter`触发 s。

下面是将绑定到两个不同的输入的按钮触发器的示例 (`email`和`phone`):

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>

  <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

`Conditions`还可以包含在集合`PropertyCondition`元素如下所示：

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>生成的"要求所有"多触发器

满足所有条件时，多触发器只更新其控件。 测试为"所有字段长度零"（如登录页面，其中所有输入必须都是完整） 是比较棘手，因为所需条件"其中 Text.Length > 0"，但这不能在 XAML 中表示。

这可以通过`IValueConverter`。 以下转换转换器代码`Text.Length`绑定到`bool`，该值指示字段是否为空：

```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

若要在多触发器中使用此转换器，首先将其添加到页面的资源字典 (以及自定义`xmlns:local`命名空间定义):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

XAML 如下所示。 请注意以下差异，从第一个多触发器示例：

* 该按钮具有`IsEnabled="false"`默认设置。
* 多触发器条件使用转换器来启用`Text.Length`值到`boolean`。
* 当所有条件都都`true`，setter 使按钮的`IsEnabled`属性`true`。

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

这些屏幕截图显示了上述的两个多触发器示例之间的差异。 屏幕的顶部，一个中输入文本`Entry`足以**保存**按钮。
在屏幕的底部**登录名**按钮始终处于非活动状态，直到这两个字段包含数据。

![](triggers-images/multi-requireall.png "MultiTrigger 示例")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions 和 ExitActions

若要实现更改触发器发生时的另一种方法是通过添加`EnterActions`并`ExitActions`集合，并指定`TriggerAction<T>`实现。

可以提供*同时*`EnterActions`并`ExitActions`，以及`Setter`中触发器，但请注意， `Setter`s 将立即调用 (它们不会等待`EnterAction`或`ExitAction`到完成）。 或者，可以在代码中执行的所有内容，且不将`Setter`根本的 s。

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
                        <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

如往常一样，一个类引用 XAML 中应如声明一个命名空间`xmlns:local`如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

`FadeTriggerAction`代码如下所示：

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public FadeTriggerAction() {}

    public int StartsFrom { set; get; }

    protected override void Invoke (VisualElement visual)
    {
            visual.Animate("", new Animation( (d)=>{
                var val = StartsFrom==1 ? d : 1-d;
                visual.BackgroundColor = Color.FromRgb(1, val, 1);

            }),
            length:1000, // milliseconds
            easing: Easing.Linear);
    }
}
```

注意：`EnterActions`并`ExitActions`上忽略**事件触发器**。



## <a name="related-links"></a>相关链接

- [触发器示例](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Xamarin.Forms API 文档](xref:Xamarin.Forms.TriggerAction`1)
