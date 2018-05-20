---
title: Xamarin.Essentials 电子邮件
description: 电子邮件类可让应用程序使用指定的信息包括使用者、 正文和收件人 （收件人、 抄送、 密件抄送） 打开默认电子邮件应用程序。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3fee30e31dc18665d59f944462959fd3f8166968
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2018
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials 电子邮件

![预发行 NuGet](~/media/shared/pre-release.png)

**电子邮件**类可让应用程序使用指定的信息包括使用者、 正文和收件人 （收件人、 抄送、 密件抄送） 打开默认电子邮件应用程序。

## <a name="using-email"></a>使用电子邮件

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

电子邮件功能的工作方式是调用`ComposeAsync`方法`EmailMessage`，包含有关电子邮件信息：

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
            }
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Sms is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occured
        }
    }
}
```

## <a name="api"></a>API

- [电子邮件源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [电子邮件 API 文档](xref:Xamarin.Essentials.Email)
