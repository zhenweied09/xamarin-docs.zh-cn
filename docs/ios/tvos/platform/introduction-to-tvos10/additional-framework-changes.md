---
title: 其他 tvOS 10 框架更改
description: 本文介绍了其他的次要更改或对 tvOS 10 现有框架的增强功能。
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c53726b91a0cd3e79041b9b1d51e9f7fbb1c79bd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="additional-tvos-10-frameworks-changes"></a>其他 tvOS 10 框架更改

_本文介绍了其他的次要更改或对 tvOS 10 现有框架的增强功能。_


除了对 tvOS 的主要更改，Apple 已修改和改进，多个现有框架 tvOS 10。

<a name="AV-Foundation-Framework" />

## <a name="av-foundation-framework-additions"></a>AV Foundation Framework 添加件

AVFoundation framework 包括以下增强功能：

 - 在 tvOS 10，应用程序不再实现不同[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行为基于内容的类型。 只需设置`Rate`属性和 AVFoundation 将确定足够的内容何时可用于播放而无需停滞。
 - 新`AVPlayerLooper`类，更便于在播放期间循环一段给定的媒体。

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>AVKit Framework 增强功能

AVKit framework 包括以下增强功能：

 - 应用程序现在具有控制正在跳过行为[AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller)以便跳过笔势可能将移动到的播放列表或在当前项的高级的下一项。

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>核心数据增强功能

tvOS 10 包括以下增强功能的核心数据 framework:

 - 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)对象支持并发出错和提取不带序列化。
 - [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)类维护 SQLite 数据存储区池。
 - [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) SQLite WAL 日志模式支持新的查询生成的数据存储对象功能可以固定到将来提取特定的数据库版本的管理对象上下文 （模式） 的位置和出错的事务。
 - 使用高级`NSPersistenceContainer`引用`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心数据配置资源。
 - 几个新的简便方法已添加到`NSManagedObject`使其更轻松地执行提取和创建子类。

有关详细信息，请参阅 Apple 的[核心数据 Framework 参考](https://developer.apple.com/reference/coredata)。

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>核心图形增强功能

tvOS 10 包括以下增强功能的核心图形 framework:

 - 新[CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref)类可以用于执行一系列的颜色转换。

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>核心映像增强功能

tvOS 10 使到 Core 映像 framework 以下增强功能：

 - `ImageWithExtent`方法[CIFilter](https://developer.apple.com/reference/coreimage/cifilter)类可以用于将自定义处理插入到该筛选器操作。 Core 映像将调用给定的回调之间筛选器时处理输出的映像，或显示。
 - 应用程序现在可以通过将转换入和移出的色彩空间之前和之后处理处理 Core 映像上下文工作颜色空间的外部颜色空间中的映像。
 - 对进行了多项的呈现性能改进`UIImage`中呈现 （时由 Core 映像映像存储区支持）`UIImageView`对象。 
 - `UIImage` 对象标记的宽色域将呈现为宽色域中的颜色`UIImageView`支持广泛的颜色的 iOS 设备上的对象。
 - 核心映像内核代码现在可以请求特定像素输出格式。

此外，已添加以下新 Core 映像筛选器：

 - `CINinePartTiled`
 - `CINinePartStretched`
 - `CIHueSaturationValueGradient`
 - `CIEdgePreserveUpsampleFilter`
 - `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Foundation 增强功能

TvOS 10 的 Foundation framework 进行了以下增强功能：

 - 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)类负责使如持续时间，用于比较的时间间隔和测试的间隔交集的日期和时间间隔计算。
 - 多个新属性已添加到[NSLocal](https://developer.apple.com/reference/foundation/nslocale)类来获取本地信息和可用的显示格式。
 - 使用新[NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement)类转换之间不同单位的度量值 (UOM) 或在不同 UOMs 值执行计算。
 - 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)类来设置用于向最终用户显示本地化的度量值的格式。
 - 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)和[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)类用于表示特定 UOMs。

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>GameKit 增强功能

对 tvOS 10 中的 GameKit framework 进行以下增强功能：

 - 新的仅 iCloud 帐户类型由[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)类。
 - 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)类提供通用的解决方案，用于管理在游戏中心上的永久数据存储区。 `GKGameSession` 维护的玩家列表，并在应用程序处于负责窗体实现如何以及何时存储参与者的日期，检索或播放器之间交换。 在许多情况下游戏会话可以替换现有的基于轮次的匹配项、 实时匹配或永久性方法保存的游戏。

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>GameplayKit 增强功能

对 tvOS 10 中的 GameplayKit framework 进行以下增强功能：

 - 过程干扰生成已添加，并且可以用于增强自然查找纹理中的要增强真实性，向相机动作数添加要增强真实性并帮助生成丰富游戏世界。
 - 使用空间分区进行分区的游戏世界数据的高效搜索。
 - 新的 Monte Carlo 规划师 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已添加了对详尽的可能移动计算。
 - 已添加新的决策树 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)和[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 来增强游戏构建 AI。
 - 三维支持已添加到现有的代理和使用新的路径查找行为[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)和[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)类。
 - 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)类以提供高性能、 自然查找的路径。
 - 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)和[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)组合 GameplayKit 和 SpriteKit 比以前更容易的类生成。

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>裸机增强功能

对 tvOS 10 中的裸机 framework 进行以下增强功能：

 - 三维应用程序和游戏现在可以使用_分割_以有效地呈现复杂的情形和通过 GPU 的几何图形。
 - 使用函数专用化来创建高度优化材料和场景浅色组合函数的集合。
 - 提供的资源分配，以优化性能的裸机细化的控制基于堆使用资源的应用和 Memoryless 呈现目标。

若要了解详细信息，请参阅 Apple 的[金属编程指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>裸机性能着色器增强功能

对 tvOS 10 中的裸机性能着色器 framework 进行以下增强功能：

 - 许多新的内核已添加到裸机性能着色器框架，以允许应用程序以充分利用高优化、 数据并行计算，如颜色空间转换和神经网络的操作。

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>ModelIO 增强功能

对 tvOS 10 中的 ModelIO framework 进行以下增强功能：

 - 现在支持价格 （美元） 文件格式。
 - 使用新`MDLMaterialPropertyGraph`类，以轻松地支持运行时更改为模型。
 - 签名支持已添加到距离字段[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)类。
 - 使用新`MDLLightProbeIrradianceDataSource`类，用于帮助在 Light 探测放置。

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>SceneKit 增强功能

对 tvOS 10 中的 SceneKit framework 进行以下增强功能：

 - SceneKit 现在包括一个新的以物理方式基于呈现 (PBR) 系统，用于更简单资产创作更加真实地模拟结果。
 - 使用新[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)明暗度模型来产品各种现实底纹效果时需要仅三个基本属性 (`Diffuse`，`Metalness`和`Roughness`)。
 - 自 PBR 了基于环境的照明最底纹工作原理，使用`LightingEnvironment`要分配基于映像的照明以 tan 整个场景属性。
 - 使用`IESProfileURL`要导入实际照明装置，它们定义对等强度 （在流明） 的实际值的照明基和配色温度 （以度为单位开氏） 属性。
 - [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera)类可以通过使用 HDR 功能和影响提供更大要增强真实性。 自适应公开用于创建自动效果或使用晕影、 色边和颜色评分为游戏添加 filmatic 效果。
 - PBR 和 HDR 相机功能提供更好的结果比传统的呈现技术，并因此，SceneKit 现在的执行所有颜色计算线性颜色空间 （在宽颜色设备显示使用 P3 的颜色域）。
 - SceneKit 现在颜色匹配的所有颜色通过阅读的颜色配置文件信息。
 - SceneKit 解释着色器的所有类型的线性 RGB 颜色空间中的颜色组件值。
 - 由于 SceneKit 读取，并调整纹理映像中颜色配置文件信息，请使用为所有图像资产目录以确保提供此信息。
 - 同时线性颜色空间呈现，可以通过指定禁用宽颜色`SCNDisableLinearSpaceRendering`和`SCNDisableWideGamut`中应用的键`Info.plist`。
 - 生成任意多边形 primates （不管是从文件加载或通过编程方式生成） 来指定与新的几何图形[SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)类。

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>SpriteKit 增强功能

对 tvOS 10 中的 SpriteKit framework 进行以下增强功能：

 - Tilemaps 现在支持针对 2D、 2.5 D 和使用端滚动游戏正方形、 六边形和等轴磁贴形状`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`类。
 - 使用新`SKWarpGeometry`类拉伸或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)呈现。 新[SKAction](https://developer.apple.com/reference/spritekit/skaction)类可以用于进行动画处理 warp 效果之间的转换。
 - 自定义的着色器可以提供属性 (`SKAttribute`)，可单独配置通过使用提供的属性值的着色器的每个节点 (`SKAttributeValue`)。
 - [SKView](https://developer.apple.com/reference/spritekit/skview)类提供了若干新方法，以便对何时以及如何呈现场景细化的控制。

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>UIKit 增强功能

对 tvOS 10 中的 UIKit framework 进行以下增强功能：

 - 焦点 API 已经过增强，支持非视图项的焦点此外到`UIViews`。 支持焦点的项_必须_实现`IUIFocusItem`接口。
 - 新`UIGraphicsRender`类提供从 UIKit 呈现或核心图形创建位图或 pdf 文件的面向对象的方法，并替换已弃用`UIGraphicsBeginImageContext`方法。
 - `UIUserInterfaceStyle`添加类以确定哪些用户界面主题 （深色或浅色） 当前处于活动状态。
 - 添加了新的完整交互式的、 基于对象的、 可中断动画支持，并且 van 链接到手势。 请参阅 Apple [UIViewAnimating 协议参考](https://developer.apple.com/reference/uikit/uiviewanimating)， [UIViewPropertyAnimator 类引用](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)， [UITimingCurveProvider 协议参考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)， [UICubicTimingParameters 类引用](https://developer.apple.com/reference/uikit/uicubictimingparameters)和[UISpringTimingParameter 类引用](https://developer.apple.com/reference/uikit/uispringtimingparameters)有关详细信息。
 - 新`UIPreviewInteraction`和`UIPreviewInteractionDelegate`允许该应用程序应用程序查看和 pop 操作提供自定义接口。
 - 新`UIAccessibilityCustomRotor`类允许该应用程序提供与辅助技术，如语音通过自定义的、 特定于上下文的功能。
 - 使用`UIAccessibilityIsAssistiveTouchRunning`和`UIAccessibilityAssistiveTouchStatusDidChangeNotification`符号以确定是否启用 AssistiveTouch。
 - 使用`UIAccessibilityHearingDevicePairedEar`和`UIAccessibilityHearingDevicePairedEarDidChangeNotification`符号来获取的任何状态配对 MFi 听力帮助。
 - 新[UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API 提供新的选项 （如生存期限制），将自动声明为公共的类类型兼容的内容类型。
 - 若要在标签中支持的动态类型，文本字段和文本框使用新`PreferredFontForTextStyle`方法`UIFont`类。
 - 若要确定是否元素应更新其字体时设备`UIContentSizeCategory`更改，将使用`AdjustsFontForContentSizeCategory`属性`UIContentSizeCategoryAdjusting`委托。
 - 应用程序现可控制用于选项卡栏项，如文本和背景的颜色标记的外观。
 - 子类中所有滚动视图和滚动视图支持现在中的刷新控件 (如`UICollectionView`)。
 - `OpenURL`方法`UIApplication`类以异步方式调用现在支持打开已完成后调用完成处理程序。
 - 启动 CloudKit 共享和修改其属性使用新`UICloudSharingController`和`UICloudSharingControllerDelegate`类。
 - 充分利用预提取的单元格，若要改善的滚动体验`UICollectionViews`与新`UICollectionViewDataSourcePrefetching`委托。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [什么是 tvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
