---
title: Azure Active Directory
description: 本文档描述必须遵守才能允许移动应用程序使用 Azure Active Directory 进行身份验证的步骤。
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780057"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_注册应用程序，以便使用 Azure Active Directory_

Azure Active Directory 允许对安全资源，如文件、 链接和使用相同的组织帐户登录到他们的系统或检查其电子邮件的员工使用的 Web Api 的开发人员。

开发移动应用程序可以对与 Azure Active Directory 进行身份验证涉及三个步骤。
前两个步骤通常是相同的而不考虑你打算使用哪些服务。 第三步是为每个服务类型不同：

  1. [注册到 Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md)上*windowsazure.com*门户，然后
  2. [配置服务](~/cross-platform/data-cloud/active-directory/get-started/configure.md)。
  3. 开发移动应用程序使用的服务。

你可以访问的不同服务的示例包括：

- [Graph API](~/cross-platform/data-cloud/active-directory/graph.md)
- Web API
- Office365


## <a name="conclusion"></a>结束语

使用上面的步骤可以进行 Azure Active Directory 针对您的移动应用身份验证。 Active Directory 身份验证库 (ADAL)，可以更轻松使用更少行代码，同时保持的大部分代码相同，并因此使其可共享跨平台。



## <a name="related-links"></a>相关链接

- [Microsoft NativeClient 示例](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
