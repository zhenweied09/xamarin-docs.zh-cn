---
title: 常规 Frequently Asked Questions
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 579393a8830e73f7ab64f750c0176eb4c4e36029
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="general-frequently-asked-questions"></a>常规 Frequently Asked Questions

## <a name="portable-class-libraries"></a>可移植类库
### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[如何查看 PCL 中支持的库？](pcl-support-libraries.md)
本指南列出资源和用于确定如果你现有的库支持的各种 PCL 目标平台，或者可以转换为 PCL 配置文件的方法。

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[PCL 反射 API](pcl-reflection.md)
Microsoft 开发了一个新的反射 API 在可移植类库中使用。 如果你有想要将移动到 PCL 某些现有反射代码，可能无法工作。

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[PCL 案例研究：如何解决与 Microsoft TPL 数据流 NuGet 包的 System.Diagnostics.Tracing 相关的问题？](pcl-case-study.md)
Xamarin.iOS 和 Xamarin.Android 未实现 100%的作为引用它们允许每个 PCL 配置文件。 为 Visual Studio 中用于 Mac、 Visual Studio 和 NuGet 包管理器的实际方便起见，Xamarin 项目允许使用仅具有不完整的实现的多个配置文件。 例如，Xamarin.iOS 和 Xamarin.Android 都不当前包括中的类型的完整实现`System.Diagnostics.Tracing`PCL 命名空间。 可通过切换应用程序项目，以引用可移植 net45 + win8 + wp8 + wpa81 解决此 TPL 数据流库版本。

## <a name="nuget-packages--xamarin-components"></a>NuGet 包 （&) 的 Xamarin 组件
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[如何更新 NuGet？](nuget-update.md)
下可以找到 NuGet 更新、 扩展和外接程序**更新**选项卡中**NuGet 包管理器**。 若要查找的更新在 Visual Studio 中针对 Mac 和 Visual Studio 的详细的导航，则本指南中。

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[如何降级 NuGet 包？](nuget-package-downgrade.md)
适用于 Mac 的 visual Studio 和 Visual Studio 这两个具有用于选择较旧版本的包以及自动; 安装它们的功能类似于如何更新包工作原理。

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[在更新 Nuget 包后出现缺失包错误](nuget-packages-missing.md)
此问题主要已报告在 Xamarin.Forms 示例应用程序解决方案，但使用 NuGet 包的任何项目可能发生此问题的可能性。

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[统一 Google Play Services 组件和 NuGet](gps-components-nuget.md)
存在使用为多个 Google Play Services 组件和 NuGet 包，但为了简化操作的开发人员，我们已现在统一我们的组件和 NuGet 包复制到以下两个。 在几乎所有情况下，应使用 Google Play 服务。 若要使用 (Froyo) 包的唯一原因是如果你主动面向 Froyo。

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[组件保存在计算机的什么位置？](component-storage.md)
每当 Xamarin 组件安装到应用程序项目中时，获取将其放入本指南中列出的两个位置。


## <a name="troubleshooting"></a>疑难解答
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[在哪里可以找到我的版本信息和日志？](version-logs.md)
本指南详细介绍在何处可以找到大多数可以用于排除 Xamarin 问题的诊断信息。

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[应何时以及如何提交 bug 报告？](howto-file-bug.md)
本指南提供了技巧填写高质量 bug 报告，以便我们工程师能够问题更有效地确定可能的原因 （和任何潜在修复程序）。

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[为什么 Xamarin 不支持 Jenkins？](xamarin-jenkins.md)
Jenkins 是开放源代码 CI 套件;由于这许多问题而直接引起 Jenkins*本身*需要被归为问题对你的代码; 例如[主要 Jenkins 存储库](https://github.com/jenkinsci/jenkins)，或的存储库[Jenkins.app](https://github.com/stisti/jenkins-app)。

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[调试器需要哪些项目设置？](debugger-settings.md)
为了使调试器以达到预期效果 （命中的断点、 显示调试日志等），开发人员检测和调试信息显示必须同时启用。 本指南详细介绍如何查找和激活这些设置。

