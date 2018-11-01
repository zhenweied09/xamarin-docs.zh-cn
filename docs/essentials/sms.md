---
title: Xamarin.Essentials：SMS
description: Xamarin.Essentials 中的 SMS 类允许应用程序使用要发送到收件人的指定消息打开默认短信应用程序。
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 276c1a03b1446c30e7047d454e06598b39bdad6c
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675231"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials：SMS

![预发行版 NuGet](~/media/shared/pre-release.png)

SMS 类允许应用程序使用要发送到收件人的指定消息打开默认短信应用程序。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-sms"></a>使用 SMS

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

SMS 功能通过调用 `ComposeAsync` 方法工作，该方法是包含消息收件人和消息正文（两者都是可选的）的 `SmsMessage`。

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

此外，还可以向 `SmsMessage` 传入多个收件人：

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string[] recipients)
    {
        try
        {
            var message = new SmsMessage(messageText, recipients);
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

- [SMS 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [SMS API 文档](xref:Xamarin.Essentials.Sms)
