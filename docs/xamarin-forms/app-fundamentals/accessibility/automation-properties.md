---
title: 自动化属性
description: 本文介绍如何在 Xamarin.Forms 应用程序中使用 AutomationProperties 类，以便屏幕阅读器可以说出是有关元素的页上。
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c720b9f38d2a34155face10b75f5f054f3313711
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130945"
---
# <a name="automation-properties-in-xamarinforms"></a>在 Xamarin.Forms 中的自动化属性

_Xamarin.Forms 允许使用附加的属性从 AutomationProperties 类，后者又设置本机可访问性的值在用户界面元素上设置的可访问性值。本文介绍如何使用 AutomationProperties 类，以便屏幕阅读器可以说出是有关元素的页上。_

Xamarin.Forms，可以通过以下附加属性的用户界面元素上设置的自动化属性：

- `AutomationProperties.IsInAccessibleTree` – 指示元素是否可用于可访问的应用程序。 有关详细信息，请参阅[AutomationProperties.IsInAccessibleTree](#isinaccessibletree)。
- `AutomationProperties.Name` – 可作为元素的 speakable 标识符的元素的简短说明。 有关详细信息，请参阅[AutomationProperties.Name](#name)。
- `AutomationProperties.HelpText` – 元素，它可以视为与元素关联的工具提示文本的详细说明。 有关详细信息，请参阅[AutomationProperties.HelpText](#helptext)。
- `AutomationProperties.LabeledBy` – 允许另一个元素定义当前元素的可访问性信息。 有关详细信息，请参阅[AutomationProperties.LabeledBy](#labeledby)。

这些附加属性设置本机可访问性的值，以便屏幕阅读器可以说出的元素。 有关附加属性的详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)。

> [!IMPORTANT]
> 使用`AutomationProperties`附加的属性可能会影响在 Android 上的 UI 测试执行。 [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId)，`AutomationProperties.Name`并`AutomationProperties.HelpText`属性将设置本机`ContentDescription`属性，与`AutomationProperties.Name`和`AutomationProperties.HelpText`属性值优先于`AutomationId`值 (如果这两个`AutomationProperties.Name`和`AutomationProperties.HelpText`进行设置，则值将连接)。 这意味着，任何测试寻找`AutomationId`如果将失败`AutomationProperties.Name`或`AutomationProperties.HelpText`还在元素上设置。 在此方案中，应更改的 UI 测试来查找的值`AutomationProperties.Name`或`AutomationProperties.HelpText`，或这两者的串联。

每个平台都有不同的屏幕读取器旁白的可访问性值：

- iOS 具有语音朗读。 有关详细信息，请参阅[VoiceOver 与你的设备上测试可访问性](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)developer.apple.com 上。
- Android 有 TalkBack。 有关详细信息，请参阅[测试您的应用程序的可访问性](https://developer.android.com/training/accessibility/testing.html#talkback)developer.android.com 上。
- Windows 具有讲述人。 有关详细信息，请参阅[使用讲述人验证主要应用方案](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/)。

但是，屏幕阅读器的具体行为取决于对软件和它的用户的配置。 例如，大多数屏幕读取器读取时接收焦点，与控件关联的文本使用户能够定位本身，因为它们在页上控件之间移动。 某些屏幕阅读器，请参阅整个应用程序用户界面时将显示一个页面，这使用户能够在尝试导航它之前收到所有页面的可用信息性内容。

屏幕阅读器，请参阅其他可访问性值。 在示例应用程序：

- 将读取 voiceOver`Placeholder`的值`Entry`后, 跟使用该控件的说明。
- 将读取 talkBack`Placeholder`的值`Entry`后, 跟`AutomationProperties.HelpText`值后, 跟使用该控件的说明。
- 讲述人将读取`AutomationProperties.LabeledBy`的值`Entry`后, 跟使用控件的说明。

此外，讲述人将确定优先级`AutomationProperties.Name`， `AutomationProperties.LabeledBy`，然后`AutomationProperties.HelpText`。 在 Android 上，可以将合并 TalkBack`AutomationProperties.Name`和`AutomationProperties.HelpText`值。 因此，建议，全面的可访问性测试上执行每个平台，以确保获得最佳体验。

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

`AutomationProperties.IsInAccessibleTree`附加的属性是`boolean`，它确定如果该元素是可访问，并且因此可见，对屏幕读取器。 它必须设置为`true`若要使用其他可访问性附加属性。 这可以实现在 XAML 中，如下所示：

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

或者，可以将其设置 C# 中，如下所示：

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> 请注意， [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法还可用于设置`AutomationProperties.IsInAccessibleTree`附加属性 – `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

`AutomationProperties.Name`附加的属性值应为屏幕读取器使用地宣布推出一个元素的较短的描述性文本字符串。 应为具有对于了解内容或与用户界面进行交互非常重要的含义的元素设置此属性。 这可以实现在 XAML 中，如下所示：

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

或者，可以将其设置 C# 中，如下所示：

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> 请注意， [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法还可用于设置`AutomationProperties.Name`附加属性 – `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

`AutomationProperties.HelpText`附加属性应设置为文本描述用户界面元素，并可以与元素关联的作为工具提示文本的想法。 这可以实现在 XAML 中，如下所示：

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

或者，可以将其设置 C# 中，如下所示：

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> 请注意， [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法还可用于设置`AutomationProperties.HelpText`附加属性 – `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

在某些平台上以进行编辑控件，如[ `Entry` ](xref:Xamarin.Forms.Entry)，则`HelpText`属性有时可以省略，替换为占位符文本。 例如，"输入您的姓名"是的良好候选项[ `Entry.Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder)将文本放在用户的实际输入之前在控件中的属性。

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

`AutomationProperties.LabeledBy`附加的属性允许另一个元素定义当前元素的可访问性信息。 例如， [ `Label` ](xref:Xamarin.Forms.Label)旁边[ `Entry` ](xref:Xamarin.Forms.Entry)可以用于描述什么`Entry`表示。 这可以实现在 XAML 中，如下所示：

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

或者，可以将其设置 C# 中，如下所示：

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> 请注意， [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法还可用于设置`AutomationProperties.IsInAccessibleTree`附加属性 – `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="related-links"></a>相关链接

- [附加属性](~/xamarin-forms/xaml/attached-properties.md)
- [可访问性 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
