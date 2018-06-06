---
title: Xamarin.Essentials： 电话拨号程序
description: 中 Xamarin.Essentials 的 PhoneDialer 类可让应用程序在优化的系统首选的浏览器或外部浏览器中打开 web 链接。
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6733e43ed4174d1dd78b2e8f70268eb54adadb98
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782837"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials： 电话拨号程序

![预发行 NuGet](~/media/shared/pre-release.png)

**PhoneDialer**类可让应用程序在优化的系统首选的浏览器或外部浏览器中打开 web 链接。

## <a name="using-phone-dialer"></a>使用电话拨号程序

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

电话拨号程序功能的工作方式是调用`Open`具有电话号码以打开与拨号程序的方法。 当`Open`请求 API 将自动尝试设置基于国家/地区代码，如果指定数字的格式。

```csharp
public class PhoneDialerTest
{
    public async Task PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [电话拨号程序源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [电话拨号程序 API 文档](xref:Xamarin.Essentials.PhoneDialer)
