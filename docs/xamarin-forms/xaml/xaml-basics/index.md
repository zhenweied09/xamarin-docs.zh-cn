---
title: Xamarin.Forms XAML 基础知识
description: 开始使用移动设备的跨平台标记
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 991d928c2c58f05098a41c84aba295a31636ab96
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-xaml-basics"></a>Xamarin.Forms XAML 基础知识

XAML (Extensible Application Markup Language) 允许开发人员在 Xamarin.Forms 应用程序中使用标记（而不是代码）来定义用户界面。 XAML 永远不需要在 Xamarin.Forms 程序中，但通常会更简洁的和比等效代码更为直观连贯的可能非常有用。 XAML 是非常适用于常用的 MVVM （模型-视图-视图模型） 应用程序体系结构： XAML 定义的视图，通过基于 XAML 的数据绑定链接到 ViewModel 代码。

## <a name="xaml-basics-contents"></a>XAML 基础知识内容

* [概述](#Overview)
* [第 1 部分：XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [第 2 部分：基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [第 3 部分：XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [第 5 部分：从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

除了这些 XAML 基础知识文章中，你可以下载书籍的章节[具有 Xamarin.Forms 创建移动应用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "封面")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

中更深入书籍 （） 的许多章节中介绍了 XAML 主题包括：

<table style="border:0px; box-shadow:0 0px 0px" cellpadding="0" cellspacing="2" border="0" width="85%">
<tr style="background:#ecf0f1">
  <td style="border:0px;">
    <h4>第 7 章。 XAML vs。代码</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf">下载 PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md">摘要</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>第 8 章。 代码和协调的 XAML</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf">下载 PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md">摘要</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>第 10 章。 XAML 标记扩展</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf">下载 PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md">摘要</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>第 18 章。 MVVM</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf">下载 PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md">摘要</a></td></tr>
</table>

这些章节可以是[免费下载](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)。

<a name="Overview" />

## <a name="overview"></a>概述

XAML 是一种基于 XML 的语言由 Microsoft 创建作为实例化和初始化对象，和组织父-子层次结构中的那些对象的编程代码的替代方法。 XAML 适应在.NET framework 中中的多种技术但它在定义 Windows Presentation Foundation (WPF)、 Silverlight、 Windows 运行时和通用的 Windows 中的用户界面的布局中找到了其最大的实用程序平台 (UWP)。

XAML 也是 Xamarin.Forms 的跨平台基于本机的编程接口 iOS、 Android 和 UWP 的一部分移动设备。 在 XAML 文件中，Xamarin.Forms 开发人员可以定义用户界面使用所有 Xamarin.Forms 视图、 布局和页，为很好地自定义类。 可以编译或可执行文件中嵌入的 XAML 文件。 无论哪种方式，在生成时查找命名的对象，再在运行时实例化和初始化对象，以及这些对象和编程的代码之间建立链接，被分析 XAML 信息。

XAML 相比具有一些优势等效代码：

-  XAML 通常会更简洁、 更具可读性比等效代码。
-  XML 中的固有的父-子层次结构允许要以更高版本的 visual 清晰度来模拟用户界面对象的父-子层次结构 XAML。
-  XAML 可以轻松地手动编写的程序员，但还适于本身非常有用且由可视化设计工具生成。

当然，也有缺点，主要与相关的固有的标记语言的限制：

-  XAML 不能包含代码。 必须在代码文件中定义所有事件处理程序。
-  XAML 不能包含重复处理的循环。 (但是，多个 Xamarin.Forms 视觉对象-最值得注意的是[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) -可以生成多个子元素中的对象上基于其`ItemsSource`集合。)
-  XAML 不能包含有条件处理 （但是，数据绑定可以引用有效地允许某些条件的处理的基于代码的绑定转换器。）
-  XAML 通常无法实例化未定义无参数构造函数的类。 （但是，没有有时方法解决此限制。）
-  XAML 通常无法调用方法。 （同样，此限制可以有时克服。）

还没有可视化设计器用于在 Xamarin.Forms 应用程序中生成 XAML。 所有 XAML 都必须手工编写的但没有[XAML 预览程序](~/xamarin-forms/xaml/xaml-previewer.md)。 新到使用 XAML 的程序员可能想要频繁生成并运行其应用程序，特别之后可能不明显正确的任何内容。 凭借大量经验在 XAML 中的偶数开发人员知道实验奖励有。

XAML 基本上是 XML，但 XAML 具有一些独特的语法功能。 最重要的是：

- 属性元素
- 附加的属性
- 标记扩展

这些功能*不*XML 扩展。 XAML 是完全合法的 XML。 但这些 XAML 语法功能使用 XML 以唯一方式。 它们详细讨论中的文章，其中介绍了如何使用 XAML 实现 MVVM 以此结束。

## <a name="requirements"></a>要求

本文假定你熟悉 Xamarin.Forms 工作。 读取[Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)强烈建议。

本文还假定你熟悉 XML，包括了解的 XML 命名空间声明和条款使用某些*元素*，*标记*，和*属性*。

当你熟悉 Xamarin.Forms 和 XML 时，即可开始读取[第 1 部分。Getting Started with XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)。



## <a name="related-links"></a>相关链接

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Xamarin.Forms 简介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [创建移动应用簿](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
