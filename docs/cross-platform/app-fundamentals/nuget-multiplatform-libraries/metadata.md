---
title: "编辑 NuGet 元数据"
description: "使用项目选项编辑的多平台的库的 NuGet 元数据"
ms.topic: article
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: babbe0344130dc0ce38023eabe7479d2b464276b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="editing-nuget-metadata"></a>编辑 NuGet 元数据

_使用项目选项编辑的多平台的库的 NuGet 元数据_

库项目类型 （如 PCL 或.NET 标准版，或新的 NuGet 项目类型） 具有**NuGet 包**主题中**项目选项**窗口。

**元数据**部分配置中使用的值[ **.nuspec** NuGet 包清单文件](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file)。

## <a name="required-information"></a>所需的信息

**常规**选项卡上包含四个字段，必须输入生成 NuGet 包：

[![](metadata-images/metadata-general-sml.png "NuGet 包所需元数据窗口")](metadata-images/metadata-general.png#lightbox)

- **ID** – Nuget.org （或将分发包的任何位置） 中应该是唯一的包标识符。 遵循此[指南](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)并且仅使用在 URL 中有效的字符 (没有空格，并避免大部分特殊字符)。
- **版本**– 选择版本号与一致[NuGet 的版本控制规则](https://docs.microsoft.com/en-us/nuget/create-packages/dependency-versions)。
- **作者**– 以逗号分隔列表的名称。
- **说明**– 当用户选择包时显示该程序包的功能的概述。

> [!NOTE]
> 请记住生成分发给 NuGet 或其他用户的新版本时递增版本号。

有关详细信息，请参阅[所需元素引用](https://docs.microsoft.com/en-us/nuget/schema/nuspec#required-metadata-elements)有关详细信息，以及为这些上详细说明[选择唯一的包标识符并设置的版本号](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)和[设置包类型](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package#setting-a-package-type)。

> [!IMPORTANT]
> 必须输入此选项卡上的所有字段;否则，将会出现一条错误消息： _"项目没有 NuGet 元数据以便 NuGet 包将不会创建。可以在项目选项中的元数据部分中指定 NuGet 包元数据"_

## <a name="optional-metadata"></a>可选元数据

**详细信息**选项卡包含可选字段，要包括在 NuGet 包清单文件中。

[![](metadata-images/metadata-detail-sml.png "NuGet 包的可选元数据窗口")](metadata-images/metadata-detail.png#lightbox)

请参阅[可选元素引用](https://docs.microsoft.com/en-us/nuget/schema/nuspec#optional-metadata-elements)有关必需和可选字段的详细信息。

> [!NOTE]
> 如果 NuGet 程序包分发[NuGet.org](https://www.nuget.org)建议提供尽可能多的信息。


## <a name="related-links"></a>相关链接

- [.nuspec 引用](https://docs.microsoft.com/en-us/nuget/schema/nuspec#general-form-and-schema)
