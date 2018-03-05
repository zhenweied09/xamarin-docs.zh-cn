---
title: "其他 watchOS 3 框架更改"
description: "本文介绍其他的次要更改或对现有框架用于 watchOS 3 的增强。"
ms.topic: article
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: e2d14bcedd6ff5570d77da8c9b54a74b5ce0752a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="additional-watchos-3-frameworks-changes"></a>其他 watchOS 3 框架更改

_本文介绍其他的次要更改或对现有框架用于 watchOS 3 的增强。_

除了对 iOS 的主要更改，Apple 已修改和改进，多个现有框架 watchOS 3。


## <a name="core-data"></a>核心数据

核心数据框架，用于监视 OS 3 具有可进行以下增强功能：

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)对象支持并发出错和提取不带序列化。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)类维护 SQLite 数据存储区池。
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) SQLite WAL 日志模式支持新的查询生成的数据存储对象功能可以固定到将来提取特定的数据库版本的管理对象上下文 （模式） 的位置和出错的事务。
- 使用高级`NSPersistenceContainer`引用`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心数据配置资源。
- 几个新的简便方法已添加到`NSManagedObject`使其更轻松地执行提取和创建子类。

有关详细信息，请参阅 Apple 的[核心数据 Framework 参考](https://developer.apple.com/reference/coredata)。


## <a name="core-motion"></a>核心移动

以下增强功能进行监视 OS 3 的核心运动 framework:

- 新的设备运动事件将加速和陀螺以提供运动和方向的更新。 app 应用可以注册此更新 （速率的最多 100 Hz）。
- 新的计步器事件快速、 使实时通知时用户暂停和继续运行。 使用[CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer)以注册前景色或背景计步器事件。


## <a name="foundation"></a>Foundation

监视操作系统 3 的 Foundation framework，已经进行了以下增强功能：

- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)类负责使如持续时间，用于比较的时间间隔和测试的间隔交集的日期和时间间隔计算。
- 多个新属性已添加到[NSLocal](https://developer.apple.com/reference/foundation/nslocale)类来获取本地信息和可用的显示格式。
- 使用新[NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement)类转换之间不同单位的度量值 (UOM) 或在不同 UOMs 值执行计算。
- 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)类来设置用于向最终用户显示本地化的度量值的格式。
- 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)和[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)类用于表示特定 UOMs。


## <a name="healthkit"></a>HealthKit

监视操作系统 3 的 HealthKit framework，已经进行了以下增强功能：

- 使用新[HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration)类指定`ActivityType`和`LocationType`的测验。
- 新[HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject)和`WheelchairUse`方法[HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)类中添加了使用轮椅与相关的运行状况数据。
- 新的元数据密钥已添加的天气类型 (如`HKWeatherConditionClear`和`HKWeatherConditionCloudy`) 和锻炼类型 (如`HKWorkoutActivityTypeFlexibility`和`HKWorkoutActivityTypeWheelchairRunPace`) 已添加。


## <a name="homekit"></a>HomeKit

监视操作系统 3 的 HomeKit framework，已经进行了以下增强功能：

- 添加能够查看并与其交互 HomeKit 连接 IP 摄像头。
- 添加几个新的服务和特征。
- 添加更多上下文和配置的主服务和链接服务的附件。


## <a name="passkit"></a>PassKit

监视操作系统 3 的 PassKit framework，已经进行了以下增强功能：

- 扩展框架以支持在物理产品和服务 Apple Watch 上的安全，应用内的付款。
- 以下类现已提供： [PKPayment](https://developer.apple.com/reference/passkit/pkpayment)， [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod)， [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest)和[PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

监视操作系统 3 的 UIKit framework，已经进行了以下增强功能：

- 若要在标签中支持的动态类型，文本字段和文本框使用新`PreferredFontForTextStyle`方法`UIFont`类。
- `ColorWithDisplayP3`方法已添加以支持广泛的颜色。


## <a name="related-links"></a>相关链接

- [入门 （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [什么是 watchOS 3 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
