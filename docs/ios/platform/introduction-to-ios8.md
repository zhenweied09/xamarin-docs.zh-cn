---
title: IOS 8 简介
description: IOS 8，Apple 提供了大量新的框架和 Api 让开发人员高兴地对。 本指南中我们将介绍这些新的 Api，并请参阅 iOS 8 开发人员和用户如何使受益。
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 2f57547356adcbafd01851bc54e42a14454ccd6a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30781111"
---
# <a name="introduction-to-ios-8"></a>IOS 8 简介

_IOS 8，Apple 提供了大量新的框架和 Api 让开发人员高兴地对。本指南中我们将介绍这些新的 Api，并请参阅 iOS 8 开发人员和用户如何使受益。_

iOS 7 直观地从哪些用户和开发人员必须希望，直接从第一个 iPhone OS 更改整个 iOS 用户界面。 IOS 8 通过允许用户控制其生命直接通过其 iPhone 的几乎每个方面为开发人员，提供很多框架与此继续执行。 例如与分析运行状况和适用性*HealthKit*，密码是退化生物识别身份验证使用*LocalAuthentication*，*应用扩展*打开第三方应用程序之间的通信通道和*HomeKit*使你能够将你在家转变为将来的主页。 

如果 iOS 7 有关 delighting 用户，iOS 8 着重于 delighting 开发人员提供这些 tasty 新工具的整个范围。 

本指南为 Xamarin.iOS 开发人员引入了新的 Api。  

也有几个 iOS 8，详细本文档末尾中已弃用的 Api。

## <a name="requirements"></a>要求

在 Visual Studio 中为 Mac 创建 iOS 8 应用程序需要以下：

- **Xcode 7 和 iOS 8 或更高版本**– Apple 的最新 Xcode 以及 iOS Api 需要安装并配置开发人员计算机上。
- **Visual Studio for Mac** – 应安装最新版本的适用于 Mac 的 Visual Studio，并将其配置用户设备上。
- **iOS 8 设备或模拟器**– 运行用于测试 iOS 8 的最新版本的 iOS 设备。

## <a name="home-and-leisure"></a>家庭和休闲

iOS 8 有助于取出插入通过使用 HomeKit 和 HealthKit 您主页的核心牢固地计划 Apple 和 iOS 设备。 本部分中，在中，我们将看看如何这些新框架的工作，以及它们如何可以集成到你的 Xamarin.iOS 应用程序。

## <a name="homekit"></a>HomeKit

控制在你的 iPhone 你设备不是技术的新的应用程序;可通过 iOS 应用程序控制许多连接主页产品。 但是 HomeKit 现在采用这一层楼通过将提升一种通用协议对于家庭自动化设备，以及通过对某些制造商，如 iHome，Philips 和 Honeywell 进行公共 API 可用。 对用户来说，这意味着它们可以控制的无缝地从其主页几乎每个方面在一个应用程序内。 它是他们知道他们正在使用 Philips 色调灯泡或嵌套警报不相关。 用户还可以将链接组合在一起成为"后台"大量智能主进程。

HomeKit，与第三方应用程序和使用 Siri 可以发现附件和将它们添加到其个人家庭配置数据库、 编辑和对此数据进行操作并与其附件和其服务，以执行操作。

### <a name="configuration"></a>配置

下图显示了 HomeKit 附件的配置的基本层次结构：

![](introduction-to-ios8-images/image1.png "此图显示了 HomeKit 附件的配置的基本层次结构")
 
若要开始使用 HomeKit，开发人员将需要确保其预配配置文件已选 HomeKit 服务。 Apple 也提供让 Xcode HomeKit 模拟器外接程序的开发人员。 这可在[Apple 开发人员中心](https://developer.apple.com/downloads/index.action)下`Hardware IO Tools for Xcode`。 

有关详细信息，请参阅我们[HomeKit](~/ios/platform/homekit.md)指南。

## <a name="healthkit"></a>HealthKit

HealthKit 是中的运行状况相关信息提供集中的、 协调，且安全的数据存储的 iOS 8 中引入的框架。 隐私和安全的运行状况信息，并使用运行状况应用，用户的仪表板，可确保操作系统。 用户的权限后，应用程序可以读取和写入各种运行状况信息。

有关使用此 Xamarin.iOS 应用程序中的详细信息，请参阅[简介 HealthKit](~/ios/platform/healthkit.md)指南。

## <a name="extending-iphone-functionality"></a>扩展 iPhone 功能
与 iOS8，开发人员正在提供更多控制谁可以使用其应用程序和增加的功能更加开放之间的通信第三方应用。 功能，例如应用程序扩展和文档选取器打开应用程序如何使用 Apple 的生态系统中的可能性很多。

### <a name="app-extensions"></a>应用程序扩展
应用程序扩展，若要概括就是，是用于与另一个进行通信的第三方应用程序的方式。 若要维护高安全性标准并遵守沙盒应用程序的完整性，此通信不会发生应用程序之间直接。 相反，它由执行*扩展*在中间。

创建应用程序扩展的第一步是定义的正确的扩展点，-这是重要确保的行为和可用性的正确的 Api。 若要创建应用程序扩展在 Visual Studio 中为 Mac 上，将其添加到现有应用程序通过将新项目添加到你的解决方案。

在**新项目**对话框导航到**C#** > **iOS** > **统一 API**  >  **扩展**，如下面的屏幕截图中所示：

![](introduction-to-ios8-images/image2.png "创建一个新的扩展")
 
新项目对话框提供七个新的项目模板创建应用程序扩展，并将在下文。 请注意，许多扩展与其他新的 Api，在 iOS 中，如文档选取器：

- **操作**– 这允许开发人员创建允许用户执行某些任务的唯一的自定义操作按钮
- **自定义键盘**– 这样，开发人员将添加到的范围加上其自己自定义的一个内置 Apple 键盘。 常用的键盘，Swype 使用它来将其键盘到 iOS。
- **文档选取器**– 这包含文档选取器视图控制器，从而允许用户访问应用程序的沙箱以外的文件。
- **文档选取器文件提供程序**– 这为文件使用文档选取器提供安全存储。
- **照片编辑**– 此扩展的筛选器并编辑已 Apple 提供要为用户提供更好地控制和更多选项上，在编辑其照片的照片应用程序中的工具。
- **今天**– 这使应用程序能够在通知中心的今天部分中显示的小组件。

有关在 Xamarin 中使用应用程序扩展的详细信息，请参阅[应用扩展简介](~/ios/platform/extensions.md)指南。

### <a name="touch-id"></a>Touch ID

Touch ID 的引入了在 iOS 7 中作为一种对用户进行身份验证-类似于密码。 但是，它是限于解锁设备、 使用应用商店、 使用 iTunes，和对 iCloud keychain 仅进行身份验证 

现在有两种方法可以使用 Touch ID 作为 iOS 8 应用程序使用本地身份验证 API 中的身份验证机制。 目前不能为使用本地身份验证进行远程身份验证。

首先，它帮助通过使用新密钥链访问控制列表 (Acl) 的现有密钥链服务。 Keychain 数据可以是与成功的身份验证的用户指纹解锁。

其次，LocalAuthentication 提供两种方法进行身份验证的本地应用程序。 开发人员应使用`CanEvaluatePolicy`来确定设备是否能够接受 Touch ID，然后`EvaluatePolicy`启动身份验证操作。

有关在 Touch ID，并了解如何将其集成到 Xamarin.iOS 应用程序的详细信息，请参阅[简介到 TouchID](~/ios/platform/touchid.md)指南。

### <a name="document-picker"></a>文档选取器

文档选取器适用于要允许用户打开已在不同的应用程序中创建、 导入和对其进行处理并将其导出的文件的用户 iCloud 驱动器回。 这将创建的直观工作流，并因此更好的体验，为用户。 iCloud 同步会增加此一个步骤-在一个应用程序中进行任何更改将也会反映一致地跨所有设备。

若要了解更深入的文档选取器并了解如何将其集成到 Xamarin.iOS 应用程序，请参阅[简介文档选取器](~/ios/platform/document-picker.md)指南。

### <a name="handoff"></a>Handoff

Handoff，这是更大的连续性功能的一部分，将进一步实现集成 OS X 和 iOS 的步。 这包括跨平台 AirDrop，能够 iPhone 调用，SMS 上 iPad 和 Mac 和中在你的 iPhone tethering 的改进。

适用于 iOS 8 和 Yosemite，和要求 iCloud 帐户登录到不同的所有设备你想要使用非切换。 它应使用预装的 Apple 应用程序，其中包括 Safari、 iWork、 地图、 日历和联系人。

有关详细信息，请参阅我们[Handoff](~/ios/platform/handoff.md)指南。

## <a name="unified-storyboards"></a>统一的情节提要
iOS 8 包括一个新易于使用的机制来创建用户界面-统一情节提要。 使用单个演示图板以涵盖所有不同的硬件的屏幕大小，可以 true"一次设计，使用许多"样式创建快速且高度可响应的视图。

在 iOS8 之前, 开发人员使用`UIInterfaceOrientation`纵向和横向模式之间进行区分和`UIInterfaceIdiom`来区分 iOS 设备。 在 iOS8 它不再创建单独的情节提要的 iPhone 和 iPad 设备所需-由方向和设备使用*大小类*。

由大小类，在垂直和水平轴，定义每个设备，并且有两种类型的大小在 iOS 8 中的类：

- **正则**-这是为较大屏幕大小 （如 iPad) 或使的较大的大小 （例如 UIScrollView 效果的小工具
- **Compact** -这是为较小的设备 （如 iPhone)。 此大小会考虑设备的方向。

如果同时使用了这两个概念，则结果将是 2 x 2 网格定义可以在这两个不同的方向中, 使用的不同可能大小，如下面的关系图中所示：

![](introduction-to-ios8-images/image3.png "关系图表示 2 x 2 网格用于定义可以使用的不同可能大小中的这两个不同的方向")
 
有关大小类的详细信息，请参阅[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)。

## <a name="photo-kit"></a>照片工具包
照片工具包是一个新的框架，允许应用程序查询系统图像库和创建自定义用户界面查看和修改其内容。 它包括了多个类表示图像和视频资产，以及资产，例如专辑和文件夹的集合。

有关详细信息，请参阅我们[PhotoKit](~/ios/platform/photokit.md)指南。

## <a name="games"></a>游戏

<a name="scenekit" />

### <a name="scene-kit"></a>场景工具包

场景工具包是一个三维场景图形 API，用于简化 3D 图形。 它在 OS X 10.8，首次引入，并具有现在进入 iOS 8。 使用场景工具包创建沉浸式 3D 可视化效果和休闲 3D 游戏不需要 OpenGL 的专业知识。 常见场景 graph 概念上构建，场景工具包简化 OpenGL 和 OpenGL ES，使其很容易添加三维内容对的应用程序。 但是，如果你是 OpenGL 方面的专家，场景工具包已针对直接与以及 OpenGL 的全力支持。 它还包括许多功能，补充 3D 图形，如物理，并与多个其他 Apple 框架，例如核心动画、 Core 映像和画面工具包很好地集成。

有关详细信息，请参阅我们[SceneKit](~/ios/platform/gaming/scenekit.md)文档。

<a name="spritekit" />

### <a name="sprite-kit"></a>动画层工具包

动画层工具包中，从 Apple 中的 2D 游戏 framework 具有 iOS 8 和 OS X Yosemite 中的一些有趣的新功能。 其中包括与场景工具包、 着色器支持、 照明、 阴影、 约束、 正常映射生成和物理增强功能的集成。 具体而言，新的物理功能使其很容易添加游戏逼真的效果。

有关详细信息，请参阅我们[SpriteKit](~/ios/platform/gaming/spritekit.md)文档。

## <a name="other-changes"></a>其他更改
在上面所述的 iOS 8 中的主要更改，以及 Apple 此外已更新很多现有框架。 这些下面详细介绍：

- **[核心映像](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** – Apple 已通过添加的矩形区域，在检测的更好地支持扩展其图像处理框架，并在映像内的 QR 代码。 Mike Bluestein 探讨这在他的博客文章标题为[iOS 8 中的映像检测](http://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>弃用的 API
通过在 iOS 8 中所做的所有改进，大量的 Api 已弃用。 其中一些下面详细介绍。

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  – 的方法和属性用于注册远程通知已弃用。 这些是 registerforremotenotificationtypes 的前面和 enabledRemoteNotificationTypes。
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  – 特征和大小类已替换的方法和属性用于描述接口方向。 请参阅[简介统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)有关如何使用这些详细信息。

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  – 这已被取代 UISearchController iOS8 中。

## <a name="summary"></a>总结
在本文中我们讨论一些 apple 在 iOS 8 中引入的新功能。



## <a name="related-links"></a>相关链接

- [UIKitEnhancements （示例）](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [应用程序扩展简介](~/ios/platform/extensions.md)
- [CloudKit 简介](~/ios/data-cloud/intro-to-cloudkit.md)
- [文档选取器简介](~/ios/platform/document-picker.md)
- [HealthKit 简介](~/ios/platform/healthkit.md)
- [手动相机控件简介](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [TouchID 简介](~/ios/platform/touchid.md)
- [统一的情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)
