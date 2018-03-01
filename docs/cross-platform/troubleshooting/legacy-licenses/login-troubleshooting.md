---
title: "为什么无法我为登录到 Visual Studio 或 Visual Studio 中的 Xamarin Mac？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6EF2B553-5DF9-41CC-B68F-77A7F64572FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: b50969e4c1d75c0bd79c08223dd959241dcb229a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="why-cant-i-log-into-xamarin-in-visual-studio-or-visual-studio-for-mac"></a>为什么无法我为登录到 Visual Studio 或 Visual Studio 中的 Xamarin Mac？

> [!IMPORTANT]
> 本指南不适用于大多数 MSDN 用户由于不需要它们来拥有或登录到 Xamarin 帐户，除非使用[Xamarin 组件存储](https://components.xamarin.com/)或[适用于 Mac (Mac) 的 Visual Studio](~/cross-platform/get-started/requirements.md)。 MSDN 许可证持有人应该引用此[许可选项指南](~/cross-platform/get-started/requirements.md)相反。



## <a name="overview"></a>概述
有可能会阻止你登录到你在 IDE 中的 Xamarin 帐户的几个常见原因。 已知的问题和修复如下所述。

### <a name="finding-the-login-screen"></a>查找登录屏幕

作为参考，以下位置找到登录屏幕：

- Visual Studio for Mac
   - 右上角的欢迎屏幕
   - **Visual Studio for Mac > 帐户**(Mac)
   - **工具 > 帐户**(Windows)
- Visual Studio
   - **工具 > Xamarin 帐户**

## <a name="the-ide-is-connecting-but-the-account-screen-isnt-showing-correct-login-information"></a>IDE 正连接，但帐户屏幕未显示正确的登录信息

通过手动许可证重新同步通常解决此问题。
请按照本文中的说明：[如何实现手动重新同步 Xamarin 许可证？](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)

## <a name="invalid-account-information"></a>帐户信息无效

如果你转到 Xamarin 网站[登录页](https://store.xamarin.com/Login?from=%2faccount%2f)，你可以尝试使用你当前帐户的凭据的日志记录。
页还具有要重置你的密码并创建新的帐户的链接。

## <a name="account-is-valid-but-the-ide-cant-connect"></a>帐户有效，但 IDE 无法连接

这通常被由于防火墙或其他安全设置阻止访问所需的终结点 IDE 时。
激活服务器必须访问以下设置：

> activation.xamarin.com store.xamarin.com auth.xamarin.com

但是，多个其他终结点非常重要的常规开发过程，例如更新、 获取 NuGet 包，等等。因此，建议你先确保*所有*终结点添加从[Xamarin 防火墙配置指南](~/cross-platform/get-started/installation/firewall.md)。

### <a name="ios-in-xamarin-studio-windows"></a>在 Windows 的 Xamarin Studio 中的 iOS
适用于 Windows 的 Xamarin Studio 中不支持 iOS 开发。 访问此处登录屏幕时, 将不会显示 iOS 许可证。

相反，通过 Xamarin Studio (Mac) 或 Visual Studio 使用旧的许可证的登录名。 请注意，在 Windows 上的 MSDN 用户不需要登录。

## <a name="additional-support"></a>其他支持

如果上述方案不描述你的情况或解决此问题，请参阅这些[支持选项](https://www.xamarin.com/support)。
