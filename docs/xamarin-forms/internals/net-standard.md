---
title: Xamarin.Forms 中的.NET 标准 2.0 支持
description: 本文介绍如何将转换为使用.NET 标准 2.0 Xamarin.Forms 应用程序。 .NET 标准是要用于在所有.NET 实现上可用的.NET Api 的规范。
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: dc90155c79d1d2850281744c4c9aac70cbd7ecc3
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242350"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Xamarin.Forms 中的.NET 标准 2.0 支持

_本文介绍如何将转换为使用.NET 标准 2.0 Xamarin.Forms 应用程序。_

.NET 标准是要用于在所有.NET 实现上可用的.NET Api 的规范。 它使更轻松地在桌面应用程序、 移动应用和游戏，间共享代码和云服务，将发送到不同的平台的相同的 Api。 有关所支持的标准.NET 平台的信息，请参阅[.NET 实现支持](/dotnet/standard/net-standard#net-implementation-support/)。

标准.NET 库是替换为可移植类库 (PCL)。 但是，面向.NET 标准库仍然是 PCL 中，并且被称为基于.NET 标准的 PCL。 某些 PCL 配置文件映射到.NET 标准版本，以及对于没有映射的配置文件，两个库类型将能够相互引用。 有关详细信息，请参阅[PCL 兼容性](/dotnet/standard/net-standard#pcl-compatibility)。

Xamarin.Forms 2.4 允许 Xamarin.Forms 应用程序迁移到目标.NET 标准 2.0 通过 PCL 替换.NET 标准 2.0 库。 做到这一点，如下所示：

- 确保[.NET 核心 2.0](https://www.microsoft.com/net/download/core)安装。
- 更新 Xamarin.Forms 解决方案，以便使用 Xamarin.Forms 2.4，或更高版本。
- 将.NET 标准库添加到解决方案，面向.NET 标准 2.0。
- 删除添加到标准.NET 库的类。
- 将 Xamarin.Forms 2.4 （或更高版本） NuGet 包添加到标准.NET 库。
- 在平台项目中，添加对标准.NET 库的引用并删除对包含 Xamarin.Forms 用户界面逻辑的 PCL 项目的引用。
- PCL 项目中的文件复制到标准.NET 库中。
- 删除包含的 Xamarin.Forms 用户界面逻辑的 PCL 项目。


## <a name="related-links"></a>相关链接

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [代码共享选项](~/cross-platform/app-fundamentals/code-sharing.md)
