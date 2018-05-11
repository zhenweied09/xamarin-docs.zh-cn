---
title: NuGet 项目 (Nugetizer 3000)
description: 自动创建 NuGet 程序包，以在使用 Nugetizer 3000 的平台间共享代码 ！
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: asb3993
ms.author: amburns
ms.date: 11/22/2017
ms.openlocfilehash: 60c78385d0ffd2fbbdd7af5a4ef4af63e30feb9f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="nuget-projects-nugetizer-3000"></a>NuGet 项目 (Nugetizer 3000)

_自动创建 NuGet 程序包，以在使用 Nugetizer 3000 的平台间共享代码 ！_

可以自动创建 NuGet 程序包，以在使用的平台间共享代码_Nugetizer 3000_。 这样就可以创建从现有的库项目或通过创建新的 NuGet 包**多平台的类库项目**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

包含在 Visual Studio for Mac 6.2 Nugetizer 3000。

[![](images/mulitplatform-library-sml.png "创建新的多平台库窗口")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要在 Visual Studio 中使用 Nugetizer 3000，请[下载并运行 VSIX 安装](http://bit.ly/nugetizer-2017)。

-----

## <a name="building-nuget-packages"></a>构建 NuGet 包

配置后，每次生成项目输出完整的 NuGet 包，它可以用来与其他应用程序内部共享代码或上载到[NuGet.org](https://www.nuget.org)。

有三种方案为使用此功能：

- [现有库项目](existing-library.md)

  从现有的 PCL （或标准.NET） 项目中创建的 NuGet 包。

- [创建一个新的多平台的类库项目](single-codebase.md)

  创建新的库共享通过 NuGet，使用 PCL 或标准.NET 公共代码。

- [创建新的特定于平台的库项目](platform-specific.md)

  创建新的库和 NuGet，包括 iOS 和 Android 的特定于平台的代码并使用共享项目来包含常用代码和特定于平台的项目以支持 iOS 或 Android 特定功能。

请参阅[元数据指南](metadata.md)有关的必选和可选的元数据，必须添加到任何 NuGet 包的详细信息。


## <a name="further-nuget-information"></a>NuGet 的详细信息

阅读更多有关[手动为 Xamarin 创建 NuGets](~/cross-platform/app-fundamentals/nuget-manual.md)和如何[应用中包含 NuGet 包](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

Microsoft 的[NuGet 文档](https://docs.microsoft.com/nuget/)上包含更多详细的信息 **.nupkg**格式和使用 Visual Studio 中的 NuGet 包。

NuGet 包项目的设计讨论 （也称为 NuGetizer 3000) 位于[NuGet GitHub 存储库](https://github.com/NuGet/Home/wiki/NuGetizer-3000)。


## <a name="related-links"></a>相关链接

- [NuGetizer 3000 用例](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [为 Xamarin 手动创建 NuGet 包](~/cross-platform/app-fundamentals/nuget-manual.md)
- [NuGet 文档](https://docs.microsoft.com/nuget/)
- [发行说明](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#NuGetizer_3000)
