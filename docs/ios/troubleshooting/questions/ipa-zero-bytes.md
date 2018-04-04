---
title: IPA 文件为 0 字节
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 40799d0b8b051459145f51671ae7f6143db9635a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="ipa-file-is-0-bytes"></a>IPA 文件为 0 字节

> [!IMPORTANT]
> Xamarin 的最新版本中，此问题已得到解决。 但是，如果最新版本的软件会出现此问题，请文件[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与你的完整版本控制信息和完整生成日志输出。



在以前版本的可能会导致为 0 字节的 Windows 上的 IPA 文件的 Xamarin 没有的一些已知的问题。 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>在 Xamarin for Visual Studio 3.11.584 固定 
- [Bug 24416-从命令行生成"即席"配置对 Windows 执行不复制 IPA 文件](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417-更改"项目属性-> iOS IPA 选项-> 包名称"防止 IPA 复制回 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822-[XVS.iOS 3.11] 设置"生成"号不同于"版本"数字原因 IPA 无法复制到 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>在 Xamarin for Visual Studio 4.1.0.496 固定
- [Bug 27989-显示 ipa 文件在生成服务器出现故障时，如果程序集名称与项目名称不匹配](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
