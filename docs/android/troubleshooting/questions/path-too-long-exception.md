---
title: 如何解决 PathTooLongException 错误？
description: 此文章介绍了如何解决生成应用程序时可能发生 PathTooLongException。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/20/2018
ms.openlocfilehash: f50ca3e738cb781f9c80e83f58f2e0fa1fa8e113
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何解决 PathTooLongException 错误？

## <a name="cause"></a>原因

在 Xamarin.Android 项目中生成的路径名称可能相当长。
例如，可以在生成期间生成的路径如下所示：

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

在 Windows 上 (其中的路径的最大长度是[260 个字符](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx))、 一个**PathTooLongException**无法生成时生成项目，如果生成的路径超过了最大长度。 

## <a name="fix"></a>修复

从开始 Xamarin.Android 8.0 `UseShortFileNames` MSBuild 属性可以设置若要避免此错误。 当此属性设置为`True`(默认值是`False`)，生成过程使用较短的路径名称来减少生成的可能性**PathTooLongException**。
例如，当`UseShortFileNames`设置为`True`，以上路径缩短为类似于以下路径：

**C:\\某些\\目录\\解决方案\\项目\\obj\\调试\\lp\\1\\jl\\资产**

若要设置此属性，请将以下 MSBuild 属性添加到项目**.csproj**文件：

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

有关设置生成属性的详细信息，请参阅[生成过程](~/android/deploy-test/building-apps/build-process.md)。
