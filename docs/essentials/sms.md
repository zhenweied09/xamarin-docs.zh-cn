---
title: Xamarin.Essentials SMS
description: Sms 类，可用于打开默认 SMS 应用程序使用指定的消息将发送到接收方应用程序。
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 5baeee03626ba659ac7e5c06be40039476a67e08
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials SMS

![预发行 NuGet](~/media/shared/pre-release.png)

**Sms**类可让应用程序使用指定的消息将发送到收件人打开默认 SMS 应用程序。

## <a name="using-sms"></a>使用短信

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

SMS 功能的工作方式是调用`ComposeAsync`方法`SmsMessage`，其中包含消息的接收方和消息，这两种都是可选的正文。

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

- [Sms 源代码](https://github.com/xamarin/Essentials/tree/master/Essentials/Sms)
- [Sms API 文档](xref:Xamarin.Essentials.Sms)
