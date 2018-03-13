---
title: "以编程方式布局约束"
description: "本指南介绍使用 iOS 自动布局约束在 C# 代码中而不是在 iOS 设计器中创建它们。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 7819201e76e268ea84bf2cc5d49a5a07b20a04e3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="programmatic-layout-constraints"></a>以编程方式布局约束

_本指南介绍使用 iOS 自动布局约束在 C# 代码中而不是在 iOS 设计器中创建它们。_

响应灵敏的设计方法 （也称为"自适应布局"） 的自动数据布局。 与过渡布局系统，其中每个元素的位置是硬编码到屏幕上的点，不同的是自动布局即将*关系*-相对于设计图面上的其他元素的元素的位置。 自动布局的核心是约束或在屏幕上的其他元素的上下文中定义的元素的位置或一组元素的规则的概念。 由于元素不会关联到特定位置在屏幕上，约束将帮助创建看起来上不同屏幕大小和设备的方向很好的自适应布局。

通常使用时自动布局在 iOS 中，你将使用 iOS 设计器来以图形方式施加 UI 项布局约束。 但是，可能需要创建并应用在 C# 代码中的约束的时间。 例如，在使用动态创建的 UI 元素添加到`UIView`。

本指南将演示如何创建和使用约束而不 iOS 设计器中以图形方式创建使用 C# 代码。

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>以编程方式创建约束

如上面所述，通常你将自动布局约束中使用 iOS 设计器。 对于那些必须以编程方式创建你的约束的时间，你将具有三个选项可供选择：

* [布局锚](#Layout-Anchors)-此 API 提供访问的定位点属性 (如`TopAnchor`，`BottomAnchor`或`HeightAnchor`) 的受约束的用户界面项。
* [布局约束](#Layout-Constraints)-您可以创建约束直接使用`NSLayoutConstraint`类。
* [可视格式设置的语言](#Visual-Format-Language)-提供 ASCII 图文类似方法来定义你的约束。

下列各节将详细阐述每个选项在详细信息。

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>布局定位点

通过使用`NSLayoutAnchor`类，你具有 fluent 界面来创建基于受约束的用户界面项的定位点属性的约束。 例如，视图控制器的顶部和底部布局指导公开`TopAnchor`，`BottomAnchor`和`HeightAnchor`定位属性，而一个视图显示边缘、 中心、 大小和基线的属性。

> [!IMPORTANT]
> **注意：**定位点属性的标准集，除了 iOS 视图还包括`LayoutMarginsGuides`和`ReadableContentGuide`属性。 这些属性公开`UILayoutGuide`对象以便使用与视图的边距和可读内容指南分别。

布局定位点提供几种方法轻松读取、 compact 格式创建约束：

- **ConstraintEqualTo** -定义的关系其中`first attribute = second attribute + [constant]`利用 （可选） 提供`constant`偏移值。
- **ConstraintGreaterThanOrEqualTo** -定义的关系其中`first attribute >= second attribute + [constant]`利用 （可选） 提供`constant`偏移值。
- **ConstraintLessThanOrEqualTo** -定义的关系其中`first attribute <= second attribute + [constant]`利用 （可选） 提供`constant`偏移值。

例如:

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

一个典型的布局约束可以表示为线性表达式。 请参见以下示例：

[![](programmatic-layout-constraints-images/graph01.png "表示为线性表达式布局约束")](programmatic-layout-constraints-images/graph01.png#lightbox)

这将转换为 C# 代码使用布局定位点的以下行：

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

其中的 C# 代码的部分对应的等式的给定部分，如下所示：

<table width="100%" border="1">
<tr>
<td width="50%"><b>公式</b></td><td><b>代码</b></td>
</tr>
<tr>
<td width="50%">第 1 项</td><td>PurpleView</td>
</tr>
<tr>
<td width="50%">属性 1</td><td>LeadingAnchor</td>
</tr>
<tr>
<td width="50%">关系</td><td>ConstraintEqualTo</td>
</tr>
<tr>
<td width="50%">乘数</td><td>默认值为 1.0，因此未指定</td>
</tr>
<tr>
<td width="50%">项 2</td><td>OrangeView</td>
</tr>
<tr>
<td width="50%">属性 2</td><td>TrailingAnchor</td>
</tr>
<tr>
<td width="50%">返回的常量</td><td>10.0</td>
</tr>
</table>

除了提供仅解决给定的布局约束公式所需的参数，每个布局定位点方法强制执行传递给它们的参数的类型安全性。 因此水平约束，如定位`LeadingAnchor`或`TrailingAnchor`只能使用与其他水平定位点类型和乘数仅提供给大小限制。

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>布局约束

您可以通过直接构造手动添加自动布局约束`NSLayoutConstraint`在 C# 代码。 与使用布局定位点，您必须指定每个参数的值，即使它将具有所定义的约束没有影响。 因此，你将得到生成大量难以阅读，样板文件代码。 例如:

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

其中`NSLayoutAttribute`枚举定义视图的边距的值，并且对应于`LayoutMarginsGuide`属性，如`Left`， `Right`，`Top`和`Bottom`和`NSLayoutRelation`枚举定义的关系，将为给定的属性之间创建`Equal`，`LessThanOrEqual`或`GreaterThanOrEqual`。

与不同使用布局定位点 API，`NSLayoutConstraint`创建方法突出特定的约束的重要方面不显示，并且没有任何编译时间执行约束检查。 因此，很容易构造一个无效的约束，将在运行时引发异常。

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>可视格式语言

可视格式语言允许你定义使用 ASCII 图文，如提供可视表示形式正在创建的约束的字符串的约束。 这具有以下优点和缺点：

- 可视格式语言强制执行有效约束仅的创建。
 - 自动布局输出到控制台中使用可视格式语言，以便调试消息将类似于代码用于创建该约束的约束。
 - 可视格式语言，可使用非常简洁表达式同时创建多个约束。
 - 由于没有可视格式语言字符串没有编译端验证，那么仅可以在运行时发现问题。
 - 由于 Visual 格式语言相对于某些约束类型将无法创建它 （例如比率） 的完整性强调可视化效果。

使用可视格式语言创建一个约束时，请执行以下步骤：

1. 创建`NSDictionary`包含视图对象和版式参考线和定义的格式时将使用的字符串密钥。
2. （可选） 创建`NSDictionary`用于定义键和值的集 (`NSNumber`) 用作约束的常量值。
3. 创建布局的格式字符串的单个列或行的项。
4. 调用`FromVisualFormat`方法`NSLayoutConstraint`类以生成约束。
5. 调用`ActivateConstraints`方法`NSLayoutConstraint`类激活，并将约束应用。

例如，若要在可视格式语言创建一个前导和尾随的约束，你可以使用以下：

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

由于始终将 Visual 格式语言创建附加到父视图的边距时使用的默认间距零个点约束，此代码将生成上面介绍的示例相同的结果。

对于更复杂的 UI 设计，如多个子视图在单独的行，可视格式语言指定的水平间距和垂直对齐方式。 如上面的示例中，它指定所示`AlignAllTop``NSLayoutFormatOptions`对齐所有行或到其顶部的列中的视图。

请参阅 Apple 的[Visual 格式语言附录](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)的指定公共约束和 Visual 格式字符串语法的一些示例。

<a name="Summary" />

## <a name="summary"></a>摘要

本指南提供了创建和使用 C# 中的自动数据布局约束而不 iOS 设计器中以图形方式创建。 首先，它看使用布局锚 (`NSLayoutAnchor`) 来处理自动布局。 接下来，它说明了如何使用布局约束 (`NSLayoutConstraint`)。 最后，它提供用于自动布局的可视格式语言。

## <a name="related-links"></a>相关链接

- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [iOS 可设计控件演练](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [与 iOS 的 Xamarin 设计器的自动数据布局](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-以编程方式创建约束](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple 的可视格式语言附录](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
