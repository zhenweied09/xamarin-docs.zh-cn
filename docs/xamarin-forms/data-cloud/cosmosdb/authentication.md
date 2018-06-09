---
title: 使用 Azure Cosmos DB 文档数据库的用户进行身份验证
description: 此文章介绍了如何将访问控制与 Azure Cosmos DB 分区集合结合起来，以便用户只能访问自己在 Xamarin.Forms 应用程序中的文档。
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 031a48e5e10100b2c57ac067a0dda916c93d20da
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241606"
---
# <a name="authenticating-users-with-an-azure-cosmos-db-document-database"></a>使用 Azure Cosmos DB 文档数据库的用户进行身份验证

_Azure Cosmos DB 文档数据库支持已分区的集合，可以跨多个服务器和分区，同时支持无限的存储和吞吐量。此文章介绍了如何组合使用分区集合，使用访问控制，以便用户只能访问自己在 Xamarin.Forms 应用程序中的文档。_

## <a name="overview"></a>概述

创建分区的集合时，必须指定分区键和文档具有相同的分区键将存储在同一分区中。 因此，指定用户的标识用作分区键将导致分区的集合，将仅存储为该用户的文档。 这还可以确保 Azure Cosmos DB 文档数据库会随着用户数和项增加。

任何集合，必须授予访问权限和 SQL API 的访问控制模型定义两种类型的访问构造：

- **主密钥**启用在 Cosmos DB 帐户中，所有资源的完全管理访问权限和创建 Cosmos DB 帐户时创建。
- **资源令牌**捕获的数据库用户和用户对某个特定的 Cosmos DB 资源，例如集合或文档拥有的权限之间的关系。

公开一个主密钥打开都会向可能的恶意或负面使用 Cosmos DB 帐户。 但是，Azure Cosmos DB 资源令牌提供一种允许客户端读取、 写入和删除 Azure Cosmos DB 帐户根据授予的权限中的特定资源的安全机制。

向请求典型的方法，生成，并将资源令牌传递到移动应用程序是使用资源令牌代理。 下图显示示例应用程序如何使用资源令牌的代理来管理对文档数据库数据的访问的高级的概述：

![](authentication-images/documentdb-authentication.png "文档数据库身份验证过程")

资源令牌 broker 是承载在 Azure App Service，拥有 Cosmos 数据库帐户主密钥的中间层 Web API 服务。 示例应用程序使用的资源令牌的代理管理对文档数据库数据的访问，如下所示：

1. 登录情况下，Xamarin.Forms 应用程序将联系 Azure 应用程序服务，以启动身份验证流。
1. Azure App Service 执行与 Facebook OAuth 身份验证流。 身份验证流完成后，Xamarin.Forms 应用程序会收到一个访问令牌。
1. Xamarin.Forms 应用程序使用访问令牌请求从资源令牌 broker 资源令牌。
1. 资源令牌 broker 使用的访问令牌从 Facebook 请求用户的标识。 然后，用户的标识用于请求从 Cosmos DB，用于授予对经过身份验证的用户的已分区集合的读/写访问的资源令牌。
1. Xamarin.Forms 应用程序使用的资源令牌直接使用的资源令牌所定义的权限访问 Cosmos DB 资源。

> [!NOTE]
> 在资源令牌过期，后续文档数据库的请求将收到 401 未经授权的异常。 此时，Xamarin.Forms 应用程序应重新建立标识，并请求新的资源令牌。

有关 Cosmos DB 分区的详细信息，请参阅[如何分区和 Azure Cosmos DB 中的小数位数](/azure/cosmos-db/partition-data/)。 有关 Cosmos 数据库访问控制的详细信息，请参阅[保护对 Cosmos DB 数据访问](/azure/cosmos-db/secure-access-to-data/)和[中 SQL API 的访问控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。

## <a name="setup"></a>安装

将资源令牌 broker 集成到 Xamarin.Forms 应用程序的过程如下所示：

1. 创建将使用访问控制的 Cosmos DB 帐户。 有关详细信息，请参阅[Cosmos 数据库配置](#cosmosdb_configuration)。
1. 创建 Azure 应用程序服务，以承载资源令牌代理。 有关详细信息，请参阅[Azure 应用程序服务配置](#app_service_configuration)。
1. 创建 Facebook 应用程序来执行身份验证。 有关详细信息，请参阅[Facebook 应用程序配置](#facebook_configuration)。
1. 配置 Azure 应用程序服务来执行与 Facebook 的简单身份验证。 有关详细信息，请参阅[Azure 应用程序服务身份验证配置](#app_service_authentication_configuration)。
1. 配置 Xamarin.Forms 示例应用程序与 Azure App Service 和 Cosmos DB 进行通信。 有关详细信息，请参阅[Xamarin.Forms 应用程序配置](#forms_application_configuration)。

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Azure Cosmos DB 配置

用于创建将使用访问控制的 Cosmos DB 帐户的过程如下所示：

1. 创建一个 Cosmos DB 帐户。 有关详细信息，请参阅[创建 Azure Cosmos DB 帐户](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account)。
1. 在 Cosmos DB 帐户中，创建一个名为的新集合`UserItems`，指定分区键为`/userid`。

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Azure App Service 配置

承载在 Azure App Service 中的资源令牌代理的过程如下所示：

1. 在 Azure 门户中，创建新的 App Service web 应用。 有关详细信息，请参阅[在 App Service 环境中创建 web 应用](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/)。
1. 在 Azure 门户中，打开 web 应用中，应用设置边栏选项卡，并添加以下设置：
    - `accountUrl` – 此值应该是从 Cosmos DB 帐户的密钥边栏选项卡的 Cosmos DB 帐户 URL。
    - `accountKey` – 值应为 Cosmos 数据库主密钥 （主要或次要） 从 Cosmos DB 帐户的密钥边栏选项卡。
    - `databaseId` – 值应为 Cosmos DB 数据库的名称。
    - `collectionId` – 值应为 Cosmos DB 集合的名称 (在这种情况下， `UserItems`)。
    - `hostUrl` – 值应为应用程序服务帐户的概述边栏选项卡中的 web 应用的 URL。

    下面的屏幕截图演示了此配置：

    [![](authentication-images/azure-web-app-settings.png "App Service Web 应用设置")](authentication-images/azure-web-app-settings-large.png#lightbox "App Service Web 应用设置")

1. 将资源令牌 broker 解决方案发布到 Azure App Service web 应用。

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Facebook 应用程序配置

用于创建 Facebook 应用程序来执行身份验证的过程如下所示：

1. 创建 Facebook 应用程序。 有关详细信息，请参阅[注册和配置应用](https://developers.facebook.com/docs/apps/register)Facebook 开发人员中心上。
1. 将 Facebook 登录名产品添加到应用程序。 有关详细信息，请参阅[到你的应用或网站添加 Facebook 登录名](https://developers.facebook.com/docs/facebook-login)Facebook 开发人员中心上。
1. 配置 Facebook 登录名，如下所示：
  - 启用客户端 OAuth 登录名。
  - 启用 Web OAuth 登录名。
  - 设置有效的 OAuth 重定向到 App Service web 应用，URI 的 URI`/.auth/login/facebook/callback`追加。

  下面的屏幕截图演示了此配置：

  ![](authentication-images/facebook-oauth-settings.png "Facebook 登录 OAuth 设置")

有关详细信息，请参阅[向 Facebook 注册你的应用程序](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook)。

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Azure App Service 身份验证配置

配置 App Service 简单身份验证的过程如下所示：

1. 在 Azure 门户中，导航到 App Service web 应用。
1. 在 Azure 门户中，打开身份验证 / 授权边栏选项卡并执行以下配置：
  - 应启用应用程序服务身份验证。
  - 当请求未经过身份验证时要执行的操作应设置为**使用 Facebook 登录名**。

  下面的屏幕截图演示了此配置：

  [![](authentication-images/app-service-authentication-settings.png "App Service Web 应用程序身份验证设置")](authentication-images/app-service-authentication-settings-large.png#lightbox "App Service Web 应用程序身份验证设置")

App Service web 应用还应配置为与 Facebook 应用程序启用的身份验证流通信。 这可以通过选择 Facebook 标识提供程序，并输入**应用程序 ID**和**应用程序密钥**从 Facebook 开发人员中心上的 Facebook 应用程序设置的值。 有关详细信息，请参阅[到你的应用程序添加 Facebook 信息](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application)。

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Xamarin.Forms 应用程序配置

配置 Xamarin.Forms 示例应用程序的过程如下所示：

1. 打开 Xamarin.Forms 解决方案。
1. 打开`Constants.cs`和更新以下常量的值：
  - `EndpointUri` – 此值应该是从 Cosmos DB 帐户的密钥边栏选项卡的 Cosmos DB 帐户 URL。
  - `DatabaseName` – 值应为文档数据库的名称。
  - `CollectionName` – 值应为文档数据库集合的名称 (在这种情况下， `UserItems`)。
  - `ResourceTokenBrokerUrl` – 值应为 App Service 帐户的概述边栏选项卡中的资源令牌的代理 web 应用程序的 URL。

## <a name="initiating-login"></a>启动的登录名

示例应用程序启动登录过程，通过使用 Xamarin.Auth 浏览器重定向到标识提供程序 URL，如下面的代码示例中所示：

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

这将导致启动之间 Azure App Service 和 Facebook，Facebook 登录页将显示一个 OAuth 身份验证流程：

![](authentication-images/login.png "Facebook 登录名")

登录可以取消按**取消**按钮在 iOS 上或通过按**回**在 Android 上，在这种情况下的用户保持状态未经身份验证和标识提供程序用户界面的按钮从屏幕上删除。

有关 Xamarin.Auth 的详细信息，请参阅[与标识提供程序进行身份验证的用户](~/xamarin-forms/data-cloud/authentication/oauth.md)。

## <a name="obtaining-a-resource-token"></a>获取一个资源令牌

成功通过身份验证，按照`WebRedirectAuthenticator.Completed`事件激发。 下面的代码示例演示如何处理此事件：

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

成功的身份验证的结果是访问令牌，它位于`AuthenticatorCompletedEventArgs.Account`属性。 提取并在资源令牌代理的 GET 请求中使用访问令牌`resourcetoken`API。

`resourcetoken` API 使用的访问令牌从 Facebook，又用于从 Cosmos DB 请求的资源令牌请求用户的标识。 如果文档数据库中的用户已存在有效的权限的文档，检索到它，并包含资源令牌的 JSON 文档返回到 Xamarin.Forms 应用程序。 如果用户不存在有效的权限文档，在文档数据库中，创建用户和权限和资源令牌是从权限文档中提取并返回到 JSON 文档中的 Xamarin.Forms 应用程序。

> [!NOTE]
> 文档数据库用户是文档数据库，与关联的资源，并且每个数据库可能包含零个或多个用户。 文档数据库权限是与文档数据库用户相关联的资源，并且每个用户可能包含零个或多个权限。 权限资源提供了对用户需要在尝试访问某个资源例如文档时的安全令牌的访问。

如果`resourcetoken`API 成功完成后，它将发送 HTTP 状态代码 200 （正常） 在响应中，以及 JSON 文档包含的资源令牌。 下面的 JSON 数据显示了典型成功的响应消息：

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

`WebRedirectAuthenticator.Completed`事件处理程序读取的回应`resourcetoken`API 并提取资源令牌和用户 id。资源令牌然后作为的自变量传递`DocumentClient`构造函数，它封装终结点、 凭据和连接策略用于访问 Cosmos DB，并用于配置和执行针对 Cosmos DB 的请求。 资源令牌与直接访问资源，每个请求一起发送，并指示授予了对经过身份验证的用户的已分区集合的读/写访问。

## <a name="retrieving-documents"></a>检索文档

可以通过在下面的代码示例中创建包括用户的 id 作为分区键，并演示了一个文档查询来检索仅属于已验证用户的文档：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

查询以异步方式检索所有属于已验证的用户，从指定集合中的文档，并将它们放入`List<TodoItem>`显示的集合。

`CreateDocumentQuery<T>`方法指定`Uri`表示应为文档，查询的集合的自变量和一个`FeedOptions`对象。 `FeedOptions`对象指定，可以通过查询，并用作分区键的用户的 id 返回无限的数量的项。 这可确保结果中返回，仅在用户的已分区集合中的文档。

> [!NOTE]
> 请注意，权限文档，它由资源令牌 broker，存储在相同的文档集合作为 Xamarin.Forms 应用程序创建的文档。 因此，该文档查询包含`Where`筛选谓词应用于针对文档集合的查询的子句。 此子句可确保权限文档不从文档集合中返回。

有关从文档集合中检索文档的详细信息，请参阅[检索文档集合文档](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#document_query)。

## <a name="inserting-documents"></a>插入文档

在将文档插入到文档集合之前,`TodoItem.UserId`应使用的值用作分区键，更新属性，如下面的代码示例中所示：

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

这可确保文档，将插入到用户的已分区的集合。

有关将文档插入到文档集合的详细信息，请参阅[将文档插入到文档集合](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#inserting_document)。

## <a name="deleting-documents"></a>删除文档

删除分区集合中从文档中下面的代码示例演示时，必须指定分区密钥值：

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

这可确保 Cosmos DB 知道其分区删除中的文档的集合。

有关从文档集合中删除文档的详细信息，请参阅[从文档集合中删除文档](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#deleting_document)。

## <a name="summary"></a>总结

本文介绍了如何将访问控制与分区集合结合起来，以便用户只能访问自己在 Xamarin.Forms 应用程序中的文档数据库文档。 指定用户的标识用作分区键可确保分区的集合可以仅存储为该用户的文档。


## <a name="related-links"></a>相关链接

- [Todo Azure Cosmos DB 身份验证 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)
- [使用 Azure Cosmos DB 文档数据库](~/xamarin-forms/data-cloud/cosmosdb/consuming.md)
- [保护对 Azure Cosmos DB 数据访问](/azure/cosmos-db/secure-access-to-data/)
- [SQL API 中的访问控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。
- [如何分区和 Azure Cosmos DB 中的小数位数](/azure/cosmos-db/partition-data/)
- [Azure Cosmos DB 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn948556.aspx)
