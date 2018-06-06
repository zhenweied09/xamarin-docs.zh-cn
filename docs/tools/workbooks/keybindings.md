---
title: Xamarin 的工作簿编辑器键盘快捷键
description: 本文档介绍可用于 Xamarin 的工作簿编辑器的键盘快捷方式。 具体而言，它考察返回密钥使用的各种方法。
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: topgenorth
ms.author: toopge
ms.openlocfilehash: b0ba268367ebf6a9eb55bd00dd84014de247649b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793899"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Xamarin 的工作簿编辑器键盘快捷键

## <a name="the-return-key-and-its-nuances"></a>返回密钥和其细微差别，

下表介绍了执行代码和创作 markdown 的各种键绑定。 我们已经格外注意以选择为熟悉和流畅的合理且一致键绑定。

|键绑定|代码单元格|Markdown 单元格|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>如果脱字号位于单元格缓冲区末尾的而且可以成功分析该单元格，则将执行结果将显示下面缓冲区，和新的代码单元格将插入和执行单元格之后的已设定焦点的单元格。</p><p>如果分析不成功，将缓冲区中插入新行。 如果未成功分析不会产生编译器诊断。</p>|<p><kbd>返回</kbd>表现出不同的行为，具体取决于脱字号的 Markdown 上下文。</p><ul><li>如果脱字号位于 Markdown 代码块中，将插入文本的新行。</li><li>如果脱字号的 Markdown 列表块中，创建新列表项或拆分当前列表项。</li><li>如果脱字号位于任何其他类型的 Markdown 块，创建一个新的段落块或拆分当前块。</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command‑Return</kbd></dd><dt>Win</dt><dd><kbd>Control‑Return</kbd></dd></dl>|<p>始终尝试解析和执行单元格内容。 如果编译成功，将缓冲区，下面显示的结果 （包括执行异常），如果不有任何后续的单元格，将创建一个新和在已设定焦点。</p><p>如果不存在任何编译错误，将显示诊断和缓冲区仍保持不变的插入符号位置已设定焦点。</p>|将插入，在当前的 markdown 单元格后着重新的代码单元格。|
|<dl><dt>Mac</dt><dd><kbd>Command‑Shift‑Return</kbd><dd><dt>Win</dt><dd><kbd>Control‑Shift‑Return</kbd></dd></dl>|将插入，当前单元格之后着重新的 markdown 单元格。|相同的行为<kbd>返回</kbd>|
|<kbd>Shift‑Return</kbd>|始终插入新行，而不考虑脱字号位置或内容。|插入在当前的 Markdown 块硬盘分行符。|
