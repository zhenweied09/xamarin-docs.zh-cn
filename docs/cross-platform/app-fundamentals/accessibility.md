---
title: 可访问性
description: 确保你的应用程序为广泛的受众可能的
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 3b7912f7875e9d07de51861e573065b3d1b73de0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="accessibility"></a>可访问性

_确保你的应用程序为广泛的受众可能的_

可访问性是指设计都能正常工作操作系统显示和输入帮助功能，如大类型，高对比度，放大的应用程序用户界面、 阅读版式 （文本到语音转换）、 visual 或 haptic 反馈提示的概念和其他输入的法。

如 iOS、 Android 和 Windows 的桌面和移动平台提供了内置的 Api，可帮助开发人员构建可访问应用程序，例如[Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)和[Apple 的语音朗读](http://www.apple.com/accessibility/ios/voiceover/)。

## <a name="platform-specific-apis"></a>特定于平台的 Api

若要实施本文档中的准则，使用每个平台提供的 Api:

- [**Android 的辅助功能**](~/android/app-fundamentals/accessibility.md)
- [**iOS 可访问性**](~/ios/app-fundamentals/accessibility.md)
- [**OS X 可访问性**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>可访问性清单

请遵循这些提示以确保你的应用程序都对最广泛的受众可能可访问。 签出[Android 可访问性测试清单](http://developer.android.com/training/accessibility/testing.html)和[Apple 的辅助功能页](http://www.apple.com/accessibility/)有关其他信息。

### <a name="support-large-fonts-and-high-contrast"></a>支持大型字体和高对比度

请避免硬编码控件维度，并改为，更喜欢可以调整大小以容纳更大的字体大小的布局。
在高对比度模式，以确保它们是可读中测试配色方案。

### <a name="make-the-user-interface-self-describing"></a>使用户界面自描述

标记具有描述性文本和与每个平台上读取 Api 屏幕兼容的提示你用户界面的所有元素。

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>确保图像和图标的替换文字说明

图像和图标 （如按钮或指示器的状态，例如） 的应用程序用户界面的一部分，应将标记与辅助性说明。

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>设计具有记住可访问导航的可视化树

使用适当的布局控件或 Api，以便使用备用的输入的方法的控件之间导航遵循相同的逻辑流与使用触摸屏。

从屏幕读取器 （修饰性图像或标签的字段已可访问，例如） 排除不必要的元素。

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>不要依赖于单独的音频或颜色提示

避免其中进度、 完成或某个其他状态的唯一指示是声音或颜色更改的情况。 请设计用户界面包括清除视觉提示 （使用声音和强化仅颜色），或添加特定的可访问性指示器。

如果选择颜色，尝试避免很难区分用户色盲的调色板。

### <a name="captioning-for-video-text-for-audio"></a>隐藏的字幕的音频的视频文本

视频内容，以及用于音频内容的可读脚本提供标题。 它也会有所帮助提供调整音频或视频内容的速度的控件，并确保该卷并播放/暂停按钮可轻松地查找和使用。

### <a name="localize"></a>Localize

可访问性说明可以 （并且应该） 进行本地化应用程序支持多种语言的位置。



## <a name="related-links"></a>相关链接

- [Android 的辅助功能](~/android/app-fundamentals/accessibility.md)
- [iOS 可访问性](~/ios/app-fundamentals/accessibility.md)
- [OS X 可访问性](~/mac/app-fundamentals/accessibility.md)
- [Xamarin.Forms 辅助功能](~/xamarin-forms/app-fundamentals/accessibility/index.md)
