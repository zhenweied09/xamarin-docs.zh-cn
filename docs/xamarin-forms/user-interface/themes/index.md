---
title: "主题"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 12741b4756d26f4090613127d143380e1c4fb55a
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="themes"></a>主题

![](~/media/shared/preview.png "此 API 目前处于预览状态")

Xamarin.Forms 主题发展变化 2016年已宣布，也可作为客户尝试并提供反馈的预览。

主题已添加到 Xamarin.Forms 应用程序包括**Xamarin.Forms.Theme.Base** Nuget 包，以及定义特定主题 （如其他包 Xamarin.Forms.Theme.Light) 或其他本地主题可以定义应用程序。

请参阅[浅色主题](light.md)和[深色主题](dark.md)有关如何将它们添加到应用程序，或签出的说明的页[示例自定义主题](custom.md)。

**重要说明：**还应遵循的步骤[加载主题程序集 （下文）](#loadtheme)通过将某些样板文件代码添加到 iOS`AppDelegate`和 Android `MainActivity`。 这将在将来的预览版得到改进。


## <a name="control-appearance"></a>控件外观

[Light](light.md)和[深色](dark.md)这两个主题定义用于标准控件特定可视外观。 将主题添加到应用程序的资源字典后，将更改标准控件的外观。

下面的 XAML 标记显示一些公共控件：

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

这些屏幕截图显示与这些控件：

* 应用无主题
* 浅色主题 （仅限于使其无主题的细微差异）
* 深色主题

![](images/standard-none-sml.png "控件而不通过主题") ![ ](images/standard-light-sml.png "控件与浅色主题") ![ ](images/standard-dark-sml.png "深色主题的控件")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

`StyleClass`属性允许视图的外观，以根据提供的主题的定义更改。

[Light](light.md)和[深色](dark.md)主题这两个定义的三个不同的外观`BoxView`: `HorizontalRule`， `Circle`，和`Rounded`。 此标记显示三个不同`BoxView`s，使用不同的样式类应用：

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

这将会呈现浅色和深色，如下所示：

![](images/boxview-light-sml.png "使用浅色主题 StyleClass 字数") ![ ](images/boxview-dark-sml.png "与深色主题 StyleClass 字数")

<a name="builtin" />

## <a name="built-in-classes"></a>内置类

除了自动样式公共控件浅色和深色主题目前支持以下类，可以通过设置应用`StyleClass`上这些控件：

**BoxView**

* HorizontalRule
* 圆圈
* 舍入

**Image**

* 圆圈
* 舍入
* 缩略图

**Button**

* 默认
* 基本
* 成功
* T:System.Diagnostics.Switch
* 警告
* 危险
* 链接
* 小
* 大型

**标签**

* Header
* 副标题
* 正文
* 链接
* 反函数


## <a name="troubleshooting"></a>疑难解答

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>无法加载文件或程序集 Xamarin.Forms.Theme.Light 或其依赖项之一

在预览版本中，主题可能不能在运行时加载。 添加下面显示的代码到相关项目要修复此错误。

**iOS**

在**AppDelegate.cs**添加以下行后 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

在**MainActivity.cs**添加以下行后 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>相关链接

- [ThemesDemo 示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)
