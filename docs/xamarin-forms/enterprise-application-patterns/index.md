---
title: 《使用 Xamarin.Forms 的企业应用程序模式》电子书
description: 此电子书提供了用于开发具有自适应性、可维护性和可测试性特点的 Xamarin.Forms 企业应用程序的体系结构指南。
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: f972a32f8daf920f2121e5aa56923c0f3a7f808a
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528437"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>《使用 Xamarin.Forms 的企业应用程序模式》电子书

_开发具有自适应性、可维护性和可测试性特点的 Xamarin.Forms 企业应用程序的体系结构指南_

![](images/cover-sml.png "《使用 Xamarin.Forms 的企业应用程序模式》电子书")

此电子书提供的指南介绍了如何在实现 Model-View-ViewModel (MVVM) 模式、依赖关系注入、导航、验证和配置管理的同时保持松散耦合。另外还指导用户利用 IdentityServer 执行身份验证和授权、从容器化微服务访问数据，以及进行单元测试。

## <a name="prefaceprefacemd"></a>[前言](preface.md)

本章介绍本指南的目的和范围，以及它面向的读者。

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

企业应用的开发人员面临着多种挑战，可能需要在开发过程中更改应用的体系结构。因此，构建一款随着时间的推移可以进行修改或扩展的应用很重要。这种自适应性的设计可能很困难，但通常需要将应用拆分成离散的、松散耦合的组件，这些组件可以轻松地集成在一起，组合成一个应用。

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

Model-View-ViewModel (MVVM) 模式有助于将应用程序的业务和呈现逻辑与其用户界面 (UI) 完全隔离。确保应用程序逻辑与 UI 完全隔离有助于解决许多开发问题，可以使应用程序更易于测试、维护和改进。它还可以大大提高代码重用机会，让开发人员和 UI 设计人员在开发各自的应用部分时更轻松地进行协作。

## <a name="dependency-injectiondependency-injectionmd"></a>[依赖关系注入](dependency-injection.md)

依赖关系注入使具体的类型与依赖这些类型的代码解偶联。它通常使用一个容器来保存一个列表，其中包含注册、接口和抽象类型之间的映射，以及用于实现或扩展这些类型的具体类型。

依赖关系注入容器提供一个设施来实例化类实例并根据容器配置来管理实例的生存期，从而减少对象之间的耦合。在对象创建期间，容器将对象需要的任何依赖关系都注入到对象中。如果尚未创建这些依赖项，则容器会首先创建并解析其依赖项。

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[松散耦合组件之间的通信](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类实现了发布-订阅模式，允许在不方便按对象和类型引用进行链接的组件之间进行基于消息的通信。此机制允许发布者和订阅者进行通信而无需相互引用，有助于减少组件之间的依赖，同时还允许单独开发和测试这些组件。

## <a name="navigationnavigationmd"></a>[导航](navigation.md)

Xamarin.Forms 包括对页面导航的支持。通常情况下，在用户与 UI 交互时需要该导航，或者在内部逻辑驱动的状态更改的情况下，应用本身需要该导航。然而，在使用 MVVM 模式的应用中，导航实现起来可能很复杂。

这一章介绍了一个 `NavigationService` 类，用于从视图模型执行视图模型优先导航。将导航逻辑放在视图模型类中意味着该逻辑可以通过自动化测试来执行。另外，视图模型此后可以通过实现逻辑来控制导航，以确保强制实施某些业务规则。

## <a name="validationvalidationmd"></a>[验证](validation.md)

任何接受用户输入的应用都应确保输入有效。如果没有进行验证，用户提供的数据可能会导致应用故障。验证可以强制实施业务规则，防止攻击者注入恶意数据。

在 Model-View-ViewModel (MVVM) 模式的上下文中，通常需要使用 ViewModel 或 Model 来执行数据验证并在视图上将验证错误指示出来，方便用户更正。

## <a name="configuration-managementconfiguration-managementmd"></a>[配置管理](configuration-management.md)

设置允许将配置应用行为的数据与代码分离，因此可以在不重新生成应用的情况下更改行为。应用设置是应用创建并管理的数据，而用户设置是应用的可自定义设置，这些设置影响应用的行为，但不需要频繁地重新调整。

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[容器化微服务](containerized-microservices.md)

微服务提供了一种符合现代云应用程序敏捷性、规模和可靠性要求的应用程序开发和部署方法。微服务的一大优点是，它们可以独立地横向扩展，这意味着你可以根据需求扩展那些需要更多处理能力或网络带宽的特定功能区域，避免在不必要的应用程序功能区域进行扩展。

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[身份验证和授权](authentication-and-authorization.md)

可以通过许多方法将身份验证和授权功能集成到与 ASP.NET MVC Web 应用程序通信的 Xamarin.Forms 应用中。在这里，身份验证和授权由一个使用 IdentityServer 4 的容器化身份微服务执行。IdentityServer 是一个适用于 ASP.NET Core 的开源 OpenID Connect 和 OAuth 2.0 框架，可以与 ASP.NET Core Identity 集成，用于执行持有者令牌身份验证。

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[访问远程数据](accessing-remote-data.md)

许多基于 Web 的现代化解决方案使用 Web 服务器托管的 Web 服务来为远程客户端应用程序提供功能。 Web 服务公开的操作可构成 Web API，并且客户端应用应能够利用 Web API，但不需要了解该 API 公开的数据或操作的实现方式。

## <a name="unit-testingunit-testingmd"></a>[单元测试](unit-testing.md)

从 MVVM 应用程序测试 Model 和 ViewModel 与测试任何其他类是一样的，可以使用相同的工具和技术。但是，有一些针对 Model 和 ViewModel 类的典型模式，这些模式可能受益于特定的单元测试技术。

## <a name="feedback"></a>反馈

此项目有一个社区站点，可以在其中发布问题并提供反馈。社区站点位于 [GitHub](https://github.com/dotnet-architecture/eShopOnContainers) 上。 也可以将有关电子书的反馈通过电子邮件发送到[ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
