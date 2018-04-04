---
title: iOS RegisterServicePort 与设计器错误
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 62d4a06c62bffb23566f4f59f42a0c980f417c45
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="ios-designer-error-with-registerserviceport"></a>iOS RegisterServicePort 与设计器错误

## <a name="sample-error"></a>示例错误
> System.AggregateException： 一个或多个错误发生---> System.SystemException: RegisterServicePort （com.xamarin.MTHosting.2a0b1，com.apple.PowerManagement.control）： 返回的内核:-308 (-308): 已中止 (ipc/mig) 服务器

## <a name="explanation"></a>说明
与错误`RegisterServicePort`和类似如上面的错误消息通常是间谍软件/恶意软件的计算机上的问题。 请考虑[此 bug 报表上的注释](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4)有关详细信息，以及指向的[Apple 论坛讨论](https://discussions.apple.com/thread/5596008)如何删除可能感染。 

若要帮助诊断问题，请打开 macOS 应用程序**控制台**和删除每个文件内的**用户诊断报告**部分[ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy)。 然后启动 Visual Studio for Mac 并尝试使用设计器。 如果设计器无法初始化后，任何新的日志文件将显示在此部分中，请保存这些密钥以供分析。  

请注意若要检查的最重要事项是此文件： 
> /usr/lib/libimckit.dylib

无论上面的结果，如果该文件存在，前面提到的间谍软件/恶意软件问题是存在于您的计算机上。  

下面的链接已删除此间谍软件/恶意软件的步骤： [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

