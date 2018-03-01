---
title: "XAML 标准 （预览） 控件"
description: "如何开始浏览 Xamarin.Forms 中 XAML Standard 预览版"
ms.topic: article
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 3f30a77975a9f42380ecf7efd73426763ec83ef0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-standard-preview-controls"></a>XAML 标准 （预览） 控件

![预览](~/media/shared/preview.png)

此页列出在预览版中，以及其等效的 Xamarin.Forms 控制可用的 XAML 标准控件。

此外，还有在 XAML 标准了新的属性和枚举名称的控件的列表。

## <a name="controls"></a>控件

<table style="width:300px">
  <tr><th>Xamarin.Forms</th><th>XAML 标准</th></tr>
  <tr><td>Frame</td><td>Border</td></tr>
  <tr><td>选取器</td><td>组合框</td></tr>
  <tr><td>ActivityIndicator</td><td>ProgressRing</td></tr>
  <tr><td>StackLayout</td><td>StackPanel</td></tr>
  <tr><td>Label</td><td>TextBlock</td></tr>
  <tr><td>条目</td><td>文本框</td></tr>
  <tr><td>开关</td><td>ToggleSwitch</td></tr>
  <tr><td>ContentView</td><td>用户控件</td></tr>
</table>

## <a name="properties-and-enumerations"></a>属性和枚举

<table>
  <tr><th>Xamarin.Forms<br/>具有更新的属性的控件</th><th>Xamarin.Forms<br/>属性或枚举</th><th>XAML 标准<br/>等效</th></tr>
  <tr><td>按钮、 条目、 标签、 包含 DatePicker、 编辑器、 SearchBar、 TimePicker</td><td>TextColor</td><td>Foreground</td></tr>
  <tr><td>VisualElement</td><td>BackgroundColor</td><td><i>后台 *</i></td></tr>
  <tr><td>选取器按钮</td><td>BorderColor, OutlineColor</td><td>BorderBrush</td></tr>
  <tr><td>Button</td><td>BorderWidth</td><td>BorderThickness</td></tr>
  <tr><td>ProgressBar</td><td>进度</td><td>“值”</td></tr>
  <tr><td>按钮、 条目、 标签、 编辑器、 SearchBar、 范围、 字体</td><td>FontAttributes<br/>粗体、 斜体、 无</td><td>FontStyle<br/>斜体正常</td></tr>
  <tr><td>按钮、 条目、 标签、 编辑器、 SearchBar、 范围、 字体</td><td>FontAttributes</td><td><i>FontWeights *</i><br/>加粗、 正常</td></tr>
  <tr><td>InputView</td><td>键盘<br/>默认情况下，Url、 数字、 电话、 文本、 聊天、 电子邮件</td><td><i>InputScopeNameValue *</i><br/>默认情况下，Url、 数字、 TelephoneNumber、 文本、 聊天、 EmailNameOrAddress</td></tr>
  <tr><td>StackPanel</td><td>StackOrientation</td><td><i>方向 *</i></td></tr>
</table>

> [!IMPORTANT]
> 项标记为 * 不全在当前的预览版


## <a name="related-links"></a>相关链接

- [预览 NuGet](https://aka.ms/xf-xamlstandard-nuget)
