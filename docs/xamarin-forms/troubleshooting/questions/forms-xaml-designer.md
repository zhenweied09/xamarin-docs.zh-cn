---
title: Visual Studio XAML 设计器不起作用的 Xamarin.Forms XAML 文件
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 43088beba6c6a86330cac164856be98d88f07fe2
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2018
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>Visual Studio XAML 设计器不起作用的 Xamarin.Forms XAML 文件

Xamarin.Forms 当前不支持的 XAML 文件的可视化设计器。 因此，尝试在 Visual Studio 中打开窗体 XAML 文件时*XAML 设计器*或*XAML 使用的编码的 UI 设计器*，引发以下错误消息：

> "使用选择的编辑器无法打开该文件。 请选择另一个编辑器。"

中介绍了此限制[概述](~/xamarin-forms/xaml/xaml-basics/index.md#Overview)部分[Xamarin.Forms XAML 基础知识](~/xamarin-forms/xaml/xaml-basics/index.md)指南：

> "不存在，但仍用于 Xamarin.Forms 应用程序中生成 XAML 可视化设计器中的，因此，所有 XAML 必须都可以手工编写。"

但是，可以通过选择显示 Xamarin.Forms XAML 预览程序**视图 > 其他 Windows > Xamarin.Forms 预览程序**菜单选项。
