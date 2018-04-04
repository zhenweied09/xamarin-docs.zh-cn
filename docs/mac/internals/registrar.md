---
title: Xamarin.Mac 注册机构
description: 本文档介绍 Xamarin.Mac 注册机构和其不同的使用配置的用途。
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 4b70ac2271b23b54e7942fdc870e0f49548e6154
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinmac-registrar"></a>Xamarin.Mac 注册机构

_本文档介绍 Xamarin.Mac 注册机构和其不同的使用配置的用途。_

## <a name="overview"></a>概述

Xamarin.Mac 之间充当桥梁托管 (.NET) 领域和 Cocoa 的运行时，允许托管的类来调用非托管的 OBJECTIVE-C 类并返回发生事件时调用。 若要执行此"幻"所需的工作由注册机构进行处理，并且，一般情况下，从视图中隐藏。

没有性能影响的此注册，特定于应用程序启动时间，并且有时可能很有帮助了解所发生事件的"在后台"位。

## <a name="configurations"></a>配置

从根本上在启动的注册机构的作业可以分为两个类别：

- 对于那些从 NSObject 派生扫描每个托管的类并收集要向 Objective C 运行时公开的项列表。
- Objective C 运行时注册此信息。

随着时间推移，三个其他注册机构配置已创建了以涵盖不同用例。 每个具有不同的生成和运行时间后果：

- **动态注册机构**– 在启动期间，使用.NET 反射来扫描每个加载的类型，确定相关项的列表，并告知本机运行时。 此选项添加到生成的时间为零，但代价很高来计算期间 （最多在数秒内） 启动。
- **静态注册机构**– 期间生成，计算要进行注册并生成 Objective C 代码来处理注册的项组。 在启动时快速注册所有项，则调用此代码。 将添加到生成，但可以显著暂停剪切从应用程序开始占用大量的时间。
- **"分部"静态**– 较新的"混合"方法，它提供了大部分这两者的优点。 从导出自**Xamarin.Mac.dll**是常量，预计算库来处理其注册和链接，在保存。 使用反射来处理用户库，但作为用户库导出的平台绑定这通常是相当快速的更少类型。 Neglectable 生成时间影响，并减少绝大多数动态的"成本"。

部分静态今天是调试配置的默认值和静态是发布配置的默认值。

有一些方案：

- 从 NSObject 派生的类可以在启动之后加载插件
- 动态创建类实例从 NSObject 派生

其中的注册机构是无法知道它需要注册开头某种类型。 `ObjCRuntime.Runtime.RegisterAssembly`方法用于通知的注册机构它具有要考虑的其他类型。
