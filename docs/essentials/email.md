---
title: Xamarin.Essentials：Email
description: Xamarin.Essentials 中的 Email 类使应用程序能够打开包含主题、正文和收件人（TO、CC、BCC）等指定信息的默认电子邮件应用程序。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d7d2536fca32fe3ae9f9692031645c42edb4ea61
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898651"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials：Email

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


## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

并非所有适用于 Android 的电子邮件客户端都支持 `Html`，因为无法检测此差异，因此我们建议使用 `PlainText` 发送电子邮件。

# <a name="iostabios"></a>[iOS](#tab/ios)

无平台差异。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

仅支持 `PlainText`，因为尝试发送 `Html` 的 `BodyFormat` 将引发 `FeatureNotSupportedException`。

-----

## <a name="api"></a>API

- [Email 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Email API 文档](xref:Xamarin.Essentials.Email)
