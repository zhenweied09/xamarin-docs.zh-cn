---
title: "章 23 的摘要。 触发器和行为"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: ddb76e00cfe1c19a9d31dc3e53b80a2be0697dbc
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>章 23 的摘要。 触发器和行为

触发器和行为的类似，在于，它们都旨在用于在 XAML 文件中，若要简化以外使用的数据绑定的元素交互和扩展的 XAML 元素的功能。 触发器和行为几乎始终用于可视化的用户界面对象。

若要支持触发器和行为，同时`VisualElement`和`Style`支持两个集合属性：

- [`VisualElement.Triggers`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/) 和[ `Style.Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Triggers/)的类型 `IList<TriggerBase>`
- [`VisualElement.Behaviors`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) 和[ `Style.Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Behaviors/)的类型 `IList<Behavior>`

## <a name="triggers"></a>触发器

触发器是生成一个响应 （另一个属性更改或运行某些代码） 的条件 （属性更改或事件的激发）。 `Triggers`属性`VisualElement`和`Style`属于类型`IList<TriggersBase>`。 [`TriggerBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerBase/) 是四个密封的类派生自抽象类：

- [`Trigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/) 有关基于属性的更改的响应
- [`EventTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/) 对于基于事件触发的响应
- [`DataTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) 为响应基于数据绑定
- [`MultiTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/) 对于基于多个触发器的响应

由该触发器更改其属性的元素上始终设置触发器。

### <a name="the-simplest-trigger"></a>最简单的触发器

[ `Trigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/)类检查属性值的更改，并通过设置相同的元素的另一个属性来进行响应。

`Trigger` 定义三个属性：

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Property/) 类型 `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Value/) 类型 `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Setters/) 类型的`IList<SetterBase>`的内容属性 `Trigger`

此外，`Trigger`需要以下属性继承自`TriggerBase`设置：

- [`TargetType`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.TargetType/) 若要指示在其上元素的类型`Trigger`附加

`Property`和`Value`构成该条件和`Setters`集合是操作的响应。 当指示`Property`具有值由`Value`，则`Setter`中的对象`Setters`应用集合。 当`Property`具有不同的值，将删除 setter。 `Setter` 定义的前两个属性相同的两个属性`Trigger`:

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) 类型 `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/) 类型 `Object`

[ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop)示例演示如何`Trigger`应用于`Entry`可以增加大小`Entry`通过`Scale`属性时`IsFocused`属性`Entry`是`true`。

虽然不常见，但`Trigger`可以在代码中，设置为[ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode)示例演示如何。

[ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers)示例演示如何`Trigger`可以在设置`Style`要应用于多个`Entry`元素。

### <a name="trigger-actions-and-animations"></a>触发器操作和动画

它还可运行基于触发器的少量代码。 此代码会以属性为目标的动画。 一种常用方式是使用[ `EventTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/)，后者定义了两个属性：

- [`Event`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Event/) 类型的`string`，事件的名称
- [`Actions`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Actions/) 类型的`IList<TriggerAction>`，要在响应中运行的操作列表。

若要使用此开关，你需要编写一个类，派生自[ `TriggerAction<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/)、 通常`TriggerAction<VisualElement>`。 此类中，可以定义属性。 这些是纯的 CLR 属性，而不是可绑定属性因为`TriggerAction`不派生自`BindableObject`。 必须重写[ `Invoke` ](https://developer.xamarin.com/api/member/Xamarin.Forms.TriggerAction%3CT%3E.Invoke/p/T/)时调用的操作调用的方法。 此参数是目标元素。

[ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库是一个示例。 它调用`ScaleTo`属性进行动画处理`Scale`元素的属性。 因为其属性之一的类型`Easing`、 [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs)类，可使用标准`Easing`XAML 中的静态字段。

[ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell)示例演示如何调用`ScaleAction`从`EventTrigger`对象该监视器`Focused`和`Unfocused`事件。

[ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell)示例演示如何定义的自定义缓动函数`ScaleAction`代码隐藏文件中。

你也可以调用操作使用`Trigger`(distinguished 从`EventTrigger`)。 这要求，你必须知道，`TriggerBase`定义两个集合：

- [`EnterActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.EnterActions/) 类型 `IList<TriggerAction>`
- [`ExitActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.ExitActions/) 类型 `IList<TriggerAction>`

[ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell)示例演示如何使用这些集合。

### <a name="more-event-triggers"></a>详细事件触发器

[ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库调用`ScaleTo`两次以向上和向下缩放。 [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth)中样式的示例使用此`EventTrigger`来提供可视反馈时`Button`按下。 此 double 动画还有可能使用的类型集合中的两个操作 [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

[ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs)类**Xamarin.FormsBook.Toolkit**库定义可自定义知道的操作。 [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo)示例演示它。

[ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs)类**Xamarin.FormsBook.Toolkit**库仅限于`Entry`元素和集`TextColor`属性设置为红色如果`Text`属性不是`double`。 [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation)示例演示它。

### <a name="data-triggers"></a>数据触发器

[ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/)类似于`Trigger`只不过而不是监视更改值的属性，它会监视数据绑定。 这使属性中会影响另一个元素中的属性的一个元素。

`DataTrigger` 定义三个属性：

- [`Binding`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Binding/) 类型 `BindingBase`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Value/) 类型 `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Setters/) 类型 `IList<SetterBase>`

[ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors)示例要求[ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)库和集的名称的学生为蓝色颜色或粉红色基于`Sex`属性：

[![三重的性别颜色的屏幕截图](images/ch23fg04-small.png "性别颜色")](images/ch23fg04-large.png#lightbox "性别颜色")

[ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler)示例集`IsEnabled`属性`Entry`到`False`如果`Length`属性`Text`属性`Entry`等于 0。 请注意，`Text`属性初始化为空字符串; 默认情况下它是`null`，和`DataTrigger`不会正常工作。

### <a name="combining-conditions-in-the-multitrigger"></a>在 MultiTrigger 组合条件

[ `MultiTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/)是一个条件集合。 当它们进行所有`true`，然后应用 setter。 类定义两个属性：

- [`Conditions`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Conditions/) 类型 `IList<Condition>`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Setters/) 类型 `IList<Setter>`

[`Condition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/) 是一个抽象类，有两个派生类：

- [`PropertyCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/)它具有[ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Property/)和[ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Value/)之类的属性 `Trigger`
- [`BindingCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingCondition/)它具有[ `Binding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Binding/)和[ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Value/)之类的属性 `DataTrigger`

在[ **AndConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions)示例中，`BoxView`仅会变色时四个`Switch`元素所有打开的。

[ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions)示例演示如何建立`BoxView`一种颜色时*任何*四个`Switch`元素处于启用状态。 这需要 De Morgan 法律和反转所有逻辑的应用程序。

组合 AND 和或逻辑不太容易，而且通常不需要不可见`Switch`中间结果的元素。 [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions)示例演示如何`Button`如果可以启用两个`Entry`元素具有在中，键入一些文本，但不是如果二者都提供在中键入一些文本。

## <a name="behaviors"></a>行为

你可以使用触发器执行任何操作，你还可以执行使用行为，但行为始终需要从派生的类[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)和替代以下两种方法：

- [`OnAttachedTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/T/)
- [`OnDetachingFrom`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/T/)

此参数是行为附加到的元素。 通常情况下，`OnAttachedTo`方法会将一些事件处理程序中，附加和`OnDetachingFrom`分离它们。 此类通常将保存某些状态，因为它通常不能共享中`Style`。

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation)示例是类似于**TriggerEntryValidation** ，只不过前者使用行为&mdash; [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。

### <a name="behaviors-with-properties"></a>具有属性的行为

`Behavior<T>` 派生自`Behavior`，它派生自`BindableObject`，因此可以对行为定义可绑定属性。 这些属性可以是数据绑定中处于活动状态。

此进行了演示[ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo)程序，用于使使用[ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 `ValidEmailBehavior` 具有只读可绑定属性，并且用作数据绑定中的源。

[ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv)示例使用此相同的行为来显示其他类型的标记，以指示电子邮件地址无效。

[ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger)示例是在前面的示例变体。 ButtonGlide 使用`DataTrigger`该行为与结合使用。

### <a name="toggles-and-check-boxes"></a>切换和复选框

可以封装类中的切换按钮的行为，例如[ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库，然后定义所有视觉对象以完全在 XAML 中切换。

[ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel)示例使用`ToggleBehavior`与`DataTrigger`使用`Label`使用切换的两个文本字符串。

[ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle)示例由两个之间切换扩展了此概念`FormattedString`对象。

[ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs)类**Xamarin.FormsBook.Toolkit**库派生自`ContentView`，定义`IsToggled`属性，并包含`ToggleBehavior`切换为逻辑。 这便于在 XAML 中，定义切换按钮所示[ **TranditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox)示例。

[ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo)包括[ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs)派生自的类`ToggleBase`并使用[ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)类构造的切换按钮，类似于 Xamarin.Forms `Switch`。

A [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs)中**Xamarin.FormsBook.Toolkit**提供用于使在动画的级别动画[ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)示例。

### <a name="responding-to-taps"></a>响应分流

一个缺陷就`EventTrigger`是，你不能将其附加到`TapGestureRecognizer`响应分流。 绕过该问题的用途是[ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs)中[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

[ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver)示例使用`TapBehavior`使用早期版本`ShiverAction`为点击`BoxView`元素。

[ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews)示例演示如何通过将封装标记减少`ShiverView`类。

### <a name="radio-buttons"></a>单选按钮

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库还具有[ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs)生成按分组的单选按钮的类`string`组名称。

[ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels)程序使用其单选按钮的文本字符串。 [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle)示例使用`Style`外观 checked 和 unchecked 按钮之间的差异。 [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages)示例为其单选按钮使用装箱的映像：

[![三重的单选映像的屏幕截图](images/ch23fg17-small.png "单选按钮图像")](images/ch23fg17-large.png#lightbox "单选按钮图像")

[ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios)示例位于圆圈内部绘制带有圆点的传统显示单选按钮。

### <a name="fades-and-orientation"></a>淡出和方向

最终的示例中， [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders)允许您使用单选按钮的三个不同的颜色选择视图之间切换。 三个视图淡入淡出使用[ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。

该程序还将响应中方向纵向和横向使用之间的更改[ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs)中**Xamarin.FormsBook.Toolkit**库。



## <a name="related-links"></a>相关链接

- [章 23 全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [章 23 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [使用触发器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 行为](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
