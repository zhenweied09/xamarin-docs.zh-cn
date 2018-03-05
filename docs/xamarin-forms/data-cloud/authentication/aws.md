---
title: "与 Amazon SimpleDB 服务的用户进行身份验证"
description: "Amazon SimpleDB 不提供其自己的基于资源的权限的系统。 相反，身份验证标识提供程序是针对可以用于确保用户只能 SimpleDB 域中有权访问他们自己的数据。 此文章介绍了如何限制对其自己 SimpleDB 数据的用户的访问权限。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 797C91A5-9720-4DAC-89D8-5C85996584C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 85413cf223e794ad2fda093601f9221d0261af39
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="authenticating-users-with-an-amazon-simpledb-service"></a>与 Amazon SimpleDB 服务的用户进行身份验证

_Amazon SimpleDB 不提供其自己的基于资源的权限的系统。相反，身份验证标识提供程序是针对可以用于确保用户只能 SimpleDB 域中有权访问他们自己的数据。此文章介绍了如何限制对其自己 SimpleDB 数据的用户的访问权限。_

[Xamarin.Auth](https://github.com/xamarin/Xamarin.Auth)用于示例应用程序中管理用户身份验证过程并安全地将用户的帐户详细信息存储在设备上。 有关详细信息请参阅[与标识提供程序进行身份验证的用户](~/xamarin-forms/data-cloud/authentication/oauth.md)。

## <a name="allowing-an-authenticated-user-access-to-simpledb-domain-data"></a>允许对 SimpleDB 域数据的身份验证的用户访问权限

示例应用程序使用`TodoItem`模型数据的类。 若要存储`TodoItem`在必须首先将转换为 SimpleDB service 实例`List`的`ReplaceableAttribute`对象。 有关详细信息请参阅[创建 SimpleDB 对象](~/xamarin-forms/data-cloud/consuming/aws.md)。

> [!NOTE]
> 在 iOS 9 及更高版本中，应用程序传输安全 (ATS) 强制实施安全连接之间 internet 资源 （如应用程序的后端服务器） 和应用程序，从而防止意外泄露的敏感信息。 由于默认情况下，生成的 ios 9 应用中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，则会失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议和安全的 internet 资源的通信。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)。

若要确保用户有权访问他们自己的数据在 SimpleDB 域中，`ToSimpleDBReplaceableAttributes`方法将存储的其他属性`TodoItem`实例，如下面的代码示例中所示：

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    ...
    new ReplaceableAttribute () {
      Name = "User",
      Value = App.User.Email,
      Replace = true
    },
  };
}
```

此属性可确保每个项存储在 SimpleDB 域中具有的用户，用于唯一标识其数据属于的用户关联的电子邮件地址。 当通过调用检索域的内容`AmazonSimpleDBClient.SelectAsync`方法，查询表达式可确保检索仅经过身份验证的用户的项的如下面的代码示例中所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
    SelectExpression = string.Format ("SELECT * from {0} WHERE User = '{1}'", tableName, App.User.Email)
  };
  var response = await client.SelectAsync (request);
  ...
}
```

`SelectAsync`方法返回包含的项和关联的查询表达式匹配的属性集合的响应。 查询表达式可确保将检索用户的电子邮件地址匹配的项。 有关查询表达式的详细信息，请参阅[使用选择 to Create Amazon SimpleDB Queries](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html) Amazon 的网站上。

> [!NOTE]
> **请注意**： 小心地将其构造的查询表达式时遵循报价的规则。 有关详细信息，请参阅[选择用引号括起来规则](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html)Amazon 的网站上。

## <a name="summary"></a>摘要

本文介绍了如何限制对其自己的 SimpleDB 数据的用户的访问。 Amazon SimpleDB 不提供其自己的基于资源的权限的系统。 相反，身份验证标识提供程序是针对可以用于确保用户有权访问他们自己的数据 SimpleDB 域中。


## <a name="related-links"></a>相关链接

- [TodoAWSAuth （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWSAuth/)
- [使用 Amazon SimpleDB 服务](~/xamarin-forms/data-cloud/consuming/aws.md)
- [使用标识提供程序的用户进行身份验证](~/xamarin-forms/data-cloud/authentication/oauth.md)
- [Amazon Cognito 标识](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Amazon SimpleDB 开发人员文档](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [Amazon Web Services SDK for .NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
