---
title: 可扩展应用程序标记语言 (XAML)
description: XAML 是一种声明性的标记语言，可以用于定义用户界面。 在单独的代码隐藏文件中定义的运行时行为的过程中使用 XAML 语法中，XML 文件中定义的用户界面。
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/24/2016
ms.openlocfilehash: bb3b4c4f80171f676e8b5f9a7464f4da890a4643
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="extensible-application-markup-language-xaml"></a>可扩展应用程序标记语言 (XAML)

_XAML 是一种声明性的标记语言，可以用于定义用户界面。在单独的代码隐藏文件中定义的运行时行为的过程中使用 XAML 语法中，XML 文件中定义的用户界面。_

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**发展 2016年： 变得 XAML Master**

> [!NOTE]
> 试用[XAML Standard 预览版](standard/index.md)

<a name="xaml" />

## <a name="xaml-basicsxaml-basicsindexmd"></a>[XAML 基础知识](xaml-basics/index.md)

XAML 允许开发人员在 Xamarin.Forms 应用程序使用的标记，而不是代码中定义的用户界面。 XAML 永远不会需要 Xamarin.Forms 程序中，但它是非常有用，并通常会更直观地连贯和比等效的代码更简洁。 XAML 是非常适用于常用的模型-视图-视图模型 (MVVM) 应用程序体系结构： XAML 定义的视图，通过基于 XAML 的数据绑定链接到 ViewModel 代码。

## <a name="xaml-compilationxamlcmd"></a>[XAML 编译](xamlc.md)

XAML 可以根据需要使用 XAML 编译器 (XAMLC) 直接编译为中间语言 (IL)。 本文介绍如何使用 XAMLC 和优点。

## <a name="xaml-previewerxaml-previewermd"></a>[XAML 预览程序](xaml-previewer.md)

[XAML 预览程序](~/xamarin-forms/xaml/xaml-previewer.md)宣布 Xamarin 发展 2016年是可用于测试 Alpha 通道中。

## <a name="xaml-namespacesnamespacesmd"></a>[XAML 命名空间](namespaces.md)

XAML 使用`xmlns`命名空间声明的 XML 属性。 本文介绍 XAML 命名空间语法中，并演示如何声明一个 XAML 命名空间，以访问的类型。

## <a name="xaml-markup-extensionsmarkup-extensionsindexmd"></a>[XAML 标记扩展](markup-extensions/index.md)

XAML 包括将属性设置为值或超出什么可以使用简单的字符串表示的对象的标记扩展。 其中包括引用常量、 静态属性和字段、 资源字典和数据绑定。

## <a name="passing-argumentspassing-argumentsmd"></a>[传递参数](passing-arguments.md)

可以使用 XAML，将自变量传递到非默认构造函数或工厂方法。 本文演示如何使用可以用于将自变量传递给构造函数，以调用工厂方法，并指定泛型自变量的类型的 XAML 属性。

## <a name="bindable-propertiesbindable-propertiesmd"></a>[可绑定属性](bindable-properties.md)

Xamarin.Forms 中的公共语言运行时 (CLR) 属性的功能扩展可绑定属性。 可绑定属性是属性的一种特殊类型，其中通过 Xamarin.Forms 属性系统跟踪属性的值。 本文介绍可绑定属性，并演示如何创建和使用它们。

## <a name="attached-propertiesattached-propertiesmd"></a>[附加属性](attached-properties.md)

附加的属性是可绑定属性，在一个类中定义但附加到其他对象的特殊类型可识别作为特性的 XAML 中包含的类和句点分隔属性名称。 这篇文章提供附加属性的简介，并演示如何创建和使用它们。

## <a name="resource-dictionariesresource-dictionariesmd"></a>[资源字典](resource-dictionaries.md)

XAML 资源是可以多次使用的对象定义。 A [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)允许定义在一个位置，并在 Xamarin.Forms 应用程序中重新使用的资源。 本文演示如何创建和使用`ResourceDictionary`，以及如何合并一个`ResourceDictionary`到另一个。
