---
title: 数据和云服务
description: 本指南将说明如何执行此操作和 Xamarin.Forms 应用程序可以使用 web 服务使用各种技术，实现。
ms.prod: xamarin
ms.assetid: 0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 6e67820fa83ddea46f934b4eaedde2c6334f9cc6
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="data--cloud-services"></a>数据和云服务

_本指南将说明如何执行此操作和 Xamarin.Forms 应用程序可以使用 web 服务使用各种技术，实现。_

在 Xamarin 平台上的跨平台 web 服务使用的简介，请参阅[介绍了 Web 服务](~/cross-platform/data-cloud/web-services/index.md)。

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[了解示例](~/xamarin-forms/data-cloud/walkthrough.md)

本文提供了演示如何与另一个 web 服务进行通信的 Xamarin.Forms 示例应用程序的演练。 涵盖的主题包括剖析应用程序、 页、 数据模型中，并调用 web 服务操作。

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[使用 Web 服务](~/xamarin-forms/data-cloud/consuming/index.md)

本指南演示如何与另一个 web 服务以提供通信创建、 读取、 更新和删除 (CRUD) 到 Xamarin.Forms 应用程序的功能。 涵盖的主题包括与通信[ASMX 服务](consuming/asmx.md)， [WCF 服务](consuming/wcf.md)， [REST 服务](consuming/rest.md)，和[Azure Mobile Apps](consuming/azure.md)。

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[对 Web 服务的访问进行身份验证](~/xamarin-forms/data-cloud/authentication/index.md)

本指南说明如何将身份验证服务集成到 Xamarin.Forms 应用程序以使用户能够共享一个后端，同时仅有权访问他们自己的数据。 涵盖的主题包括[与 REST 服务中使用基本身份验证](authentication/rest.md)，[使用 Xamarin.Auth 组件对 OAuth 标识提供程序进行身份验证](authentication/oauth.md)，且使用内置身份验证机制提供[Azure Mobile Apps](authentication/azure.md)。

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[将数据与 Web 服务同步](sync/index.md)

此文章介绍了如何向 Xamarin.Forms 应用程序添加脱机同步功能。 脱机同步允许用户交互与移动应用程序、 查看、 添加或修改数据，甚至在没有网络连接。 更改将存储在本地数据库中，并且一旦设备处于联机状态，可以与 web 服务同步所做的更改。

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[发送推送通知](push-notifications/index.md)

本文演示如何向 Xamarin.Forms 应用程序添加推送通知。 Azure 通知中心提供可缩放的推送基础结构用于发送移动推送通知从任意后端向任何移动平台，同时无后端不必与不同的平台通知系统通信的复杂性。

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[在云中存储文件](storage/index.md)

本文演示如何使用 Xamarin.Forms 将文本和二进制数据存储在 Azure 存储空间，以及如何访问数据。 Azure 存储是一种可扩展的云存储解决方案，可以用于存储非结构化和结构化数据。

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[在云中搜索数据](search/index.md)

本文演示如何使用 Microsoft Azure 搜索库将 Azure Search 集成到 Xamarin.Forms 应用程序。 Azure 搜索是云服务，提供了索引和查询上载的数据的功能。 这将删除基础结构要求和传统上与应用程序中实现搜索功能关联的搜索算法复杂性。

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[在文档数据库中存储数据](cosmosdb/index.md)

本指南演示如何使用 Azure Cosmos DB 标准.NET 客户端库来将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序。 Azure Cosmos DB 文档数据库是提供对 JSON 文档，提供快速、 高度可用、 可缩放数据库服务需要无缝缩放和全局复制的应用程序的较低的延迟访问的 NoSQL 数据库。

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[通过认知服务添加智能](cognitive-services/index.md)

本指南说明如何在 Xamarin.Forms 应用程序中使用的一些 Microsoft 认知服务 Api。 认知服务是一套 Api、 Sdk 和供开发人员可以通过添加功能，如面部识别、 语音识别和语言理解，使其应用程序更智能的服务。
