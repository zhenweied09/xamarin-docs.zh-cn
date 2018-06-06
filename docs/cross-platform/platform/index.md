---
title: 编程中 Xamarin 的语言支持
description: '本文档介绍了 Xamarin 支持的各种编程语言。 它讨论 C#、 F #、 Visual Basic.NET 可移植和 Razor 模板。'
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 715f63a0be54ba3342bd63c1c76d89656313359a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781671"
---
# <a name="programming-language-support-in-xamarin"></a>编程中 Xamarin 的语言支持

## <a name="c"></a>C# 

###  <a name="async-support-overviewcross-platformplatformasyncmd"></a>[异步支持概述](~/cross-platform/platform/async.md)

版本 5 的 C# 引入两个新的关键字表示异步操作： async 和 await。 这些关键字，可以编写在另一个线程中使用任务并行库执行长时间运行操作 （如网络访问权限） 的简单代码并轻松地访问在完成的结果。 Xamarin.iOS 和 Xamarin.Android 的最新版本支持 async 和 await-本文档提供了说明和 xamarin 中使用的新语法的示例。

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[C# 6 语言功能](~/cross-platform/platform/csharp-six.md)

最新版本的 C# 语言 – 版本 6 – 一直发展的要具有样本要少、 改进的清楚起见和更多的一致性的语言。 清理器初始化语法，能够使用`await`中`catch/finally`块和 null 条件`?`运算符是特别有用。

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

使用 F # 和 Xamarin 的构建移动应用程序。

##  <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[可移植 Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md)

Visual Studio 支持使用 Visual Basic.NET 然后可以合并到 Xamarin 应用程序的可移植类库的创建。 这篇文章演示如何创建新的 Visual Basic PCL，然后将其用于示例 Xamarin.iOS、 Xamarin.Android 和 Windows Phone 应用程序。

##  <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[使用 Razor 模板生成 HTML 视图](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin 使开发人员能够利用 Razor 模板化引擎，最初使用 ASP.NET MVC，以及 C# 轻松地将数据与 HTML、 Javascript 和 CSS 结合而无需手动生成代码中的 HTML 字符串的引入。
本文演示如何使用 Xamarin Android 和 iOS Razor 模板。
