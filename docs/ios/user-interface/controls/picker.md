---
title: 在 Xamarin.iOS 选取器控件
description: 本文档介绍如何设计和使用 Xamarin.iOS 应用程序中的选取器控件。 它讨论如何在代码中并在 iOS 设计器中实现了选取器。
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/02/2017
ms.openlocfilehash: 7f46d354af86027d1e2656171c6595562d3555a6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789907"
---
# <a name="picker-control-in-xamarinios"></a>在 Xamarin.iOS 选取器控件

选取器控件显示包含正在突出显示的选定值的值的可滚动列表的类似的滚轮控件。 用户旋转鼠标滚轮以选择他们希望的选项。

一个特定用户案例的选取以设置日期和/或时间。 若要为此 Apple 提供已创建自定义调用 UIDatePicker UIPickerView 类的子类。

本文介绍了实现和使用[选取器](#picker)和[日期选取器](#datepicker)控件。

<a name="picker" />

## <a name="picker"></a>选取器

### <a name="implementing-a-picker"></a>实现了选取器

选取器实现的实例化一个新[`UIPickerView`](https://developer.xamarin.com/api/type/UIKit.UIPickerView/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="pickers-and-storyboards"></a>选取和情节提要

如果使用 iOS 设计器来创建你的 UI，则选择器可以添加到你的布局中从工具箱:

![](picker-images/image1.png)


### <a name="working-with-picker"></a>使用选取器

在代码中是否创建了选取器，或通过将情节提要，你将需要分配后_模型_到它，以便可以传递并与数据; 交互

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    var pickerModel = new PeopleModel(personLabel);

    personPicker.Model = pickerModel;
}
```

下面的代码演示了模型的示例：

```csharp
public class PeopleModel : UIPickerViewModel
{
    public string[] names = new string[] {
            "Amy Burns",
            "Kevin Mullins",
            "Craig Dunn",
            "Joel Martinez",
            "Charles Petzold",
            "David Britch",
            "Mark McLemore",
            "Tom Opegenorth",
            "Joseph Hill",
            "Miguel De Icaza"
        };

    private UILabel personLabel;

    public PeopleModel(UILabel personLabel)
    {
        this.personLabel = personLabel;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 2;
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return names.Length;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        if (component == 0)
            return names[row];
        else
            return row.ToString();
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {   
        personLabel.Text = $"This person is: {names[pickerView.SelectedRowInComponent(0)]},\n they are number {pickerView.SelectedRowInComponent(1)}";
    }

    public override nfloat GetComponentWidth(UIPickerView picker, nint component)
    {
        if (component == 0)
            return 240f;
        else
            return 40f;
    }

    public override nfloat GetRowHeight(UIPickerView picker, nint component)
    {
        return 40f;
    }
```

首先你需要传入一些数据来提供不同的用户能够选择选项。 当可能尝试保留尽可能短，此列表，或者如果需要尝试使用多个拨号 (调用*组件*):

![使用两个组件的选取器](picker-images/image3.png)

若要设置的组件数量，请重写`GetComponentCount`方法： 

```csharp
public override nint GetComponentCount(UIPickerView pickerView)
{
    return 2;
}
```

返回值表示的拨打你选取器将具有数。

### <a name="customizing-appearance"></a>自定义外观
 
外观`UIPickerView`可以通过使用自定义`UIPickerView.UIPickerViewAppearance`类或通过重写`UIPickerViewModel.GetView`和`UIPickerViewModel.GetRowHeight`中的方法`UIPickerViewModel`。


<a name="datepicker" />

## <a name="date-picker"></a>日期选取器

### <a name="implementing-a-date-picker"></a>实现日期选取器

日期选取器实现的实例化一个新[ `UIDatePickerView` ](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="date-pickers-and-storyboards"></a>日期选取器和情节提要

如果你使用 iOS 设计器来创建你的 UI，**日期选取器**可以从工具箱添加到你的布局。 可以从属性填充调整以下属性：

![](picker-images/image2.png)

* **模式**– 该日期和时间模式。 这可以是日期、 时间、 日期和时间或一个倒计时计时器。 
* **区域设置**– 日期选取器的区域设置。 选择**默认**设置为系统默认值或设置为任何特定的区域设置。
* **间隔**– 显示的时钟选项将显示在其中的增量。
* **日期、 最小日期、 最大日期**– 为可选的日期设置初始日期选取器将显示和约束。

### <a name="configuring-the-datepicker"></a>配置包含 DatePicker

你可以限制用户可以通过使用从选择的日期范围`MinimumDate`和`MaximumDate`属性。 下面的代码段演示如何设置 60 年以前，但今天之间的范围的示例：

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();

components.Year = -60;

NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);

datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

或者，你可以使用.NET 控件设置最小和最大日期范围。 例如：

```csharp
DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
```

你还可以设置`MinuteInterval`属性来设置从该处选取器将显示分钟的间隔。 下面的代码段可以用于设置将在 10 为间隔的分钟选择器。

```csharp
datePickerView.MinuteInterval = 10;
```

有四种模式日期选取器可通过使用设置为[ `UIDatePicker.Mode` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.Mode/)属性。 下面列出了每个以及如何实现它的一个示例：

#### <a name="time"></a>时间

时模式显示使用小时和分钟的选择器和可选 AM 或 PM 指定内容的时间。 设置与`UIDatePickerMode.Time`属性。 例如：

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

下图说明了此 DatePicker 模式的示例：

![](picker-images/image8.png)



#### <a name="date"></a>日期

日期模式显示带有月、 日和年选择器的日期。 设置与`UIDatePickerMode.Date`属性。 例如：

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

下图说明了此 DatePicker 的示例：

![](picker-images/image7.png)

选择器的顺序取决于的区域设置`UIDatePicker`。 默认情况下必须先将此设置为系统默认值。 图中显示的布局中的选择器`en_US`区域设置，但若要将其更改为一天 |月 |年布局，你可以使用下面的代码设置的区域设置：

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![](picker-images/image9.png)


#### <a name="date-and-time"></a>日期和时间

日期和时间模式显示日期的时间以小时和分钟和可选的 AM 或 PM 代码 dependings shortend 视图上，如果使用 12 或 24 小时时钟制。 设置与`UIDatePickerMode.DateAndTime`属性。 例如：

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

下图说明了此 DatePicker 的示例：

![](picker-images/image6.png)

与[日期](#Date)，依赖于的区域设置的选择器的顺序和 12 或 24 小时制利用`UIDatePicker`。

#### <a name="countdown-timer"></a>倒计时计时器

倒计时计时器模式显示小时和分钟值。 设置与`UIDatePickerMode.CountDownTimer`属性。 例如：

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

下图说明了此 DatePicker 的示例：

![](picker-images/image5.png)

你可以使用`CountDownDuration`要捕获的倒计时日期选取器值 dispayed 属性。 例如，若要添加到当前日期的倒计时值，可以使用下面的代码：

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="formatting"></a>格式化 

可以使用捕获的值的时间、 日期和 DateAndTime 模式`Date`UIDatePicker 属性 (例如： `datePickerView.Date`)，它属于类型 NSDate。 若要格式化此日期为易读的事，使用[ `NSDateFormatter` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatter/)。 下面的示例演示如何使用某些可用的属性对此类。

`DateFormat`设置以字符串形式来表示日期的显示方式：

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

`TimeStyle`属性集`NSDateFormatterStyle`:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

为字段`NSDateFormatterStyle`显示，如下所示：

![](picker-images/timestyle.png)

`DateStyle`属性集`NSDateFormatterStyle`:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

为字段`NSDateFormatterStyle`显示，如下所示：

![](picker-images/datestyle.png)

然后可以通过使用下面的代码将输出格式化为字符串 NSDate:

```csharp
dateLabel.Text = dateTimeformat.ToString(datePickerView.Date);
```

## <a name="related-links"></a>相关链接

- [PickerControl （示例）](https://developer.xamarin.com/samples/monotouch/PickerControl/)
