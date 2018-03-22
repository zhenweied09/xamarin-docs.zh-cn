---
title: "设备样式"
description: "Xamarin.Forms 包括六个动态样式，称为设备样式，Device.Styles 类中。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 7e9d8768969affdbaf1172c59eaa5fc1e64460e8
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2018
---
# <a name="device-styles"></a>设备样式

_Xamarin.Forms 包括六个动态样式，称为设备样式，Device.Styles 类中。_

*设备*了样式：

- [`BodyStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)
- [`CaptionStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyle/)
- [`ListItemDetailTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyle/)
- [`ListItemTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyle/)
- [`SubtitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyle/)
- [`TitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyle/)

所有六个样式只能应用于[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例。 例如，`Label`正在显示的段落正文可能设置其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)属性[ `BodyStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)。

下面的代码示例演示如何使用*设备*XAML 页中的样式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="myBodyStyle" TargetType="Label"
              BaseResourceKey="BodyStyle">
                <Setter Property="TextColor" Value="Accent" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="Title style"
              Style="{DynamicResource TitleStyle}" />
            <Label Text="Subtitle text style"
              Style="{DynamicResource SubtitleStyle}" />
            <Label Text="Body style"
              Style="{DynamicResource BodyStyle}" />
            <Label Text="Caption style"
              Style="{DynamicResource CaptionStyle}" />
            <Label Text="List item detail text style"
              Style="{DynamicResource ListItemDetailTextStyle}" />
            <Label Text="List item text style"
              Style="{DynamicResource ListItemTextStyle}" />
            <Label Text="No style" />
            <Label Text="My body style"
              Style="{StaticResource myBodyStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

设备样式绑定到使用`DynamicResource`标记扩展。 可以通过更改在 iOS 中看到样式具有动态性**可访问性**文本大小的设置。 外观*设备*样式是每个平台上不同，如以下屏幕截图中所示：

![](device-images/device-styles.png "每个平台上的设备样式")

*设备*样式还从通过设置派生[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)为设备样式的密钥名称的属性。 在上面的代码示例`myBodyStyle`继承自[ `BodyStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)将重音字符的文本颜色设置。 有关动态样式继承的详细信息，请参阅[动态样式继承](~/xamarin-forms/user-interface/styles/dynamic.md#dynamic-style-inheritance)。

下面的代码示例演示 C# 中的等效页：

```csharp
public class DeviceStylesPageCS : ContentPage
{
    public DeviceStylesPageCS ()
    {
        var myBodyStyle = new Style (typeof(Label)) {
            BaseResourceKey = Device.Styles.BodyStyleKey,
            Setters = {
                new Setter {
                    Property = Label.TextColorProperty,
                    Value = Color.Accent
                }
            }
        };

        Title = "Device";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label { Text = "Title style", Style = Device.Styles.TitleStyle },
                new Label { Text = "Subtitle style", Style = Device.Styles.SubtitleStyle },
                new Label { Text = "Body style", Style = Device.Styles.BodyStyle },
                new Label { Text = "Caption style", Style = Device.Styles.CaptionStyle },
                new Label { Text = "List item detail text style",
                  Style = Device.Styles.ListItemDetailTextStyle },
                new Label { Text = "List item text style", Style = Device.Styles.ListItemTextStyle },
                new Label { Text = "No style" },
                new Label { Text = "My body style", Style = myBodyStyle }
            }
        };
    }
}
```

[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)每个属性[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例设置为适当的属性从[ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)类。

## <a name="accessibility"></a>可访问性

*设备*样式遵从辅助工具首选项，因此当辅助工具首选项更改每个平台上，将发生更改字体大小。 因此，若要支持辅助功能的文本，确保*设备*样式用作应用程序中的任何文本样式的基础。

以下屏幕截图演示在每个平台上，用最小的可访问的字体大小的设备样式：

[![](device-images/minimum-size.png "每个平台上的可访问的小型设备样式")](device-images/minimum-size-large.png#lightbox "每个平台上的可访问的小型设备样式")

以下屏幕截图演示具有最大的可访问的字体大小的每个平台上的设备样式：

![](device-images/maximum-size.png "每个平台上的可访问的大型设备样式")

## <a name="summary"></a>总结

Xamarin.Forms 包括六个*动态*样式，称为*设备*样式，在[ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)类。 所有六个样式只能应用于[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)实例。


## <a name="related-links"></a>相关链接

- [文本样式](~/xamarin-forms/user-interface/text/styles.md)
- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [动态样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [使用样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device.Styles](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [样式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
