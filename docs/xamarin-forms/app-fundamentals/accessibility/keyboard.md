---
title: 键盘导航
description: 不使用默认选项卡上序列，而是，有时需要通过 tab 键序列指定的 TabIndex 和 IsTapStop 属性组合优化你的 UI。
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: f703dff56d2947c35a9bc76e0eb909bfe9023bac
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130958"
---
# <a name="keyboard-navigation-in-xamarinforms"></a>在 Xamarin.Forms 中的键盘导航

某些用户都可以使用不提供相应的键盘访问的应用程序引起的困难。 指定控件的 tab 键顺序使键盘导航并准备应用程序页，以按特定顺序接收输入。

默认情况下，控件的 tab 键顺序是它们是列入 XAML，或以编程方式添加到子集合的相同顺序。 此顺序是在其中控件将导航到与键盘、 顺序和此默认顺序通常是最佳顺序。 但是，默认顺序并不总是与预期的顺序相同，在下面的 XAML 代码示例所示：

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname" />
</Grid>
```

下面的屏幕截图显示了此代码示例的默认选项卡顺序：

![](keyboard-images/default-tab-order.png "默认值基于行的 tab 键顺序")

此处的 tab 键顺序是基于行，控件 XAML 中的列出顺序。 因此，按 Tab 键导航名[ `Entry` ](xref:Xamarin.Forms.Entry)情况下后, 跟姓氏`Entry`实例。 但是，更直观的体验是使用列的第一个选项卡导航栏中，以便按 Tab 键导航名姓氏对。 这可以通过指定的输入控件的 tab 键顺序来实现。

> [!NOTE]
> 通用 Windows 平台上的键盘快捷方式可定义，使应用程序的可见 UI 通过键盘，而不是通过触摸或鼠标用户可以快速导航并进行交互以直观的方式。 有关详细信息，请参阅[设置 VisualElement 访问密钥](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#visualelement-accesskeys)。

## <a name="setting-the-tab-order"></a>设置 tab 键顺序

`VisualElement.TabIndex`属性用于指示的顺序[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)实例时用户通过按 Tab 键导航控件接收焦点。 该属性的默认值为 0，并将它设置为任何`int`值。

以下规则适用时使用默认 tab 键顺序，或通过`TabIndex`属性：

 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) 实例`TabIndex`等于 0 添加到基于其在 XAML 或子集合中的声明顺序的 tab 键顺序。
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) 实例`TabIndex`大于 0 添加到 tab 键顺序基于其`TabIndex`值。
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) 实例`TabIndex`小于 0 添加到 tab 键顺序，并且出现前面任何零值。
 - 在冲突`TabIndex`通过按声明顺序来解析。

在定义后 tab 键顺序，按 Tab 键将循环移动焦点控件按升序`TabIndex`顺序，一旦达到最终的控件周围包装到开头。

下面的 XAML 示例演示`TabIndex`设置输入控件的启用列第一个选项卡导航属性：

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="1" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="3" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="2" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="4" />
</Grid>
```

下面的屏幕截图显示了此代码示例的选项卡顺序：

![](keyboard-images/correct-tab-order.png "基于列的 tab 键顺序")

此处的 tab 键顺序是基于列的。 因此，按 Tab 键导航名姓氏[ `Entry` ](xref:Xamarin.Forms.Entry)对。

## <a name="excluding-controls-from-the-tab-order"></a>不从 tab 键顺序包括控件

除了设置控件的 tab 键顺序，可能需要从 tab 键顺序中排除控件。 这是通过设置一种方法的实现[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement)属性的控件，以便`false`，因为已禁用的控件不在 tab 键顺序。

但是，可能需要从 tab 键顺序中排除控件，即使它们不已禁用。 这可以通过`VisualElement.IsTapStop`属性，用于指示是否[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)包含在 tab 导航。 其默认值是`true`，并且其值为时`false`如果控件将被忽略的 tab 键导航基础结构，而无论`TabIndex`设置。

## <a name="supported-controls"></a>支持的控件

`TabIndex`和`IsTapStop`接受键盘输入一个或多个平台上的以下控件支持属性：

- [`Button`](xref:Xamarin.Forms.Button)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`Switch`](xref:Xamarin.Forms.Switch)
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

> [!NOTE]
> 每个控件不是每个平台上可获得焦点的选项卡。

## <a name="related-links"></a>相关链接

- [可访问性 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
