---
title: Xamarin.Essentials： 电话拨号程序
description: 在 Xamarin.Essentials PhoneDialer 类使应用程序能够在拨号器中打开一个电话号码
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 34a6c80836d8cb42b1f8fd95718fe248d4701c0f
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130788"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials： 电话拨号程序

![预发行版 NuGet](~/media/shared/pre-release.png)

**PhoneDialer**类使应用程序能够在拨号器中打开一个电话号码。

## <a name="using-phone-dialer"></a>使用电话拨号程序

在类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

电话拨号程序功能的工作方式是调用`Open`方法使用的电话号码以打开与拨号程序。 当`Open`请求 API 将自动尝试根据国家/地区代码，如果指定该数字的格式。

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
