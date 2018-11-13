---
title: 适用于 Xamarin.Forms 的 XAML 预览程序
description: 本文介绍如何使用 XAML 预览程序若要查看的呈现将如你所键入的 Xamarin.Forms 布局。 XAML 预览程序现已推出 Visual Studio 2017 和 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: d29186681f79a2f7591978411b5d15fd9f90f807
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563467"
---
# <a name="xaml-previewer-for-xamarinforms"></a>适用于 Xamarin.Forms 的 XAML 预览程序

_请参阅的呈现将如你所键入的 Xamarin.Forms 布局 ！_

## <a name="requirements"></a>要求

项目需要 XAML 预览程序若要运行的最新 Xamarin.Forms NuGet 包。 预览 Android 应用程序需要[JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

中的详细信息[发行说明](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer)。

## <a name="getting-started"></a>入门

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

XAML 预览程序默认情况下，可以控制从**工具 > 选项 > Xamarin > 窗体预览程序**对话框。 在此对话框可以选择默认文档视图和拆分方向。

[![在 Visual Studio 中的 ListView 控件预览](xaml-previewer-images/xamlp-options-vs.png "Visual Studio 中的窗体预览程序选项")](xaml-previewer-images/xamlp-options-vs.png#lightbox "Visual Studio 中的窗体预览程序选项")

在设置中选择打开 XAML 页面编辑器将拆分的基于**工具 > 选项 > Xamarin > 窗体预览程序**对话框。 但是，可以在编辑器窗口中更改这些首选项。

## <a name="xaml-preview-controls"></a>XAML 预览控件

编辑器窗口的顶部具有要选择哪个窗格正在使用中，切换到设计窗格顶部的按钮和切换到源窗格的底部按钮的按钮。 中间的按钮交换窗格顺序。

[![在 Visual Studio 中的 ListView 控件预览](xaml-previewer-images/xamlp-controls-vs.png "控制在 Visual Studio 中的窗体预览程序窗格")](xaml-previewer-images/xamlp-controls-vs.png#lightbox "在 Visual Studio 中的窗体预览程序窗格控件")

编辑器窗口的底部有水平和垂直方向拆分窗格，以展开或折叠当前子窗格的按钮。

[![在 Visual Studio 中的 ListView 控件预览](xaml-previewer-images/xamlp-controls2-vs.png "控制在 Visual Studio 中的窗体预览程序窗格")](xaml-previewer-images/xamlp-controls2-vs.png#lightbox "在 Visual Studio 中的窗体预览程序窗格控件")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

**预览版**打开 XAML 页时，在编辑器上显示的按钮。 在预览窗格可显示或隐藏通过按**预览版**在任何 XAML 文档窗口的右上角的按钮：

[![在 Visual Studio for Mac 的 ListView 控件预览版](xaml-previewer-images/xamlp-list-sml.png "窗体预览程序在 Visual Studio for Mac")](xaml-previewer-images/xamlp-list.png#lightbox "Visual Studio for Mac 中的窗体预览程序")

-----

## <a name="xaml-preview-options"></a>XAML 预览选项

预览窗格顶部的选项包括：

* **Phone** – 在电话大小屏幕中呈现
* **平板电脑**– 呈现在平板电脑大小屏幕 （请注意在窗格的右下角有缩放控件）
* **Android** – 显示在屏幕的 Android 版本
* **iOS** – 显示在屏幕的 iOS 版本
* Portait （图标） – 使用纵向方向预览版
* 横向 （图标） – 使用横向方向预览版

## <a name="adding-design-time-data"></a>添加设计时数据

某些布局可能很难直观显示不包含任何数据绑定到用户界面控件。 若要使预览更有用，分配一些静态数据的控件进行硬编码的绑定上下文 （不管是在代码隐藏中或使用 XAML）。

请参阅 James Montemagno[博客文章上添加设计时数据](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data)若要了解如何将绑定到 XAML 中静态 ViewModel。

## <a name="detecting-design-mode"></a>检测设计模式

静态[ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled)属性可以检查以确定是否在预览程序中运行应用程序。 这使您可以指定在预览程序中运行应用程序时，将仅执行的代码：

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

## <a name="troubleshooting"></a>疑难解答

检查以下问题并[Xamarin 论坛](https://forums.xamarin.com/categories/xamarin-forms)，如果遇到问题。

### <a name="xaml-preview-isnt-showing"></a>未显示 XAML 预览

请检查是否属于以下情况：

* 项目应构建 （编译） 然后再尝试预览 XAML 文件。
* 设计器代理必须安装第一次预览 XAML 文件-进度指示器将显示在预览程序，以及进度消息，直到准备就绪。
* 请尝试关闭并重新打开 XAML 文件。
* 确保你`App`类具有无参数构造函数。

### <a name="invalid-xaml-the-android-project-needs-to-built-before-preview-can-be-created"></a>XAML 无效： 需要在创建预览之前生成 Android 项目

XAML 预览程序需要在呈现页面之前生成该项目。
如果在预览窗格的顶部会出现以下错误，重新生成应用程序，然后重试。

![错误消息： 必须首先生成项目](xaml-previewer-images/error-not-built-sml.png "错误消息： 重新生成项目")
