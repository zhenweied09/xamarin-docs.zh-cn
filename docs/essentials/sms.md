---
title: Xamarin.Essentials： 短信
description: Xamarin.Essentials 中的 Sms 类使应用程序以使用指定的消息将发送到收件人打开默认短信应用程序。
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a93a67b83ea8f435a5e3ad5d26e1d6cbbb7092f7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815592"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials： 短信

![预发行版 NuGet](~/media/shared/pre-release.png)

**Sms**类使应用程序以使用指定的消息将发送到收件人打开默认短信应用程序。

## <a name="using-sms"></a>使用短信

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

短信功能适用于通过调用`ComposeAsync`方法`SmsMessage`，其中包含消息的收件人和消息，这两者都是可选的正文。

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, recipient);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Sms 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [短信 API 文档](xref:Xamarin.Essentials.Sms)
