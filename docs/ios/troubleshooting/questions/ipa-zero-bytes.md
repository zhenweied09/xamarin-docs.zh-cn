---
title: IPA 文件为 0 字节
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4835c980b6b11c92ec1c81dea69f229aa5652275
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102647"
---
# <a name="ipa-file-is-0-bytes"></a>IPA 文件为 0 字节

> [!IMPORTANT]
> 最新版本的 Xamarin 中已解决此问题。 但是，如果最新版本的软件会出现此问题，请提出[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与完整的版本控制信息和完整生成日志输出。



在以前版本的 Xamarin，可能会导致为 0 字节的 Windows 上的 IPA 文件没有一些已知的问题。 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>已在 Xamarin 中修复了 Visual Studio 3.11.584 
- [Bug 24416-从命令行构建"临时"配置 Windows 还执行不复制 IPA 文件](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417-更改"项目属性-> iOS IPA 选项-> 包名称"防止 IPA 正在复制回 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822-[XVS.iOS 3.11] 设置"生成"数量不同于"版本"号原因 IPA，则无法将复制到 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>已在 Xamarin 中修复了 Visual Studio 4.1.0.496
- [Bug 27989-上显示 ipa 文件生成服务器失败，如果程序集名称与项目名称不匹配](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
