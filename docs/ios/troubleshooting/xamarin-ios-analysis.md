---
title: Xamarin.iOS 分析规则
description: 本文档介绍了一套用于检查 Xamarin.iOS 项目设置，以帮助确定是否可更/better optimized 设置分析规则。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 25d2936f70981ed239626193ba6e4993f1378108
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788893"
---
# <a name="xamarinios-analysis-rules"></a>Xamarin.iOS 分析规则

Xamarin.iOS 分析是一套规则，检查你的项目设置以帮助你确定是否有更高/更多优化的设置。

尽可能在早期查找可能的改进和节省开发时间通常运行分析规则。

若要运行规则，在 Visual Studio 中的 Mac 的菜单，选择**项目 > 运行代码分析**。

> [!NOTE]
> 你当前所选的配置仅运行 Xamarin.iOS 分析。 我们强烈建议运行调试工具**和**发布配置。

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **问题：** 链接器禁用设备上为调试模式。
- **修复：** 应尝试与链接器以避免任何意外事件运行代码。
若要将它设置，请转到项目 > iOS 生成 > 链接器行为。

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **问题：** Apple 提交时，将拒绝初始化测试云代理的应用程序生成，使用他们在私有 API。
- **修复：** 添加或修复需要 #if 并在代码中定义。

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **问题：** 调试配置使用签名密钥的开发人员不应生成 IPA 仅需要为分发，现在使用发布向导。
- **修复：** 禁用 IPA 生成项目选项中的调试配置功能。

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **问题：** 的支持体系结构"释放 |设备"不兼容，缺少 ARM64 的 64 位。 这是一个问题，如 Apple 不接受 32 位唯一 iOS 应用商店中。
- **修复：** Double 单击您的 iOS 项目，请转到生成 > iOS 生成并更改支持的体系结构，因此它具有 ARM64。

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **问题：** 不使用 float32 选项 (-aot 选项 =-O = float32) 导致繁重的性能开销，尤其是在移动设备、 双精度算术是显著降低速度较慢。 请注意，.NET 双精度在内部使用，即使对于 float，因此精度和兼容性，如果可能，请启用此选项会影响。
- **修复：** Double 单击您的 iOS 项目，请转到生成 > iOS 生成并取消选中"执行作为 64 位浮点数的 32 位浮点型的所有操作"。

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **问题：** 我们建议使用本机 HttpClient 处理程序，而不托管一个用于更好的性能，较小的可执行文件大小，从而轻松地支持较新的标准。
- **修复：** Double 单击您的 iOS 项目，请转到生成 > iOS 生成和 HttpClient 实现变为 NSUrlSession (iOS 7 +) 或 CFNetwork 若要支持前面 iOS 7 的版本。
