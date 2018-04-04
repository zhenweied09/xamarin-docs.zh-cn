---
title: 我 Xamarin.Forms.Maps Android 项目，COMPILETODALVIK 意外顶级错误为什么失败？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>我 Xamarin.Forms.Maps Android 项目，COMPILETODALVIK 意外顶级错误为什么失败？

适用于 Mac 或 Visual Studio; 的生成输出窗口中，可能会在 Visual Studio 的错误小键盘出现此错误在 Android 项目中使用 Xamarin.Forms.Maps。

这通常是通过增加你的 Xamarin.Android 项目的 Java 堆大小进行解决。 请按照这些步骤以提高堆大小：

## <a name="visual-studio"></a>Visual Studio

1. 右击 Android 项目 （&) 打开项目选项。
2. 转到**Android 选项-> 高级**
3. 在 Java 堆大小文本框中输入 1 G。
4. 重新生成项目。

![Visual Studio 项目选项的屏幕截图](maps-compiletodalvik-error-images/vsjavaheap.png "Android 生成 Visual Studio 中的选项")

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1.  右击 Android 项目 （&) 打开项目选项。
2.  转到**生成-> Android 生成-> 高级**
3.  在 Java 堆大小文本框中输入 1 G。
4.  重新生成项目。  

![Visual Studio for Mac 项目选项的屏幕截图](maps-compiletodalvik-error-images/xsjavaheap.png "Android 生成 Visual Studio 中适用于 Mac 的选项")

