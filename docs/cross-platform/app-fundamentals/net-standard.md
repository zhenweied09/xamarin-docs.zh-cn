---
title: .NET Standard
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/12/2017
ms.openlocfilehash: 20bbb6386de3fb83df22de5df6e9ee99e3913eaa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="net-standard"></a>.NET Standard

## <a name="using-net-standard-library-projects-to-share-code"></a>使用.NET 标准的类库项目来共享代码

.NET 标准库是一套正式的 .NET API 规范，有望在所有 .NET 运行时中推出。 推出标准库的动机是在 .NET 生态系统中建立更好的统一性。
[ECMA 335](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md) 持续为 .NET 运行时行为建立统一性，但适用于 .NET 库实现的 .NET 基类库 (BCL) 没有类似的规范。

你可以将它视为的简化的下一代[可移植类库](https://msdn.microsoft.com/library/gg597391.aspx)。
它是所有.NET 平台，包括.NET 核心统一 API 的单个库。 你只需创建单个.NET 标准库，然后从任何支持.NET 标准平台的运行时使用它。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

本部分演练如何创建和使用的 mac。 使用 Visual Studio.NET 标准库 请参阅完整的实现中.NET 标准库示例部分。

### <a name="creating-a-net-standard-library"></a>创建.NET 标准库

将.NET 标准库添加到你的解决方案是一个相当直截了当。

1. 在添加新项目对话框中，选择`.NET Core`类别，然后选择`Class Library(.NET Core)`。

  **注意：**此模板将重命名为`.NET Standard`Visual Studio for mac。 的未来版本中

  ![创建.NET 核心类库](net-standard-images/vsm01.png)

2. 解决方案资源管理器中所示，将显示.NET 标准库的项目。 依赖项节点将指示库使用[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/)。

  ![在解决方案中的依赖关系节点指示.NET 标准](net-standard-images/vsm02.png)

#### <a name="editing-net-standard-library-settings"></a>编辑.NET 标准库设置

可查看并更改通过右键单击项目并选择.NET 标准库设置`Options`此屏幕截图中所示：

![编辑项目选项中的标准.NET 目标框架](net-standard-images/vsm03.png)

你可以在内更改你的版本`netstandard`通过更改`Target Framework`下拉列表中值。

**此外：**可以编辑`.csproj`直接来更改此值。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

此部分将指导完成如何创建和使用使用 Visual Studio.NET 标准库。 请参阅完整的实现中.NET 标准库示例部分。

### <a name="creating-a-net-standard-library"></a>创建.NET 标准库

#### <a name="visual-studio-2017"></a>Visual Studio 2017

将.NET 标准库添加到你的解决方案是一个相当直截了当。

1. 在添加新项目对话框中，选择`.NET Standard`类别，然后选择`Class Library(.NET Standard)`。

  ![](net-standard-images/vs01.png "创建新的标准.NET 类库")

2. 解决方案资源管理器中所示，将显示.NET 标准库的项目。 依赖项节点将指示库使用[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/)。

  ![](net-standard-images/vs02.png "在解决方案中的标准.NET 项目")

#### <a name="editing-net-standard-library-settings"></a>编辑.NET 标准库设置

可查看并更改通过右键单击项目并选择.NET 标准库设置`Properties`此屏幕截图中所示：

![](net-standard-images/vs03.png "引用与其他项目相同的方式的标准.NET 库")

你可以在内更改你的版本`netstandard`通过更改`Target Framework`下拉列表中值。

**此外：**可以编辑`.csproj`直接来更改此值。

#### <a name="using-net-standard-library"></a>使用.NET 标准库

一旦创建.NET 标准库后，你可以添加任何兼容的应用程序或库项目中对它的引用与通常添加引用的方式相同。 在 Visual Studio 中，右键单击引用节点，然后选择`Add Reference...`然后切换到`Solution : Projects`选项卡如下所示：

![](net-standard-images/vs04.png "在 Visual Studio 中，右键单击引用节点并选择添加引用...然后切换到解决方案项目选项卡如下所示")

-----

