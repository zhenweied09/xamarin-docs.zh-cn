---
title: UrhoSharp Mac 支持
description: Mac 特定设置和 UrhoSharp 的功能。
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: c7210cbd5586df9018c2779bf0b92aa7c1c56e89
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="urhosharp-mac-support"></a>UrhoSharp Mac 支持

_Mac 特定设置和功能_

尽管 Urho 是一个可移植类库，并且允许相同的 API，用于跨各种平台的游戏的逻辑，仍需要初始化 Urho，在您平台特定的驱动程序，并在某些情况下，你将想要利用平台特定的功能.

在下面的页面中，假定`MyGame`是的一个子类`Application`类。

## <a name="macos"></a>macOS

**支持的体系结构：** x86/x86-64 为 32 位和 64 位。

## <a name="creating-a-project"></a>创建项目

创建控制台项目、 引用 Urho NuGet 和确保找到资产 （这些目录包含的数据目录）。

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>示例

[完整示例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


