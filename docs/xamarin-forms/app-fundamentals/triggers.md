---
title: Xamarin.Forms 触发器
description: 此文章介绍了如何使用 Xamarin.Forms 触发器来响应用户界面与 XAML 将会更改。 触发器可以表达更改基于事件或属性更改的控件的外观以声明方式在 XAML 中的操作。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: b28ebb8845b7eae0d818e1279b4d6eaef4ad5b8b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241430"
---
# <a name="xamarinforms-triggers"></a>Xamarin.Forms 触发器

触发器可以表达更改基于事件或属性更改的控件的外观以声明方式在 XAML 中的操作。

你可以触发器将直接分配给一个控件，或将其添加到要应用于多个控件的页面级别或应用程序级别的资源字典。

有四种类型的触发器：

* [属性触发器](#property)-当控件的属性设置为特定值时发生。

* [数据触发器](#data)-使用数据绑定到触发器基于另一个控件的属性。

* [事件触发器](#event)-当事件发生在控件上时发生。

* [多触发器](#multi)-允许多个触发器条件之前发生某项操作设置。

<a name="property" />

## <a name="property-triggers"></a>属性触发器

简单的触发器可以表示完全在 XAML 中，添加`Trigger`到控件的元素触发集合。
此示例显示了更改的触发器`Entry`背景色在接收焦点时：

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

* **TargetType** -触发器适用于控件类型。

* **属性**-上受到监视的控件的属性。

* **值**-的值，则针对受监视的属性进行时，，从而导致要激活的触发器。

* **Setter**的一套`Setter`可添加元素和触发条件得以满足时。 必须指定`Property`和`Value`设置。

* **EnterActions 和 ExitActions** （未显示）-在代码中编写，并且可以用于除了 （或 instead of）`Setter`元素。 它们是[下述](#enterexit)。

### <a name="applying-a-trigger-using-a-style"></a>应用使用样式的触发器

此外可以将触发器添加到`Style`页上或应用程序中的控件上声明`ResourceDictionary`。 此示例声明隐式样式 (即。 没有`Key`设置) 这意味着它将适用于所有`Entry`页上的控件。

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

数据触发器使用数据绑定来监视另一个控件，若要使`Setter`s 获取调用。 而不是`Property`特性属性触发器中，设置`Binding`属性要监视指定的值。

下面的示例使用数据绑定语法`{Binding Source={x:Reference entry}, Path=Text.Length}`这是我们如何引用另一个控件的属性。 时的长度`entry`为零，则在激活该触发器。 在此示例中触发器禁用的按钮时输入为空。

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

提示： 在计算时`Path=Text.Length`总是为目标属性 （如提供默认值 `Text=""`) 因为否则它将为`null`和触发器不会工作方式与你预期。

除了指定`Setter`s 你还可以提供[`EnterActions`和`ExitActions` ](#enterexit)。

<a name="event" />

## <a name="event-triggers"></a>事件触发器

`EventTrigger`元素仅需要`Event`属性，如`"Clicked"`在下面的示例。

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

请注意，有没有`Setter`元素但而是对通过定义的类的引用`local:NumericValidationTriggerAction`这要求`xmlns:local`页面中声明的 XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

类本身实现`TriggerAction`这意味着应提供的替代`Invoke`每当触发器事件发生时调用的方法。

应触发器操作实现：

* 实现泛型`TriggerAction<T>`类，该触发器将应用于控件的类型与对应的泛型参数。 你可以使用超类如`VisualElement`编写使用各种控件，或指定控件类型的触发器操作`Entry`。

* 重写`Invoke`-这是当时调用方法是否符合的触发器条件。

* （可选） 公开声明触发器时，可以在 XAML 中设置的属性 (如`Anchor`， `Scale`，和`Length`在此示例中)。

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

公开触发操作的属性可以在 XAML 声明中，如下所示设置：

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

共享中的触发器时请小心`ResourceDictionary`，将在控件之间共享一个实例，因此一次配置任何状态将应用于所有。

请注意，不支持事件触发器`EnterActions`和`ExitActions`[下述](#enterexit)。    

<a name="multi" />

## <a name="multi-triggers"></a>多触发器

A`MultiTrigger`看起来类似于`Trigger`或`DataTrigger`只可以有多个条件。 所有条件均都为 true，然后才能`Setter`触发 s。

下面是一个示例将绑定到两个不同的输入按钮的触发器 (`email`和`phone`):

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

`Conditions`集合可能还包含`PropertyCondition`元素如下所示：

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>生成的"要求所有"多触发器

当所有条件都都成立时，多触发器只更新其控件。 测试的"所有字段长度都为零"（如登录页，其中所有输入必须都是完整） 是很棘手，因为你希望条件"其中 Text.Length > 0"，但这不都能在 XAML 中表示。

这可通过`IValueConverter`。 下面转换的转换器代码`Text.Length`绑定到`bool`，该值指示字段是否为空：


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

下面显示了 XAML。 请注意第一个多触发器示例中的以下差异：

* 该按钮具有`IsEnabled="false"`默认设置。
* 多触发条件使用转换器若要打开`Text.Length`值转换为一个布尔值。
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

这些屏幕截图显示上面的两个多触发器示例之间的差异。 屏幕的顶部，文本输入中只有一种`Entry`要启用就足够了**保存**按钮。
在屏幕的底部**登录**按钮保持非活动状态，直到这两个字段包含的数据。


![](triggers-images/multi-requireall.png "MultiTrigger 示例")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions 和 ExitActions

若要实现更改触发器发生时的另一个方法是通过添加`EnterActions`和`ExitActions`集合和指定`TriggerAction<T>`实现。

你可以提供*同时*`EnterActions`和`ExitActions`以及`Setter`中了触发器，但请注意， `Setter`s 将立即调用 (它们不会等待`EnterAction`或`ExitAction`到完成）。 或者，你可以在代码中执行的所有内容，并不使用`Setter`根本 s。

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

如往常一样，类引用 XAML 中应如声明命名空间`xmlns:local`如下所示：

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

注意：`EnterActions`和`ExitActions`上忽略**事件触发器**。



## <a name="related-links"></a>相关链接

- [触发器示例](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Xamarin.Forms API 文档](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/)
