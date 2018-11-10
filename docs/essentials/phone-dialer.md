---
title: Xamarin.Essentials：电话拨号程序
description: Xamarin.Essentials 中的 PhoneDialer 类使应用程序能够在拨号程序中打开一个电话号码
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: bccc76e1602c475a5e4cc9a95d498d11f9a379b1
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675414"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials：电话拨号程序

![预发行版 NuGet](~/media/shared/pre-release.png)

PhoneDialer 类使应用程序能够在拨号程序中打开一个电话号码。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>使用电话拨号程序

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用具有要用于打开拨号程序的一个电话号码的 `Open` 方法来使用电话拨号程序功能。 当请求 `Open` 时，API 将自动尝试根据国家/地区代码设置号码的格式（如果已指定）。

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
