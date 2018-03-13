---
title: "从现有的库项目创建 NuGet"
ms.topic: article
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: ef34b1be5f993a3be819cd4ae1a9dcd5dfc27715
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>从现有的库项目创建 NuGet

现有 PCL 或标准.NET 库可以转换为 NuGets 通过**项目选项**窗口：

1. 右键单击库项目中**解决方案 Pad**选择**选项**。

2. 转到**NuGet 包 > 元数据**部分，然后输入所有[所需的信息](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)中**常规**选项卡：

  [![](existing-library-images/existing-metadata-sml.png "输入必需的元数据")](existing-library-images/existing-metadata.png#lightbox)

3. （可选）[添加其他元数据](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)中**详细信息**选项卡。

4. 元数据配置后，你可以右键单击该项目并选择**创建 NuGet 包**和**.nupkg** NuGet 包文件将保存在**/bin/**文件夹 （调试或发布，取决于配置）。

  ![](existing-library-images/create-nuget-package.png "从右键单击菜单中选择创建 NuGet 包")

5. 若要创建 NuGet 包在_每个_生成或部署，请转到**NuGet 包 > 生成**部分和刻度线**生成项目时创建 NuGet 包**:

    [![](existing-library-images/existing-tickbox-sml.png "刻度线创建 NuGet 包")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> 生成 NuGet 包会显著降低生成过程中。 如果未勾选此框，您仍可以生成 NuGet 包手动随时在项目上下文菜单中 （如上面的步骤 4 所示）。

## <a name="verifying-the-output"></a>验证输出

NuGet 包还为 ZIP 文件，因此可以检查生成的包的内部结构。

此屏幕截图显示了仅单个 PCL 程序集将包含基于 PCL NuGet – 的内容：

![](existing-library-images/nuget-output.png "NuGet 包中包含文件")


## <a name="related-links"></a>相关链接

- [元数据指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
