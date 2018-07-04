---
title: 使用 Xamarin.Forms 电子书的企业应用程序模式
description: 此电子书提供开发自适应的、 可维护性、 和可测试 Xamarin.Forms 企业应用程序的体系结构指南。
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: c401465d8a57abe1d5a1cfaf9ee2616888332ea3
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242158"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>使用 Xamarin.Forms 电子书的企业应用程序模式

_开发自适应的、 可维护性、 和可测试 Xamarin.Forms 企业应用程序的体系结构指南_

![](images/cover-sml.png "使用 Xamarin.Forms 电子书的企业应用程序模式")

此电子书提供有关如何实现模型-视图-视图模型 (MVVM) 模式、 依赖关系注入、 导航、 验证和配置管理，同时保持松散耦合的指导。 此外，还有指南执行身份验证和授权与 IdentityServer，从容器化微服务和单元测试访问数据。

## <a name="prefaceprefacemd"></a>[前言](preface.md)

本章介绍的目的和范围的指南，以及它旨在的人员。

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

企业应用的开发人员面临着可能会在开发过程中更改应用程序的体系结构的多个挑战。 因此，很重要，以便可以修改或扩展随着时间的推移生成的应用程序。 有关此类适应性设计可能很困难，但通常涉及分区为离散、 松散耦合可以轻松地集成在一起到应用的组件的应用程序。

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

模型-视图-视图模型 (MVVM) 模式可帮助完全分隔其用户界面 (UI) 中的应用程序的业务和演示文稿逻辑。 维护应用程序逻辑和 UI 之间完全分离有助于解决许多开发问题，并且可以使应用程序更轻松地测试、 维护和发展。 它还将大大提高代码重用的机会，并允许开发人员和开发其相应部分的应用程序时，UI 设计器能够更轻松地进行协作。

## <a name="dependency-injectiondependency-injectionmd"></a>[依赖关系注入](dependency-injection.md)

依赖关系注入可以实现的具体类型来自依赖于这些类型的代码分离。 它通常使用实现或扩展这些类型的具体类型并包含一系列的注册和接口和抽象类型之间的映射的容器。

依赖关系注入容器减少通过提供一个工具用于实例化类实例和管理基于容器的配置其生存期的对象之间的耦合。 对象创建期间，容器将该对象需要的任何依赖关系注入到它。 如果尚未创建这些依赖关系，容器创建并首先解决它们的依赖项。

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[松散耦合组件之间的通信](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)类实现发布-订阅模式，允许基于消息的是很不方便链接对象和类型引用的组件之间的通信。 此机制允许发布服务器和订阅服务器进行通信而无到对方，有助于降低组件，同时还允许独立开发和测试的组件之间的依赖关系的引用。

## <a name="navigationnavigationmd"></a>[导航](navigation.md)

Xamarin.Forms 包括页面导航、 从用户界面时，用户的交互或从该应用程序自身，由于进行了逻辑驱动的内部状态更改时，通常会对支持。 但是，导航非常复杂，无法使用 MVVM 模式应用程序中实现。

这一章介绍`NavigationService`类，用于执行从查看模型的视图模型第一个导航。 将导航逻辑放在视图模型类意味着，通过自动测试，可以执行过逻辑。 此外，视图模型可以实现对控件导航，以确保会实施某些业务规则的逻辑。

## <a name="validationvalidationmd"></a>[验证](validation.md)

接受用户输入的任何应用程序应确保输入有效。 如果未经验证，用户可以提供将导致应用程序失败的数据。 验证强制实施业务规则并防止攻击者将恶意数据注入。

中的上下文的模型-视图模型的模型 (MVVM) 模式，视图模型或模型通常需要执行数据验证和信号到视图的任何验证错误，以便用户可以更正它们。

## <a name="configuration-managementconfiguration-managementmd"></a>[配置管理](configuration-management.md)

设置允许分离数据，用于配置从代码中，应用程序中的行为允许更改而不重新生成应用程序的行为。 应用设置是应用程序创建和管理的数据和用户设置均影响应用的行为，并且不需要频繁重新调整的应用程序的可自定义设置。

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[容器化微服务](containerized-microservices.md)

微服务提供一种方法应用程序开发和部署适用于现代云应用程序的灵活性、 缩放和可靠性要求。 微服务的主要优势之一是，它们可以是向外扩展的独立，这意味着需要更多的处理断电或网络带宽，以支持需，不进行不必要地缩放的区域，可以扩展的特定功能区域没有遇到提高的需求，应用程序。

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[身份验证和授权](authentication-and-authorization.md)

有许多方法将身份验证和授权集成到 Xamarin.Forms 应用 ASP.NET MVC web 应用程序与之通信。 在这里，使用 IdentityServer 4 容器化的标识 microservice 执行身份验证和授权。 IdentityServer 是用于与 ASP.NET Core标识来执行持有者令牌身份验证集成的 ASP.NET Core 的开放源代码 OpenID Connect 和 OAuth 2.0 框架。

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[访问远程数据](accessing-remote-data.md)

许多现代的基于 web 的解决方案进行 web 承载的服务，由 web 服务器，以便为远程客户端应用程序提供功能的使用。 Web 服务公开的操作构成 web API，并且客户端应用程序应该能够利用 web API，而不必知道如何实现的数据或 API 公开的操作。

## <a name="unit-testingunit-testingmd"></a>[单元测试](unit-testing.md)

测试模型和从 MVVM 应用程序的视图模型等同于测试任何其他类，并可以使用相同的工具和技术。 但是，有一些典型到模型的模式和视图模型类，可以受益于特定的单元测试方法。

## <a name="feedback"></a>反馈

此项目具有社区站点，你可以在其发布的问题，并提供反馈。 社区网站位于[GitHub](https://github.com/dotnet-architecture/eShopOnContainers)。 或者，可以发送有关的电子书的反馈到[ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
