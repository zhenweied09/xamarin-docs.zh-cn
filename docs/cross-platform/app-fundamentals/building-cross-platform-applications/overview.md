---
title: 构建跨平台应用程序概述
description: 本文档提供构建跨平台应用程序的高级概述。 它讨论的 C#，如 MVC/MVVM 和本机 Ui 的设计模式的值。
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 1eb308e0095c29d8ab0d0bdf1f74b807fd2ab97f
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780778"
---
# <a name="building-cross-platform-applications-overview"></a>构建跨平台应用程序概述

本指南介绍了 Xamarin 平台以及如何构建一个跨平台应用程序，来最大化代码重用，并在所有主要移动平台上提供高质量本机体验： iOS、 Android 和 Windows Phone。

本文档中使用的方法是通常适用于高效应用和游戏应用程序，但是的重点是工作效率和实用程序 （非游戏应用程序）。 请参阅[简介 MonoGame 文档](~/graphics-games/monogame/introduction/index.md)或了解[Visual Studio Tools for Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity)有关跨平台游戏开发指南。

短语"编写-一次，运行 everywhere"通常用于 extol 的优点的单个基本代码运行多个平台上不被修改。 虽然它有代码重用的好处，这种方法通常会导致具有最低公分的功能集的应用程序和一般查看用户界面不适合的很好地到任何目标平台。

Xamarin 不只是"写入的一次，运行无处不在"平台，因为其优点之一是能够实现专门为每个平台的本机用户界面。 但是，仍可以共享的精心设计的非用户大多数接口代码，并获取这两个领域的最佳： 编写数据存储和业务逻辑代码一次，并显示每个平台上的本机 Ui。 本文档讨论了一种通用体系结构方法，以实现此目标。

下面是用于创建跨平台的 Xamarin 应用的关键点的摘要：

-   **使用 C#** -在 C# 中编写你的应用。 可移植到 iOS 和 Android 很容易，使用 Xamarin 和显然在 Windows 应用中使用 C# 中编写的现有代码。
-   **利用 MVC 或 MVVM 设计模式**-开发使用模型/视图/控制器模式的应用程序的用户界面。 使用模型/视图/控制器方法或模型/视图/视图模型方法将应用程序设计没有"模型"与其余之间形成清晰的隔离。 确定你的应用程序的哪些部分将使用的每个平台 (iOS、 Android、 Windows、 Mac) 的本机用户界面元素，并使用此原则拆分成两个组件的应用程序:"核心"和"用户界面"。
-   **生成本机 Ui** -每个特定于操作系统的应用程序提供了不同的用户界面层 (版本中实现 C# 的协助的本机 UI 设计工具):

1.  在 iOS 中，使用 UIKit Api 创建本机外观的应用程序，可以选择利用 Xamarin 的 iOS 设计器直观地创建你的 UI。
1.  在 Android 上，使用 Android.Views 创建本机外观的应用程序，利用 Xamarin 的 UI 设计器。
1.  在 Windows 上你将使用 XAML 演示文稿层，在 Visual Studio 或 Blend 的 UI 设计器中创建的。
1.  在 Mac 上你将在 Xcode 中创建的表示层使用情节提要。

Xamarin.Forms 项目所有平台上受支持，并允许您创建可以在使用 Xamarin.Forms XAML 的平台之间共享的用户界面。 

代码重用的量将取决于很大程度上多少代码就会保留在共享的内核和多少代码是用户界面特定。 核心代码是任何不直接与用户交互，但改为提供应用程序的部分，将收集并显示此信息的服务。

若要增加的代码重新使用量，你可以采用如跨所有这些系统提供公共服务的跨平台组件：

1.   [SQLite net](https://www.nuget.org/packages/sqlite-net-pcl/)的本地 SQL 存储区，
1.   [Xamarin 插件](https://xamarin.com/plugins)用于访问特定于设备的功能，包括摄像头、 联系人和地理位置，
1.   [NuGet 包](https://nuget.org)如也是与 Xamarin 项目兼容[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)，
1.  用于网络和 web 服务、 IO 等使用.NET framework 功能。


这些组件的某些实现中*Tasky*案例研究。

 <a name="Separate_Reusable_Code_into_a_Core_Library" />


## <a name="separate-reusable-code-into-a-core-library"></a>将可重用的代码单独放入核心库

通过这一原则的责任分离通过分层应用程序体系结构，然后将移动到可重用的核心库的平台不可知的核心功能，你可以最大化跨平台下, 图为共享的代码演示：

 ![](overview-images/layers2.png "通过这一原则的责任分离通过分层应用程序体系结构，然后将移动到可重用的核心库的平台不可知的核心功能，你可以最大化跨平台共享代码")

 <a name="Case_Studies" />


## <a name="case-studies"></a>案例研究

没有一个案例研究随附本文档中 – *Tasky Pro*。 每个案例研究讨论在现实世界的示例中的此文档中所述的概念的实现。 代码是开源并在上可用[github](https://github.com/xamarin/mobile-samples/)。
