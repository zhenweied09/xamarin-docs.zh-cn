---
title: Xamarin.Essentials： 剪贴板
description: 本文档介绍 Xamarin.Essentials，从中可以复制并粘贴到系统剪贴板中的应用程序之间的文本中的剪贴板类。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 41b15b480fa23bd49667b68e904043e4f1a95732
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38842610"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials： 剪贴板

![预发行版 NuGet](~/media/shared/pre-release.png)

**剪贴板**类，可以复制并粘贴到系统剪贴板中的应用程序之间的文本。

## <a name="using-clipboard"></a>使用剪贴板

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

若要检查是否**剪贴板**具有当前准备要粘贴的文本：

```csharp
var hasText = Clipboard.HasText;
```

若要将文本设置为**剪贴板**:

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
