---
title: 其他 iOS 10 框架更改
description: 本文档介绍细微的更改和对现有框架在 iOS 10 中所做的增强功能，并介绍了如何使这些更新在 Xamarin.iOS 中的使用。
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: 6ae08264218c8f959b351f059d73fc0aebfea39e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118079"
---
# <a name="additional-ios-10-frameworks-changes"></a>其他 iOS 10 框架更改

_本文介绍如何附加的次要更改或增强功能到现有框架，可用于 iOS 10。_

## <a name="av-foundation-framework-additions"></a>AV Foundation Framework 新增功能

AVFoundation 框架包括以下增强功能：

- 在 iOS 10 中，开发人员不再必须实现不同[AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/)行为基于内容类型。 只需设置`Rate`属性和 AVFoundation 将确定足够的内容时可用于播放而无需停止。
- 新[AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/)类将替换不推荐使用`AVCaptureStillImageOutput`类，并提供统一的方法以通过提供复杂的控制和监视的捕获进程处理摄影的所有工作流和支持新功能，例如 Live 照片和原始捕获格式。
- 新`AVPlayerLooper`类可以更轻松地在播放期间循环一段给定的媒体。
- `AVAssetDownloadURLSession`类可用于下载和更高版本播放 FairPlay 加密 HLS 流。
- 默认情况下[AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/)类自动支持范围内颜色、 范围 gamut 捕获时设备硬件支持。 请参阅 Apple [iOS 设备兼容性参考](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599)的更多详细信息。

## <a name="avkit-additions"></a>AVKit 新增功能

AVKit 框架现在包括新`UpdatesNowPlayingInfoCenter`属性以指示应何时更新现在播放信息中心。

## <a name="core-data-enhancements"></a>核心数据增强功能

iOS 10 包括核心数据框架的以下增强功能：

- [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) SQLite 在 WAL 日志模式下支持新的查询生成的数据存储的对象功能可以将托管对象上下文 (MOC) 固定到将来中提取的特定数据库版本和出错的事务。
- 根[NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/)对象支持并发故障和提取而无需序列化。
- [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/)类维护的 SQLite 数据存储区池。
- 已添加到多个新的便捷方法`NSManagedObject`轻松地执行提取操作，并创建子类。
- 使用高级`NSPersistenceContainer`引用`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/)和其他核心数据配置资源。

有关详细信息，请参阅 Apple[核心数据框架引用](https://developer.apple.com/reference/coredata)。

## <a name="core-image-enhancements"></a>核心图像增强功能

iOS 10 进行核心映像框架的以下增强功能：

- 开发人员现在可以通过之前和之后处理传入和传出的颜色空间转换处理颜色空间以外的核心映像上下文的工作颜色空间中的映像。
- 对于使用 A8 或 A9 的 Cpu 的 iOS 设备，现在支持原始图像格式。 从原始图像解码内置 iSight 照相机或从第三方照相机，核心映像现在提供支持。 使用`FilterWithImageData`或`FilterWithImageURL`方法的[CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/)类来处理原始映像。
- 对进行了多个呈现性能方面的增强`UIImage`呈现 （时由 Core 映像映像存储区） 中`UIImageView`对象。 
- `UIImage` 对象标记的范围 gamut 将呈现为范围内所有颜色在`UIImageView`支持广泛的颜色的 iOS 设备上的对象。
- 核心映像内核代码现在可以请求特定像素输出格式。
- `ImageWithExtent`方法[CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/)类可用于筛选器操作中插入自定义处理。 核心映像将调用给定的回调之间筛选器时处理输出的图像或显示。

此外，已添加以下新的核心映像筛选器：

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>核心运动新增功能

新 ios 10、 Core 运动框架包括计步器事件允许应用以接收快速、 暂停和恢复运行时跟踪用户的实时通知。 使用[CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/)注册前景色或背景计步器事件。

## <a name="foundation-enhancements"></a>Foundation 增强功能

适用于 iOS 10 Foundation 框架进行了以下增强功能：

- 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)类设置用于向最终用户显示本地化的度量值的格式。
- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)类，以使日期和时间间隔计算，如持续时间，用于比较的时间间隔和测试的时间间隔的交叉点。
- 使用新[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)类来转换之间不同单位的度量值 (UOM) 或对不同 UOMs 中的值执行计算。

- 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)并[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)类用于表示特定 UOMs。
- 多个新属性已添加到[NSLocal](https://developer.apple.com/reference/foundation/nslocale)类来获取本地信息和可用的显示格式。

## <a name="gamekit-enhancements"></a>GameKit 增强功能

在 iOS 10 GameKit 框架进行了以下增强功能：

- **游戏中心应用**已弃用，从 iOS 中删除。 如果应用使用 GameKit，其_必须_提供其自己的界面以显示 GameKit 功能，例如排行榜，等等。 
- 新的仅限 iCloud 帐户类型由[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)类。
- 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)类提供通用的解决方案用于管理在游戏中心永久性数据存储。 `GKGameSession` 维护的玩家列表，并应用负责实施方式和时间参与者日期是存储、 检索或播放机之间交换。 在许多情况下游戏会话可以替换现有的基于轮次的匹配项、 实时的匹配项或持久性方法保存的游戏。

## <a name="gameplaykit-enhancements"></a>GameplayKit 增强功能

在 iOS 10 GameplayKit 框架进行了以下增强功能：

- 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)类以提供高性能、 自然的路径。
- 过程干扰生成已添加，并可用于增强真实性自然纹理中的、 添加照相机移动到的真实性并帮助生成丰富游戏世界。
- 使用空间分区用于提高搜索效率的游戏世界数据进行分区。
- 新的 Monte Carlo 规划师 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已添加对详尽的可能移动计算。
- 三维支持已添加到现有的代理，并使用新的路径查找行为[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)并[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)类。
- 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)并[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)类进行组合 GameplayKit 和 SpriteKit 比以往更容易。
- 已添加新的决策树 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)并[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 来增强游戏构建 AI。

## <a name="healthkit-enhancements"></a>HealthKit 增强功能

在 iOS 10 HealthKit 框架进行了以下增强功能：

- 新的元数据密钥已添加的天气类型 (如`HKWeatherConditionClear`并`HKWeatherConditionCloudy`) 和健身类型 (如`HKWorkoutActivityTypeFlexibility`和`HKWorkoutActivityTypeWheelchairRunPace`) 已添加。
- 新`HKCDADocument`类已添加表示临床文档体系结构 (CDA) 格式的文档。
- 使用新[HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration)类，以指定`ActivityType`和`LocationType`的健身。
- 新[HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject)并`WheelchairUse`方法[HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)类中添加了使用轮椅通道相关的运行状况数据。

## <a name="homekit-enhancements"></a>HomeKit 增强功能

在 iOS 10 HomeKit 框架进行了以下增强功能：

- 已添加新的服务和特征。
- IPad 可以配置为充当 HomeKit 中心提供附件的远程访问、 运行自动化触发器并启用共享的用户权限。
- 照相机和门铃 accessories 添加了支持。
- 已针对附件提供了更多上下文和配置。

请参阅我们[简介 HomeKit](~/ios/platform/homekit.md)文档了解详细信息。

## <a name="metal-enhancements"></a>裸机增强功能

对 iOS 10 中的金属框架做了以下增强功能：

- 三维应用和游戏现在可以使用_分割_以有效地呈现复杂的场景和通过 GPU 的几何图形。
- 提供的更好地控制资源分配，以优化性能的金属基于堆使用资源的应用和 Memoryless 呈现器目标。
- 使用函数专用化来创建高度优化的材料和光线组合函数场景的集合。

若要了解详细信息，请参阅 Apple[金属编程指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

## <a name="modelio-enhancements"></a>ModelIO 增强功能

在 iOS 10 ModelIO 框架进行了以下增强功能：

 - 现在支持美元文件格式。
 - 签名距离字段支持添加到[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)类。
 - 使用新`MDLLightProbeIrradianceDataSource`类，以帮助在 Light 探测位置。
 - 使用新`MDLMaterialPropertyGraph`类，以轻松地支持运行时更改为模型。

## <a name="photos-enhancements"></a>照片增强功能

对 iOS 10 中的照片框架做了以下增强功能：

- 使用[CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput)并[CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput)类，以充分利用新的核心映像处理器功能来执行编辑。
- 实时照片编辑现已推出为支持的照片框架的应用和照片编辑扩展插件 (用于内部的照片和相机应用)。
- 使用新[PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext)类，以编辑同时适用于视频和仍的 Live 照片内容。

## <a name="replaykit-enhancements"></a>ReplayKit 增强功能

在 iOS 10 ReplayKit 框架进行了以下增强功能：

- 使用[RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder)， [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller)并[RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller)类来支持广播的记录通过第三方媒体站点。
- 在应用中支持 ReplayKit 第三方广播的服务所需的广播 UI 和广播上传扩展。

## <a name="scenekit-enhancements"></a>SceneKit 增强功能

在 iOS 10 SceneKit 框架进行了以下增强功能：

- [SCNCamera](https://developer.xamarin.com/api/type/SceneKit.SCNCamera/)类可以通过使用 HDR 功能和影响提供更高版本的真实性。 使用自适应暴露创建自动效果或使用晕影、 色边和颜色评分为游戏添加 fillmatic 效果。
- SceneKit 现在包括新的以物理方式基于呈现 (PBR) 系统，用于更简单资产创作更真实的结果。
- 使用新[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)明暗度模型到产品范围广泛的实心底纹效果要求只有三个基本属性 (`Diffuse`，`Metalness`和`Roughness`)。
- 自 PBR 最佳基于环境的照明着色的工作原理，使用`LightingEnvironment`要分配给整个场景的基于映像的照明属性。
- 使用`IESProfileURL`要导入定义照明的实际灯具属性基于强度 （以流明） 和 （以度为单位 Kelvin） 颜色温度等实际值。
- PBR 和 HDR 相机功能提供更好的结果比传统的呈现技术，因此，SceneKit 现在中及执行的所有颜色计算线性颜色空间 （使用 P3 颜色域范围内颜色设备显示器上）。
- SceneKit 现在颜色按读取颜色配置文件信息匹配的所有颜色。
- SceneKit 解释所有着色器类型线性 RGB 颜色空间中的颜色组件值。
- 同时线性颜色空间呈现，可以禁用通过指定的范围内颜色`SCNDisableLinearSpaceRendering`并`SCNDisableWideGamut`在应用的密钥`Info.plist`。
- 生成任意多边形一下灵长类 （不管是从文件加载或以编程方式生成） 来指定与新的几何图形[SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)类。
- 由于 SceneKit 读取并调整纹理图像中的颜色配置文件信息，使用的所有图像资产目录以确保提供此信息。

## <a name="spritekit-enhancements"></a>SpriteKit 的增强功能

对 iOS 10 中的 SpriteKit 框架做了以下增强功能：

- 自定义着色器可以提供属性 (`SKAttribute`)，可单独进行配置的情况下提供属性值，使用着色器的每个节点 (`SKAttributeValue`)。
- Tilemaps 现在支持针对 2D、 2.5 D 和使用端滚动游戏方形、 六边形和等角的磁贴形状`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`类。
- 使用新`SKWarpGeometry`类来拉伸或扭曲[SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/)或[SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/)呈现。 新[SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/)类可用于对 warp 效果之间的转换进行动画处理。
- [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/)类提供了几种新方法来提供精细地控制何时以及如何呈现场景。

## <a name="scrollview-enhancements"></a>ScrollView 增强功能

在 iOS 10.3 ScrollView 控件已得到以下增强功能：

- `UIScrollView` 现在包括`IndexDisplayMode`属性来控制用户滚动作为时如何显示索引`UIScrollViewIndexDisplayMode`的：
    - `Automatic` -索引显示由操作系统控制。
    - `AlwaysHidden` -始终隐藏索引显示。

请参阅[iOSTenThree 示例](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree)的使用情况。

## <a name="uikit-enhancements"></a>UIKit 增强功能

在 iOS 10 UIKit 框架已得到以下增强功能：

- 新[UIPasteboard](https://developer.xamarin.com/api/type/UIKit.UIPasteboard/) API 提供了新选项 （如生存期限制），并将自动声明公共类类型兼容的内容类型。
- 新的完全交互式的、 基于对象的、 可中断动画支持已添加，并且可以链接到手势。 请参阅 Apple [UIViewAnimating 协议参考](https://developer.apple.com/reference/uikit/uiviewanimating)， [UIViewPropertyAnimator 类引用](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)， [UITimingCurveProvider 协议参考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)， [UICubicTimingParameters 类引用](https://developer.apple.com/reference/uikit/uicubictimingparameters)并[UISpringTimingParameter 类引用](https://developer.apple.com/reference/uikit/uispringtimingparameters)有关详细信息。
- 新`UIPreviewInteraction`和`UIPreviewInteractionDelegate`允许开发人员应用将提供用于查看和 pop 操作自定义接口。
- 新`UIAccessibilityCustomRotor`类允许应用程序以提供对 Voice Over 等辅助技术的自定义的特定于上下文的功能。
- 使用`UIAccessibilityIsAssistiveTouchRunning`和`UIAccessibilityAssistiveTouchStatusDidChangeNotification`符号来确定是否启用 AssistiveTouch。
- 使用`UIAccessibilityHearingDevicePairedEar`和`UIAccessibilityHearingDevicePairedEarDidChangeNotification`要获取的任何状态的符号配对 MFi 听力辅助工具。
- 若要在标签中支持动态类型，文本字段和文本框中使用的新`PreferredFontForTextStyle`方法的`UIFont`类。
- 若要确定元素应更新其字体时设备的`UIContentSizeCategory`发生更改，使用`AdjustsFontForContentSizeCategory`属性的`UIContentSizeCategoryAdjusting`委托。
- `OpenURL`方法的`UIApplication`类以异步方式调用，并且现在支持打开操作完成后调用完成处理程序。
- 启动 CloudKit 共享和修改其属性使用的新`UICloudSharingController`和`UICloudSharingControllerDelegate`类。
- 充分利用预提取的单元格以改进的滚动体验`UICollectionViews`与新`UICollectionViewDataSourcePrefetching`委托。
- 开发人员现在可以控制选项卡栏项 （如文本和背景色） 该徽标的外观。
- 刷新控件现在支持所有滚动视图和滚动视图子类 (如`UICollectionView`)。

## <a name="webkit-enhancements"></a>易于使用的功能的增强功能

对 iOS 10 中的易于使用的功能框架做了以下增强功能：

- 查看和 pop 支持添加到`WKWebView`类。 使用`ShouldPreviewElement`方法，以确定给定的 web 视图应显示预览。


## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [什么是 iOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
