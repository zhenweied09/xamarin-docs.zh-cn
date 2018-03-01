---
title: "第 2 部分-体系结构"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: 25cd374ef2b3026f5ac7242ee076c8593ce806da
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="part-2---architecture"></a>第 2 部分-体系结构

生成跨平台应用的关键原则是代码的创建适于本身进行的跨平台共享最大化的体系结构。 遵守以下对象面向编程原则可帮助生成构架良好的应用程序：

-   **封装**– 确保类和甚至体系结构层仅公开执行其所需的最小 API 函数，并隐藏实现细节。 在类级别中，这意味着对象作为黑色框，并使用代码不需要将了解如何在完成的任务。 在体系结构级别，这意味着实现鼓励安排代表更抽象的层中的代码更复杂的交互的简化的 API 外观类似的模式。 这意味着，UI 代码 （例如） 只应为负责显示的屏幕和接受用户输入;和永远不会直接与数据库交互。 同样的数据访问代码应只读取和写入到数据库，但是永远不会直接与按钮或标签进行交互。
-   **职责分离**– 确保每个组件 (同时在体系结构和类级别) 都有清晰且明确定义的用途。 每个组件应执行其已定义的任务，并公开该功能通过为需要使用它的其他类可以访问的 API。
-   **多态性**– 到接口 （或抽象类） 支持多个实现表示可以编写和跨平台，共享时仍与特定于平台的功能进行交互核心代码的编程。


自然的结果是实际或抽象实体具有单独的逻辑层之后制定的应用。 将代码分离到层使应用程序更易于理解，测试和维护。 建议每层中的代码是物理上独立 （是目录或甚至分隔项目非常大的应用程序），以及逻辑上独立地 （使用命名空间）。

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>典型的应用程序层级

在本文档和案例研究我们指以下六个应用程序层：

-   **数据层**– 非易失性数据暂留，可能是一个 SQLite 数据库但无法使用 XML 文件或任何其他合适的机制来实现。
-   **数据访问层**– 提供创建、 读取、 更新、 删除 (CRUD) 访问而无需公开给调用方的实现详细信息数据的数据层的包装。 例如，DAL 可能包含 SQL 语句的查询或更新数据，但引用的代码不需要知道此。
-   **业务层**– （有时称为业务逻辑层或 BLL） 包含业务实体定义 （模型） 和业务逻辑。 适合业务外观模式。
-   **服务访问层**– 用于在云中访问服务： 从复杂的 web 服务 (REST，JSON，WCF) 的数据和映像的简单检索到从远程服务器。 封装的网络的行为，并提供一个简单的 API，将使用的应用程序和用户界面层。
-   **应用程序层**– 通常是特定于平台 （通常不在平台之间共享） 或代码的代码是特定于应用程序 （不通常可重用）。 是否要将代码放在应用程序层与 UI 层中的很好的测试是 （a） 确定此类是否具有任何实际显示控件或 （b） 是否它无法之间共享多个屏幕或设备 （如。 iPhone 和 iPad）。
-   **用户界面 (UI) 层**– 面向用户的层中，包含屏幕，小组件，控制器用于管理它们。


应用程序可能不一定包含所有层 – 例如服务访问层将不存在的应用程序不会访问网络资源中。 一个非常简单的应用程序可能会将数据层和数据访问层合并，因为此操作是非常基本。

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>移动软件的常见模式

模式是捕获的常见问题的定期解决方案的建立的方法。 有几个关键的模式，可在生成更容易维护/可以理解的移动应用程序中，了解。

-   **模型中，视图中，视图模型 (MVVM)** – 模型-视图-视图模型的模式是常用于支持数据绑定，如 Xamarin.Forms 的框架。 它已普及，并用通过启用 XAML 的 Windows Presentation Foundation (WPF) 和 Silverlight; 等的 Sdk其中 ViewModel 充当的中间计算机之间的数据 （模型） 和通过数据绑定和命令的用户界面 （视图）。
-   **模型中，视图中，控制器 (MVC)** – 一种常见和通常容易产生误解模式，MVC 构建用户界面时最常使用和提供用户界面屏幕 （视图），其背后的引擎，它处理的实际定义之间的分隔交互 （控制器），并填充它 （模式） 的数据。 因此，了解此模式的核心在于的视图和控制器和模型都实际是完全可选的功能。 MVC 是对于 iOS 应用程序的常用方法。
-   **业务外观**– 又称管理器模式，为复杂的操作提供了简化的入口点。 例如，在任务跟踪应用程序，你可能必须`TaskManager`类使用的方法，如`GetAllTasks()`， `GetTask(taskID)` ， `SaveTask (task)` ，等等。`TaskManager`类提供到内部工作情况，实际保存/检索的任务对象的外观。
-   **Singleton** – 单独模式提供一种方法可以根本不存在单个实例的特定对象。 例如，在使用 SQLite 在移动应用程序时，你仅想的数据库的一个实例。 一种简单的方法，以确保这是使用单独模式。
-   **提供程序**– 一种模式创造由 Microsoft （可能是类似于策略或基本依赖关系注入） 以鼓励跨 Silverlight、 WPF 和 WinForms 应用程序的代码重新使用。 共享的代码可以编写针对接口或抽象类，并编写并使用了代码时，会传入特定于平台的具体实现。
-   **异步**– 无法与 Async 关键字，当长时间运行的工作需要不阻止的用户界面或当前处理的情况下执行时使用模式的异步相混淆。 在最简单的形式的异步模式只需描述，应启动长时间运行的任务在另一个线程 （或类似的线程抽象，如任务），当前线程时继续处理并侦听的后台进程响应的并返回数据和/或状态时将更新 UI。


每个模式将检查以其实际用途如下所示案例研究的更多详细信息。 Wikipedia 提供更多详细的说明[MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel)， [MVC](https://en.wikipedia.org/wiki/Model–view–controller)，[外观](http://en.wikipedia.org/wiki/Facade_pattern)， [Singleton](http://en.wikipedia.org/wiki/Singleton_pattern)，[策略](http://en.wikipedia.org/wiki/Strategy_pattern)和[提供程序](http://en.wikipedia.org/wiki/Provider_model)模式 (和[设计模式](http://en.wikipedia.org/wiki/Design_Patterns)通常)。
