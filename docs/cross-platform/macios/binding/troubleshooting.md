---
title: 绑定故障排除
description: 本指南介绍了要执行的操作如果你有困难绑定 OBJECTIVE-C 的库。
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: asb3993
ms.author: amburns
ms.date: 10/19/2016
ms.openlocfilehash: cb685bb60d49615c69925d17f69b0342d4f0a1a6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="binding-troubleshooting"></a>绑定故障排除

有关故障排除到 macOS （以前称为 OS X） 的绑定的一些提示 Xamarin.Mac 中的 Api。

## <a name="missing-bindings"></a>缺少绑定

虽然 Xamarin.Mac 涉及很多 Apple Api，有时你可能需要调用没有绑定某些 Apple API 尚未。 在其他情况下，你需要调用第三方 C/OBJECTIVE-C 它 Xamarin.Mac 绑定的范围之外。

如果你处理的 Apple API，第一步是让 Xamarin 知道，你已达到某一部分的 API，我们不必再为尚未覆盖率。 [提交 bug](#reporting-bugs)注意缺少 API。 我们使用客户的报告为优先处理我们在下一步工作的 Api。 此外，如果你拥有业务或企业版许可证，并且此缺乏绑定正在阻止你的进度，还请遵循的说明[支持](http://xamarin.com/support)文件票证。 我们不能保证一个绑定，但在某些情况下我们可以避开你工作。

缺少绑定的 （如果适用） 通知 Xamarin 下, 一步是要考虑自己绑定。 我们有的完整指南[此处](~/cross-platform/macios/binding/overview.md)和某些非正式文档[此处](http://brendanzagaeski.appspot.com/xamarin/0002.html)手动环绕 OBJECTIVE-C 的绑定。 如果你正在调用 C API 时，您可以使用 C# P/Invoke 机制，文档[此处](http://www.mono-project.com/docs/advanced/pinvoke/)。

如果你决定要在绑定上你自己，请注意在绑定中的错误可以生成各种类型的本机运行时中的有趣崩溃。 具体而言，要非常小心您在 C# 中的签名匹配的自变量的数量和每个自变量的大小中的本机签名。 如果不这样做可能会损坏内存和/或堆栈和无法将来崩溃立即或在某个任意时间点或损坏的数据。

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>当将 null 传递到绑定的自变量异常

Xamarin 工作以提供高质量和经过充分测试的绑定 Apple Api，有时错误而 bug 中的滑动。 到目前为止的最常见，可能会遇到的问题是 API 引发`ArgumentNullException`传递时，null 当基础 API 接受`nil`。 通常定义 API 的本机标头文件不提供足够的信息在其 API 接受 nil 和如果将它传递其将崩溃。

如果插入用例运行其中传入`null`引发`ArgumentNullException`但您认为它应工作，请按照下列步骤：

1. 检查 Apple 文档和/或示例，以了解是否可以找到它接受的证明`nil`。 如果你熟悉 Objective C，你可以编写一个小型测试程序，以验证它。
2. [提交 bug](#reporting-bugs)。
3. 可以解决 bug？ 如果你可以避免调用与 API `null`，调用周围的简单 null 检查可以轻松解决办法。
4. 但是，某些 Api 需要传入 null 来关闭或禁用某些功能。 在这些情况下，你可以要解决此问题通过将程序集浏览器 (请参阅[查找给定选择器的 C# 成员](~/mac/app-fundamentals/mac-apis.md#finding_selector))，复制的绑定，和删除 null 检查。 请确保要提交 bug （步骤 2） 如果你这样做，请作为你复制的绑定不会接收更新和修补程序，我们使中 Xamarin.Mac，并且应将此视为短期解决办法。

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>报告 bug

你的反馈对我们很重要。 如果你发现 Xamarin.Mac 中的任何问题：

- 查看 [Xamarin.Mac 论坛](https://forums.xamarin.com/categories/mac)
- 搜索[问题存储库](https://github.com/xamarin/xamarin-macios/issues) 
- 切换到 GitHub 问题之前，会在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) 中跟踪 Xamarin 问题。 请在其中搜索匹配的问题。
- 如果找不到匹配的问题，请在 [GitHub 问题存储库](https://github.com/xamarin/xamarin-macios/issues/new)中提交一个新问题。

GitHub 的问题是完全公开的。 不能隐藏注释或附件。 

请尽可能多地包含以下内容：

- 一个重现此问题的简单示例。 在可能的情况下，这非常有用。 
- 故障的完整堆栈跟踪。
- 故障周围的 C# 代码。 
