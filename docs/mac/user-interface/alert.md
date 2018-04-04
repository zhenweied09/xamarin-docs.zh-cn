---
title: 警报
description: 本文介绍如何使用 Xamarin.Mac 应用程序中的警报。 它介绍创建和显示 C# 代码中的警报以及对用户交互作出响应。
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a451d0a5535915d9e52f687ae07ea028c0ccd5ef
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="alerts"></a>警报

_本文介绍如何使用 Xamarin.Mac 应用程序中的警报。它介绍创建和显示 C# 代码中的警报以及对用户交互作出响应。_

在使用 C# 和.NET Xamarin.Mac 应用程序中，可以访问到相同的警报的开发人员*Objective C*和*Xcode*未。 

警报是严重问题发生 （如错误） 时，将显示的对话框的特殊类型或为警告 （例如，准备要删除的文件）。 由于警报是一个对话框，它还要求用户响应可以在关闭之前。

[![](alert-images/alert06.png "示例警报")](alert-images/alert06.png#lightbox)

在本文中，我们将介绍使用 Xamarin.Mac 应用程序中的警报的基础知识。 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>警报简介

警报是严重问题发生 （如错误） 时，将显示的对话框的特殊类型或为警告 （例如，准备要删除的文件）。 由于警报会中断用户，因为用户可以继续在其任务之前，必须关闭它们，因此应避免显示警报，除非绝对必要。

Apple 提供建议的以下准则：

- 不要使用警报仅为向用户提供信息。
- 不会显示公共、 可撤消的操作的警报。 即使这种情况下可能会导致数据丢失。
- 如果一个任务是值得的警报，避免使用任何其他 UI 元素或方法来显示它。
- 应尽量少使用警告图标。
- 在警报消息中明确并用于简单地描述警报的情况。
- 默认按钮名称应对应于在警报消息中描述的操作。

有关详细信息，请参阅[警报](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>警报的剖析

如上面所述，对应用程序的用户出现严重问题时或作为 （如关闭未保存的文件） 的潜在数据丢失的警告，应显示警报。 在 Xamarin.Mac，警报创建在 C# 代码中，例如：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

上面的代码与应用程序图标叠加显示在警告图标、 标题、 一条警告消息和一个显示一条警报**确定**按钮：

[![](alert-images/alert01.png "与确定按钮的警报")](alert-images/alert01.png#lightbox)

Apple 提供可以用于自定义警报的多个属性：

- **AlertStyle**警报的类型定义为以下项之一：
    - **警告**-用于用户的当前或即将发生的事件，并不重要，则发出警告。 这是默认样式。
    - **信息性**-用于当前或即将发生的事件相关用户，则发出警告。 目前，没有之间没有明显的差异**警告**和**信息**
    - **关键**-用来警告用户即将发生的事件 （如删除的文件） 的严重后果。 应尽量少使用此类型的警报。
- **MessageText** -这是主消息或警告的标题，应快速向用户定义这种情况。
- **InformativeText** -这是的警报应在此清楚地定义这种情况，并向用户显示可用选项的正文。
- **图标**-允许一个自定义的图标，以向用户显示。
- **HelpAnchor** & **ShowsHelp** -允许警报会绑定到应用程序 HelpBook 和显示有关警报帮助。
- **按钮**-默认情况下，警报仅具有**确定**按钮但**按钮**集合可用于添加更多选择根据需要。
- **ShowsSuppressionButton** -如果`true`显示用户可用来抑制警报的触发它的事件的后续匹配项的重要性复选框。
- **AccessoryView** -允许你将另一个子视图附加到该通知以提供额外信息，例如添加**文本字段**输入数据。 如果你设置一个新**AccessoryView**或修改一个现有，你需要调用`Layout()`方法以调整警报的可见的布局。

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>显示警报

有两种不同方法，警报可以显示，自由浮动或工作表的形式。 下面的代码显示为自由浮动的警报：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
如果运行此代码，则显示以下信息：

[![](alert-images/alert02.png "一个简单的警报")](alert-images/alert02.png#lightbox)

下面的代码工作表的形式显示相同的警报：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

如果运行此代码，以下将显示：

[![](alert-images/alert03.png "工作表的形式显示警报")](alert-images/alert03.png#lightbox)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>使用警报的按钮

默认情况下，警报显示仅**确定**按钮。 但是，你不限于，你可以通过追加到创建额外的按钮**按钮**集合。 下面的代码将创建使用自由浮动警报**确定**，**取消**和**可能**按钮：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

添加的第一个按钮将_默认按钮_，将激活如果用户按下 Enter 键。 返回的值将是一个整数，表示该按钮的按下的用户。 在我们的示例将返回以下值：

- **确定**-1000年。
- **取消**-1001年。
- **可能**-1002年。

如果我们运行代码，以下将显示：

[![](alert-images/alert04.png "与三个按钮选项的警报")](alert-images/alert04.png#lightbox)

以下是针对同一警报工作表的形式的代码：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}", result);
});
```
如果运行此代码，以下将显示：

[![](alert-images/alert05.png "工作表的形式显示三个按钮警报")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> 你应永远不会将超过三个按钮添加到警报中。

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>显示取消按钮

如果警报的`ShowSuppressButton`属性是`true`，警报将显示一个复选框，用户可以使用以禁用警报触发它的事件的后续匹配项。 下面的代码显示带有取消按钮自由浮动警报：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

如果值`alert.SuppressionButton.State`是`NSCellStateValue.On`，用户已选中隐藏复选框，否则它们不具有时。

如果运行代码，以下将显示：

[![](alert-images/alert06.png "与隐藏按钮的警报")](alert-images/alert06.png#lightbox)

以下是针对同一警报工作表的形式的代码：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

如果运行此代码，以下将显示：

[![](alert-images/alert07.png "工作表的形式显示带有取消按钮警报")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>添加自定义的子视图

警报具有`AccessoryView`属性，可以用于自定义警报进一步并添加一些操作，例如**文本字段**为用户输入。 下面的代码将与已添加的文本输入字段创建自由浮动警报：

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

键的行是`var input = new NSTextField (new CGRect (0, 0, 300, 20));`这将创建一个新**文本字段**，我们将添加警报。 `alert.AccessoryView = input;` 哪些附加**文本字段**到警报，并调用`Layout()`方法，需调整大小以适应新的子视图中的警报。

如果我们运行代码，以下将显示：

[![](alert-images/alert08.png "如果我们运行此代码，以下将显示")](alert-images/alert08.png#lightbox)

下面是工作表的形式相同的警报：

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

如果我们运行此代码，以下将显示：

[![](alert-images/alert09.png "与自定义视图的警报")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>总结

本文已详细的介绍了在使用 Xamarin.Mac 应用程序中的警报。 我们已了解了不同类型和警报、 如何创建和自定义警报和如何在 C# 代码中使用的警报的使用。

## <a name="related-links"></a>相关链接

- [MacWindows （示例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
