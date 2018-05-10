---
title: Xamarin.Essentials 剪贴板
description: 剪贴板类允许您复制并粘贴到系统剪贴板应用程序之间的文本。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 44ba8090851b65327682b3d290d58fb23bb8aae4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials 剪贴板

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
ClipBoard.SetText("Hello World");
```

若要读取从文本**剪贴板**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [剪贴板源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/Clipboard)
- [剪贴板 API 文档](xref:Xamarin.Essentials.Clipboard)
