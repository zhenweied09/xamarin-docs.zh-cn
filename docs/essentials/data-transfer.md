---
title: Xamarin.Essentials： 数据传输
description: Xamarin.Essentials 中的 DataTransfer 类，应用程序共享数据，例如在设备上的其他应用程序的文本和 web 链接。
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 31e27556a6681b144084d2177cf3fde8fe8e5459
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353514"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials： 数据传输

![预发行版 NuGet](~/media/shared/pre-release.png)

**DataTransfer**类，应用程序可以共享数据，例如在设备上的其他应用程序的文本和 web 链接。

## <a name="using-data-transfer"></a>使用数据传输

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

数据传输功能的工作方式是调用`RequestAsync`与所含信息用于对其他应用程序共享的数据请求有效负载的方法。 可以混合文本和 Uri 和每个平台将处理基于内容的筛选。

```csharp

public class DataTransferTest
{
    public async Task ShareText(string text)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

要共享给发出请求时出现的外部应用程序的用户界面：

![数据传输](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `Subject` 属性用于所需主题的一条消息。

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` 不使用。
* `Title` 不使用。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `Title` 将默认为应用程序名称如果未设置。
* `Subject` 不使用。

-----

## <a name="api"></a>API

- [数据传输的源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [数据传输 API 文档](xref:Xamarin.Essentials.DataTransfer)
