---
title: 使用 CocoaPods 实际示例
description: 本文档演示如何使用目标 Sharpie 从 CocoaPod 自动生成的 C# 绑定定义。
ms.topic: article
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/28/2018
ms.openlocfilehash: 24c796cb258578fdfc68c5b4aa1079d3c589da0f
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="real-world-example-using-cocoapods"></a>使用 CocoaPods 实际示例

> [!NOTE]
> 此示例使用[AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking)。

3.0 版开始中的新增功能目标 Sharpie 支持绑定 CocoaPods，，甚至可以包括命令 (`sharpie pod`) 以下载、 配置和构建 CocoaPods 非常简单。 你应[熟悉 CocoaPods](https://cocoapods.org)通常之前使用此功能。

## <a name="creating-a-binding-for-a-cocoapod"></a>为 CocoaPod 创建绑定

`sharpie pod`命令具有一个全局选项和两个子命令：

```bash
$ sharpie pod -help
usage: sharpie pod [OPTIONS] COMMAND [COMMAND_OPTIONS]

Pod Options:
  -d, -dir DIR     Use DIR as the CocoaPods binding directory,
                   defaulting to the current directory

Available Commands:
  init         Initialize a new Xamarin C# CocoaPods binding project
  bind         Bind an existing Xamarin C# CocoaPods project
```

`init`子命令还具有一些有用的帮助：

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

多个 CocoaPod 名称和 subspec 名称可以提供给`init`。

```bash
$ sharpie pod init ios AFNetworking
** Setting up CocoaPods master repo ...
   (this may take a while the first time)
** Searching for requested CocoaPods ...
** Working directory:
**   - Writing Podfile ...
**   - Installing CocoaPods ...
**     (running `pod install --no-integrate --no-repo-update`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
** 🍻 Success! You can now use other `sharpie podn`  commands.
```

一旦你 CocoaPod 设置完成后，你现在可以创建绑定：

```bash
$ sharpie pod bind
```

这将导致 CocoaPod Xcode 项目正在生成，然后计算以及由目标 Sharpie 进行分析。 大量的控制台输出将生成，但应导致末尾的绑定定义：

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>后续步骤

在生成后**ApiDefinitions.cs**和**StructsAndEnums.cs**文件，请查看以下文档生成要在你的应用程序中使用的程序集：

- [绑定 OBJECTIVE-C 的概述](~/cross-platform/macios/binding/overview.md)
- [绑定 Objective C 库](~/cross-platform/macios/binding/objective-c-libraries.md)
- [演练： 将绑定 iOS Objective C 库](~/ios/platform/binding-objective-c/walkthrough.md)

