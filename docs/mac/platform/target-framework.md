---
title: "目标 Framework"
description: "本文介绍如何为 Xamarin.Mac，可用的目标框架 （基类库） 以及 Xamarin.Mac 项目中使用它们的影响。"
ms.topic: article
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: f657fc3dd87d5c39d442a863e4acc00ac320b00d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="target-framework"></a>目标 Framework

_本文介绍如何为 Xamarin.Mac，可用的目标框架 （基类库） 以及 Xamarin.Mac 项目中使用它们的影响。_

![Target Xamarin.Mac 的 framework 选项](target-framework-images/select-target.png "Target Xamarin.Mac 的 framework 选项")

## <a name="background"></a>背景

每个.NET 程序或库依赖于提供由基类库 (BCL) 的功能。 此 BCL 包括提供内置于所有.NET 语言的常见功能如 mscorlib、 系统、 System.Net.Http，和 System.Xml 程序集。

多年来，又那里已开发此 BCL，针对不同用例进行了优化的多个不同版本。 "桌面"BCL 包括一组更丰富的库，它可能太 heavyweight 对于其他使用情况，虽然移动着重介绍确保 Api 安全的链接，该对话框可移除未使用的代码，以减少应用程序占用空间。

这些不同的目标框架的更重要的负面影响之一是，在给定程序的程序集的所有*必须*目标兼容 BCL 程序集。 如果这不是这种情况，你可以针对不同版本的链接的两个程序集**System.dll** disagreeing 有关给定类型的签名。 共享的库可以任一目标[.NET 标准 2](https://blog.xamarin.com/share-code-net-standard-2-0/)，这是常见的目标框架或特定的目标框架子集。

有三个目标框架选项可用于 Xamarin.Mac，每个都有不同的优点和缺点：

- **现代**（在较旧的文档中称为移动 –） 非常相似哪些乘方 Xamarin.iOS，高度优化，能够对性能和大小的子集。 此目标框架是链接器安全的因此这些项目可以通过删除未使用的代码极大地减少其最终占用空间。

- **完整**（在较旧的文档中称为 XM 4.5 –） 到"桌面"BCL，与几个小删除非常相似的子集。 由于目标框架是几乎完全相同，net45 到 （和更高版本），它可以轻松地使用许多 nugets 不提供任一 netstandard2 或特定 Xamarin.Mac 生成。 但是，由于 System.Configuration 用法是与链接不兼容。

- **不支持**（称为系统较旧的文档中） – 改为将链接到提供的 Xamarin.Mac BCL，使用当前安装的系统 mono。 这提供了最大的一组程序集，包括一些已知会出现问题 (例如 System.Drawing)。 此选项仅存在具有"最后一招"并且强烈建议在使用它之前耗尽其他选项。 顾名思义，使用不受正式支持通道。

## <a name="setting-the-target-framework"></a>设置目标框架

若要更改到 Xamarin.Mac 项目的目标框架类型，请执行以下操作：

1. 在 Visual Studio for Mac 中打开 Xamarin.Mac 项目。
2. 在“解决方案资源管理器”中，双击项目文件打开“项目选项”对话框。
3. 从**常规**选项卡上，选择的一种**目标框架**适合应用程序的需求：

  [![使用项目选项窗口选择的目标框架](target-framework-images/select-target-full.png "使用项目选项窗口选择的目标框架")](target-framework-images/select-target-full-large.png#lightbox)

4. 单击“确定”按钮保存更改。

你应**清理**然后**重新生成**Xamarin.Mac 项目后切换目标 Framework 类型。

## <a name="summary"></a>摘要

本文简要介绍了不同类型的目标框架 （基类库），提供给 Xamarin.Mac 应用程序，并且应使用每种类型的框架。


## <a name="related-links"></a>相关链接

- [iOS 和 Mac 代码共享](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [可移植类库](~/cross-platform/app-fundamentals/pcl.md)
- [程序集](~/cross-platform/internals/available-assemblies.md)
- [更新现有的 Mac 应用程序](~/cross-platform/macios/unified/updating-mac-apps.md)
