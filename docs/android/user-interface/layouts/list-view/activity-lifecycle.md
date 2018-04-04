---
title: ListView 和活动的生命周期
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 6e15fb8796ae6a616c5eae44059caae3d9478aef
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView 和活动的生命周期

活动通过某些状态转作为在应用程序运行过程中，如启动、 运行、 正在暂停和正在停止。 有关详细信息，以及在处理状态转换的特定准则，请参阅[活动生命周期教程](~/android/app-fundamentals/activity-lifecycle/index.md)。
务必了解的活动的生命周期和位置你`ListView`的正确位置中的代码。

在活动的所有本文档中的示例执行安装任务`OnCreate`方法和 （如果必需） 在执行拆卸`OnDestroy`。 示例通常使用小型数据集不会更改，因此是不必要的更频繁地重新加载数据。

但是，如果你的数据经常更改，或使用大量的内存它可能适合使用不同的生命周期方法来填充和刷新你`ListView`。 例如，如果基础数据始终不断在更改 （或可能受其他活动上的更新） 然后创建中的适配器`OnStart`或`OnResume`将确保最新的数据将显示每次显示活动。

如果适配器使用的资源，如内存或托管的光标，请记得释放这些资源在其中它们已实例化 （如在互补方法 中创建的对象`OnStart`可以中释放`OnStop`)。


## <a name="configuration-changes"></a>配置更改

务必记住该配置更改&ndash;尤其是屏幕旋转和键盘的可见性&ndash;可以使当前活动销毁或重新创建 (除非另行指定，否则使用`ConfigurationChanges`属性）。 这意味着，正常情况下，旋转设备将导致`ListView`和`Adapter`必须重新创建和 (除非编写代码后`OnPause`和`OnResume`) 滚动位置和行选择状态将会丢失。

以下属性将阻止从正在销毁，并重新创建由于进行了配置更改的活动：

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

活动应然后替代`OnConfigurationChanged`适当地响应这些更改。 有关如何处理配置更改的更多详细信息请参阅文档。

