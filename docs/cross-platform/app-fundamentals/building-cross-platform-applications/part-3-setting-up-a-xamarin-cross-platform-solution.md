---
title: "第 3-部分设置 Xamarin 跨平台解决方案"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: 852c11eeccf347c3175cc5d8d42f55616aedfb84
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>第 3-部分设置 Xamarin 跨平台解决方案

无论使用何种平台，所有的 Xamarin 项目使用相同的解决方案文件格式 (Visual Studio **.sln**文件格式)。 即使在单独的项目不能加载 （如适用于 Mac 的 Visual Studio 中的 Windows 项目），则可以在开发环境之间共享解决方案。



在创建新的跨平台应用程序时，第一步是创建一个空白解决方案。 这一部分的内容会发生什么情况下一步： 设置以便生成跨平台移动应用程序项目。

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>共享代码

请参阅[代码共享选项](~/cross-platform/app-fundamentals/code-sharing.md)有关如何实现跨平台的代码共享的详细说明的文档。

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>共享的项目

共享代码文件的最简单方法是使用[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)。

此方法，可跨不同平台项目中，共享相同的代码并使用编译器指令以包括不同、 特定于平台的代码路径。

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>可移植类库 (PCL)

从历史上看它为特定的框架版本针对.NET 项目文件 （和生成的程序集）。 这样可以防止项目或共享的不同框架的程序集。

可移植类库 (PCL) 是项目的一种特殊类型，可跨不同的 CLI 平台，如 Xamarin.iOS 和 Xamarin.Android，以及 WPF、 通用 Windows 平台和 Xbox。 库仅可以利用完整的.NET framework 中，作为目标的平台受限制的子集。

你可以阅读更多有关 Xamarin 的[可移植类库的支持](~/cross-platform/app-fundamentals/pcl.md)并按照说明进行操作以查看如何[TaskyPortable 示例](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)工作原理。


### <a name="net-standard"></a>.NET Standard

在 2016 中，引入[.NET 标准](~/cross-platform/app-fundamentals/net-standard.md)项目还提供在平台，生成可以在 Windows 上使用的程序集、 Xamarin 平台 (iOS、 Android、 Mac) 和 Linux 间共享代码的简单办法。

可以创建和使用 Pcl，类似，只不过更轻松地发现 （从 1.0 到 1.6) 的每个版本中提供的 Api，并且每个版本是向后兼容.NET 标准库具有较低的版本号。



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>填充解决方案

无论使用哪种方法来共享代码，整体解决方案结构应实现分层体系结构，从而鼓励代码共享。
Xamarin 方法将是组分为两种项目类型的代码：

-   **核心项目**– 在一个位置，在不同平台之间共享中编写可重用代码。 使用封装原则以隐藏实现细节，只要有可能。
-   **特定于平台的应用程序项目**– 使用尽可能少耦合使用的可重用代码。 基于公开核心项目中的组件，此级别添加特定于平台的功能。


 <a name="Core_Project" />


### <a name="core-project"></a>核心项目

共享的代码项目应只能引用可的程序集在所有平台 – ie。常见 framework 命名空间喜欢`System`，`System.Core`和`System.Xml`。

共享的项目应实现尽可能非 UI 功能，如可能，它可能包含以下层：

-   **数据层**– 例如将负责的物理数据存储区的代码。  [SQLite NET](https://github.com/praeclarum/sqlite-net)，一个备用数据库喜欢[Realm.io](https://realm.io/products/realm-mobile-database/)甚至 XML 文件。 数据层类通常是仅供数据访问层。
-   **数据访问层**– 定义一个 API，支持对应用程序的功能，执行所需的数据操作，如方法来访问数据的列表，各项数据和还创建、 编辑，并将其删除。
-   **服务访问层**– 一个可选层，以提供云服务应用程序。 包含可访问远程网络资源 （web 服务、 图像下载，等等） 的代码，并且可能对结果进行缓存。
-   **业务层**– 模型类和特定于平台的应用程序向公开功能的外观或管理器类的定义。


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>特定于平台的应用程序项目

特定于平台的项目必须引用以及核心共享的代码项目绑定到每个平台的 SDK （Xamarin.iOS、 Xamarin.Android、 Xamarin.Mac，或 Windows） 所需的程序集。

应实现特定于平台的项目：

-   **应用程序层**– 平台特定功能和绑定/业务层对象和用户界面之间的转换。
-   **用户界面层**– 屏幕、 自定义用户界面控件、 演示文稿的验证逻辑。


<a name="Example" />


### <a name="example"></a>示例

应用程序体系结构此图所示：

 [ ![](part-3-setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "此图中所示的应用程序体系结构")](part-3-setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

此屏幕截图显示与共享的核心项目、 iOS 和 Android 应用程序项目的解决方案设置。 共享项目包含与每个体系结构层 （业务、 服务、 数据和数据访问代码） 相关的代码：

 ![](part-3-setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "共享项目包含与每个体系结构层 （业务、 服务、 数据和数据访问代码） 相关的代码")


 <a name="Project_References" />


## <a name="project-references"></a>项目引用

项目引用反映项目的依赖关系。 核心项目限制其对公共程序集的引用，因此可以方便地共享代码。
特定于平台的应用程序项目引用的共享代码，以及他们需要利用目标平台的任何其他特定于平台的程序集。

应用程序项目每个引用共享项目中，而包含对用户来说，存在功能所需的用户界面代码，这些屏幕截图中所示：

![](part-3-setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "应用程序项目共享项目中的每个引用") ![ ](part-3-setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "应用程序项目每个引用共享项目中")


案例研究中给出了应如何构造项目的特定示例。

 <a name="Adding_Files" />


## <a name="adding-files"></a>将文件添加

 <a name="Build_Action" />


### <a name="build-action"></a>生成操作

请务必设置正确的生成操作，对于某些文件类型。 此列表显示了一些常见的文件类型的生成操作：

-  **所有 C# 文件**– 生成操作： 编译
-   **Xamarin.iOS 和 Windows 中的映像**– 生成操作： 内容
-   **在 Xamarin.iOS XIB 和情节提要文件**– 生成操作： 接口定义
-   **图像和在 Android 中的 AXML 布局**– 生成操作： AndroidResource
-  **Windows 项目中的 XAML 文件**– 生成操作： 页
-  **Xamarin.Forms XAML 文件**– 生成操作： EmbeddedResource


通常 IDE 将检测的文件类型，并且建议的正确生成操作。

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>区分大小写

最后，请记住，某些平台 （如区分大小写的文件系统
iOS 和 Android) 因此请确保使用一致的文件命名标准并确保在代码中使用的文件名称与文件系统完全匹配。 这一点尤其重要的映像和在代码中引用其他资源。
