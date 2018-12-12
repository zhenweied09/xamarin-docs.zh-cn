---
title: Xamarin.Essentials：Clipboard
description: 本文档介绍 Xamarin.Essentials 中的 Clipboard 类，此类使你能够在应用程序之间将文本复制并粘贴到系统剪贴板。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 90ede9d0d0fbee9efabcce25c0ae7c3c439d9e69
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898696"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials：Clipboard

Clipboard 类使你能够在应用程序之间将文本复制并粘贴到系统剪贴板。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>使用 Clipboard

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

检查 Clipboard 是否有当前已准备好要粘贴的文本：

```csharp
var hasText = Clipboard.HasText;
```

将文本设置到 Clipboard：

```csharp
await Clipboard.SetTextAsync("Hello World");
```

从 Clipboard 读取文本：

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Clipboard 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Clipboard API 文档](xref:Xamarin.Essentials.Clipboard)
