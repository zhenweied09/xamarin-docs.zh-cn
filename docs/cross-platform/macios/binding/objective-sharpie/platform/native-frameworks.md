---
title: 绑定本机框架
description: 本文档介绍如何使用目标 Sharpie 的-框架选项以创建一个绑定到库分布式用作框架。
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 02ee21ce58ecf945893f7e4f94763731abe92018
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781440"
---
# <a name="binding-native-frameworks"></a>绑定本机框架

有时本机库作为分发[framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html)。 目标 Sharpie 绑定正确定义框架通过提供便利功能`-framework`选项。

例如，绑定[Adobe 创造性 SDK 框架](https://creativesdk.adobe.com/downloads.html)为 iOS 非常简单：

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

在某些情况下，一个框架，将指定**Info.plist**框架针对哪些 SDK 指示应进行编译。 如果该信息存在和没有显式`-sdk`一起传递选项，则目标 Sharpie 将从 framework 的推断**Info.plist** (任一`DTSDKName`密钥或的组合`DTPlatformName`和`DTPlatformVersion`密钥)。

`-framework`选项不允许显式标头文件传递。 通过基于框架名称的约定选择涵盖标头文件。 如果找不到涵盖标头目标 Sharpie 不会尝试将绑定框架，你必须手动执行通过提供正确的涵盖标头文件包含进行分析、 和 clang 任何 framework 自变量的绑定 (如`-F`framework 的搜索路径选项)。

实质上，指定`-framework`是只的快捷方式。 以下绑定自变量是等于`-framework`上面的速记。
特殊的重要的是`-F .`framework 搜索路径用于 clang （注意空格和句点，所需的命令的一部分）。

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>

