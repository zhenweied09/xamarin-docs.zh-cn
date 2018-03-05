---
title: "新的用户界面样式"
description: "本文介绍如何光并且深色 UI 主题该 Apple 已添加到 tvOS 中，10 以及如何在 Xamarin.tvOS 应用程序中实现它们。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 55a9e56585521a0a7b89a7b881dfecd4e1c14b08
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="new-user-interface-styles"></a>新的用户界面样式

_本文介绍如何光并且深色 UI 主题该 Apple 已添加到 tvOS 中，10 以及如何在 Xamarin.tvOS 应用程序中实现它们。_

tvOS 10 现在支持深色和浅色用户界面主题，所有生成在 UIKit 控件将自动调整以适应，根据用户的首选项。 此外，开发人员可以手动调整基于用户选定的主题的 UI 元素，并且可重写给定的主题。


<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>有关新的用户界面样式

如上面所述，tvOS 10 现在支持深色和浅色用户界面主题，所有生成在 UIKit 控件将自动调整以适应，基于用户的首选项。

用户可以通过转到切换此主题**设置** > **常规** > **外观**和之间切换**Light**和**深色**:

[ ![](user-interface-styles-images/theme01.png "设置应用程序")](user-interface-styles-images/theme01.png)

当**深色**选择主题后，所有的用户界面元素将切换到上暗色背景的浅文本：

[ ![](user-interface-styles-images/theme02.png "深色主题")](user-interface-styles-images/theme02.png)

用户将无法在任何时候切换主题，并因此基于当前的活动，Apple TV 所在的位置或一天的时间可能会执行。

Light UI 主题是默认主题，并用任何现有的 tvOS 应用将仍采用浅色主题，而不考虑用户的首选项，除非为 tvOS 10 以利用深色主题对其进行修改。 TvOS 10 应用还具有重写当前主题和始终使用 Light 或深色主题，为某些或所有其 UI 的功能。

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>采用浅色和深色主题

若要支持此功能，Apple 已添加到一个新的 API`UITraitCollection`类和一个 tvOS 应用必须选择加入以支持深色外观 (通过中的设置其`Info.plist`文件)。

要选择加入到浅色和深色主题支持，执行以下操作：

1. 在“解决方案资源管理器”中，双击 `Info.plist` 文件，将其打开进行编辑。
2. 选择**源**视图 （从编辑器底部）。
3. 添加新的密钥，并调用它`UIUserInterfaceStyle`: 

    [ ![](user-interface-styles-images/theme03.png "UIUserInterfaceStyle 密钥")](user-interface-styles-images/theme03.png)
4. 将类型设置为保留`String`和输入的值为`Automatic`: 

    [ ![](user-interface-styles-images/theme04.png "输入自动")](user-interface-styles-images/theme04.png)
5. 保存对文件所做的更改。

有三个可能值`UIUserInterfaceStyle`密钥：

- **Light** -强制 tvOS 应用程序的 UI，始终使用浅色主题。
- **深色**-强制 tvOS 应用程序的 UI，始终使用深色主题。
- **自动**-浅色和深色主题之间切换基于中设置的用户的首选项。 这是首选的设置。

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>UIKit 主题支持

如果 tvOS 应用使用标准的内置`UIView`基于控件，它们将自动响应用户界面主题，而无需任何开发人员介入。

此外，`UILabel`和`UITextView`将自动更改其颜色基于选择的用户界面主题：

- 文本将显示为黑色浅色主题中。
- 文本将为白色深色主题中。

如果开发人员发生变化的文本颜色手动 （或者在情节提要或代码中），则会将负责处理颜色更改基于 UI 的主题。

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>新的模糊效果

对于在 tvOS 10 应用程序中支持浅色和深色主题，Apple 已添加两个新的模糊效果。 这些新的效果会自动调整变得模糊基于 UI 的主题在用户选择，如下所示：

- `UIBlurEffectStyleRegular` -使用浅色模糊采用浅色主题中在深色模糊深色主题中。
- `UIBlurEffectStyleProminent` -使用浅色主题中的超浅色模糊和深色主题中的非常深色模糊。

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>使用特征集合

新`UserInterfaceStyle`属性`UITraitCollection`类可以用于获取当前所选的用户界面主题，并将`UIUserInterfaceStyle`枚举的以下值之一：

- **Light** -选择的 Light UI 主题。
- **深色**-选定的深色 UI 主题。
- **未指定**-查看尚未显示屏幕，因此当前 UI 主题是未知。

此外，特征集合 tvOS 10 中具有以下功能：
 
- 可以根据自定义外观代理`UserInterfaceStyle`的给定`UITraitCollection`更改等映像或项基于主题的颜色。
- TvOS 应用可以通过重写处理特征集合更改`TraitCollectionDidChange`方法`UIView`或`UIViewController`类。

> [!IMPORTANT]
> **注意：** Xamarin.tvOS 早期预览版中对 tvOS 10 不完全支持`UIUserInterfaceStyle`为`UITraitCollection`尚未。 将从未来版本中添加完整支持。




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>自定义基于主题的外观

对于支持外观代理的用户界面元素，其外观可以根据进行调整其特征集合 UI 主题。 因此，对于给定的 UI 元素，开发人员可以指定一个采用浅色主题颜色和深色主题为另一种颜色。

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> **注意：**遗憾的是，不完全支持对 tvOS 10 Xamarin.tvOS 预览`UIUserInterfaceStyle`为`UITraitCollection`，因此这种类型的自定义尚不可用。 将从未来版本中添加完整支持。

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>直接响应主题更改

在开发人员需要的 UI 元素的外观的更深入地控制基于所选的用户界面主题，它们可以重写`TraitCollectionDidChange`方法`UIView`或`UIViewController`类。

例如:

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);
    
    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="overriding-a-trait-collection"></a>重写特征集合

根据 tvOS 应用程序的设计，可能有的时间开发人员需要重写给定的用户界面元素的特征集合，并将其始终使用特定的用户界面主题。

这可以使用`SetOverrideTraitCollection`方法`UIViewController`类。 例如:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

有关详细信息，请参阅[特征](~/ios/user-interface/storyboards/unified-storyboards.md)和[重写特征](~/ios/user-interface/storyboards/unified-storyboards.md)的部分我们[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)文档。

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>特征集合和情节提要

TvOS 10，可以设置应用的情节提要响应特征集合，并且许多 UI 元素可以进行浅色和深色主题感知。 当前 Xamarin.tvOS 早期预览版对 tvOS 10 尚不支持此功能在接口设计器中，因此情节提要将需要在 Xcode 的接口生成器将作为一种解决方法中进行编辑。

若要启用特征集合支持，请执行以下操作：

1. 右键单击情节提要文件**解决方案资源管理器**和选择**打开** > **Xcode 接口生成器**: 

    [ ![](user-interface-styles-images/theme05.png "使用 Xcode 接口生成器打开")](user-interface-styles-images/theme05.png) 
2. 若要启用特征集合支持，请切换到**文件检查器**并检查**使用特征变体**中的属性**接口生成器文档**部分： 

    [ ![](user-interface-styles-images/theme06.png "启用特征集合支持")](user-interface-styles-images/theme06.png)
3. 确认使用特征变体的更改： 

    [ ![](user-interface-styles-images/theme07.png "使用特征变体警报")](user-interface-styles-images/theme07.png)
4. 将所做的更改保存到情节提要文件。

在编辑 tvOS 情节提要界面生成器中的时，Apple 添加了以下功能：

* 开发人员可以指定不同的基于 UI 主题中的用户界面元素的变体**属性检查器**:
    
    * 现在有多个属性 **+** 旁边这可通过单击添加 UI 主题特定版本： 

        [ ![](user-interface-styles-images/theme08.png "添加 UI 主题特定版本")](user-interface-styles-images/theme08.png) 
    
    * 开发人员可以指定一个新属性，或单击**x**按钮将其删除： 

        [ ![](user-interface-styles-images/theme09.png "指定新属性，或单击 x 按钮以将其删除")](user-interface-styles-images/theme09.png)
* 开发人员可以预览 Light 或深色主题中从接口生成器中的 UI 设计：
    
    * 设计图面的底部允许开发人员切换当前 UI 主题： 

        [ ![](user-interface-styles-images/theme10.png "底部的设计图面")](user-interface-styles-images/theme10.png)
        
    * 新的主题将显示在界面生成器，将显示任何特征集合特定调整： 

        [ ![](user-interface-styles-images/theme11.png "显示在界面生成器主题")](user-interface-styles-images/theme11.png)

此外，tvOS 模拟器现在具有键盘快捷方式，使开发人员能够快速调试 tvOS 应用时在浅色和深色主题之间切换。 使用**命令 Shift D**键盘序列浅色和深色之间切换。

<a name="Summary" />

## <a name="summary"></a>摘要

本文已覆盖光和深色 UI 主题该 Apple 已添加到 tvOS 10 以及如何实现它们 Xamarin.tvOS 应用中。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [什么是 tvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
