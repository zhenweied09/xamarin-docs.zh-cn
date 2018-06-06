---
title: 32/64 位平台注意事项
description: 本文档介绍当目标为 Xamarin.iOS 或 Xamarin.Mac 应用程序的 32 位和 64 位体系结构时需要牢记的各种注意事项。
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: c722efc0bc6e8a4ea29af603f88c0e0644c2ed8c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781544"
---
# <a name="3264-bit-platform-considerations"></a>32/64 位平台注意事项

虽然从历史上看，iOS 和 macOS 具有支持 32 和 64 位应用程序，Apple 逐渐已否决 32 位支持。

从 iOS 11，开始将不再启动 32 位应用，和[所有提交到应用商店必须都支持 64 位](https://developer.apple.com/news/?id=06282017b)。

从开始年 1 月 2018年[提交到 Mac 应用商店的新应用程序必须支持 64 位](https://developer.apple.com/news/?id=06282017a)，并且必须按年 6 月 2018年更新现有应用。

Xamarin 的经典 API (`XamMac.dll`和`monotouch.dll`) 支持仅 32 位应用程序。 但是，新的 Xamarin.iOS 和 Xamarin.Mac 应用程序使用[统一 API](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS`和`Xamarin.Mac`) 默认情况下，并因此可以目标 32 和 64 位，如有必要。

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>启用 64 位版本的 Xamarin.iOS 应用程序

> [!WARNING]
> 本部分是包含出于历史原因，并帮助将较旧的 Xamarin.iOS 项目移动到统一 API 和支持 64 位。 所有新的 Xamarin.iOS 项目将默认使用的统一 API 和面向 64 位。

对于 Xamarin.iOS 移动应用程序已转换为统一 API，开发人员必须手动更新到面向 64 位的生成设置：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**解决方案 Pad**，双击应用程序的项目以打开**项目选项**窗口。
2. 选择**iOS 生成**。
3. 适用于 iPhone 模拟器中，在**支持的体系结构**下拉列表中，选择**x86\_64**或**i386 + x86\_64**:

   [![将支持体系结构设置为 x86\_64 或 i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. 对于物理设备，选择的某个可用**ARM64**组合：

   [![将支持体系结构设置为 ARM64 组合之一](Images/Image02.png "ARM64 组合之一设置支持体系结构")](Images/Image02-large.png#lightbox)

5. 单击 **“确定”**。
6. 执行干净的生成。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**解决方案资源管理器**，右键单击应用程序的项目，然后选择**属性**。
2. 选择**iOS 生成**。
3. 对于 iPhone 模拟器中，设置**支持的体系结构**为**x86\_64**或**i386 + x86\_64**: 

   [![将支持体系结构设置为 x86_64 或 i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. 对于物理设备，选择的某个可用**ARM64**组合：
    
   [![将支持体系结构设置为 ARM64 组合之一](Images/VS01.png "ARM64 组合之一设置支持体系结构")](Images/VS01-large.png#lightbox)

5. 保存更改。
6. 执行干净的生成。

-----

ARMv7s 仅受 A6 处理器纳入 iPhone 5 （或更高版本）。 ARMv7 代码更快且小于 ARMv6、 仅可与 iPhone 3GS 及更高版本，和时，需要的 apple iPad 或 5.0 的最低 iOS 版本作为目标。 ARMv6 所有的设备上运行，但不再支持由编译器发运 Xcode 4.5 和更高版本。 

ARM64 支持 iOS 8 iPhone 6 或其他 64 位设备上所需和提交新的或更新退出 iTunes 应用商店中的应用程序时，Apple 将需要。

了解各种 iOS 设备的功能的全面信息，请查看 Apple 的[设备兼容性](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html)文档。

### <a name="64-bit-and-binary-size-increases"></a>64 位和二进制大小增加

在从 32 位到 64 位 iOS Apple 的转换期间应用将需要在 32 位和 64 位硬件上运行。 因此，Xamarin 的统一 API 允许开发人员可以面向两者。

面向 32 位和 64 位体系结构会显著增加应用程序的大小。 但是，因此这样做将允许较新的设备，同时仍支持较旧的设备运行优化的代码。

> [!IMPORTANT]
> 如果提交到 iTunes 应用商店，iOS 应用程序时收到以下消息 _"警告 ITMS 9000： 缺少 64 位支持。开始于 2015 年 2 月 1 日，新的 iOS 应用上载到应用商店必须包括 64 位支持，并且可以生成使用 iOS 8 SDK，包含在 Xcode 6 或更高版本。若要启用 64 位在项目中，我们建议使用 Xcode 生成设置的"标准体系结构"的默认值生成一个单一的二进制文件，使用 32 位和 64 位代码。"_ 你需要切换到的某个可用的支持的体系结构**ARM64**组合 （如上所示）、 重新编译和重新提交。

## <a name="mac"></a>Mac

> [!IMPORTANT]
> 从年 1 月 2018年开始，到 Mac 应用商店提交的所有新 Mac 应用程序必须支持 64 位。 现有的 Mac 应用商店应用和其更新必须支持 64 位启动年 6 月 2018年中。 请参阅[Apple 的发布](https://developer.apple.com/news/?id=06282017a)和[描述如何更新 Xamarin.Mac 应用到 64 位的指南](~/cross-platform/macios/32-and-64/mac-64-bit.md)。

大多数现代 Mac 计算机支持 32 位和 64 位应用程序。   MacOS 10.6 (Snow Leopard) 时要在 32 位系统上运行的最后一个操作系统。   大多数 Mac 发布以来 2010年支持这两个系统。

与 iOS，不同的最新版本的 macOS 中引入的新框架许多仅支持在 64 位模式下 （CloudKit、 EventKit、 GameController、 LocalAuthentication、 MediaLibrary、 MultipeerConnectivity、 NotificationCenter、 GLKit、 SpriteKit、 社会、和 MapKit，等等）。

统一 API 允许开发人员能够选择他们想要生成哪种类型的应用程序： 32 位或 64 位。

**32 位应用程序**将会在 32 位和 64 位 Mac 计算机上运行、 的地址空间限制为 32 位，并要求所有库都是 32 位。

如果您有不在 64 位模式下运行的 32 位依赖关系，如果你想要的较小的下载，或如果在将迁移到 64 位不有任何性能优势，你通常将使用此模式。

此模式下限制您将不能够使用在 macOS Mavericks 和 macOS Yosemite 中可用的很多框架。

**64 位应用程序**将仅在 64 位 Mac 设备上运行。

适用于 Mac，这是操作的首选的模式，因为正在使用的大多数 Mac 目前支持 64 位模式，并有权访问框架 Apple 提供的完整集合。

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Xamarin.Mac 应用启用 64 位版本

有关生成使用 Xamarin.Mac 的 64 位应用程序的信息，请参阅[更新 Xamarin.Mac 统一应用程序迁移到 64 位](~/cross-platform/macios/32-and-64/mac-64-bit.md)指南。

## <a name="related-links"></a>相关链接

- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
