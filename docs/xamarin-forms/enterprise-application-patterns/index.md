---
title: 使用 Xamarin.Forms 电子书的企业应用程序模式
description: 此电子书提供了用于开发自适应、 可维护性、 和可测试的 Xamarin.Forms 企业应用程序的体系结构指南。
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: ecfe99f66e16eafabc3117036ff065e3a35259c3
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994343"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>使用 Xamarin.Forms 电子书的企业应用程序模式

_开发自适应、 可维护性、 和可测试的 Xamarin.Forms 企业应用程序的体系结构指南_

![](images/cover-sml.png "使用 Xamarin.Forms 电子书的企业应用程序模式")

此电子书提供有关如何实现模型-视图-视图模型 (MVVM) 模式、 依赖关系注入、 导航、 验证和配置管理，同时保持松散耦合的指南。 此外，还有指导执行身份验证和授权与 IdentityServer，从容器化微服务和单元测试访问的数据。

## <a name="prefaceprefacemd"></a>[前言](preface.md)

本章介绍的目的和范围的指南，以及它旨在的人员。

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

企业应用的开发人员面临着多种挑战，可以在开发过程中更改应用的体系结构。 因此，很重要，以便可以修改或扩展随着时间的推移生成应用。 这种适应性的设计可能很困难，但通常涉及到离散的松散耦合组件，可以轻松地集成在一起到应用程序分区应用程序。

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

模型-视图-视图模型 (MVVM) 模式有助于完全隔离的应用程序从其用户界面 (UI) 的业务和演示文稿逻辑。 维护应用程序逻辑与 UI 之间完全分离有助于解决许多开发问题，并可以使应用程序更易于测试、 维护和改进。 它还可以显著改善代码重用机会，并允许开发人员和开发的应用程序及其相应部分时，UI 设计器更轻松地进行协作。

## <a name="dependency-injectiondependency-injectionmd"></a>[依赖关系注入](dependency-injection.md)

依赖关系注入，具体取决于这些类型的代码类型的分离。 它通常使用保存的注册和接口和抽象类型之间的映射列表的容器和实现或扩展这些类型的具体类型。

依赖关系注入容器减少通过提供一个工具用于实例化类实例和管理基于容器的配置其生存期的对象之间的耦合。 对象在创建期间，容器将该对象需要的任何依赖关系注入到它。 如果尚未创建这些依赖项，该容器创建，并将首先解析其依赖项。

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[松散耦合组件之间的通信](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类实现发布-订阅模式，允许基于消息的不太方便链接对象和类型引用的组件之间的通信。 此机制允许发布服务器和订阅服务器进行通信而无需到对方，帮助减少组件，同时还允许要进行单独开发和测试的组件之间的依赖关系的引用。

## <a name="navigationnavigationmd"></a>[导航](navigation.md)

Xamarin.Forms 包括对页面导航、 从用户的交互用户界面时，或从应用本身，由于内部逻辑驱动的状态更改时，通常会支持。 但是，导航可能很复杂，若要在应用中使用 MVVM 模式的实现。

这一章介绍`NavigationService`类，用于执行从视图模型的视图模型第一个导航。 将导航逻辑放在视图模型类意味着通过自动测试可在逻辑。 此外，视图模型然后可以实现对控件导航，以确保实施某些业务规则的逻辑。

## <a name="validationvalidationmd"></a>[验证](validation.md)

接受用户输入的任何应用程序应确保输入有效。 而不进行验证，用户可以提供会导致应用失败的数据。 验证强制实施业务规则，可防止攻击者将恶意数据注入。

在上下文的模型-视图模型的模型 (MVVM) 模式，视图模型或模型通常需要执行数据验证和信号到视图的任何验证错误，以便用户可以更正它们。

## <a name="configuration-managementconfiguration-managementmd"></a>[配置管理](configuration-management.md)

设置允许的数据的配置，请将代码中应用程序的行为的分离允许要进行更改而无需重新生成应用程序的行为。 应用程序设置数据应用程序创建和管理，并且用户设置是可自定义应用的设置，会影响应用的行为，并且不需要频繁重新调整。

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[容器化微服务](containerized-microservices.md)

微服务提供了应用程序开发和部署适用于现代云应用程序的敏捷性、 规模和可靠性要求的方法。 微服务的主要优点之一是，它们可以是扩展的独立，这意味着需要更多的处理能力或网络带宽，以支持需求，而不会不必要地缩放的区域，可以扩展特定的功能区域不会遇到更高的需求应用程序。

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[身份验证和授权](authentication-and-authorization.md)

有许多方法与 ASP.NET MVC web 应用程序进行通信的 Xamarin.Forms 应用中集成身份验证和授权。 在这里，与使用 IdentityServer 4 的容器化的标识微服务执行身份验证和授权。 IdentityServer 是用于与 ASP.NET Core 标识来执行持有者令牌身份验证集成的 ASP.NET Core 的开放源代码 OpenID Connect 和 OAuth 2.0 框架。

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[访问远程数据](accessing-remote-data.md)

许多现代的基于 web 的解决方案进行 web 服务，由 web 服务器，以便为远程客户端应用程序提供功能的使用。 Web 服务公开的操作构成 web API，并且客户端应用程序应该能够使用 web API，而不知道如何实现的数据或 API 公开的操作。

## <a name="unit-testingunit-testingmd"></a>[单元测试](unit-testing.md)

测试模型和视图模型的 MVVM 应用程序从等同于测试任何其他类，并可以使用相同的工具和技术。 但是，有一些是模型的典型的模式和视图模型类，可受益于特定的单元测试技术的。

## <a name="feedback"></a>反馈

此项目有一个社区站点，可以在其发布问题，并提供反馈。 社区站点将位于[GitHub](https://github.com/dotnet-architecture/eShopOnContainers)。 或者，有关电子书的反馈可以通过电子邮件发送到[ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
