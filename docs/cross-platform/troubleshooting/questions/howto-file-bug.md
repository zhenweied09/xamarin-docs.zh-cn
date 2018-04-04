---
title: 何时以及如何我应文件一个 bug 报告？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 1b23ea33fe19e0d9ae07d1f1e6213c65439f58b1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>何时以及如何我应文件一个 bug 报告？


Xamarin 的 Bugzilla bug 跟踪器中此处报告 bug: [ https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all ](https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all)。

## <a name="file-a-bug-if"></a>如果，提交 bug...


具有一组你认为 Xamarin 工程师将能够使用再现问题所致 Xamarin 的步骤。

或

尤其是如果你还可以描述与问题相关某些精确的情况下，你可以仔细描述的问题，明显的表现。<sup> [[1]](#note-1)</sup>


## <a name="best-practices-to-help-xamarin-address-bugs-quickly-and-efficiently"></a>最佳方案来帮助快速、 高效地 Xamarin 地址 bug


1. <a name="ref-1" />搜索[Bugzilla](https://bugzilla.xamarin.com/query.cgi?format=specific&amp;bug_status=__all__)和 bug 报表或直接可能解决问题的用法建议为现有 web。<sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />请按照[bug 编写准则](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html)来描述清楚明白，简明扼要尽可能情况下，包括新增功能发生了变化和已的说明预计会发生的问题。

1. <a name="ref-3" />包含任何相关的堆栈跟踪、 错误消息文本，或崩溃日志。 <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />记下将显示在屏幕截图附件纯文本太任何重要的错误消息。

1. <a name="ref-5" />包括小、 自包含测试用例可产生与 bug 时尽可能少的代码。  如果您无法重现 （使用内置模板之一创建） 的全新项目的问题，然后请压缩演示问题的项目，然后将其附加到 bug 报表中。  请将其连接之前尽可能简单的示例项目。<sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />描述其中遇到 bug，包括操作系统的环境和[版本的 Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md)以及任何依赖项。

---

## <a name="additional-details"></a>更多详细信息

1. <a name="note-1" />[*^*](#ref-1) 理想情况下的"可见故障现象"说明应包含足够的详细信息，以便其他客户可以确认是否有相同的问题 (相同的错误消息，相同的性能下降，相同的堆栈跟踪从崩溃，_等。_). 对于"精确情况下，"如果您可以说类似于下面是一个很好的示例:"我通常命中问题 75%的时间，但如果更改此项然后我可以避免此问题完全"。 "精确情况"的另一个类似示例是如果降级到以前版本的 Xamarin 停止问题。

1. <a name="note-2" />[*^*](#ref-2) 如你所料，片段的错误文本 （或任何其他唯一的描述性文本） 将是通常是最佳的搜索词。 如果现有 bug 报表不完整，则你将欢迎添加详细信息或文件新，则更好地 bug 报表。

1. <a name="note-3" />[*^*](#ref-3) 另一个良好的问题是，是否将同样的问题报告的任何 Java Objective C 或 Swift 应用。 如果是这样，然后的问题是很有可能属于 Android 或 iOS 本身，而不是 Xamarin 的一部分。

1. <a name="note-4" />[*^*](#ref-4) 要包括的信息的几个示例：

    1. 生成项目，时发生错误，请包括完整[诊断生成输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)上 bug 报表中。
    
    1. 生成或调试 iOS 项目从 Visual Studio 时出现的错误，请运行_帮助 > Xamarin > Zip 日志_后遇到错误，包括在 bug 报表中生成的.zip 文件。
    
    1. 对于异常或崩溃 Android 或 iOS 应用中的，请包括相关"[调试对于 Xamarin.Android 和 Xamarin.iOS 应用程序日志](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)。"

1. <a name="note-5" />[*^*](#ref-5) 如果可能对于你特定的问题，一个很好的选择是通过将一小部分文件从原始解决方案添加到一个全新的解决方案来重新创建该问题。 Xamarin 团队将通常能够调查甚至上更大的测试用例 （假设清楚地说明了重现步骤），但更简单的测试用例提供最佳的机会将快速解决 bug 的问题。


1. <a name="note-6" />[*^*](#ref-6) 如果它是_不_可以通过将一个小文件数添加到全新的解决方案再现该问题，然后你可以压缩，然后为完整应用程序中附加的整个解决方案文件夹。 请删除`bin`， `obj`， `Components`，和`packages`以 zip 文件较小的文件夹。 （IDE 和生成过程将通常还原或根据需要重新创建这些文件夹的内容。）你还可以删除尽可能多的代码和资源的文件从项目根据需要，只要生成的解决方案仍演示原始的问题。

