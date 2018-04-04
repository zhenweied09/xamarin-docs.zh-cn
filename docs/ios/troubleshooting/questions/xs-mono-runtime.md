---
title: 如何在 Xamarin Studio 中设置对于 iOS 项目的 Mono 运行时环境变量？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/31/2017
ms.openlocfilehash: 5f4f3a2de012d35ddca9c1fa830d599d9d5acb17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>如何在 Xamarin Studio 中设置对于 iOS 项目的 Mono 运行时环境变量？

如果你需要为 Mono 设置任何运行时环境变量，可以将它们设置**项目选项 > 运行 > 常规**页。

注意： 垃圾回收环境变量 SGen (MONO\_GC\_PARAMS) 设置从 Xamarin Studio 启动时，将仅使用这种方式。 如果启动设备中的应用程序时，将忽略 Sgen 的设置。 

若要永久设置应用程序的环境变量，你需要添加此作为其他 mtouch 自变量 （对于所有相关的配置）：

```csharp
   --setenv=NAME=VALUE
```

若要查看可设置的环境变量，请参阅单声道手册页： [ http://docs.go-mono.com/?link=man%3amono(1) ](http://docs.go-mono.com/?link=man%3amono(1))请参阅一节： `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "项目的设置环境变量")
