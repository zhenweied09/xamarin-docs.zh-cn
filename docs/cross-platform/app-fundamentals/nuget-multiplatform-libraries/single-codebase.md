---
title: "NuGet 为创建新的多平台库"
ms.topic: article
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e95cf18c281732c85c2029e4ff35e8dd8be0f5e2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>NuGet 为创建新的多平台库

创建一个多平台类库项目来使用 PCL 或标准.NET 意味着生成 NuGet 可以添加到任何支持的目标配置文件，包括 ASP.NET 项目或使用 WinForms、 WPF 中或 UWP 的桌面应用的.NET 项目。

库只能包含由所选的 PCL 或标准.NET 配置文件，以及添加的任何其他 NuGets 支持的代码。
这被适用于业务逻辑和可以完全在.NET 基类库中表示的算法。

创建一个程序集并将其内置于 NuGet 包。

如果你稍后需要特定于平台的功能，[可以添加特定于平台的项目](#add-platforms)。

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>若要创建多平台库 NuGet 的步骤

1. 选择**文件 > 新解决方案**(或右键单击现有的解决方案，然后选择**添加 > 新建项目**)。

2. 选择**多平台库**从**多平台 > 库**部分：

  [ ![](single-codebase-images/mulitplatform-library-sml.png "配置的单个基本代码的多平台库")](single-codebase-images/mulitplatform-library.png)

3. 输入**名称**和**说明**，然后选择**单个对于所有平台**:

  [ ![](single-codebase-images/single-configure-sml.png "配置的单个基本代码的多平台库")](single-codebase-images/single-configure.png)

4. 完成向导。 在解决方案中创建单个库项目。

5. 右键单击新的库项目，然后选择**选项**。 **生成 > 常规**部分允许**目标框架**若要设置 – 选择.NET 可移植 PCL 配置文件或标准.NET 版本：

  [ ![](single-codebase-images/single-choose-type-sml.png "为库的类型选择 PCL 或标准.NET")](single-codebase-images/single-choose-type.png)

6. 另外，请在**项目选项**窗口中，打开**NuGet 包 > 元数据**部分，然后输入[必需的元数据](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（以及任何可选的元数据）：

  [ ![](single-codebase-images/single-metadata-sml.png "输入必需的元数据")](single-codebase-images/single-metadata.png)

7. 右键单击库项目并选择**创建 NuGet 包**（或生成或部署解决方案） 和**.nupkg** NuGet 包文件将保存在**/bin/**文件夹 （调试或发布，具体取决于配置）：

  ![](single-codebase-images/create-nuget-package.png "NuGet 包文件将保存在 bin 文件夹中调试或发布，具体取决于配置")


## <a name="verifying-the-output"></a>验证输出

NuGet 包还为 ZIP 文件，因此可以检查生成的包的内部结构。

此屏幕截图显示了仅单个 PCL 程序集将包含基于 PCL NuGet – 的内容：

![](single-codebase-images/nuget-output.png "NuGet 包中包含文件")

<a name="add-platforms" />

# <a name="adding-platform-specific-code"></a>添加特定于平台的代码

基于 PCL 项目和基于.NET 标准的项目不能包含特定于平台的引用 （如 iOS 或 Android 功能）。

如果现有的 PCL 项目或标准.NET 项目需要进行扩展以包含特定于平台的代码，这可以通过右键单击项目并选择**添加 > 添加平台实现...**:

[ ![](single-codebase-images/add-later-sml.png "添加平台实现菜单")](single-codebase-images/add-later.png)

一个或多个平台项目可以添加到解决方案，并且现有的 PCL 或.NET 标准库 （可选） 可以转换为共享的项目：

[ ![](single-codebase-images/add-later-platforms-sml.png "添加平台选项，例如 iOS、 Android 和共享项目")](single-codebase-images/add-later-platforms-sml.png)

在将转换为共享的项目，请访问**项目选项 > NuGet 包 > 引用程序集**
[部分](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md)并确保所有必需的配置文件选择 (以便NuGet 会继续与项目中以前使用兼容）。


## <a name="related-links"></a>相关链接

- [元数据指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
