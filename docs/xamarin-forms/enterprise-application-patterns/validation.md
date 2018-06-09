---
title: 在企业应用程序中的验证
description: 本章介绍 eShopOnContainers 移动应用程序如何执行验证的用户输入。 这包括指定验证规则，触发验证，并显示验证错误。
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 6a7f244b78d5b48dd219f59f1191993d62663bbf
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243172"
---
# <a name="validation-in-enterprise-apps"></a>在企业应用程序中的验证

接受用户输入的任何应用程序应确保输入有效。 输入包含特定范围内的唯一字符、 属于某个特定长度，或与特定的格式无法，例如，检查应用程序。 如果未经验证，用户可以提供将导致应用程序失败的数据。 验证强制实施业务规则并防止攻击者将恶意数据注入。

中的上下文的模型-视图模型的模型 (MVVM) 模式，视图模型或模型通常需要执行数据验证和信号到视图的任何验证错误，以便用户可以更正它们。 EShopOnContainers 移动应用程序执行的查看模型属性的同步客户端验证，并通过突出显示该控件包含无效的数据，并显示错误消息，告知用户将通知用户任何验证错误数据无效的原因。 图 6-1 演示涉及在 eShopOnContainers 移动应用程序中执行验证的类。

[![](validation-images/validation.png "EShopOnContainers 移动应用程序中的验证类")](validation-images/validation-large.png#lightbox "eShopOnContainers 移动应用程序中的验证类")

**图 6-1**: eShopOnContainers 移动应用程序中的验证类

要求进行验证的查看模型属性属于类型`ValidatableObject<T>`，和每个`ValidatableObject<T>`实例具有验证规则添加到其`Validations`属性。 通过调用从视图模型调用验证`Validate`方法`ValidatableObject<T>`实例，检索验证规则，然后执行针对它们`ValidatableObject<T>``Value`属性。 任何验证错误放入`Errors`属性`ValidatableObject<T>`实例，与`IsValid`属性`ValidatableObject<T>`实例将更新以指示验证是成功还是失败。

属性更改通知由`ExtendedBindableObject`类，因此[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件可以绑定到`IsValid`属性`ValidatableObject<T>`视图模型类，即是否通知中的实例输入的数据有效。

## <a name="specifying-validation-rules"></a>指定验证规则

通过创建派生自的类指定验证规则`IValidationRule<T>`接口，下面的代码示例中所示：

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

此接口指定验证规则类必须提供`boolean``Check`用于执行所需的验证的方法和一个`ValidationMessage`其值是如果将会显示验证错误消息的属性验证将失败。

下面的代码示例演示`IsNotNullOrEmptyRule<T>`验证规则，用于执行验证的用户名和密码在输入用户`LoginView`eShopOnContainers 移动应用程序中使用模拟的服务时：

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

`Check`方法返回`boolean`，该值指示值自变量是否`null`、 空还是仅由空白字符组成。

尽管不使用 eShopOnContainers 移动应用程序，下面的代码示例显示了验证电子邮件地址的验证规则：

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

`Check`方法返回`boolean`，该值指示值自变量是否为有效的电子邮件地址。 这通过搜索中指定的正则表达式模式的第一个匹配项的值自变量来实现`Regex`构造函数。 可以通过检查的值来确定是否将正则表达式模式找到输入字符串中`Match`对象的`Success`属性。

> [!NOTE]
> 属性验证有时可能涉及依赖项属性。 依赖项属性的一个示例是当的一套有效的值对于属性 A 依赖于已设置属性 B.中的特定值时若要检查一个属性的值是允许的值之一将涉及检索的值的属性 b。此外，当属性 B 的值更改时，属性 A 将需要重新验证。

## <a name="adding-validation-rules-to-a-property"></a>将验证规则添加到属性

在 eShopOnContainers 移动应用中，需要验证的查看模型属性声明为类型`ValidatableObject<T>`，其中`T`是要验证的数据的类型。 下面的代码示例显示两个此类属性的示例：

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

要进行的验证，必须将验证规则添加到`Validations`的每个集合`ValidatableObject<T>`实例，如下面的代码示例中所示：

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

此方法将添加`IsNotNullOrEmptyRule<T>`验证规则应用于`Validations`的每个集合`ValidatableObject<T>`实例，它指定的验证规则的值`ValidationMessage`属性，用于指定如果将会显示验证错误消息验证将失败。

## <a name="triggering-validation"></a>触发验证

属性更改时 eShopOnContainers 移动应用程序中使用的验证方法手动触发某个属性的验证和自动触发器验证。

### <a name="triggering-validation-manually"></a>手动触发验证

视图模型属性，可以手动触发验证。 例如，此错误出现在 eShopOnContainers 移动应用程序时用户点击**登录**按钮上`LoginView`，当使用模拟的服务。 命令委托调用`MockSignInAsync`中的方法`LoginViewModel`，这样就可通过执行调用验证`Validate`方法，下面的代码示例中所示：

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

`Validate`方法会执行验证的用户名和密码在输入用户`LoginView`，通过调用每个的 Validate 方法`ValidatableObject<T>`实例。 下面的代码示例演示了 Validate 方法，从`ValidatableObject<T>`类：

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

此方法清除`Errors`集合，然后检索任何验证规则，它已添加到对象的`Validations`集合。 `Check`执行的每个检索到的验证规则的方法，与`ValidationMessage`任何无法验证数据的验证规则的属性值添加到`Errors`集合`ValidatableObject<T>`实例。 最后，`IsValid`设置属性，并且其值返回到调用的方法，，指明验证是成功还是失败。

### <a name="triggering-validation-when-properties-change"></a>在属性发生更改时触发验证

绑定的属性发生更改时，也可以触发验证。 例如，当在一个双向绑定时`LoginView`设置`UserName`或`Password`触发属性，验证。 下面的代码示例演示如何发生这种情况：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件绑定到`UserName.Value`属性`ValidatableObject<T>`实例，且该控件的`Behaviors`集合具有`EventToCommandBehavior`添加到它的实例。 此行为执行`ValidateUserNameCommand`以响应 [`TextChanged`] 上触发的事件`Entry`，后者时引发中的文本`Entry`更改。 反过来，`ValidateUserNameCommand`委托执行`ValidateUserName`方法，执行`Validate`方法`ValidatableObject<T>`实例。 因此，每次用户输入中的字符`Entry`执行用户名的输入数据的验证的控件。

有关各种行为的详细信息，请参阅[实现行为](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>显示验证错误

EShopOnContainers 移动应用通知任何验证错误的用户通过突出显示包含带有红色下划线，无效的数据的控件，并通过显示错误消息，告知用户为何的数据无效下面包含的控件数据无效。 排除无效的数据后，更改为黑色的行并删除错误消息。 当存在验证错误时，图 6-2 eShopOnContainers 移动应用程序中显示登录视图。

![](validation-images/validation-login.png "在登录过程中显示验证错误")

**图 6-2:** 在登录过程中显示验证错误

### <a name="highlighting-a-control-that-contains-invalid-data"></a>突出显示包含无效的数据的控件

`LineColorBehavior`附加的行为用于突出显示[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)已发生验证错误的控件。 下面的代码示例演示如何`LineColorBehavior`附加的行为附加到`Entry`控件：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件使用显式样式，下面的代码示例中所示：

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

此样式设置`ApplyLineColor`和`LineColor`附加属性的`LineColorBehavior`上附加行为[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件。 有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

时的值`ApplyLineColor`附加的属性是集或更改，`LineColorBehavior`附加的行为执行`OnApplyLineColorChanged`方法，下面的代码示例中所示：

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

此方法的参数提供给连接行为的控件实例和的新旧值`ApplyLineColor`附加属性。 `EntryLineColorEffect`类添加到控件的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合如果`ApplyLineColor`附加的属性`true`，否则它从控件中移除的`Effects`集合。 有关各种行为的详细信息，请参阅[实现行为](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

`EntryLineColorEffect`子类[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)类，并在下面的代码示例所示：

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)类表示对包装出特定于平台的内部效果的独立于平台的影响。 这简化了影响删除过程，因为没有编译时访问的类型信息特定于平台的效果。 `EntryLineColorEffect`调用基类构造函数中，传入参数包含的串联解析组名称，并在每个特定于平台的效果类指定的唯一 ID。

下面的代码示例演示`eShopOnContainers.EntryLineColorEffect`适用于 iOS 的实现：

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

`OnAttached`方法检索的本机控件的 Xamarin.Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制，并通过调用更新线条颜色`UpdateLineColor`方法。 `OnElementPropertyChanged`替代响应上的可绑定的属性更改`Entry`通过如果更新的线条颜色的控件附加`LineColor`属性更改或[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) 属性`Entry`更改。 有关效果的详细信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

有效的数据中的输入时[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件，它将该控件，以指示不存在验证错误的底部应用黑色的行。 图 6-3 显示了此示例。

![](validation-images/validation-blackline.png "黑色的行，该值指示任何验证错误")

**图 6-3**： 黑色的行，该值指示任何验证错误

[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件还具有[ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/)添加到其[ `Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/)集合。 下面的代码示例演示`DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

这[ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/)监视器`UserName.IsValid`属性，而如果它是值将变成`false`，它会执行[ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)，哪些更改`LineColor`附加属性`LineColorBehavior`附加到红色的行为。 图 6-4 显示了此示例。

![](validation-images/validation-redline.png "红色下划线指示验证错误")

**图 6-4**： 红色下划线指示验证错误

中的行[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)在输入的数据无效时，控件将保留红色，否则它将会更改为黑色以指示输入的数据是否有效。

有关触发器的详细信息，请参阅[触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

### <a name="displaying-error-messages"></a>显示错误消息

UI 在下面的数据未能通过验证每个控件的标签控件中显示验证错误消息。 下面的代码示例演示[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)如果用户未输入有效的用户名显示验证错误消息：

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

每个[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)将绑定到`Errors`正在验证该视图模型对象的属性。 `Errors`提供属性`ValidatableObject<T>`类，并且类型是`List<string>`。 因为`Errors`属性可以包含多个验证错误，`FirstValidationErrorConverter`实例用于从显示的集合中检索的第一个错误。

## <a name="summary"></a>总结

EShopOnContainers 移动应用程序执行的查看模型属性的同步客户端验证，并通过突出显示该控件包含无效的数据，并显示错误消息，告知用户将通知用户任何验证错误数据无效的原因。

要求进行验证的查看模型属性属于类型`ValidatableObject<T>`，和每个`ValidatableObject<T>`实例具有验证规则添加到其`Validations`属性。 通过调用从视图模型调用验证`Validate`方法`ValidatableObject<T>`实例，检索验证规则，然后执行针对它们`ValidatableObject<T>``Value`属性。 任何验证错误放入`Errors`属性`ValidatableObject<T>`实例，与`IsValid`属性`ValidatableObject<T>`实例将更新以指示验证是成功还是失败。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
