---
title: "在用户界面元素上设置可访问性值"
description: "Xamarin.Forms 允许可访问性值在用户界面元素上设置通过使用 AutomationProperties 类，后者又设置本机可访问性值的附加的属性。 本文介绍如何使用 AutomationProperties 类，以便屏幕读取器可在页上的元素进行通信。"
ms.topic: article
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 4aeeea7f946a121b12741d2da217daf531935849
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="setting-accessibility-values-on-user-interface-elements"></a>在用户界面元素上设置可访问性值

_Xamarin.Forms 允许可访问性值在用户界面元素上设置通过使用 AutomationProperties 类，后者又设置本机可访问性值的附加的属性。本文介绍如何使用 AutomationProperties 类，以便屏幕读取器可在页上的元素进行通信。_

## <a name="overview"></a>概述

Xamarin.Forms 允许在通过以下附加属性的用户界面元素上设置辅助功能值：

- `AutomationProperties.IsInAccessibleTree` – 指示元素是否是可用于可访问应用程序。 有关详细信息，请参阅[AutomationProperties.IsInAccessibleTree](#isinaccessibletree)。
- `AutomationProperties.Name` – 用作元素的 speakable 标识符的元素的简短说明。 有关详细信息，请参阅[AutomationProperties.Name](#name)。
- `AutomationProperties.HelpText` – 了元素，它可视为与元素关联的工具提示文本较长说明。 有关详细信息，请参阅[AutomationProperties.HelpText](#helptext)。
- `AutomationProperties.LabeledBy` – 允许另一个元素定义当前元素的可访问性信息。 有关详细信息，请参阅[AutomationProperties.LabeledBy](#labeledby)。

这些附加属性设置本机可访问性值，以便屏幕阅读器可以说出有关该元素。 有关附加属性的详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)。

> [!IMPORTANT]
> 使用`AutomationProperties`附加的属性可能会影响在 Android 上的 UI 测试执行。 [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/)，`AutomationProperties.Name`和`AutomationProperties.HelpText`属性都将设置本机`ContentDescription`属性，与`AutomationProperties.Name`和`AutomationProperties.HelpText`属性值优先于`AutomationId`值 (如果这两个`AutomationProperties.Name`和`AutomationProperties.HelpText`的设置，将连接值)。 这意味着，查找任何测试`AutomationId`如果将失败`AutomationProperties.Name`或`AutomationProperties.HelpText`还在元素上设置。 在此方案中，应更改的 UI 测试的值看起来`AutomationProperties.Name`或`AutomationProperties.HelpText`，或这两者的串联。

每个平台都有不同的屏幕读取器旁白辅助功能值：

- iOS 具有语音朗读。 有关详细信息，请参阅[语音朗读与你的设备上测试辅助功能](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)developer.apple.com 上。
- Android 有 TalkBack。 有关详细信息，请参阅[测试应用的可访问性](https://developer.android.com/training/accessibility/testing.html#talkback)developer.android.com 上。
- Windows 具有讲述人。 有关详细信息，请参阅[验证主应用程序方案，通过使用讲述人](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/)。

但是，屏幕阅读器的确切行为取决于对该软件和它的用户的配置。 例如，大多数屏幕读取器读取时接收焦点，与控件关联的文本使用户能够定向本身，因为它们在页上的控件之间移动。 某些屏幕阅读器，请参阅整个应用程序用户界面时将显示一个页面，从而使用户能够在尝试导航它之前接收所有页面的可用信息性内容。

屏幕读取器还读取可访问性不同值。 在示例应用程序：

- 语音朗读将读取`Placeholder`值`Entry`后, 跟使用控件的说明。
- 将读取 talkBack`Placeholder`值`Entry`后, 跟`AutomationProperties.HelpText`值后, 跟使用控件的说明。
- 讲述人将读取`AutomationProperties.LabeledBy`值`Entry`后, 跟使用控件的说明。

此外，讲述人将设置优先级`AutomationProperties.Name`， `AutomationProperties.LabeledBy`，，然后`AutomationProperties.HelpText`。 在 Android 上，可以将结合 TalkBack`AutomationProperties.Name`和`AutomationProperties.HelpText`值。 因此，建议，全面的可访问性测试执行在每个平台上以确保获得最佳体验。

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

`AutomationProperties.IsInAccessibleTree`附加的属性`boolean`，它确定元素是否可访问，并对屏幕阅读器因此可见。 该属性必须设置为`true`若要使用其他可访问性附加属性。 这可以实现在 XAML 中使用，如下所示：

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

或者，可以将其设置在 C# 中，如下所示：

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> 请注意， [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法还可以用于设置`AutomationProperties.IsInAccessibleTree`附加属性 – `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

`AutomationProperties.Name`附加的属性值应是屏幕读取器使用地宣布元素的较短的描述性文本字符串。 应为具有个了解内容或与用户界面进行交互很重要的含义的元素设置此属性。 这可以实现在 XAML 中使用，如下所示：

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

或者，可以将其设置在 C# 中，如下所示：

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> 请注意， [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法还可以用于设置`AutomationProperties.Name`附加属性 – `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

`AutomationProperties.HelpText`附加属性应设置为文本，用于描述用户界面元素，并可以为工具提示文本的思想关联的元素。 这可以实现在 XAML 中使用，如下所示：

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

或者，可以将其设置在 C# 中，如下所示：

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> 请注意， [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法还可以用于设置`AutomationProperties.HelpText`附加属性 – `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

在某些平台上以进行编辑控件如[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)、`HelpText`属性有时可以省略并替换为占位符文本。 例如，"输入你在本文中的名称"是好的候选[ `Entry.Placeholder` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/)放置文本之前用户的实际输入控件中的属性。

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

`AutomationProperties.LabeledBy`附加的属性允许另一个元素定义当前元素的可访问性信息。 例如， [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)旁边[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)可用于描述什么`Entry`表示。 这可以实现在 XAML 中使用，如下所示：

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

或者，可以将其设置在 C# 中，如下所示：

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> 请注意， [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法还可以用于设置`AutomationProperties.IsInAccessibleTree`附加属性 – `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="summary"></a>摘要

本文介绍了如何设置可访问性值对用户界面元素在 Xamarin.Forms 应用程序中使用从的附加的属性`AutomationProperties`类。 这些附加属性设置本机可访问性值，以便屏幕读取器可在页上的元素进行通信。


## <a name="related-links"></a>相关链接

- [附加属性](~/xamarin-forms/xaml/attached-properties.md)
- [可访问性 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
