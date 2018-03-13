---
title: "在 macOS 上的辅助功能"
description: "本指南介绍用于生成可访问的 Xamarin.Mac 应用程序的功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 4363258a9047ee4e2de4f53595a6eedc5dfe5861
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="accessibility-on-macos"></a>在 macOS 上的辅助功能

本页介绍如何使用 macOS 可访问性 Api 来生成应用根据[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。
请参阅[Android 可访问性](~/android/app-fundamentals/accessibility.md)和[iOS 可访问性](~/ios/app-fundamentals/accessibility.md)其他平台 Api 的页。

若要了解的可访问性 Api 中的工作原理 macOS （以前称为 OS X），第一个评审[OS X 辅助功能模型](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html)。

## <a name="describing-ui-elements"></a>描述 UI 元素

AppKit 使用`NSAccessibility`协议来公开帮助使用户界面可访问的 Api。 这包括尝试设置为辅助功能属性，如设置按钮的有意义的值的默认行为`AccessibilityLabel`。 标签通常是单个单词或短语描述的控件或视图。

### <a name="storyboard-files"></a>情节提要文件

Xamarin.Mac 使用 Xcode 接口生成器编辑情节提要文件。
可以在中编辑的辅助功能信息**标识检查器**（如下面的屏幕截图中所示），设计图面上选择一个控件是时：

[![在 Xcode 的接口生成器中添加可访问性](accessibility-images/xcode.png "在 Xcode 的接口生成器中添加可访问性")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>代码

作为当前不公开 Xamarin.Mac `AccessibilityLabel` setter。  添加下面的 helper 方法，以设置可访问性标签：

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

此方法随后可在代码中用所示：

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

`AccessibilityHelp`属性是有关的控件或视图的功能，说明和标签可能无法提供足够的信息时应仅添加。 帮助文本应仍将保持尽可能短，为示例"中删除文档"。

某些用户界面元素不相关的可访问的访问 （例如旁都有其自己的可访问性标签和帮助输入标签）。
在这些情况下，设置`AccessibilityElement = false`，以便这些控件或视图将被跳过由屏幕阅读器或其他辅助功能工具。

Apple 提供[辅助功能准则](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html)说明可访问性标签和帮助文本的最佳做法。

## <a name="custom-controls"></a>自定义控件

请参阅 Apple 的[可访问的自定义控件的准则](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html)有关所需的其他步骤的详细信息。

## <a name="testing-accessibility"></a>测试辅助功能

macOS 提供**可访问性检查器**帮助测试可访问性功能。 该检查器将附带 Xcode。

第一次启动它时，**可访问性检查器**将需要权限来控制通过可访问性的计算机：

![可访问性要求运行权限的检查器](accessibility-images/accessibility-inspector-1.png "可访问性要求运行权限的检查器")

解锁的设置屏幕 （如果需要，左下方） 和刻度**可访问性检查器**:

![若要启用辅助功能检查器的设置屏幕](accessibility-images/accessibility-inspector-2.png "设置屏幕，若要启用辅助功能检查器")

启用后，该检查器将显示为一个可以移动屏幕的浮动窗口。 下面的屏幕截图显示旁边示例 Mac 应用程序运行该检查器。 该时段内通过在移动光标时，该检查器将显示每个控件的所有可访问属性：

[![可访问性检查器运行的示例](accessibility-images/accessibility-example.png "的辅助功能示例拦截器运行")](accessibility-images/accessibility-example-large.png#lightbox)

详细信息，请阅读[测试辅助功能有关 OS X 指南](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html)。



## <a name="related-links"></a>相关链接

- [跨平台可访问性](~/cross-platform/app-fundamentals/accessibility.md)
- [Mac 可访问性](https://www.apple.com/accessibility/mac/)
