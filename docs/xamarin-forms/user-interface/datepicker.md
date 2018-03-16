---
title: "使用包含 DatePicker"
description: "Xamarin.Forms 视图，它允许用户选择日期"
ms.topic: article
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/12/2018
ms.openlocfilehash: d47499c1e309fbc67c85b55cacbbba3942188f54
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="using-datepicker"></a>使用包含 DatePicker

_Xamarin.Forms 视图，它允许用户选择日期_

Xamarin.Forms [ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)调用平台的日期选取器控件，并允许用户选择一个日期。 `DatePicker` 定义五个属性：

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) 类型的[ `DateTime` ](https://developer.xamarin.com/api/type/System.DateTime/)，默认为 1900 年的第一天。
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) 类型的`DateTime`，2100 年的最后一天的默认值。
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) 类型的`DateTime`、 所选的日期，默认值为[ `DateTime.Today` ](https://developer.xamarin.com/api/property/System.DateTime.Today/)。
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) 类型的`string`、[标准](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自定义](/dotnet/standard/base-types/custom-date-and-time-format-strings/).NET 格式字符串，默认为"D"，长日期模式。
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.TextColor/) 类型的[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)，用来显示所选的日期，默认为颜色[ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/)。

`DatePicker`激发[ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/)事件时用户选择日期。

> [!WARNING]
> 设置时`MinimumDate`和`MaximumDate`，请确保`MinimumDate`是否始终小于或等于`MaximumDate`。 否则为`DatePicker`将引发的异常。

在内部，`DatePicker`确保`Date`之间`MinimumDate`和`MaximumDate`(含） 之间。 如果`MinimumDate`或`MaximumDate`设置以便`Date`它们之间不是`DatePicker`将调整的值`Date`。

所有五个属性由[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)对象，这意味着它们可以风格，并且这些属性可以将数据绑定的目标。 `Date`属性具有的默认绑定模式[ `BindingMode.TwoWay` ](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/)，这意味着它可以是数据绑定中使用的应用程序的目标[模型-视图-视图模型 (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)体系结构。

## <a name="initializing-the-datetime-properties"></a>初始化的日期时间属性

在代码中，你可以初始化`MinimumDate`， `MaximumDate`，和`Date`类型的值的属性`DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

当`DateTime`中的值指定在 XAML 中，XAML 分析器使用`DateTime.Parse`方法替换`CultureInfo.InvariantCulture`参数，以将字符串转换为`DateTime`值。 必须在精确的格式指定日期： 两位数的月份、 两位数日和四位数年份由斜杠分隔：

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

如果`BindingContext`属性`DatePicker`被设置为包含类型的属性 ViewModel 实例`DateTime`名为`MinDate`， `MaxDate`，和`SelectedDate`（例如），你可以实例化`DatePicker`如下所示:

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

在此示例中，所有三个属性将初始化为视图模型中的相应属性。 因为`Date`属性具有的绑定模式`TwoWay`、 用户选择自动反映在视图模型中任何新的日期。

如果`DatePicker`不包含一个绑定上其`Date`属性，应用程序应将处理程序附加到`DateSelected`事件要通知当用户选择新的日期。

## <a name="datepicker-and-layout"></a>包含 DatePicker 和布局

可以使用不受约束的水平布局选项，如`Center`， `Start`，或`End`与`DatePicker`:

```xaml
<DatePicker ··· 
            HorizontalOptions="Center" 
            ··· />
```

但是，这不是建议在一起。 具体取决于的设置`Format`属性，选择日期可能需要不同的显示宽度。 例如，"D"格式字符串会导致`DateTime`若要显示日期的长格式和"星期三，年 9 月 12，2018"中，需要更大的显示宽度比"星期五，5 月 4，2018"。 根据所用平台，这种差异可能会导致`DateTime`视图，以更改宽度在布局中，或显示被截断。

> [!TIP]
> 最好是使用默认`HorizontalOptions`设置`Fill`与`DatePicker`，而不是使用宽度为`Auto`时将`DatePicker`中`Grid`单元格。

## <a name="datepicker-in-an-application"></a>应用程序中的 DatePicker

[ **DaysBetweenDates** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)示例包含两个`DatePicker`页面上的视图。 这些可用于选择两个日期，并程序计算的这些日期之间的天数。 程序不会更改的设置`MinimumDate`和`MaximumDate`属性，因此在两个日期必须介于 1900 年 2100年。

下面是 XAML 文件：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DaysBetweenDates"
             x:Class="DaysBetweenDates.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <StackLayout Margin="10">
        <Label Text="Days Between Dates"
               Style="{DynamicResource TitleStyle}"
               Margin="0, 20"
               HorizontalTextAlignment="Center" />

        <Label Text="Start Date:" />

        <DatePicker x:Name="startDatePicker"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <Label Text="End Date:" />

        <DatePicker x:Name="endDatePicker"
                    MinimumDate="{Binding Source={x:Reference startDatePicker},
                                          Path=Date}"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <StackLayout Orientation="Horizontal"
                     Margin="0, 0, 0, 30">
            <Label Text="Include both days in total: "
                   VerticalOptions="Center" />
            <Switch x:Name="includeSwitch"
                    Toggled="OnSwitchToggled" />
        </StackLayout>

        <Label x:Name="resultLabel"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

每个`DatePicker`分配`Format`长日期格式的"D"属性。 此外请注意，`endDatePicker`对象绑定中有一个面向其`MinimumDate`属性。 绑定源是所选`Date`属性`startDatePicker`对象。 这可确保的结束日期始终更高版本或等于开始日期。 除了这两个`DatePicker`对象，`Switch`标记为"Include 总共这两个天"。 

这两个`DatePicker`视图具有处理程序附加到`DateSelected`事件，和`Switch`具有一个处理程序附加到其`Toggled`事件。 这些事件处理程序中的代码隐藏文件和触发新计算的两个日期之间的天数：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    void OnDateSelected(object sender, DateChangedEventArgs args)
    {
        Recalculate();
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        Recalculate();
    }

    void Recalculate()
    {
        TimeSpan timeSpan = endDatePicker.Date - startDatePicker.Date +
            (includeSwitch.IsToggled ? TimeSpan.FromDays(1) : TimeSpan.Zero);

        resultLabel.Text = String.Format("{0} day{1} between dates",
                                            timeSpan.Days, timeSpan.Days == 1 ? "" : "s");
    }
}
```

该示例首先运行时，同时`DatePicker`视图都已初始化为今天的日期。 下面的屏幕截图显示在 iOS、 Android 和通用 Windows 平台上运行的程序：

[![开始日期之间的天数](datepicker-images/DaysBetweenDatesStart.png "开始日期之间的天数")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "开始日期之间的天数")

点击任一`DatePicker`显示时，将调用平台日期选取器。 三个平台，以非常不同方式实现此日期选取器，但每个方法是为该平台的用户所熟悉：

[![日期之间的天数选择](datepicker-images/DaysBetweenDatesSelect.png "日期之间的天数选择")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "日期之间的天数选择")

选择两个日期后，应用程序将显示的这些日期之间的天数：

[![日期结果之间的天数](datepicker-images/DaysBetweenDatesResult.png "日期结果之间的天数")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "日期结果之间的天数")

## <a name="related-links"></a>相关链接

- [DaysBetweenDates 示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)
- [DatePicker API](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)
