---
title: 对 Web 服务的访问进行身份验证
description: 本指南说明如何将身份验证服务集成到 Xamarin.Forms 应用程序以使用户能够共享一个后端，同时仅有权访问他们自己的数据。 涉及的主题包括使用基本身份验证与 REST 服务中，使用 Xamarin.Auth 组件对 OAuth 标识提供程序，进行身份验证，以及使用内置身份验证机制提供的不同的提供程序。
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: df0e188efd2791b03a63c31b715ed1da77079230
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="authenticating-access-to-web-services"></a>对 Web 服务的访问进行身份验证

_本指南说明如何将身份验证服务集成到 Xamarin.Forms 应用程序以使用户能够共享一个后端，同时仅有权访问他们自己的数据。涉及的主题包括使用基本身份验证与 REST 服务中，使用 Xamarin.Auth 组件对 OAuth 标识提供程序，进行身份验证，以及使用内置身份验证机制提供的不同的提供程序。_

## <a name="authenticating-a-restful-web-servicerestmd"></a>[对 RESTful Web 服务进行身份验证](rest.md)

HTTP 支持使用多个身份验证机制来控制对资源的访问。 基本身份验证提供对资源的访问权限仅这些客户端具有正确的凭据。 本文演示如何使用基本身份验证来保护对 RESTful web 服务资源的访问。

## <a name="authenticating-users-with-an-identity-provideroauthmd"></a>[使用标识提供程序的用户进行身份验证](oauth.md)

Xamarin.Auth 是跨平台 SDK 进行身份验证用户以及存储的帐户。 它包括提供对使用如 Google、 Microsoft、 Facebook 和 Twitter 标识提供程序支持的 OAuth 身份验证器。 此文章介绍了如何使用 Xamarin.Auth 管理 Xamarin.Forms 应用程序中的身份验证过程。

## <a name="authenticating-users-with-azure-mobile-appsazuremd"></a>[使用 Azure 移动应用程序的用户进行身份验证](azure.md)

Azure Mobile Apps 使用各种外部标识提供程序来支持进行身份验证和授权应用程序用户。 权限可以然后设置上表限制为仅限经过身份验证的用户的访问。 此文章介绍了如何使用 Azure 移动应用管理 Xamarin.Forms 应用程序中的身份验证过程。

## <a name="authenticating-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[使用 Azure Active Directory B2C 的用户进行身份验证](azure-ad-b2c.md)

Azure Active Directory B2C 是面向使用者的 web 和移动应用程序的云标识管理解决方案。 本文演示如何使用 Microsoft 身份验证库 (MSAL) 和 Azure Active Directory B2C 来将使用者标识管理集成到 Xamarin.Forms 应用程序。

## <a name="integrating-azure-active-directory-b2c-with-azure-mobile-appsazure-ad-b2c-mobile-appmd"></a>[将 Azure Active Directory B2C 与 Azure 移动应用集成](azure-ad-b2c-mobile-app.md)

Azure Active Directory B2C 可以用于管理的 Azure 移动应用的身份验证工作流。 使用此方法时，标识管理体验在云中，已完全定义，并且可以修改而无需更改你的移动应用程序代码。 本文演示如何使用 Azure Active Directory B2C Xamarin.Forms 中提供身份验证和授权与 Azure Mobile Apps 实例。

## <a name="authenticating-users-with-an-amazon-simpledb-serviceawsmd"></a>[与 Amazon SimpleDB 服务的用户进行身份验证](aws.md)

Amazon SimpleDB 不提供其自己的基于资源的权限的系统。 相反，身份验证标识提供程序是针对可以用于确保用户只能 SimpleDB 域中有权访问他们自己的数据。 此文章介绍了如何限制对其自己 SimpleDB 数据的用户的访问权限。


## <a name="related-links"></a>相关链接

- [Web 服务简介](~/cross-platform/data-cloud/web-services/index.md)
- [异步支持概述](~/cross-platform/platform/async.md)
