---
title: "错误 MT1009： 无法将复制程序集"
ms.topic: article
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 030c54275ef384f4a020abaf79456705e8fac0d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>错误 MT1009： 无法将复制程序集

> [!IMPORTANT]
> 此问题已得到解决的 Xamarin.iOS 的最新版本中。 但是，如果最新版本的软件会出现此问题，请文件[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与你的完整版本控制信息和完整生成日志输出。

中所述我们[发行说明](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/)，这是 Xamarin.iOS 7.2.6 中的已知的问题。 此问题是由于 Xamarin.iOS 安装使用不同的用户帐户时需要更高特权的文件权限然后开发人员的主帐户。

解决该问题，请打开在 Mac 工作站上的 Terminal.app，然后运行以下命令：

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

