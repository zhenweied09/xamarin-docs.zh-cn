---
title: Xamarin.Forms 的 XAML 预览
description: 此文章介绍了如何使用 XAML 预览程序以查看 Xamarin.Forms 布局呈现你进行键入。 XAML 预览程序可用于 Visual Studio 2017 和 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/31/2018
ms.openlocfilehash: 25c8e1a34f8be5ab2f8491e75fa5aac470d55bc8
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245854"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Xamarin.Forms 的 XAML 预览

_请参阅 Xamarin.Forms 布局呈现你进行键入 ！_

## <a name="requirements"></a>要求

项目需要 XAML 预览程序，工作的最新 Xamarin.Forms NuGet 包。 预览 Android 应用程序需要[JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

中的详细信息[发行说明](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer)。

## <a name="getting-started"></a>入门

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

使用**视图 > 其他 Windows > Xamarin.Forms 预览程序**Visual Studio 打开预览窗口中的菜单。 使用**窗口 > 新建垂直制表符组**菜单上，将其定位并排显示。

[![Visual Studio 中的 ListView 控件预览](xaml-previewer-images/xamlp-list-vs-sml.png "Visual Studio 中的窗体预览程序")](xaml-previewer-images/xamlp-list-vs.png#lightbox "Visual Studio 中的窗体预览程序")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

**预览**按钮可以通过右键单击某个 XAML 文件，并选择显示在编辑器上**打开 > 窗体预览程序**。 然后可显示或隐藏通过按预览窗格**预览**任何 XAML 文档窗口的右上角的按钮：

[![适用于 Mac 的 Visual Studio 中的 ListView 控件预览](xaml-previewer-images/xamlp-list-sml.png "适用于 Mac 的 Visual Studio 中的窗体预览程序")](xaml-previewer-images/xamlp-list.png#lightbox "适用于 Mac 的 Visual Studio 中的窗体预览程序")

-----

## <a name="xaml-preview-options"></a>XAML 预览选项

顶部的预览窗格中的选项如下：

* **Phone** – 呈现 phone 大小屏幕中
* **平板电脑**– 呈现平板电脑大小屏幕中 （请注意在窗格的底部右侧有缩放控件）
* **Android** – 显示在屏幕的 Android 版本
* **iOS** – 显示在屏幕的 iOS 版本
* Portait （图标） – 使用纵向进行预览
* 横向 （图标） – 使用横向预览版

## <a name="adding-design-time-data"></a>添加设计时数据

某些布局可能很难直观显示不包含任何数据绑定到用户界面控件。 若要使预览更为有用，分配某些静态数据的控件通过硬编码 （无论是在隐藏代码还是使用 XAML） 的绑定上下文。

请参阅 James Montemagno 的[上添加设计时数据的博客文章](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data)若要了解如何将绑定到 XAML 中静态 ViewModel。

## <a name="detecting-design-mode"></a>检测的设计模式

静态[ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled)属性可以进行检查以便确定是否在预览程序中运行该应用程序。 这可以指定在预览程序中运行应用程序时，将仅执行代码：

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

## <a name="troubleshooting"></a>疑难解答

检查下面，问题和[Xamarin 论坛](https://forums.xamarin.com/categories/xamarin-forms)，如果你遇到问题。

### <a name="xaml-preview-isnt-showing"></a>没有显示 XAML 预览

请检查是否属于以下情况：

* 应生成项目 （编译） 然后再尝试预览 XAML 文件。
* 设计器代理必须是设置在预览 XAML 文件的第一个时间-进度指示器将出现在预览程序，以及进度消息，直到准备就绪。
* 尝试关闭并重新打开 XAML 文件。

### <a name="invalid-xaml-the-android-project-needs-to-built-before-preview-can-be-created"></a>无效的 XAML： 需要之前可以创建预览生成 Android 项目

XAML 预览程序要求在呈现页面之前生成该项目。
如果在预览窗格中的顶部出现以下错误消息，则重新生成应用程序，然后重试。

![错误消息： 必须首先生成项目](xaml-previewer-images/error-not-built-sml.png "错误消息： 重新生成项目")
