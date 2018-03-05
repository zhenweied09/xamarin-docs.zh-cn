---
title: "Xamarin.Android 和 JDK 9"
description: "此文章介绍了如何解决在 Xamarin.Android 的 Java 开发工具包 (JDK) 9 错误。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 9e1dd2fac015783e06bad6656f09a687f3abba2c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinandroid-and-jdk-9"></a>Xamarin.Android 和 JDK 9

_此文章介绍了如何解决在 Xamarin.Android 的 Java 开发工具包 (JDK) 9 错误。_


## <a name="overview"></a>概述

Xamarin.Android 使用 Java 开发工具包 (JDK) 将与用于构建 Android 应用和运行 Android 的设计器 Android SDK 集成。 最新版本的 Android sdk (API 24 和更高版本) 需要 JDK 8 (1.8)。 **提供将来自 Google 的 Android SDK 工具尚不可与 JDK 9 兼容，因为 Xamarin.Android 并不适用于 JDK 9。**

## <a name="jdk-9-errors"></a>JDK 9 错误

如果你尝试使用安装的 JDK 9 生成 Xamarin.Android 项目，你将获得一个显式的错误，指示不支持 JDK 9。

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

若要解决这些错误，你必须安装 JDK 8 (1.8) 中所述[如何更新 Java 开发工具包 (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)。


## <a name="checking-the-jdk-version"></a>正在检查的 JDK 版本

你可以查看哪些版本的 Java 已通过输入以下命令安装 (JDK`bin`目录必须包含在你`PATH`):

```shell
java -version
```

如果安装 JDK 9，你将看到如下消息：

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

如果已安装 JDK 9，则必须安装 Java JDK 8 (1.8)。 有关如何安装 JDK 8 的信息，请参阅[如何更新 Java 开发工具包 (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)

## <a name="known-issues-with-jdk-9"></a>JDK 9 的已知的问题

### <a name="apksigner"></a>apksigner

没有 apksigner 和 JDK 9 在其中一个已知的问题`apksigner.bat`文件时，将调用`apksigner.jar`与`-Djava.ext.dirs`而不是`-classpath`JDK 9 期望。 建议使用 JDK 8 (1.8)。 有关如何安装 JDK 8 的信息，请参阅[如何更新 Java 开发工具包 (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)

卸载后 JDK 9，确保以下路径未设置上你`PATH`环境变量，因为它仍将指向 JDK 9: `C:\ProgramData\Oracle\Java\javapath`。 删除它之后,`java -version`在命令行应显示 JDK 8。
