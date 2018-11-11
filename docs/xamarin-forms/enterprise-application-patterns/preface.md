---
title: 企业应用程序开发前言
description: 本章提供《Xamarin.Forms企业应用程序模式》的前言。
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fd085d2fb12e82233f6d3be2e2773a84539f837b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242363"
---
# <a name="preface-to-enterprise-app-development"></a>加到企业应用程序开发

此电子书为使用 Xamarin.Forms 构建跨平台企业应用提供指导。 Xamarin.Forms 是一个跨平台的 UI 工具包，允许开发人员轻松地创建可以跨平台共享的，包括 iOS、 Android 和通用 Windows 平台 (UWP) 的原生用户界面布局。 它为企业到员工 (B2E) 企业到企业 (B2B) 和企业对消费者 (B2C) 应用，提供了一个全面的解决方案，包括能够在所有目标平台间共享代码的能力，并有助于降低总拥有成本 (TCO) 。

本指南提供开发可适应的、 可维护的、 和可测试的 Xamarin.Forms 企业应用程序的架构指南。 是提供有关如何实现 MVVM、 依赖关系注入、 导航、 验证和配置管理，同时保持松散耦合的指南。 此外，还有提供利用 IdentityServer 执行身份验证和授权，从容器化微服务访问数据和单元测试的指南。

指南附带了[eShopOnContainers 移动应用](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)的源代码，以及[eShopOnContainers 参考应用](https://github.com/dotnet-architecture/eShopOnContainers)的源代码。 eShopOnContainers 移动应用是一款使用 Xamarin.Forms 开发的跨平台企业应用，它与名为 eShopOnContainers 参考应用程序的一系列容器化微服务连接。 然而，eShopOnContainers 移动应用程序可以被配置为使用模拟服务中的数据，从而避免部署容器化微服务。

## <a name="whats-left-out-of-this-guides-scope"></a>本书的适用范围

本指南面向已熟悉 Xamarin.Forms 的读者。 有关 Xamarin.Forms 的详细介绍，请参阅[Xamarin.Forms 文档](~/xamarin-forms/index.yml)，和[使用 Xamarin.Forms 创建移动应用](https://aka.ms/xamebook)。

该指南可以作为[《.NET 微服务： 容器化.NET 应用程序架构》](https://aka.ms/microservicesebook)的补充，其中重点介绍开发和部署容器化微服务。 其他值得阅读的指南包括[《使用 ASP.NET Core 和 Microsoft Azure 架构和开发现代 Web 应用程序》](http://aka.ms/WebAppEbook)，[《Docker容器化应用生命周期与 Microsoft 平台和工具》](http://aka.ms/dockerlifecycleebook)，和[《针对移动应用程序开发的 Microsoft 平台和工具》](http://aka.ms/MobAppDev/StndPDF)。

## <a name="who-should-use-this-guide"></a>哪些人应该使用本指南

本指南的主要受众是希望学习如何利用 Xamarin.Froms 架构和实现跨平台企业应用程序的开发人员和架构师。

另一部分受众是技术决策人员，他们希望在决定使用哪些方法来进行 Xamarin.Forms 跨平台企业应用程序开发之前，得到架构上和技术上概览。

## <a name="how-to-use-this-guide"></a>如何使用本指南

本指南重点介绍使用 Xamarin.Forms 构建跨平台企业应用。 在这种情况下，应该完整阅读本指南以对此类应用程序有基本了解以及其技术上的考虑。 本指南，以及其示例应用，还可以充当用于创建新的企业应用程序的起点或参考。使用相关的示例应用程序作为一个模板，或查看如何组织一个应用的组件。 然后，回头参考本指南中关于架构上指引。

本指南可随意转发给团队成员，有助于其对使用 Xamarin.Forms 跨平台企业应用程序开发有大致了解。 确保每个人工作在使用一组通用的术语和原则的基础上，可以帮助团队确保一致的应用程序架构模式和实践。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
