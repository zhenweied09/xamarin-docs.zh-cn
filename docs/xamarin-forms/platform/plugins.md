---
title: 使用和创建 Xamarin.Forms 插件
description: 此文章介绍了如何使用和创建 Xamarin.Forms 插件。 插件通常用于轻松地公开本机平台功能。
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/05/2018
ms.openlocfilehash: 4d121c2dfcca380e1735da1a4ca47c42d1957b8a
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854735"
---
# <a name="consuming-and-creating-xamarinforms-plugins"></a>使用和创建 Xamarin.Forms 插件

在所有平台之间存在的许多本机平台功能，但具有略有不同的 Api。 开发人员能够使用这些功能的一种方法是创建一个抽象的跨平台接口，然后在各种平台中实现该接口。 然后，Xamarin.Forms 应用程序访问使用这些平台的实现[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

开发人员可以通过编写共享此工作_插件_并将其发布到 NuGet。

> [!NOTE]
> 许多以前只能通过插件提供的跨平台功能现在是开放源代码的一部分**[Xamarin.Essentials](~/essentials/index.md)** 库。 这些功能包括： 电池状态、 指南针、 运动传感器、 地理位置、 文本到语音转换，和更多。 在将来**Xamarin.Essentials**将 Xamarin.Forms 应用程序的跨平台功能的主要来源。 尽管开发人员仍然可以创建和发布插件，请考虑促成**Xamarin.Essentials**。

## <a name="finding-and-adding-plugins"></a>查找和添加插件

Xamarin 社区已创建许多跨平台插件与 Xamarin.Forms 兼容。 大型集合可以以下位置找到：

[**Xamarin 插件**](https://github.com/xamarin/XamarinComponents)

向项目添加 NuGet 包的指南，请参阅我们的演练上[在项目中包括 NuGet 包](/visualstudio/mac/nuget-walkthrough/)。

## <a name="creating-plugins"></a>创建插件

还有可能要创建并将你自己的插件发布为 Nuget 包 （和 Xamarin 组件）。 许多现有插件是开放源代码，因此你可以查看自己的代码以了解如何它们已进行 writtern。

例如，下面的插件列表是所有的开放源代码，并且它们对应于中的一些示例[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)部分：

- **文本到语音转换**James montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/TextToSpeechPlugin)和[NuGet  ](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech)
- **电池状态**James montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/BatteryPlugin)和[NuGet](https://www.nuget.org/packages/Xam.Plugin.Battery)

这些 Github 项目可以提供很好的起点来创建你自己的跨平台插件，如有关这些说明操作[为 Xamarin 创建插件](https://github.com/xamarin/XamarinComponents#create-a-plugin-for-xamarin)。

### <a name="structuring-cross-platform-plugin-projects"></a>构建跨平台插件项目

虽然有用于设计的 NuGet 包没有特定要求，但有一些指导原则，用于创建跨平台应用的包。

在过去，跨平台插件通常包括以下组件：

- 一个表示该插件的 API 接口使用 PCL
- iOS、 Android 和通用 Windows 平台 (UWP) 的类与接口的实现的库。

读取 James Montemagno[博客文章](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/)描述为 Xamarin 创建插件的过程。

最近，插件可以将创建具有单一的多重目标平台。 James Montemagno 的讨论了这种方法[博客文章](https://montemagno.com/converting-xamarin-libraries-to-sdk-style-multi-targeted-projects/)。 James Montemagno 的插件，上述链接中使用此方法和格式中也使用**Xamarin.Essentials**。

最好避免引用 Xamarin.Forms 直接从插件是。
当其他开发人员尝试使用该插件时，这可以创建版本冲突问题。 改为尝试设计 API，使其可由任何 Xamarin 或.NET 应用程序。

### <a name="publishing-nuget-packages"></a>发布 NuGet 包

NuGet 包已**nuspec**文件，它是定义在包中发布你的项目中的哪些部分的 xml 文件。 **Nuspec**文件还包含有关程序包，例如 id、 标题和作者。

请参阅[NuGet 的文档](/nuget/create-packages/creating-a-package.md)有关创建和发布 NuGet 包的详细信息。

## <a name="related-links"></a>相关链接

- [为 Xamarin.Forms 创建可重用插件](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [用于 Xamarin （视频） 的使用和开发插件](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
