---
title: 本机引用
description: 本机引用使你能够将本机框架嵌入到 Xamarin.iOS 或 Xamarin.Mac 项目或绑定项目。
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: c56e392420debb21998363cfffa288aec51691ea
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="native-references"></a>本机引用

_本机引用使你能够将本机框架嵌入到 Xamarin.iOS 或 Xamarin.Mac 项目或绑定项目。_


自 iOS 8.0 就已可以创建应用程序扩展和在 Xcode 中的主应用程序之间共享代码的嵌入式的框架。 使用本机引用功能则可能要在 Xamarin.iOS 中使用这些 （使用 Xcode 创建） 的嵌入式的框架。
 
> [!IMPORTANT]
> 它将不能从任何类型的 Xamarin.iOS 或 Xamarin.Mac 项目创建嵌入的框架，本机引用只允许使用现有的本机 (OBJECTIVE-C) 框架的消耗。




<a name="Terminology" />

## <a name="terminology"></a>术语

在 iOS 8 （和更高版本），**嵌入框架**可以同时嵌入静态链接和动态链接框架。 若要正确分发它们，你必须将其设置到"fat"框架，包括了所有其_切片_想要支持它与你的应用每个设备体系结构。

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>静态 vs。动态框架

**静态框架**链接在编译时其中**动态框架**链接在运行时，因此可以修改而无需重新链接。 如果你使用 iOS 8 之前的任何第三方框架，已使用**静态 Framework** ，已编译到应用。 请参阅 Apple 的[动态库编程](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1)更多详细信息的文档。

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>嵌入的 vs。系统框架

**嵌入框架**都包含在应用捆绑包，才可向其沙盒通过特定应用程序访问。 **系统框架**可用的设备上的所有应用和存储在操作系统级别。 目前，仅 Apple 还能够创建操作系统级别框架。

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>精简 vs。Fat 框架

**精简框架**包含仅适用于特定系统体系结构编译的代码其中**Fat 框架**包含多个体系结构的代码。 编译成一个框架，每个特定于体系结构的基本代码被称为_切片_。 因此，例如，如果我们必须为两个 iOS 模拟器体系结构 （i386 和 X86_64） 编译的一个框架，它将包含两个切片。

如果你尝试将分发此示例 Framework 与您的应用程序，它将在模拟器中上, 正确运行，但在设备上失败，因为框架不包含任何特定于代码的切片为 iOS 设备。 若要确保在所有情况下，框架将正常工作，它还需要包括如 arm64、 armv7 和 armv7s 的特定于设备的切片。

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>使用嵌入的框架

必须完成可嵌入框架在 Xamarin.iOS 或 Xamarin.Mac 应用的两个步骤： 创建一个 Fat 框架和嵌入框架。

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>创建一个 Fat 框架

如上面所述，若要能够使用应用程序中嵌入 Framework 它必须包括所有的设备将在上运行你的应用程序的系统体系结构切片 Fat Framework。

当框架和使用方应用程序相同的 Xcode 项目中时，这不是问题作为 Xcode 将生成的框架并使用相同的生成设置的应用。 由于 Xamarin 应用程序无法创建嵌入的框架，将无法使用此方法。

若要解决此问题，`lipo`命令行工具可以用于将两个或多个框架合并到一个 Fat 框架中包含所有必要的切片。 有关详细信息使用`lipo`命令，请参阅我们[链接本机库](~/ios/platform/native-interop.md)文档。

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>嵌入一个框架

按照步骤需要使用本机引用 Xamarin.iOS 或 Xamarin.Mac 项目中嵌入一个框架：

1. 新建报表或打开现有 Xamarin.iOS、 Xamarin.Mac 或绑定项目。
2. 在**解决方案资源管理器**，右键单击项目名称并选择**添加** > **添加本机引用**: 

    [![](native-references-images/ref01.png "在解决方案资源管理器，右键单击项目名称，然后选择添加本机引用")](native-references-images/ref01.png#lightbox)
3. 从**打开**对话框框中，选择你想要嵌入，然后单击本机框架名称**打开**按钮： 

    [![](native-references-images/ref02.png "选择本机框架，嵌入并单击打开按钮的名称")](native-references-images/ref02.png#lightbox)
4. 框架将添加到项目的树： 

    [![](native-references-images/ref03.png "框架将添加到项目树")](native-references-images/ref03.png#lightbox)

编译项目后，将应用的捆绑中嵌入本机框架。

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>应用程序扩展和嵌入的框架

内部 Xamarin.iOS 可能充分利用此功能将其用作框架单声道的运行时与链接 (当部署目标是 > = iOS 8.0)，从而减少显著为具有扩展的应用的应用程序大小 （因为单声道运行时一次将包含有关整个应用程序捆绑包，而不是一次针对容器应用程序，并一次针对每个扩展）。

扩展将与单声道运行时作为链接一个框架，因为所有扩展都需要 iOS 8.0。

没有扩展和应用该面向 iOS 的应用 

<a name="Summary" />

## <a name="summary"></a>总结

本文已了解嵌入到一个 Xamarin.iOS 或 Xamarin.Mac 应用程序的本机框架的详细的信息。

