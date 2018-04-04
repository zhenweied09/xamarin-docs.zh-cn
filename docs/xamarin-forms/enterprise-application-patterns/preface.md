---
title: 加
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: e9dd5f663fc98ac1e869721517e9a3d0fb2033bb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="preface"></a>加

此电子书构建跨平台企业应用使用 Xamarin.Forms 提供指导。 Xamarin.Forms 是一个跨平台 UI 工具包，允许开发人员轻松地创建本机用户可以共享跨平台，包括 iOS、 Android 和通用 Windows 平台 (UWP) 的接口布局。 它对于业务到员工 (B2E) 企业到企业 (B2B) 和企业对消费者 (B2C) 应用，提供能够在所有目标平台间共享代码，并有助于降低总拥有成本 (TCO) 提供全面的解决方案。

本指南提供开发自适应的、 可维护性、 和可测试 Xamarin.Forms 企业应用程序的体系结构指南。 指南提供有关如何实现 MVVM、 依赖关系注入、 导航、 验证和配置管理，同时保持松散耦合。 此外，还有指南执行身份验证和授权与 IdentityServer，从容器化微服务和单元测试访问数据。

指南附带源代码[eShopOnContainers 移动应用](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)，和源代码， [eShopOnContainers 引用应用](https://github.com/dotnet-architecture/eShopOnContainers)。 EShopOnContainers 移动应用是使用 Xamarin.Forms，后者将连接到名为 eShopOnContainers 引用应用程序的容器化微服务的一系列开发跨平台企业应用。 但是，eShopOnContainers 移动应用程序可以配置为使用模拟的用户的用户想要避免部署容器化微服务的服务中的数据。

## <a name="whats-left-out-of-this-guides-scope"></a>剩下的本指南的作用域外

本指南面向已熟悉使用 Xamarin.Forms 的读者。 Xamarin.Forms 的详细介绍，请参阅[Xamarin.Forms 文档](~/xamarin-forms/index.yml)，和[具有 Xamarin.Forms 创建移动应用](https://aka.ms/xamebook)。

该指南可以补充[.NET 微服务： 为容器化.NET 应用程序的体系结构](https://aka.ms/microservicesebook)，其中重点介绍开发和部署容器化微服务。 其他值得读取的指南包括[Architecting 和开发现代 Web 应用程序使用 ASP.NET 核心和 Microsoft Azure](http://aka.ms/WebAppEbook)，[容器 Docker 应用生命周期内使用 Microsoft 平台和工具](http://aka.ms/dockerlifecycleebook)，和[Microsoft 平台和移动应用程序开发的工具](http://aka.ms/MobAppDev/StndPDF)。

## <a name="who-should-use-this-guide"></a>哪些人应该使用本指南

本指南的受众主要是开发人员和架构师想要了解如何设计并实施一些使用 Xamarin.Forms 的跨平台企业应用。

辅助受众是技术决策者，他们希望使用 Xamarin.Forms 的跨平台企业应用程序开发为选择的方法上做出决定之前接收体系结构和技术概述。

## <a name="how-to-use-this-guide"></a>如何使用本指南

本指南重点介绍构建跨平台企业应用使用 Xamarin.Forms。 在这种情况下，它应读取整个奠定了解此类应用程序和其技术注意事项。 指南中的，以及其示例应用，还可以充当的起始点或用于创建新的企业应用程序的引用。 对于新的应用，或若要了解如何组织应用的组件部分，请使用相关的示例应用程序作为模板。 然后，回头参考有关体系结构指南的本指南。

随意转发给团队成员，以帮助确保使用 Xamarin.Forms 的跨平台企业应用程序开发有大致了解本指南。 具有使用从一组通用的术语和基础原则的每个人都可以帮助确保一致的应用程序的体系结构模式和实践。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
