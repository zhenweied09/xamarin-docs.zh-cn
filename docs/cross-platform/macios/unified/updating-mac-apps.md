---
title: 更新现有的 Mac 应用程序
description: 本文档描述必须遵守才能更新 Xamarin.Mac 应用程序从经典 API 到统一 API 的步骤。
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 5e6034b079bba5e884872e4f2096d677fd3641d0
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782406"
---
# <a name="updating-existing-mac-apps"></a>更新现有的 Mac 应用程序

更新现有应用程序使用统一的 API 需要更改项目文件本身以及命名空间和应用程序代码中使用的 Api。

## <a name="the-road-to-64-bits"></a>为 64 位道路

支持 64 位设备从 Xamarin.Mac 应用程序的体系结构需要新的统一的 Api。 截至 2015 年 2 月 1 日，Apple 要求所有的新应用程序提交时 Mac 应用商店支持 64 位体系结构。

Xamarin 提供了为适用于 Mac 的 Visual Studio 和 Visual Studio 自动执行迁移过程从经典 API 到统一 API 的工具，或者可以手动将转换项目文件。 虽然强烈建议使用自动工具，本文将介绍这两种方法。

### <a name="before-you-start"></a>开始之前...

你的现有代码更新为统一 API 之前，强烈建议您消除所有*编译警告*。 许多*警告*在经典 API 将会变得错误后将迁移到统一。 在开始之前修复它们是更轻松，因为从经典 API 编译器消息通常在要更新提供提示。

## <a name="automated-updating"></a>自动更新

后已修复警告，用于 Mac 或 Visual Studio 在 Visual Studio 中选择一个现有的 Mac 项目并选择**迁移到 Xamarin.Mac 统一 API**从**项目**菜单。 例如：

![](updating-mac-apps-images/beta-tool1.png "从项目菜单中选择迁移到 Xamarin.Mac 统一 API")

你将需要同意此警告之前将运行自动的迁移 （显然你应确保在此 adventure 启动之前已备份/源代码管理）：

![](updating-mac-apps-images/migrate01.png "自动的迁移将运行之前同意此警告")

有两种受支持的目标框架类型在 Xamarin.Mac 应用程序中使用统一的 API 时可以选择：

- **Xamarin.Mac Mobile 框架**-这是使用 Xamarin.iOS 和 Xamarin.Android 支持完整的子集的相同优化的.NET framework**桌面**framework。 这是建议的框架，因为它提供较小平均二进制文件，由于上级链接行为。
- **Xamarin.Mac.NET 4.5 Framework** -此框架再次，是的子集**桌面**framework。 但是，它修剪远远少于完全**桌面**比 framework**移动**framework，应使用 _"仅工作"_ 对大多数 NuGet 包或第三方库。 这允许开发人员使用标准**桌面**程序集时仍在使用支持的框架，但此选项产生更大的应用程序捆绑包。 这是第三方.NET 程序集使用情况与不兼容的建议的 framework **Xamarin.Mac Mobile 框架**。 有关受支持的程序集的列表，请参阅我们[程序集](~/cross-platform/internals/available-assemblies.md)文档。

有关目标框架的详细的信息和选择 Xamarin.Mac 应用程序的特定目标的影响，请参阅我们[目标框架](~/mac/platform/target-framework.md)文档。 

该工具基本上自动化中所述的所有步骤**手动更新**部分下面给出，和是将现有 Xamarin.Mac 项目转换为统一 API 的建议的方法。

## <a name="steps-to-update-manually"></a>若要手动更新的步骤

同样后已修复警告，, 请按照以下步骤手动更新 Xamarin.Mac 应用程序以使用新的统一 API 操作：

### <a name="1-update-project-type--build-target"></a>1.更新项目类型和 Build 目标

更改中的项目风格你**csproj**文件从`42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23`到`A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`。 编辑**csproj**在文本编辑器中，替换中的第一个项的文件`<ProjectTypeGuids>`元素如下所示：

![](updating-mac-apps-images/csproj.png "编辑在文本编辑器中，如所示替换 ProjectTypeGuids 元素中的第一项 csproj 文件")

更改**导入**包含的元素`Xamarin.Mac.targets`到`Xamarin.Mac.CSharp.targets`如所示：

![](updating-mac-apps-images/csproj2.png "更改包含到 Xamarin.Mac.CSharp.targets Xamarin.Mac.targets，如所示的导入元素")

添加以下行后的代码`<AssemblyName>`元素：

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

示例:

![](updating-mac-apps-images/csproj3.png "< 程序集名称 > 元素之后添加这些代码行")

### <a name="2-update-project-references"></a>2.更新项目引用

展开 Mac 应用程序项目的**引用**节点。 它最初会显示 * 中断- **XamMac**类似于此屏幕截图 （因为我们刚刚更改了项目类型） 的引用：

![](updating-mac-apps-images/references.png "它将最初显示的中断 XamMac 引用类似于此屏幕截图")

单击**齿轮图标**旁边**XamMac**项，并选择**删除**删除损坏的引用。

接下来，右键单击**引用**文件夹中的**解决方案资源管理器**和选择**编辑引用**。 滚动到的引用列表的底部并勾选除了**Xamarin.Mac**。

![](updating-mac-apps-images/references2.png "滚动到的引用列表的底部并勾选除了 Xamarin.Mac")

按**确定**保存此项目引用的更改。

### <a name="3-remove-monomac-from-namespaces"></a>3.从命名空间中删除 MonoMac

删除**MonoMac**从中的命名空间的前缀`using`语句或任何位置 classname 具有已完全限定 （例如。 `MonoMac.AppKit` 只需将成为`AppKit`)。

### <a name="4-remap-types"></a>4.重新映射类型

[本机类型](~/cross-platform/macios/nativetypes.md)已引入了用于取代了以前使用，如实例的某些类型`System.Drawing.RectangleF`与`CoreGraphics.CGRect`（例如）。 上找不到类型的完整列表[本机类型](~/cross-platform/macios/nativetypes.md)页。

### <a name="5-fix-method-overrides"></a>5.修复方法重写

某些`AppKit`方法已更改为使用新其签名[本机类型](~/cross-platform/macios/nativetypes.md)(如`nint`)。 如果自定义子类重写这些方法的签名将不再匹配，并将导致错误。 通过更改以匹配新的签名使用本机类型的子类来修复这些方法重写。 

## <a name="considerations"></a>注意事项

将现有 Xamarin.Mac 项目从经典 API 转换为新的统一 API，如果该应用程序依赖于一个或多个组件或 NuGet 包时，应考虑以下注意事项。 

### <a name="components"></a>组件数

已包括你的应用程序中的任何组件还需要更新到统一 API 或您尝试编译时，您将看到冲突。 对于任何包括的组件，使用来自支持统一 API Xamarin 组件应用商店中的新版本替换当前版本和执行干净的生成。 尚未转换由作者，任何组件将显示在组件应用商店中的唯一警告一个 32 位。

### <a name="nuget-support"></a>NuGet 支持

我们提供到 NuGet 要使用的统一 API 支持的更改，而没有新版本的 NuGet，因此我们正在评估如何获取 NuGet 能够识别新的 Api。 

到那时，就像组件，你将需要切换有支持统一 Api 的版本到项目中包含任何 NuGet 包之后执行一个干净的生成。

> [!IMPORTANT]
> 如果窗体中有错误 _"错误 3 不能在同一 Xamarin.Mac 项目中包含 monomac.dll 和 Xamarin.Mac.dll-Xamarin.Mac.dll 显式引用，而 monomac.dll 引用的 xxx，版本 = 0.0.000，区域性 =neutral，PublicKeyToken = null'"_ 后转换到统一 Api 应用程序，它通常是因为尚未更新到统一 API 的项目中采用的组件或 NuGet 包。 你将需要删除现有的组件/NuGet，更新到版本支持统一 Api 并执行一个干净的生成。

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Xamarin.Mac 应用启用 64 位版本

对于 Xamarin.Mac 移动应用程序已转换为统一 API，开发人员仍需要启用应用程序的选项为 64 位计算机应用程序的构建。 请参阅**启用 64 位版本的 Xamarin.Mac 应用**的[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)启用 64 位的详细说明的文档生成。
    
## <a name="finishing-up"></a>完成

无论选择使用自动或手动方法将 Xamarin.Mac 应用程序从经典转换为统一 Api，有几个实例将进一步，需要手动干预。 请参阅我们[向统一 API 更新代码的提示](~/cross-platform/macios/unified/updating-tips.md)文档有关的已知问题和解决方法。

## <a name="related-links"></a>相关链接

- [将代码更新为 Unified API 的提示](~/cross-platform/macios/unified/updating-tips.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
