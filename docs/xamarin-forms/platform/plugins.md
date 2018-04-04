---
title: 插件
description: 轻松地将本机功能添加到 Xamarin.Forms 应用
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2016
ms.openlocfilehash: 5770d13c46998872752820b7a0cbb222a04c3ff8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="plugins"></a>插件

在所有平台之间存在的许多本机平台功能，但具有略有不同的 Api。 开发人员编写插件来创建一个抽象的跨平台界面，以便它们还可以与他人共享这些功能。

这些功能包括： 电池状态、 指南针、 运动传感器、 地理位置、 文本到语音转换，和更多操作。 插件允许 Xamarin.Forms 应用程序轻松地访问这些功能。

## <a name="finding-and-adding-plugins"></a>查找和添加插件

Xamarin 社区创建许多跨平台插件与 Xamarin.Forms-兼容的大型集合可以位于以下位置：

[**Xamarin 插件**](https://github.com/xamarin/plugins)

将 NuGet 包添加到你的项目的指南，请参阅我们的演练上[包括你的项目中的 NuGet 包](/visualstudio/mac/nuget-walkthrough/)。


## <a name="creating-plugins"></a>创建的插件

还有可能创建和发布为 Nuget 包 （和 Xamarin 组件） 的你自己的插件。 许多现有插件都是开放源代码，因此你可以查看其代码，以了解如何已 writtern。

例如，下面的插件列表是所有的开放源代码，并且它们对应中的示例[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)部分：

- **文本到语音转换**通过 James Montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/TextToSpeech)和[NuGet](https://www.nuget.org/packages/Xam.Plugin.Battery)
- **电池状态**通过 James Montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery)和[NuGet](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech/)

这些 Github 项目可以提供很好的起点用于创建你自己跨平台的插件，这些指令一样[为 Xamarin 创建插件](https://github.com/xamarin/plugins#create-a-plugin-for-xamarin)。

### <a name="structuring-cross-platform-plugin-projects"></a>构建跨平台插件项目

尽管没有设计 NuGet 包的特定要求，有一些有关创建包为跨平台应用的一些准则。

跨平台插件通常应包括以下组件：

- 表示用于插件的 API 的接口的 PCL
- iOS、 Android 和 Windows 类与接口的实现的库。

读取 James Montemagno 的[博客文章](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/)描述为 Xamarin 创建插件的过程。

最好避免直接从插件中引用 Xamarin.Forms。
当其他开发人员尝试使用插件时，这可以创建版本冲突问题。 改为尝试设计的 API，以便它可以由任何 Xamarin 或.NET 应用程序。

### <a name="publishing-nuget-packages"></a>发布的 NuGet 包

NuGet 包具有**nuspec**文件，这是定义在包中发布你的项目的哪些部分的 xml 文件。 **Nuspec**文件还包括有关包，如 id、 标题和作者的信息。

请参阅[NuGet 的文档](http://docs.nuget.org/create/creating-and-publishing-a-package)有关创建和发布 NuGet 包的详细信息。


## <a name="related-links"></a>相关链接

- [为 Xamarin.Forms 创建可重用的插件](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [Xamarin （视频） 的的使用和开发插件](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
