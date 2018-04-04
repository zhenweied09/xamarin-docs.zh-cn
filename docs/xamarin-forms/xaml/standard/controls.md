---
title: XAML 标准 （预览） 控件
description: 如何开始浏览 Xamarin.Forms 中 XAML Standard 预览版
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 2fc7fb9581f344e0d54bd9f690d334eda78cc97a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-standard-preview-controls"></a>XAML 标准 （预览） 控件

![预览](~/media/shared/preview.png)

此页列出在预览版中，以及其等效的 Xamarin.Forms 控制可用的 XAML 标准控件。

此外，还有在 XAML 标准了新的属性和枚举名称的控件的列表。

## <a name="controls"></a>控件

|Xamarin.Forms|XAML 标准|
|--- |--- |
|Frame|Border|
|选取器|组合框|
|ActivityIndicator|ProgressRing|
|StackLayout|StackPanel|
|Label|TextBlock|
|条目|文本框|
|开关|ToggleSwitch|
|ContentView|用户控件|


## <a name="properties-and-enumerations"></a>属性和枚举

|Xamarin.Forms 控件与更新的属性|Xamarin.Forms 属性或枚举|XAML 标准等效项|
|--- |--- |--- |
|按钮、 条目、 标签、 包含 DatePicker、 编辑器、 SearchBar、 TimePicker|TextColor|Foreground|
|VisualElement|BackgroundColor|后台 *|
|选取器按钮|BorderColor, OutlineColor|BorderBrush|
|Button|BorderWidth|BorderThickness|
|ProgressBar|进度|值|
|按钮、 条目、 标签、 编辑器、 SearchBar、 范围、 字体|FontAttributesBold、 倾斜无|FontStyleItalic, Normal|
|按钮、 条目、 标签、 编辑器、 SearchBar、 范围、 字体|FontAttributes|FontWeights * 设置为粗体，正常|
|InputView|KeyboardDefault、 Url、 数字、 电话、 文本、 聊天，电子邮件|InputScopeNameValue * 默认、 Url、 数字、 TelephoneNumber、 文本、 聊天、 EmailNameOrAddress|
|StackPanel|StackOrientation|方向 *|

> [!IMPORTANT]
> 项标记为 * 不全在当前的预览版

## <a name="related-links"></a>相关链接

- [预览 NuGet](https://aka.ms/xf-xamlstandard-nuget)
