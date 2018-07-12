---
title: Xamarin.Essentials： 电子邮件
description: Xamarin.Essentials 中的电子邮件类使应用程序能够使用指定的信息包括主题、 正文和收件人 （收件人、 抄送、 密件抄送） 打开默认的电子邮件应用程序。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: aea2f429126180ae3d98bc665bed5574f416ea53
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848541"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials： 电子邮件

![预发行版 NuGet](~/media/shared/pre-release.png)

**电子邮件**类使应用程序能够使用指定的信息包括主题、 正文和收件人 （收件人、 抄送、 密件抄送） 打开默认的电子邮件应用程序。

## <a name="using-email"></a>使用电子邮件

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

电子邮件功能的工作方式是调用`ComposeAsync`方法`EmailMessage`，包含有关该电子邮件信息：

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
            // Some other exception occured
        }
    }
}
```

## <a name="api"></a>API

- [电子邮件的源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [电子邮件 API 文档](xref:Xamarin.Essentials.Email)
