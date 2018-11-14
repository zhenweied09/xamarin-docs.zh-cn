---
title: Xamarin.Essentials：数据传输
description: Xamarin.Essentials 中的 DataTransfer 类使应用程序能够将数据（例如文本和 Web 链接）共享到设备上的其他应用程序。
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 179d4327aa768e7aa2c81dbbffd694d078327400
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674829"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials：数据传输

![预发行版 NuGet](~/media/shared/pre-release.png)

DataTransfer 类使应用程序能够将数据（例如文本和 Web 链接）共享到设备上的其他应用程序。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-data-transfer"></a>使用数据传输

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用具有数据请求有效负载的 `RequestAsync` 方法来使用数据传输功能，此有效负载包括要共享到其他应用程序的信息。 文本和 URI 可以混合使用，每个平台都将根据内容进行筛选处理。

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

在进行请求时显示的共享到外部应用程序的用户界面：

![数据传输](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `Subject` 属性用于所需的消息主题。

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` 未使用。
* `Title` 未使用。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* 如果未设置，`Title` 将默认为应用程序名称。
* `Subject` 未使用。

-----

## <a name="api"></a>API

- [数据传输源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [数据传输 API 文档](xref:Xamarin.Essentials.DataTransfer)
