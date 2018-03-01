---
title: "已知的问题和解决方法"
ms.topic: article
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: d8d419dd5eeb44f9b86ec8668888325e5dfde5bf
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="known-issues--workarounds"></a>已知的问题和解决方法

## <a name="persistence-of-cultureinfo-across-cells"></a>在单元格之间的 CultureInfo 持久性

设置`System.Threading.CurrentThread.CurrentCulture`或`System.Globalization.CultureInfo.CurrentCulture`不会保留在工作簿在 Mono 基于工作簿目标 （Mac、 iOS 和 Android） 上的单元格之间由于[中 Mono 的 bug `AppContext.SetSwitch` ] [ appcontext-bug]实现.

### <a name="workarounds"></a>问题解决

* 设置应用程序域本地`DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* 或者，对工作簿 1.2.1 更新或更新版本中，这将重写分配到`System.Threading.CurrentThread.CurrentCulture`和`System.Globalization.CultureInfo.CurrentCulture`可供所需的行为 （修复单声道 bug 工作）。

## <a name="unable-to-use-newtonsoftjson"></a>无法使用 Newtonsoft.Json

### <a name="workaround"></a>解决方法

* 更新到的工作簿 1.2.1，这将安装 Newtonsoft.Json 9.0.1。
  工作簿 1.3，当前在 alpha 通道，支持 10 和更高版本。

### <a name="details"></a>详细信息

Newtonsoft.Json 10 已发布了可解除了一个依赖于版本的工作簿冲突附带以支持的 Microsoft.CSharp `dynamic`。 在工作簿 1.3 预览版本中，解决了该问题，但现在我们解决了这通过特定于版本 9.0.1 钉住 Newtonsoft.Json。

显式根据 Newtonsoft.Json 10 或更高版本的 NuGet 包仅支持在工作簿 1.3 中当前的 alpha 通道。

## <a name="code-tooltips-are-blank"></a>代码工具提示是空白

没有[Monaco 编辑器中的 bug] [ monaco-bug]中 Safari/WebKit，Mac 工作簿应用中使用时，那样会导致代码不包含文本的工具提示呈现。

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>解决方法

* 单击工具提示显示后，将强制要呈现的文本。

* 或更新到工作簿 1.2.1 或更高版本

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>SkiaSharp 呈现器中工作簿 1.3 丢失了

从工作簿 1.3 开始，我们已经删除了我们发运 SkiaSharp 呈现器在工作簿 0.99.0，为了支持 SkiaSharp 本身提供的呈现器，请使用我们 [SDK] [/ 参考线/跨-平台/工作簿/sdk /]。

### <a name="workaround"></a>解决方法

* 在 NuGet 的最新版本更新 SkiaSharp。 在撰写本文之际，这是 1.57.1。

## <a name="related-links"></a>相关链接

- [报告 Bug](~/tools/workbooks/install.md#reporting-bugs)
