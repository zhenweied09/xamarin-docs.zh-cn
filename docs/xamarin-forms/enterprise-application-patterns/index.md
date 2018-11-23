---
title: 《Xamarin.Forms企业应用程序模式》电子书
description: 此电子书提供了用于开发可适应的、 可维护的、 和可测试的 Xamarin.Forms 企业应用程序的架构指南。
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
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>《Xamarin.Forms企业应用程序模式》电子书

_开发可适应的、 可维护的、 和可测试的 Xamarin.Forms 企业应用程序的架构指南_

![](images/cover-sml.png "《Xamarin.Forms企业应用程序模式》电子书")

此电子书提供有关如何实现Model-View-ViewModel (MVVM) 模式、 依赖关系注入、 导航、 验证和配置管理，同时保持松散耦合的指南。 此外，还有指导利用 IdentityServer 执行身份验证和授权，从容器化微服务访问数据和单元测试。

## <a name="prefaceprefacemd"></a>[前言](preface.md)

本章介绍关于本指南的目的和范围，以及它面向的读者。

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

企业应用的开发人员面临着多种挑战，这会在开发过程中更改应用的架构。 因此，构建一款随着时间的推移可以被修改或扩展的应用很重要。 这种适应性的设计可能很困难，通常涉及把应用程序分到离散的，松散耦合的组件，这些组件可以轻松地集成在一起到应用程序。

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

Model-View-ViewModel (MVVM) 模式有助于完全隔离的应用程序从其用户界面 (UI) 的业务和演示文稿逻辑。 维护应用程序逻辑与 UI 之间完全分离有助于解决许多开发问题，并可以使应用程序更易于测试、 维护和改进。 它还可以显著改善代码重用机会，并允许开发人员和 UI 设计人员在开发一个应用程序的各自部分时更轻松地进行协作。

## <a name="dependency-injectiondependency-injectionmd"></a>[依赖关系注入](dependency-injection.md)

依赖关系注入使代码无需依赖于具体类。 它通常使用一个容器来保存一个注册列表以及接口和抽象类型之间的映射，同时具体类型实现或扩展了这些类型。

依赖关系注入容器通过提供一个用于实例化类实例的工具以及基于容器的配置来管理其生存期，来减少对象之间的耦合。 在对象创建期间，容器将该对象需要的任何依赖关系注入到它。 如果尚未创建这些依赖项，该容器会创建并将首先解析其依赖项。

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[松散耦合组件之间的通信](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类实现了发布-订阅模式，允许在不方便被对象链接和类型引用的组件之间基于消息的通信。 此机制允许发布者和订阅者进行通信而无需相互引用，帮助减少组件之间的相互依赖，同时还允许这些组件被单独开发和测试。

## <a name="navigationnavigationmd"></a>[导航](navigation.md)

Xamarin.Forms 包含对页面导航的支持， 通常在用户与UI交互时，或从应用本身，在内部逻辑驱动的状态更改时。 然而，若要在使用 MVVM 模式的应用中实现，导航可能很复杂。

这一章介绍了一个`NavigationService`类，用于从ViewModel执行 “view model-first” 导航。 将导航逻辑放在ViewModel类意味着这些逻辑可以运用在自动测试。 此外，ViewModel然后可以实现逻辑来控制导航，以确保某些业务规则被强制实施。

## <a name="validationvalidationmd"></a>[验证](validation.md)

任何接受用户输入的应用程序都应确保输入是有效的。 没有进行验证，用户提供的数据可能会导致应用程序故障。 验证强制业务规则的实施，可防止攻击者将恶意数据注入。

在Model-ViewModel的模型 (MVVM) 模式的上下文中，ViewModel 或 Model 通常需要执行数据验证以及通知 View 任何的验证错误，以便用户可以更正它们。

## <a name="configuration-managementconfiguration-managementmd"></a>[配置管理](configuration-management.md)

设置允许配置一个应用程序行为的数据从代码的分离，这些行为的变更无需重新生成应用程序。 应用程序设置是应用程序创建和管理的数据，同时用户设置是一个应用程序可自定义的设置，这些设置会影响应用的行为，并且不需要频繁地重新调整。

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[容器化微服务](containerized-microservices.md)

微服务提供了一种适用于现代云应用程序敏捷性、 伸缩和可靠性要求的应用程序开发和部署方法。 微服务的主要优点之一是，它们可以独立地横向伸缩 (scaled-out)，这意味着那些需要更多的处理能力或网络带宽的特定的功能区域可以被扩展，以支持需求，避免在应用程序不必要的地方提供伸缩。

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[身份验证和授权](authentication-and-authorization.md)

有许多方法可以为 Xamarin.Forms 应用集成身份验证和授权功能，与 ASP.NET MVC web 应用程序进行通信。 在这里，身份验证和授权，由一个使用了 IdentityServer 4 的容器化身份识别的微服务来提供。 IdentityServer 是一个ASP.NET Core 下开源的 OpenID Connect 和 OAuth 2.0 框架，集成到 ASP.NET Core Identity 来提供持有者令牌身份验证。

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[访问远程数据](accessing-remote-data.md)

许多基于 web 的现代化解决方案使用 web 服务，以 web 服务器为宿主，以便为远程客户端应用程序提供功能的使用。 Web 服务公开的操作构成了 web API，并且客户端应用程序应该能够在不知道这些API公开的数据或操作是如何被实现的情况下来使用 web API。

## <a name="unit-testingunit-testingmd"></a>[单元测试](unit-testing.md)

从 MVVM 应用程序测试 Model 和 ViewModel 等同于测试任何其他类，并可以使用相同的工具和技术。 但是，有一些对于 Model 和 ViewModel 类的典型模式，可受益于特定的单元测试技术。

## <a name="feedback"></a>反馈

此项目有一个社区站点，可以在其发布问题，并提供反馈。 社区站点位于[GitHub](https://github.com/dotnet-architecture/eShopOnContainers)。 或者，有关电子书的反馈可以通过电子邮件发送到[ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
