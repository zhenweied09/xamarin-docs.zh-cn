---
title: 其他 macOS Sierra Framework 更改
description: 本文档介绍细微的更改以及现有框架 macOS Sierra 中引入的增强功能。 它检查到加速 framework、 AppKit、 AVFoundation、 核心数据、 Core 映像、 Foundation、 和的详细信息的更改。
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 3cfa2e9bcb0be4d65462914215045c9c7f01da5b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792588"
---
# <a name="additional-macos-sierra-framework-changes"></a>其他 macOS Sierra Framework 更改

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>加速 Framework 增强功能

MacOS Sierra 的加速 framework 进行了以下增强功能：

- 添加正交 （整型微积分）。
- 添加了用于构造神经网络的基本功能。
- 添加几何谓词函数来测试两个几何对象的交集之类的内容。

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>AppKit Framework 增强功能

MacOS Sierra 的 AppKit framework 进行了以下增强功能：

- 对多项增强`NSCollectionView`如：
    - **可折叠的部分**-允许用户为单个水平行折叠集合视图部分。
    - **浮点标头**-页眉和页脚现在可以浮动 （在数据流布局中） 使用相同的 API 作为[UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview)在 iOS 中。
    - **可滚动后台视图**-现在可以设置集合视图背景随内容一起滚动。
- 已优化和扩展延迟的视图布局过程。
- 拖放 API 现在包括新`NSFilePromiseProvider`和`NSFilePromiseReceiver`类以支持拖动群。
- 多个方便构造函数已添加到现有控件：
    -  `NSButton` 包括新的构造函数创建推送按钮、 复选框和单选按钮。
    -  `NSTextField` 包括新的构造函数创建包装和非换行的标签、 特性化的标签和可编辑文本字段。
    -  `NSSegmentedControl` 包括用于从一组标签或映像创建分段的控件的新构造函数。
    -  `NSSlider` 包括新的构造函数创建水平线性滑块。
    -  `NSImageView` 包括新的构造函数创建非可编辑的图像视图从给定`NSImage`。
-  新`NSGridView`已添加到的子视图转换为网格与变量的集合大小行和列的动态隐藏或显示的自动数据布局。

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>AVFoundation Framework 增强功能

MacOS Sierra 的 AVFoundation framework 进行了以下增强功能：

- 在 macOS，应用程序不再必须实现不同[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行为基于内容的类型。 只需设置`Rate`属性和 AVFoundation 将确定足够的内容何时可用于播放而无需停滞。
- 新`AVPlayerLooper`类，更便于在播放期间循环一段给定的媒体。
- `AVAssetDownloadURLSession`类允许在下载和更高版本播放 FairPlay 加密 HLS 流。

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>核心数据 Framework 增强功能

MacOS Sierra 的核心数据 framework 进行了以下增强功能：

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)对象支持并发出错和提取不带序列化。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)类维护 SQLite 数据存储区池。
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) SQLite WAL 日志模式支持新的查询生成的数据存储对象功能可以固定到将来提取特定的数据库版本的管理对象上下文 （模式） 的位置和出错的事务。
- 使用高级`NSPersistenceContainer`引用`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心数据配置资源。
- 几个新的简便方法已添加到`NSManagedObject`使其更轻松地执行提取和创建子类。

有关详细信息，请参阅 Apple 的[核心数据 Framework 参考](https://developer.apple.com/reference/coredata)。

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>核心映像 Framework 增强功能

MacOS Sierra 的核心映像 framework 进行了以下增强功能：

- `ImageWithExtent`方法[CIFilter](https://developer.apple.com/reference/coreimage/cifilter)类可以用于将自定义处理插入到该筛选器操作。 Core 映像将调用给定的回调之间筛选器时处理输出的映像，或显示。
- 应用程序现在可以通过将转换入和移出的色彩空间之前和之后处理处理 Core 映像上下文工作颜色空间的外部颜色空间中的映像。
- Core 映像内核现在可以请求特定像素输出格式。
- 已添加以下新映像筛选器： `CINinePartTitled`， `CINinePartStretched`， `CIHueSaturationValueGradient`，`CIEdgePreserveUpsampleFilter`和`CIClamp`。

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Foundation Framework 增强功能

MacOS Sierra 的 Foundation framework 进行了以下增强功能：

- 使用[NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API，用于表示、 转换和显示的最常见的物理单元的许多如大容量，长度、 速度、 持续时间和温度。
- 使用[NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter)类用于分析和生成 ISO 8601 格式的日期。
- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)类负责使如持续时间，用于比较的时间间隔和测试的间隔交集的日期和时间间隔计算。
- 使用[NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter)类，以分析从字符串的人员的名称的元素。
- 使用新[NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics)类获取网络会话的 URL 的度量值。

有关详细信息，请参阅 Apple 的[Foundation 发行说明适用于 OS X v10.12 和 iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html)。

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>GameKit Framework 增强功能

MacOS Sierra 的 GameKit framework 进行了以下增强功能：

- **游戏中心应用**已弃用并从 macOS 中删除。 如果应用使用 GameKit，它_必须_提供其自己的界面，以显示 GameKit 功能，例如排名，等等。 
- 新的仅 iCloud 帐户类型由[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)类。
- 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)类提供通用的解决方案，用于管理在游戏中心上的永久数据存储区。 `GKGameSession` 维护的玩家列表，并在应用程序处于负责窗体实现如何以及何时存储参与者的日期，检索或播放器之间交换。 在许多情况下游戏会话可以替换现有的基于轮次的匹配项、 实时匹配或永久性方法保存的游戏。

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>GamePlayKit Framework 增强功能

MacOS Sierra 的 GamePlayKit framework 进行了以下增强功能：

- 过程干扰生成已添加，并且可以用于增强自然查找纹理中的要增强真实性，向相机动作数添加要增强真实性并帮助生成丰富游戏世界。
- 使用空间分区进行分区的游戏世界数据的高效搜索。
- 新的 Monte Carlo 规划师 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已添加了对详尽的可能移动计算。
- 已添加新的决策树 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)和[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 来增强游戏构建 AI。
- 三维支持已添加到现有的代理和使用新的路径查找行为[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)和[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)类。
- 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)类以提供高性能、 自然查找的路径。
- 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)和[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)组合 GameplayKit 和 SpriteKit 比以前更容易的类生成。

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>裸机 Framework 增强功能

MacOS Sierra 的裸机 framework 进行了以下增强功能：

- 三维应用程序和游戏现在可以使用_分割_以有效地呈现复杂的情形和通过 GPU 的几何图形。
- 使用函数专用化来创建高度优化材料和场景浅色组合函数的集合。
- 提供的资源分配，以优化性能的裸机细化的控制基于堆使用资源的应用和 Memoryless 呈现目标。

若要了解详细信息，请参阅 Apple 的[金属编程指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>模型 I/O 框架增强功能

MacOS Sierra 的模型 I/O framework 进行了以下增强功能：

- 现在支持价格 （美元） 文件格式。
- 使用新`MDLMaterialPropertyGraph`类，以轻松地支持运行时更改为模型。
- 签名支持已添加到距离字段[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)类。
- 使用新`MDLLightProbeIrradianceDataSource`类，用于帮助在 Light 探测放置。

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>照片 Framework 增强功能

MacOS Sierra 的照片 framework 进行了以下增强功能：

- 实时照片编辑现可用于支持照片框架的应用程序的编辑扩展的照片 (用于内部的照片和相机应用)。
- 使用新[PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext)类来编辑同时适用于视频和仍的 Live 照片内容。
- 使用[CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput)和[CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput)类以利用新的核心映像处理器功能，用于执行编辑。
- 若要支持 Live 照片[PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto)和[PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview)类具有从 iOS 移植到 macOS。

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>SceneKit Framework 增强功能

MacOS Sierra 的 SceneKit framework 进行了以下增强功能：

- 现在包括一个新的以物理方式基于呈现 (PBR) 系统，用于更简单资产创作更加真实地模拟结果。
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

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>安全框架增强功能

对 macOS Sierra 安全框架进行以下增强功能：

- `SecKey`已得到改进并跨所有平台 （iOS、 tvOS、 watchOS 和 macOS） 统一接口。

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>SpriteKit Framework 增强功能

MacOS Sierra 的 SpriteKit framework 进行了以下增强功能：

- Tilemaps 现在支持针对 2D、 2.5 D 和使用端滚动游戏正方形、 六边形和等轴磁贴形状`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`类。
- 使用新`SKWarpGeometry`类拉伸或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)呈现。 新[SKAction](https://developer.apple.com/reference/spritekit/skaction)类可以用于进行动画处理 warp 效果之间的转换。
- 自定义的着色器可以提供属性 (`SKAttribute`)，可单独配置通过使用提供的属性值的着色器的每个节点 (`SKAttributeValue`)。
- [SKView](https://developer.apple.com/reference/spritekit/skview)类提供了若干新方法，以便对何时以及如何呈现场景细化的控制。

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>新的框架

到 macOS Sierra 添加了以下框架：

- **意向 Framework** -此框架允许应用程序检查交互 （如位置或用户操作），并采取操作基于该信息。
- **SafariServices Framework** -此框架允许应用程序 （如内容阻止程序问题） 的 Safari macOS 和 iOS 开发应用程序扩展。

## <a name="related-links"></a>相关链接

- [Mac 示例](https://developer.xamarin.com/samples/mac/)
- [OS X 10.12 的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
