---
title: 更新到 64 位 Xamarin.Mac 统一应用程序
description: 本指南介绍了如何更新面向 64 位您 Xamarin.Mac 的应用程序
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/22/2018
ms.openlocfilehash: e365fe1af47338f41aebe4bc0d81d289466a9b6c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>更新到 64 位 Xamarin.Mac 统一应用程序

截至年 1 月 2018 Apple 要求新[Mac 应用商店提交面向 64 位](https://developer.apple.com/news/?id=06282017a)。 在 Mac 应用商店上已经可用的应用必须更新到面向 64 位中，按年 6 月 2018年中。

**文件** > **新建**Xamarin.Mac 项目模板默认情况下，创建 64 位应用程序，因此任何最近创建的应用程序已将 64 位兼容且不需要任何更改。

## <a name="targeting-64-bit"></a>面向 64 位

1. 打开**项目选项**窗口，供你正在 Xamarin.Mac 应用：

   ![项目的上下文菜单](mac-64-bit-images/1-contextual_menu-vsmac.png "项目的上下文菜单")

2. 选择**Mac 生成**并设置**支持的体系结构**到**x86\_64**:

   [![将支持的体系结构设置为 x86_64](mac-64-bit-images/2-project_options-vsmac.png "到 x86_64 设置支持的体系结构")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. 如果你的应用程序如本机引用或绑定项目任何外部依赖关系，请将它们更新改为面向 64 位。

### <a name="errors"></a>错误

第一次在生成或运行你的应用程序与 64 位支持，你可能会遇到链接错误从 clang 或运行时问题。 可能出现这些错误，如果第三方依赖项-例如，本机引用 Xamarin.Mac 或绑定项目或手动加载系统级框架中的 — 尚未更新到 64 位。

> [!TIP]
> 将你的项目转换为 64 位进行了重大更改，并可能间接揭示各种编程错误。 具体而言，它可能更改的大小和数据结构，这会影响 p/invoke 签名和链接你的项目中的本机代码的对齐。 请考虑查看给定任何生成警告并测试你的应用程序 throughly 之后若要捕获潜在问题。

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>示例的动态链接第三方依赖项未针对 64 位导致错误：

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

此错误无法在运行时通过遵循`dlopen`返回`IntPtr.Zero`而不是预期的句柄。

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>示例的静态链接第三方依赖项未针对 64 位导致错误：

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

若要生成并运行成功，到 64 位更新这些依赖关系并重新编译你的应用程序。

