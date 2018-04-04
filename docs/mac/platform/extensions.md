---
title: Xamarin.Mac 扩展支持
description: 本文介绍如何 Xamarin.Mac 版本 2.10 （和更高版本） 中的扩展支持。
ms.prod: xamarin
ms.assetid: 4148F1BE-DFA0-46B6-9FCD-425A6541F510
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 03936c75d31bfd01e741ad2c09096c925dc9dbfc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinmac-extension-support"></a>Xamarin.Mac 扩展支持

Xamarin.Mac 2.10 预览版中已添加了多个 macOS 扩展点：

- 查找工具
- 共享
- 今天

<a name="Limitations-and-Known-Issues" />

## <a name="limitations-and-known-issues"></a>限制和已知的问题

以下限制并且知道开发 Xamarin.Mac 中的扩展时可能发生的问题：

* 当前没有 Visual Studio 中的调试支持 for mac。 所有调试将需要通过完成**NSLog**和**控制台**。 请参阅下面有关详细信息的提示部分。
* 扩展都必须包含在主机应用程序中，当运行一次使用系统的寄存器。 然后必须在中启用它们**扩展**部分**系统首选项**。 
* 某些扩展故障可能导致主机应用程序不稳定和导致奇怪的行为。 具体而言， **Finder**和**今天**部分**通知中心**可能变为"卡住"，并且变得不响应。 这已在扩展项目中在 Xcode 中，有经验和当前显示与 Xamarin.Mac 不相关。 通常这可以在系统日志中看到 (通过**控制台**，有关详细信息，请参阅提示) 打印重复的错误消息。 重新启动 macOS 似乎解决此问题。

<a name="Tips" />

## <a name="tips"></a>提示

使用 Xamarin.Mac 中的扩展时，以下提示可以是很有帮助：

- 因为 Xamarin.Mac 当前不支持调试扩展，调试体验将主要取决于执行和`printf`喜欢语句。 但是，扩展沙盒在进程中运行，因此`Console.WriteLine`将不执行操作的其他 Xamarin.Mac 应用程序中一样。 调用[`NSLog`直接](https://gist.github.com/chamons/e2e409013a449cfbe1f2fbe5547f6554)将调试消息输出到系统日志。
- 任何未捕获的异常时将崩溃扩展过程中，提供只有少量的中的有用信息**系统日志**。 包装中的让人头痛代码`try/catch`（异常） 阻止，`NSLog`的之前重新引发可能会很有用。
- **系统日志**可以从访问**控制台**下的应用**应用程序** > **实用工具**:

    [![](extensions-images/extension02.png "系统日志")](extensions-images/extension02.png#lightbox)
- 如上所述，运行扩展主机应用程序将其注册到系统。 删除与应用程序捆绑包将其取消注册。 
- 如果注册的应用的扩展的"孤立"版本，使用以下命令 （以便它们可以 deleted) 找到它们： `plugin kit -mv`


<a name="Walkthrough-and-Sample-App" />

## <a name="walkthrough-and-sample-app"></a>演练和示例应用

由于开发人员将创建和使用 Xamarin.Mac 扩展 Xamarin.iOS 扩展的方式相同，请参阅我们[扩展简介](~/ios/platform/extensions.md)更多详细信息的文档。

包含小示例 Xamarin.Mac 项目中，可以找到的每个扩展类型的工作示例[此处](https://developer.xamarin.com/samples/mac/ExtensionSamples/)。

<a name="Summary" />

## <a name="summary"></a>总结

本文已采取了快速了解一下使用 Xamarin.Max 版本 2.10 （和更高版本） 应用程序中的扩展。

## <a name="related-links"></a>相关链接

- [了解 Mac](~/mac/get-started/hello-mac.md)
- [ExtensionSamples](https://developer.xamarin.com/samples/mac/ExtensionSamples/)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
