---
title: 在 iOS 上的辅助功能
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/18/2016
ms.openlocfilehash: af28d0866337c769d1d6102317fc186c49ec259e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility-on-ios"></a>在 iOS 上的辅助功能

本页介绍如何使用 iOS 可访问性 Api 来生成应用根据[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。
请参阅[Android 可访问性](~/android/app-fundamentals/accessibility.md)和[OS X 可访问性](~/mac/app-fundamentals/accessibility.md)其他平台 Api 的页。

## <a name="describing-ui-elements"></a>描述 UI 元素

iOS 提供`AccessibilityLabel`和`AccessibilityHint`属性开发人员可以添加描述性文本可以由语音朗读使用屏幕阅读器，以使控件更易于访问。 此外可以使用提供的其他上下文中可访问模式的一个或多个特征标记控件。

某些控件可能不需要的可访问 （示例中，输入文本或图像，仅仅是装饰性的上一个标签） –`IsAccessibilityElement`用于禁用在这些情况下可访问性。

**UI 设计器**

**属性填充**包含可访问性节，允许这些设置，以在 iOS UI 设计器中选择了一个控件时才进行编辑：

![](accessibility-images/ios-designer-sml.png "辅助功能设置")

**C#**

此外可以直接在代码中设置这些属性：

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>什么是 AccessibilityIdentifier？

`AccessibilityIdentifier`用于设置的唯一密钥，可以用于通过 UIAutomation API 的用户界面元素引用。

值`AccessibilityIdentifier`永远不会读出或向用户显示。

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

`UIAccessibility.PostNotification`方法可将事件引发到外部 （例如，在它们与特定控件交互时） 的直接交互用户。

### <a name="announcement"></a>公告

用于通知用户某些状态已更改 （如后台操作已完成），可以从代码发送公告。 这可能伴随着用户界面中的可视指示：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

`LayoutChanged`使用公告时屏幕布局：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>可访问性和本地化

辅助功能属性一样的标签和提示可以本地化只需像用户界面中的其他文本。

**MainStoryboard.strings**

如果在情节提要中的用户界面布局方式，你可以在与其他属性相同的方式提供辅助功能属性的翻译。 在示例中，`UITextField`具有**本地化 ID**的`Pqa-aa-ury`和西班牙语设置的两个可访问性属性：

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

此文件将放在**es.lproj**西班牙语内容目录。

**Localizable.strings**

或者，可以将翻译添加到**Localizable.strings**文件的本地化内容目录中 （例如。 **es.lproj**为西班牙语):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

可以在 C# 中通过使用这些翻译`LocalizedString`方法：

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

请参阅[iOS 本地化指南](~/ios/app-fundamentals/localization/index.md)有关本地化内容的详细信息。

<a name="testing" />

## <a name="testing-accessibility"></a>测试辅助功能

在中启用语音朗读**设置**通过导航到应用**常规 > 可访问性 > 语音朗读**:

![](accessibility-images/settings-sml.png "设置的语速")

**可访问性**屏幕还提供了缩放、 文本大小、 颜色和对比度选项、 语音设置和其他配置选项设置。

请按照以下[语音朗读说明](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)在 iOS 设备上的辅助功能进行测试。


## <a name="simulator-testing"></a>模拟器测试

在模拟器中，测试时**可访问性检查器**是可用于帮助验证辅助功能属性和事件是否正确配置。 打开检查功能在**设置**通过导航到应用**常规 > 可访问性 > 可访问性检查器**:

![](accessibility-images/settings-inspector-sml.png "启用辅助功能检查器")

启用后，检查器窗口悬停 iOS 屏幕在所有时间。
下面是输出的示例，选择表视图行时，请注意**标签**包含提供的行，并确保它"已完成"的内容的句子 (ie。 刻度线是可见):

![](accessibility-images/tableview-a11y-sml.png "使用可访问性检查器")

该检查器可见时，使用在左上角的"X"图标来暂时显示和隐藏在覆盖区上，启用/禁用辅助功能设置。



## <a name="related-links"></a>相关链接

- [跨平台可访问性](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS (Apple) 的辅助功能](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
