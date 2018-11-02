---
title: 可重用 EventToCommandBehavior
description: 行为可用于将命令与不设计与命令进行交互的控件相关联。 本文演示如何创建和使用 Xamarin.Forms 行为以事件激发时调用命令。
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2609ce4ea677c6b03021a919599c250abff663a3
ms.sourcegitcommit: 06a52ac36031d0d303ac7fc8163a59c178799c80
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2018
ms.locfileid: "50911549"
---
# <a name="reusable-eventtocommandbehavior"></a>可重用 EventToCommandBehavior

_行为可用于将命令与不设计与命令进行交互的控件相关联。本文演示如何创建和使用 Xamarin.Forms 行为以事件激发时调用命令。_

## <a name="overview"></a>概述

`EventToCommandBehavior`类是在响应中执行的命令可重复使用 Xamarin.Forms 自定义行为*任何*事件触发。 默认情况下，事件参数的事件将传递到该命令，并且可以根据需要通过转换[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)实现。

以下行为属性必须设置为使用行为：

- **EventName** – 行为侦听事件的名称。
- **命令**–`ICommand`要执行。 行为期望找到`ICommand`实例上[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)可能继承自父元素的附加控件。

此外可以设置以下可选行为属性：

- **CommandParameter** – `object` ，将传递到该命令。
- **转换器**– [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)实现，它将更改的事件参数数据的格式，如之间传递*源*和*目标*由绑定引擎。

> [!NOTE]
> `EventToCommandBehavior`是一个自定义类，可以位于[EventToCommand 行为示例](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)，并不是 Xamarin.Forms 的一部分。

## <a name="creating-the-behavior"></a>创建行为

`EventToCommandBehavior`类派生自`BehaviorBase<T>`类，后者又派生[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类。 目的`BehaviorBase<T>`类是为需要的任何 Xamarin.Forms 行为提供基类[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)设置为附加的控件的行为。 这可确保该行为可以绑定到或执行`ICommand`指定的`Command`属性时使用行为。

`BehaviorBase<T>`类提供了可重写[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法以设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的行为，并且可重写[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法清除的`BindingContext`。 此外，类存储中的附加控件的引用`AssociatedObject`属性。

### <a name="implementing-bindable-properties"></a>实现可绑定属性

`EventToCommandBehavior`类定义了四个[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)情况下，执行用户定义事件激发时的命令。 这些属性下面的代码示例所示：

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  public static readonly BindableProperty EventNameProperty =
    BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
  public static readonly BindableProperty CommandProperty =
    BindableProperty.Create ("Command", typeof(ICommand), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty CommandParameterProperty =
    BindableProperty.Create ("CommandParameter", typeof(object), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty InputConverterProperty =
    BindableProperty.Create ("Converter", typeof(IValueConverter), typeof(EventToCommandBehavior), null);

  public string EventName { ... }
  public ICommand Command { ... }
  public object CommandParameter { ... }
  public IValueConverter Converter { ...  }
  ...
}
```

当`EventToCommandBehavior`用类`Command`属性应为数据绑定到`ICommand`执行响应事件激发中定义的`EventName`属性。 该行为将想要查找`ICommand`上[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)附加的控件。

默认情况下，该事件的事件参数将传递给命令。 此数据可以根据需要转换之间传递*源*并*目标*由绑定引擎使用，通过指定[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)实现作为`Converter`属性值。 或者，参数可以传递给该命令通过指定`CommandParameter`属性值。

### <a name="implementing-the-overrides"></a>实现重写

`EventToCommandBehavior`类将重写[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))并[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法的`BehaviorBase<T>`类，如下面的代码示例中所示：

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  protected override void OnAttachedTo (View bindable)
  {
    base.OnAttachedTo (bindable);
    RegisterEvent (EventName);
  }

  protected override void OnDetachingFrom (View bindable)
  {
    DeregisterEvent (EventName);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法执行安装程序通过调用`RegisterEvent`方法，传递的值中`EventName`属性作为参数。 [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法通过调用执行清理`DeregisterEvent`方法，传递的值中`EventName`属性作为参数。

### <a name="implementing-the-behavior-functionality"></a>实现行为功能

行为的目的是执行命令定义的`Command`属性中定义的事件触发响应`EventName`属性。 核心行为功能下面的代码示例所示：

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  void RegisterEvent (string name)
  {
    if (string.IsNullOrWhiteSpace (name)) {
      return;
    }

    EventInfo eventInfo = AssociatedObject.GetType ().GetRuntimeEvent (name);
    if (eventInfo == null) {
      throw new ArgumentException (string.Format ("EventToCommandBehavior: Can't register the '{0}' event.", EventName));
    }
    MethodInfo methodInfo = typeof(EventToCommandBehavior).GetTypeInfo ().GetDeclaredMethod ("OnEvent");
    eventHandler = methodInfo.CreateDelegate (eventInfo.EventHandlerType, this);
    eventInfo.AddEventHandler (AssociatedObject, eventHandler);
  }

  void OnEvent (object sender, object eventArgs)
  {
    if (Command == null) {
      return;
    }

    object resolvedParameter;
    if (CommandParameter != null) {
      resolvedParameter = CommandParameter;
    } else if (Converter != null) {
      resolvedParameter = Converter.Convert (eventArgs, typeof(object), null, null);
    } else {
      resolvedParameter = eventArgs;
    }        

    if (Command.CanExecute (resolvedParameter)) {
      Command.Execute (resolvedParameter);
    }
  }
  ...
}
```

`RegisterEvent`方法执行以响应`EventToCommandBehavior`要附加到控件，并且它收到的值`EventName`属性作为参数。 然后，该方法尝试找到中定义的事件`EventName`属性，可在附加的控件。 前提是该事件可定位，请`OnEvent`方法注册为事件处理程序方法。

`OnEvent`以响应事件激发中定义的执行方法`EventName`属性。 前提`Command`属性引用的有效`ICommand`，该方法尝试检索参数要传递给`ICommand`，如下所示：

- 如果`CommandParameter`属性定义了一个参数，它就会检索。
- 否则为如果`Converter`属性定义[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)实现，该转换器执行，并将事件参数数据转换之间传递*源*并*目标*由绑定引擎。
- 否则，事件自变量被认为是该参数。

数据绑定`ICommand`然后执行，则将在参数中传递给提供程序的命令[ `CanExecute` ](xref:Xamarin.Forms.Command.CanExecute(System.Object))方法将返回`true`。

尽管没有显示在这里，但是`EventToCommandBehavior`还包括`DeregisterEvent`方法，则由[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法。 `DeregisterEvent`方法用于查找并取消注册中定义的事件`EventName`属性中，清除任何潜在的内存泄漏。

## <a name="consuming-the-behavior"></a>使用行为

`EventToCommandBehavior`类可以附加到[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)集合的一个控件，如以下 XAML 代码示例所示：

```xaml
<ListView ItemsSource="{Binding People}">
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}"
                                  Converter="{StaticResource SelectedItemConverter}" />
  </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

以下代码示例显示相应的 C# 代码：

```csharp
var listView = new ListView ();
listView.SetBinding (ItemsView<Cell>.ItemsSourceProperty, "People");
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
  Converter = new SelectedItemEventArgsToSelectedItemConverter ()
});

var selectedItemLabel = new Label ();
selectedItemLabel.SetBinding (Label.TextProperty, "SelectedItemText");
```

`Command`行为的属性被数据绑定到`OutputAgeCommand`属性关联的 ViewModel，而`Converter`属性设置为`SelectedItemConverter`实例，它将返回[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)的[ `ListView` ](xref:Xamarin.Forms.ListView)从[ `SelectedItemChangedEventArgs` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs)。

在运行时，行为将响应与控件交互。 中选择一项时[ `ListView` ](xref:Xamarin.Forms.ListView)，则[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)会触发事件，其将执行`OutputAgeCommand`ViewModel 中。 这又更新 ViewModel`SelectedItemText`属性的[ `Label` ](xref:Xamarin.Forms.Label)将绑定到，如以下屏幕截图中所示：

[![](event-to-command-behavior-images/screenshots-sml.png "示例应用程序与 EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "示例应用程序与 EventToCommandBehavior")

使用此行为时触发事件时，执行命令的优点是命令可以与没有专门用于使用命令进行交互的控件相关联。 此外，这将从代码隐藏文件移除样板事件处理代码。

## <a name="summary"></a>总结

本文演示了使用 Xamarin.Forms 行为事件激发时调用命令。 行为可用于将命令与不设计与命令进行交互的控件相关联。


## <a name="related-links"></a>相关链接

- [EventToCommand 行为 （示例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [行为](xref:Xamarin.Forms.Behavior)
- [行为&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
