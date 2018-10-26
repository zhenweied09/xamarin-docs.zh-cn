---
title: 错误 MT1009： 无法将复制程序集
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4a67537cc53aeecf1b86d11dbf041cea79587dd2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105386"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>错误 MT1009： 无法将复制程序集

> [!IMPORTANT]
> 最新版本的 Xamarin.iOS 中已解决此问题。 但是，如果最新版本的软件会出现此问题，请提出[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与完整的版本控制信息和完整生成日志输出。

如中所述我们[发行说明](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/)，这是 Xamarin.iOS 7.2.6 中的已知的问题。 此问题的原因需要更高的权限时使用不同的用户帐户安装 Xamarin.iOS 的文件权限然后开发人员的主帐户。

解决此问题，请打开 Terminal.app 在 Mac 工作站上的，然后运行以下命令：

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

