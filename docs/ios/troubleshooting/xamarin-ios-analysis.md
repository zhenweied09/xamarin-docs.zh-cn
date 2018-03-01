---
title: "Xamarin.iOS 分析规则"
ms.topic: article
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/26/2017
ms.openlocfilehash: 7cf627f369b666bb54d0f512dc1361d2a685a057
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinios-analysis-rules"></a>Xamarin.iOS 分析规则


## <a name="a-namexia0001xia0001-disabledlinkerrule"></a><a name="XIA0001"/>XIA0001: DisabledLinkerRule

- **问题：**链接器禁用设备上为调试模式。
- **修复：**应尝试与链接器以避免任何意外事件运行代码。
若要将它设置，请转到项目 > iOS 生成 > 链接器行为。

## <a name="a-namexia0002xia0002-testcloudagentreleaserule"></a><a name="XIA0002"/>XIA0002: TestCloudAgentReleaseRule

- **问题：** Apple 提交时，将拒绝初始化测试云代理的应用程序生成，使用他们在私有 API。
- **修复：**添加或修复需要 #if 并在代码中定义。

## <a name="a-namexia0003xia0003-ipadebugbuildsrule"></a><a name="XIA0003"/>XIA0003: IPADebugBuildsRule

- **问题：**调试配置使用签名密钥的开发人员不应生成 IPA 仅需要为分发，现在使用发布向导。
- **修复：**禁用 IPA 生成项目选项中的调试配置功能。

## <a name="a-namexia0004xia0004-missing64bitsupportrule"></a><a name="XIA0004"/>XIA0004: Missing64BitSupportRule

- **问题：**的支持体系结构"释放 |设备"不兼容，缺少 ARM64 的 64 位。 这是一个问题，如 Apple 不接受 32 位唯一 iOS 应用商店中。
- **修复：** Double 单击您的 iOS 项目，请转到生成 > iOS 生成并更改支持的体系结构，因此它具有 ARM64。

## <a name="a-namexia0005xia0005-float32rule"></a><a name="XIA0005"/>XIA0005: Float32Rule

- **问题：**不使用 float32 选项 (-aot 选项 =-O = float32) 导致繁重的性能开销，专门上移动，双精度算术是显著降低速度较慢。 请注意，.NET 双精度在内部使用，即使对于 float，因此精度和兼容性，如果可能，请启用此选项会影响。
- **修复：** Double 单击您的 iOS 项目，请转到生成 > iOS 生成并取消选中"执行作为 64 位浮点数的 32 位浮点型的所有操作"。
