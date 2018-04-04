---
title: 交互式工作簿
description: 使用工作簿包含 C# 代码进行试验，创建实时文档教学、 培训或浏览。
ms.prod: xamarin
ms.assetid: B79E5DE9-5389-4691-9AA3-FF4336CE294E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 7577380ff78b9b94b88f5a4190df32400d2c573f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="interactive-workbooks"></a>交互式工作簿

_使用工作簿包含 C# 代码进行试验，创建实时文档教学、 培训或浏览。_

你可以作为独立应用程序，独立于 IDE 中使用工作簿。

若要开始创建新的工作簿，运行工作簿应用。 如果你尚未已安装此，请访问[安装](~/tools/workbooks/install.md#install)页。 将提示你在你的平台的选项，它会自动将连接到代理应用程序允许您可视化你实时的文档中创建的工作簿。

如果尚未运行的工作簿应用，你可以通过浏览到创建新文档**文件 > 新建**。

可以保存并应用程序中再打开工作簿。 你可以然后与他人共享演示想法、 浏览新 Api，或教授新概念。

## <a name="code-editing"></a>编辑代码

提供编辑窗口的代码的代码完成、 语法着色、 内联实时诊断和多行语句支持。

[ ![](workbook-images/inspector-0.6.0-repl-small.png "编辑窗口的代码提供了代码完成、 语法着色、 内联实时诊断和多行语句支持")](workbook-images/inspector-0.6.0-repl.png#lightbox)

Xamarin 工作簿保存为`.workbook`文件，它是具有顶部一些元数据的 CommonMark 文件 (请参阅[工作簿文件类型](#workbooks-files-types)有关详细信息可以保存工作簿的方式)。

### <a name="nuget-package-support"></a>NuGet 包支持

在 Xamarin 的工作簿中直接支持许多常用的 NuGet 包。 你可以通过浏览到搜索包**文件 > 添加包**。 添加包将自动显示`#r`语句引用包的程序集，从而可以立即使用它们。

与包引用保存工作簿时，也保存那些引用。 如果与其他人共享工作簿，它将自动下载引用的包。

有一些与工作簿中的 NuGet 包支持的已知的限制：

  * 本机库是仅在 iOS 上受支持，仅当与托管的库链接。
  * 包依赖于`.targets`文件或 PowerShell 脚本将可能无法按预期方式工作。
  * 若要删除或修改的包依赖关系，请使用文本编辑器中编辑工作簿的清单。 正确的包管理的方式是。

### <a name="xamarinforms-support"></a>Xamarin.Forms 支持

如果在你的工作簿中引用的 Xamarin.Forms NuGet 包，则工作簿应用将更改其主视图是基于 Xamarin.Forms 的。 您可以访问它通过`Xamarin.Forms.Application.Current.MainPage`。

视图检查器选项卡还具有用于显示 Xamarin.Forms 视图层次结构，来帮助你了解你的布局的特殊支持。

## <a name="rich-text-editing"></a>格式文本编辑

在使用包含，丰富的文本编辑器，如下所示代码周围，可以编辑的文本：

![](workbook-images/inspector-0.6.2-editing.gif "编辑使用内置的富文本编辑器的代码周围的文本")

### <a name="markdown-authoring"></a>Markdown 创作

工作簿作者可能有时还会发现它更轻松地直接使用其喜爱的编辑器中编辑 CommonMark 工作簿的"源"。

请注意，如果你然后编辑并保存工作簿中工作簿客户端，CommonMark 文本可能会重新设置格式。

请注意，由于 CommonMark 扩展我们使用来启用 YAML 工作簿文件中的元数据`---`是保留为该目的。 如果你想要创建[主题中断](http://spec.commonmark.org/0.27/#thematic-break)在文本中，你应使用`***`或`___`相反。 在工作簿 1.2 和更早版本由于期间保存 bug，则应避免此类中断。

### <a name="improvements-in-workbooks-13"></a>工作簿 1.3 中的改进

我们已扩展稍有用于提高演示文稿的 Markdown 块引号语法。 通过将 emoji 添加为块引号中的第一个字符，您可以影响引号的背景色：

- `> [!NOTE]
>将呈现为具有蓝色背景的注释
- `> [!IMPORTANT]
>将呈现为包含一个黄色背景警告
- `> [!WARNING]
>将呈现为包含一个红色背景问题

你也可以链接到工作簿文档中的标头。 我们使用正在处理，如下所示的标头文本的定位点 ID 生成每个标头，定位的点：

1. 标头是较低大小写形式。
1. 删除除字母数字和短划线之外的所有字符。
1. 所有空格都替换短划线。

这意味着，一个标头一样"重要标头"获取 id 为`important-header`和可以插入到链接链接到`#important-header`工作簿中。

## <a name="document-structure"></a>文档结构

### <a name="cell"></a>单元格

表示可执行代码或 markdown 离散单元的内容。 代码单元格包含的最多四个子组件：

- 编辑器
  - 缓冲区
- 编译器诊断
- 控制台输出
- 执行结果

### <a name="editor"></a>编辑器

单元格交互式文本组件。 对于代码单元格，这是语法突出显示等的实际代码编辑器。为 markdown 单元格，这是与上下文相关的设置格式和创作工具栏的多格式文本内容编辑器。

### <a name="buffer"></a>缓冲区
编辑器实际文本内容。

### <a name="compiler-diagnostics"></a>编译器诊断

编译代码时，呈现只在需要显式执行时，将生成的任何诊断信息。 立即显示在单元格编辑器下方。

### <a name="console-output"></a>控制台输出

在单元格执行过程中写入标准输出或标准错误任何输出。 将以黑色文本呈现标准输出和标准错误将以红色文本呈现。

### <a name="execution-results"></a>执行结果

丰富且可能交互式表示形式的单元格的结果将呈现在成功编译时，提供通过执行实际生成结果。 异常认为是在此上下文中的结果，因为它们生成实际执行编译。

## <a name="workbooks-files-types"></a>工作簿文件类型

### <a name="plain-files"></a>普通文件

默认情况下，工作簿将保存为纯文本`.workbook`包含 CommonMark 格式的文本的文件。

### <a name="packages"></a>包

工作簿包是与名为的目录`.workbook`扩展。
上的 Mac 的 Xamarin 工作簿打开的对话框和最近的文件菜单中，此目录将能够识别和就像它是一个文件。

目录必须包含`index.workbook`文件，它是将加载到 Xamarin 工作簿中的实际的纯文本工作簿。 目录还可以包含所需的资源`index.workbook`，包括图像或其他文件。

如果纯文本`.workbook`引用资源从其相同的目录的文件将在工作簿 0.99.3 中打开或更高版本，它保存时，它将转换为`.workbook`包。 这是在 Mac 和 Windows 上 true。

> [!NOTE]
> Windows 用户将打开`package.workbook\index.workbook`文件直接，但包将行为 mac 上相同

### <a name="archives"></a>存档

工作簿包，正在目录，可能很难通过 internet 轻松分发。 解决方案是工作簿存档。 工作簿存档是 zip 压缩工作簿程序包，名为`.workbook`扩展。

保存工作簿包时，从工作簿 1.1 中开始，保存对话框提供改为将保存为存档的选项。 工作簿 1.0 必须创建或保存存档没有内置的方法。

在工作簿 1.0 中，打开工作簿存档时，它以透明方式转换到工作簿包中，和 zip 文件已丢失。 在工作簿 1.1 中保留的 zip 文件。 当用户保存存档时，它都替换为使用新的 zip 文件。

你可以通过右键单击工作簿包并选择手动创建的工作簿存档**压缩**在 Mac 上，或**发送到 > 压缩 (zipped) 文件夹**Windows 上。 然后将 zip 文件能够重命名`.workbook`文件扩展名。 这仅适用于工作簿程序包，不纯的工作簿文件。

## <a name="related-links"></a>相关链接

- [欢迎使用工作簿](https://developer.xamarin.com/workbooks/workbooks/getting-started/welcome.workbook)
