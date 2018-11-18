---
title: 企业应用程序开发介绍
description: 本章提供企业应用程序开发介绍，并引入了 eShopOnContainers 移动应用程序。
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9deb685c92092ceb0e1c775a1e53ac1bce5a4a57
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242955"
---
# <a name="introduction-to-enterprise-app-development"></a>企业应用程序开发介绍

不考虑平台，企业应用的开发人员面临一些挑战：

-   应用的需求可随时间而变化。
-   新的业务机会以及挑战。
-   在开发过程中的持续反馈可明显的影响应用程序的机会(scope)以及需求。

考虑到这些挑战，构建可以随着时间推移能够被轻松地修改或扩展的应用程序很重要。 设计出这样的适应性可能很困难，因为它需要一种架构能同时允许应用程序的单个部分能够被独立开发以及隔离测试，而不会影响其余的部分。

许多企业应用程序都足够复杂，需要多个开发人员。 决定如何设计一个应用程序，以便多个开发人员可以有效地在不同的应用程序片段上独立工作，同时确保这些片段无缝集成到应用程序，可能会是一项重大的挑战。

传统的方法设计和构建一款应用称作 *Monolithic* 应用， 这些应用的组件之间紧密耦合并且没有清晰的分层。 通常情况下，这种 monolithic 方法会导致应用程序维持起来十分困难和低效，因为在这种应用上， 可能很难在不破坏其他组件的情况下解决 bug，并且可能难以添加新功能或替换现有功能。

应对这些挑战的一种有效补救措施是将应用划分为离散、 松散耦合的组件，这些组件可以轻松地被集成在一起到应用中。 这样的方法有多个优点：

-   它允许单个功能由不同的个人或团队来开发、 测试、 扩展和维护。
-   它有助于在应用的功能间提升重用，并清晰分离关注点；例如身份验证和数据访问等横向功能，以及如应用程序特定的业务功能等纵向功能。 这让应用程序组件之间的依赖关系和交互能够更轻松地被管理。
-   它可帮助维持角色的分离，通过允许不同的个人或团队据他们的专长，以专注于一项特定任务或功能块。 具体而言，它提供了在用户界面和应用程序的业务逻辑之间更清晰的分离。

然而，在把应用程序划分为离散、 松散耦合的组件时，有许多必须解决的问题。 这些问题包括：

-   决定如何在用户界面控件及其逻辑之间提供清晰的关注点分离。 创建 Xamarin.Forms 企业应用程序时的最重要决策之一是是否要将业务逻辑放在 code-behind 文件中，或是否在用户界面控件及其逻辑之间构建清晰的关注点分离，以使应用程序的更有可维护性和可测试性。 有关详细信息，请参阅[Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。
-   决定是否使用依赖关系注入容器。 依赖关系注入容器通过提供一个工具来构造类的实例以及他们的依赖项注入，以减少对象间的依赖耦合，以及基于容器的配置来管理他们的生命周期。 有关详细信息，请参阅[依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。
-   通信方式的选择，如平台提供的事件机制，以及那些不方便对象链接和类型引用的基于消息的组件间通信。 有关详细信息，请参阅简介[松散耦合组件之间的通信](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)。
-   决定如何在页面间导航，包括如何发起导航，以及导航逻辑应保留在哪里。 有关详细信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。
-   确定如何验证用户输入的正确性。 这样的决策必须包括如何验证用户输入，以及如何通知用户有关验证的错误。 有关详细信息，请参阅[验证](~/xamarin-forms/enterprise-application-patterns/validation.md)。
-   决定如何执行身份验证，以及如何保护带有授权的资源。 有关详细信息，请参阅[身份验证和授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md)。
-   确定如何从 web 服务访问远程数据，包括如何可靠地检索数据，以及如何缓存数据。 有关详细信息，请参阅[访问远程数据](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。
-   决定如何测试应用程序。 有关详细信息，请参阅[单元测试](~/xamarin-forms/enterprise-application-patterns/unit-testing.md)。

本指南提供有关这些问题的指导，并着重关注使用 Xamarin.Forms 构建一款跨平台企业应用程序的核心模式和架构。 本指南旨在通过针对常见 Xamarin.Forms 企业应用程序开发情景，以及通过 Model-View-ViewModel(MVVM) 模式的支撑提供了外观、 展现逻辑和实体间的分离，帮助生产出可适应的、 可维护的、 和可测试的代码。

## <a name="sample-application"></a>示例应用程序

本指南包含的示例应用，eShopOnContainers，即在线商店包含以下功能：

-   进行身份验证和针对后端服务进行授权。
-   浏览 shirts、 coffee 杯子和其他市场营销的项的目录。
-   筛选该目录。
-   对目录中的项进行排序。
-   查看用户的订单历史记录。
-   配置设置。

### <a name="sample-application-architecture"></a>示例应用程序体系结构

图 1-1 提供的示例应用程序的体系结构的高级概述。

![](introduction-images/architecture.png "eShopOnContainers 高级体系结构")

**图 1-1**: eShopOnContainers 高级体系结构

示例应用程序附带有三个客户端应用程序：

-   使用 ASP.NET Core 开发的 MVC 应用程序。
-   用角度 2 和 Typescript 开发单页面应用程序 (SPA)。 此方法用于 web 应用程序可避免执行往返与每个操作的服务器。
-   使用 Xamarin.Forms，支持 iOS、 Android 和通用 Windows 平台 (UWP) 开发移动应用程序。

有关 web 应用程序的信息，请参阅[Architecting 和开发现代 Web 应用程序使用 ASP.NET Core 和 Microsoft Azure](http://aka.ms/WebAppEbook)。

示例应用程序包括以下后端服务：

-   标识微服务，使用 ASP.NET Core 标识和 IdentityServer 中。
-   目录微服务，这是数据驱动的创建、 读取、 更新和删除 (CRUD) 服务，使用使用 EntityFramework 核心的 SQL Server 数据库。
-   排序 microservice，这是一个域驱动的服务使用域驱动的设计模式。
-   购物篮微服务使用 Redis 缓存的数据驱动 CRUD 服务。

这些后端服务作为微服务使用核心的 ASP.NET MVC，实现，并部署为在单个 Docker 主机的唯一容器。 统称为这些后端服务被指 eShopOnContainers 引用应用程序。 客户端应用与通过具象状态传输 (REST) web 界面的后端服务进行通信。 有关微服务和 Docker 的详细信息，请参阅[容器化微服务](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md)。

有关后端服务的实现的信息，请参阅[.NET 微服务： 为容器化.NET 应用程序的体系结构](https://aka.ms/microservicesebook)。

### <a name="mobile-app"></a>移动应用程序

此指南重点介绍构建跨平台企业应用使用 Xamarin.Forms，并使用 eShopOnContainers 移动应用程序作为示例。 图 1-2 显示提供前面介绍的功能 eShopOnContainers 移动应用中的页。

[![](introduction-images/screenshots.png "EShopOnContainers 移动应用")](introduction-images/screenshots-large.png#lightbox "eShopOnContainers 移动应用程序")

**图 1-2**: eShopOnContainers 移动应用程序

移动应用程序使用提供 eShopOnContainers 引用应用程序的后端服务。 但是，它可以被配置为使用模拟的用户的用户想要避免部署后端服务的服务中的数据。

EShopOnContainers 移动应用程序执行以下 Xamarin.Forms 功能：

-   XAML
-   控件
-   绑定
-   转换器
-   样式
-   动画
-   命令
-   行为
-   触发器
-   效果
-   自定义呈现器
-   MessagingCenter
-   自定义控件

有关此功能的详细信息，请参阅[Xamarin.Forms 文档](~/xamarin-forms/index.yml)，和[具有 Xamarin.Forms 创建移动应用](https://aka.ms/xamebook)。

此外，对于某些 eShopOnContainers 移动应用程序中的类提供单元测试。

#### <a name="mobile-app-solution"></a>移动应用程序解决方案

EShopOnContainers 移动应用程序解决方案将源代码和其他资源组织到项目中。 所有项目使用文件夹组织为类别的源代码和其他资源。 下表概述了构成 eShopOnContainers 移动应用程序的项目：

|项目|描述|
|--- |--- |
|eShopOnContainers.Core|此项目是可移植类库 (PCL) 项目包含共享的代码和共享的 UI。|
|eShopOnContainers.Droid|此项目包含 Android 的特定代码并且 Android 应用程序的入口点。|
|eShopOnContainers.iOS|此项目保存特定代码的 iOS 并且 iOS 应用程序的入口点。|
|eShopOnContainers.UWP|此项目保存通用 Windows 平台 (UWP) 的特定代码，并且是 Windows 应用程序的入口点。|
|eShopOnContainers.TestRunner.Droid|此项目是 eShopOnContainers.UnitTests 项目 Android 测试运行程序。|
|eShopOnContainers.TestRunner.iOS|此项目是 eShopOnContainers.UnitTests 项目 iOS 测试运行程序。|
|eShopOnContainers.TestRunner.Windows|此项目是 eShopOnContainers.UnitTests 项目通用 Windows 平台测试运行程序。|
|eShopOnContainers.UnitTests|此项目包含 eShopOnContainers.Core 项目的单元测试。|

可以在包含很少或没有修改任何 Xamarin.Forms 应用中重复使用从 eShopOnContainers 移动应用程序类。

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core 项目

EShopOnContainers.Core PCL 项目中包含的以下文件夹：

|文件夹|描述|
|--- |--- |
|动画|包含可启用动画在 XAML 中使用的类。|
|行为|包含公开要查看类的行为。|
|控件|包含应用使用的自定义控件。|
|转换器|包含将自定义逻辑应用于绑定的值转换器。|
|效果|包含`EntryLineColorEffect`类，该类用于更改特定的边框颜色`Entry`控件。|
|异常|包含自定义`ServiceAuthenticationException`。|
|扩展|包含用于扩展方法`VisualElement`和`IEnumerable`类。|
|帮助器|包含用于应用程序的帮助器类。|
|模型|包含应用程序的模型类。|
|属性|包含`AssemblyInfo.cs`，.NET 程序集元数据文件。|
|服务|包含接口和实现提供给应用程序的服务的类。|
|触发器|包含`BeginAnimation`触发器，这将用于调用 XAML 中的动画。|
|验证|包含所涉及的验证数据输入的类。|
|ViewModels|包含被公开到页的应用程序逻辑。|
|视图|包含应用程序的页。|

##### <a name="platform-projects"></a>平台项目

平台项目包含效果实现、 自定义呈现器实现中和其他特定于平台的资源。

## <a name="summary"></a>总结

Xamarin 的跨平台移动应用开发工具和平台提供全面的解决方案 B2E、 B2B 和 B2C 移动客户端应用程序，从而能够在所有目标平台 （iOS、 Android 和 Windows） 并帮助降低间共享代码总拥有成本。 应用可以共享其用户界面和应用程序逻辑代码，同时保留本机平台外观和感觉。

企业应用的开发人员面临着可能会在开发过程中更改应用程序的体系结构的多个挑战。 因此，很重要，以便可以修改或扩展随着时间的推移生成的应用程序。 有关此类适应性设计可能很困难，但通常涉及分区为离散、 松散耦合可以轻松地集成在一起到应用的组件的应用程序。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
