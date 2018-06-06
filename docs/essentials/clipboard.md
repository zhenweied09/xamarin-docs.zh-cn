---
title: Xamarin.Essentials： 剪贴板
description: 本文档介绍 Xamarin.Essentials，从中可以复制并粘贴到系统剪贴板应用程序之间的文本中的剪贴板类。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 41b15b480fa23bd49667b68e904043e4f1a95732
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782354"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials： 剪贴板

![预发行 NuGet](~/media/shared/pre-release.png)

**剪贴板**类可复制并粘贴到系统剪贴板应用程序之间的文本。

## <a name="using-clipboard"></a>使用剪贴板

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要检查**剪贴板**具有当前准备要粘贴的文本：

```csharp
var hasText = Clipboard.HasText;
```

将文本设置为**剪贴板**:

```csharp
Clipboard.SetText("Hello World");
```

若要读取从文本**剪贴板**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [剪贴板源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [剪贴板 API 文档](xref:Xamarin.Essentials.Clipboard)
