---
title: MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 6d7fe8f48d22c6b5d445fa8356e52f924549f6e0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest

> [!IMPORTANT]
> Xamarin 的最新版本中，此问题已得到解决。 但是，如果最新版本的软件会出现此问题，请文件[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与你的完整版本控制信息和完整生成日志输出。


## <a name="error-message"></a>错误消息

此错误可能出现在*Mac 服务器日志*Visual Studio 中：

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

此消息中有 2 单独问题：

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    此错误不会造成损害，但它还具有误导性。 它[将删除](https://bugzilla.xamarin.com/show_bug.cgi?id=21667)的未来版本中。

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    此错误是真正的问题。 遗憾的是，导致[限制](https://bugzilla.xamarin.com/show_bug.cgi?id=22080)此异常堆栈跟踪是*不完整*。 如果您注意到如下 Mac 服务器日志中的不完整的堆栈跟踪，你可以检查`~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log`找到完整的堆栈跟踪 Mac 生成主机上的文件。
