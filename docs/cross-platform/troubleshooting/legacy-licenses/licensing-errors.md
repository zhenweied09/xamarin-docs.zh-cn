---
title: 一些特定的授权错误
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D26BDF2D-923B-4BC1-841A-74583155DB71
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 8592fe5381c974e999477d0ef6ca6ebdd8b38cc4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="some-specific-licensing-errors"></a>一些特定的授权错误

> [!IMPORTANT]
> 下面的信息不适用于为 MSDN 用户，因为它们是旧 Xamarin 许可证相关的问题。 如果你是 MSDN 用户，并且你看到错误类似于下面，请尝试[更新至最新版本的 Xamarin](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) （& a) 引用此[许可选项指南](~/cross-platform/get-started/requirements.md)。



## <a name="invalid-license"></a>"许可证无效"

> 许可证无效。 请重新激活 Xamarin.Android (XA9999) 无法确定许可版。 (XA9010)

可以在几个不同的情况下出现这些消息。

-   在磁盘上的当前许可证可能是扩展的同步的计算机上的当前用户信息。 [刷新许可证文件](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)将解决此问题在许多情况下。

-   计算机可能具有 2 冲突的重复激活。 Xamarin 不再使用此类型的许可证。 如果您遇到看起来与此错误，请相关的问题[电子邮件支持](https://www.xamarin.com/support)。

-   Xamarin 帐户可能无法尚未参与到 Xamarin 许可证。 另请参阅：[团队许可证管理](~/cross-platform/troubleshooting/legacy-licenses/team-management.md)。

## <a name="failed-to-load-android-entitlements"></a>"无法加载 Android 权利"

> Mono.VisualStudio.Shell.ShellPackage 错误： 0： 未能加载 Android 权利： 许可证无效。 请重新激活 Xamarin.Android (XA9999) Mono.VisualStudio.Shell.ShellPackage 错误： 0： 失败许可证更新： 许可证无效。 请重新激活 Xamarin.Android (XA9999)

这是上面的"许可证无效"问题的较旧版本。 故障排除步骤同样适用。

## <a name="this-version-was-released-after-your-subscription-expired"></a>"你的订阅过期后，已发布此版本"

> 错误 XA9000： 此版本发布你的订阅过期后 (11/11/2014年 5:11:41 PM)。 (XA9000)(Mobile.Android.Utilities) 错误 XA9010： 无法确定许可版。 (XA9010)(Mobile.Android.Utilities)

这些消息通常出现在两种情况下：

-   磁盘上的许可证已过时。 [刷新许可证文件](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)将解决此问题在许多情况下。

-   Xamarin 订阅不再处于活动状态，和 Xamarin 的当前安装的版本是比订阅的到期日期。 在这种情况下正确修复是[降级](http://kb.xamarin.com/customer/portal/articles/1699777)到 Xamarin 订阅过期之前发布的版本。 请尝试发送电子邮件至[ hello@xamarin.com ](mailto:hello@xamarin.com)以请求为你的订阅有效版本。 如果你仍在将降级之后看到此错误，请务必尝试过刷新许可证文件。

## <a name="additional-references"></a>其他参考

-   [如何刷新许可证](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)
-   [如何将降级 Xamarin](http://kb.xamarin.com/customer/portal/articles/1699777-downgrading)
-   [Xamarin.Android 错误代码列表](~/android/troubleshooting/errors.md)
-   [MonoTouch (Xamarin.iOS) 错误代码列表](~/ios/troubleshooting/mtouch-errors.md)

### <a name="next-steps"></a>后续步骤
有关进一步的帮助，请与我们联系，或如果此问题仍然存在即使利用的上述信息，请参阅[哪些支持选项均可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要的文件的新 bug。
