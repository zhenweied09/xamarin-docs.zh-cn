---
title: "System.Exception AMDeviceNotificationSubscribe 返回..."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 14b138138f3d6297ff587aacc9ea5cde9df54381
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe 返回...

> [!IMPORTANT]
> Xamarin 的最新版本中，此问题已得到解决。 但是，如果最新版本的软件会出现此问题，请文件[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与你的完整版本控制信息和完整生成日志输出。


## <a name="fix"></a>修复

1.  终止`usbmuxd`处理，这样系统将重新启动它：

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2.  如果这样做无法解决问题，请重新启动 mac

## <a name="error-message"></a>错误消息

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

适用于 Mac，或在首次启动 Visual Studio 时，此消息可以出现在错误对话框`mtbserver.log`Xamarin.iOS 生成主机应用程序中的文件 (**Xamarin.iOS 生成主机 > 视图生成主机日志**)。

请注意这是一个常见的问题。 Visual Studio 时遇到无法连接到 Mac 生成主机，是否会更容易出现在其他错误`mtbserver.log`文件。

### <a name="errors-in-systemlog"></a>System.log 中的错误

在某些情况下的以下两个错误消息可能也会显示重复在`/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>其他信息

在错误的根本原因的一个猜测是 OS X 系统服务负责 reporting iOS 设备和模拟器信息可以在极少数情况下，输入意外的状态。 Xamarin 不能与此状态中的系统服务正确交互。 重新启动计算机重新启动系统服务，并解决了问题。

根据产生的错误`system.log`出现此问题可能与 Bonjour (`mDNSResponder`)。 不同 WiFi 网络之间更改似乎会增加发生问题的可能性。

## <a name="references"></a>参考资料

*   [Bug 11789-MonoTouch.MobileDevice.MobileDeviceException: AMDeviceNotificationSubscribe 返回： 0xe8000063 [解析 NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
