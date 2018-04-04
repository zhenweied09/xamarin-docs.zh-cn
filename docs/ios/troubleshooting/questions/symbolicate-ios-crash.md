---
title: 在哪里可以找到要 symbolicate iOS 崩溃日志的.dSYM 文件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ce60c19ab0b680e00338f517e5a3f17f725ed329
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>在哪里可以找到要 symbolicate iOS 崩溃日志的.dSYM 文件？

从 visual studio 的 iOS 应用时，可以用于 symbolicate 崩溃报告.dSYM 文件会得到在生成主机上的路径：
```
    /Users/<username>/Library/Caches/Xamarin/mtbs/builds/<appname>/<guid>/bin/iPhone/<configuration>
```

请注意，`~/Library`文件夹隐藏默认情况下，在查找工具中，因此，如果需要使用 Finder 的**转 > 转到文件夹**菜单，然后输入：`~/Library/Caches/Xamarin/mtbs/builds/`若要打开的文件夹。  

或者你可以取消隐藏`~/Library`文件夹使用**显示视图选项**主文件夹的面板。 如果你在查找工具中侧栏中选择主文件夹，并使用查找程序菜单**视图 > 显示视图选项**（或 cmd j），则会显示一个复选框为**显示 Library 文件夹**。


### <a name="see-also"></a>另请参阅
- 扩展的步骤 symbolicating iOS 崩溃报告： [http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Demystifying iOS 应用程序崩溃日志](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
