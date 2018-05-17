---
title: Xamarin.Essentials 数据传输
description: DataTransfer 类可让应用程序共享数据，例如向其他应用程序在设备上的文本和 web 链接。
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b03ec1330aff1210350adf2600c63d7d84bc1125
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials 数据传输

![预发行 NuGet](~/media/shared/pre-release.png)

**DataTransfer**类可让应用程序共享数据，例如向其他应用程序在设备上的文本和 web 链接。

## <a name="using-data-transfer"></a>使用数据传输

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

数据传输功能的工作方式是调用`RequestAsync`与包含要向其他应用程序共享的信息的数据请求负载的方法。 可以混合使用文本和 Uri 和每个平台将处理根据内容进行筛选。

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

共享到外部应用程序发出请求时出现的用户界面：

![数据传输](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>平台的差异

| 平台 | 差异 |
| --- | --- |
| Android | 使用者属性用于消息的所需的使用者。 |
| iOS | 未使用的主题。 |
| iOS | 未使用的标题。 |
| UWP | 标题将默认为应用程序名称如果未是设置。 |
| UWP | 未使用的主题。 |

## <a name="api"></a>API

- [数据传输源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [数据传输 API 文档](xref:Xamarin.Essentials.DataTransfer)
