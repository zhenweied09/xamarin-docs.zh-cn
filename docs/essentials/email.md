---
title: Xamarin.Essentials：Email
description: Xamarin.Essentials 中的 Email 类使应用程序能够打开包含主题、正文和收件人（TO、CC、BCC）等指定信息的默认电子邮件应用程序。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c8d4a83caf6832f911193067324915fd6226b380
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674959"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials：Email

![预发行版 NuGet](~/media/shared/pre-release.png)

Email 类使应用程序能够打开包含主题、正文和收件人（TO、CC、BCC）等指定信息的默认电子邮件应用程序。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-email"></a>使用 Email

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用 `ComposeAsync` 方法和包含有关电子邮件信息的 `EmailMessage` 来使用 Email 功能：

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string body, List<string> recipients)
    {
        try
        {
            var message = new EmailMessage
            {
                Subject = subject,
                Body = body,
                To = recipients,
                //Cc = ccRecipients,
                //Bcc = bccRecipients
            };
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Email is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occurred
        }
    }
}
```

## <a name="api"></a>API

- [Email 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Email API 文档](xref:Xamarin.Essentials.Email)
