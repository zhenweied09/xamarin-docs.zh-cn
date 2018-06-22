---
title: 常见问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 5a36c6ab14fdc7bfc5916456670be9c8fe4476ff
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049878"
---
# <a name="frequently-asked-questions"></a>常见问题


## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[可否将 Xamarin.Forms 默认模板更新到较新的 NuGet 包？](update-forms-template.md)
本指南使用 Xamarin.Forms.NET 标准库模板作为示例，但相同的常规方法也适用于 Xamarin.Forms 共享项目模板。 

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[为何 Visual Studio XAML 设计器对 Xamarin.Forms XAML 文件不起作用？](forms-xaml-designer.md)
Xamarin.Forms 当前不支持的 XAML 文件的可视化设计器。

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Android 生成错误：“LinkAssemblies”任务意外失败](android-linkassemblies-error.md)
你可能会看到一条错误消息`The "LinkAssemblies" task failed unexpectedly`时生成，该服务 Xamarin.Android 项目使用窗体。 链接器处于活动状态时将发生这种情况 (通常在*版本*生成，以减少应用包的大小); 这是由于 Android 目标不更新为最新的框架和。 


## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["为什么不会通过与 COMPILETODALVIK 我 Xamarin.Forms.Maps Android 项目： 意外的顶级错误？"](maps-compiletodalvik-error.md)
适用于 Mac 或 Visual Studio; 的生成输出窗口中，可能会在 Visual Studio 的错误小键盘出现此错误在 Android 项目中使用 Xamarin.Forms.Maps。 它通常是通过增加你的 Xamarin.Android 项目的 Java 堆大小进行解决。

