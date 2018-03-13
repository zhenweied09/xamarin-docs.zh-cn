---
title: "使用 Azure Cosmos DB 文档数据库"
description: "Azure Cosmos DB 文档数据库是提供对 JSON 文档，提供快速、 高度可用、 可缩放数据库服务需要无缝缩放和全局复制的应用程序的较低的延迟访问的 NoSQL 数据库。 此文章介绍了如何使用 Microsoft Azure DocumentDB 客户端库来将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 5013b35828cecc2e38600839f306f3c0fc1366b9
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="consuming-an-azure-cosmos-db-document-database"></a>使用 Azure Cosmos DB 文档数据库

_Azure Cosmos DB 文档数据库是提供对 JSON 文档，提供快速、 高度可用、 可缩放数据库服务需要无缝缩放和全局复制的应用程序的较低的延迟访问的 NoSQL 数据库。此文章介绍了如何使用 Microsoft Azure DocumentDB 客户端库来将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序。_

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Microsoft Azure Cosmos DB，也可由[Xamarin 大学](https://university.xamarin.com/)**

可以使用 Azure 订阅设置的 Azure Cosmos DB 文档数据库帐户。 每个数据库帐户可以有零个或多个数据库。 Azure Cosmos DB 中的文档数据库是文档集合和用户的逻辑容器。

Azure Cosmos DB 文档数据库可能包含零个或多个文档集合。 每个文档集合可以具有不同的性能级别，允许更大的吞吐量为经常访问的集合，指定和吞吐量降低对于不常访问的集合。

每个文档集合包含零个或多个 JSON 文档。 集合中的文档是免架构，并因此不需要共享相同的结构或字段。 当文档添加到文档集合时，Cosmos DB 自动索引以及在变得可供查询。

出于开发目的，文档数据库也可以使用通过仿真程序。 使用仿真程序，应用程序能够进行开发和测试本地，而无需创建 Azure 订阅或会产生任何费用。 有关仿真程序的详细信息，请参阅[Azure Cosmos DB 模拟器使用本地开发](/azure/cosmos-db/local-emulator/)。

这篇文章，并随附的示例应用程序，演示 Azure Cosmos DB 文档数据库中存储任务的 Todo 列表应用程序。 有关示例应用程序的详细信息，请参阅[了解示例](~/xamarin-forms/data-cloud/walkthrough.md)。

> [!NOTE]
> DocumentDB 客户端库目前不符合通用 Windows 平台 (UWP) 应用程序。 但是，可以通过创建中间层 web 服务使用 DocumentDB 客户端库，并调用此服务从 UWP 应用程序从 UWP 应用程序使用 Azure Cosmos DB 文档数据库。

有关 Azure Cosmos DB 的详细信息，请参阅[Azure Cosmos DB 文档](/azure/cosmos-db/)。

## <a name="setup"></a>安装

将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序的过程如下所示：

1. 创建一个 Cosmos DB 帐户。 有关详细信息，请参阅[创建 Cosmos DB 帐户](/azure/cosmos-db/documentdb-dotnetcore-get-started#step-1-create-a-documentdb-account)。
1. 添加[DocumentDB 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)Xamarin.Forms 解决方案中项目的平台的 NuGet 包。
1. 添加`using`指令`Microsoft.Azure.Documents`， `Microsoft.Azure.Documents.Client`，和`Microsoft.Azure.Documents.Linq`命名空间添加到将访问 Cosmos DB 帐户的类。

执行这些步骤后，可以使用 DocumentDB 客户端库进行配置和执行针对文档数据库的请求。

> [!NOTE]
> 可移植类库 (PCL) 项目到平台项目中，和不可以安装 Azure DocumentDB 客户端库。 因此，示例应用程序是一个共享访问项目 (SAP) 以避免代码重复。 但是，`DependencyService`类可以使用 PCL 项目，来调用特定于平台的项目中包含的 Azure DocumentDB 客户端库代码。

## <a name="consuming-the-azure-cosmos-db-account"></a>使用 Azure Cosmos DB 帐户

`DocumentClient`类型封装终结点、 凭据和连接策略用于访问 Azure Cosmos DB 帐户，并用于配置和执行针对帐户的请求。 下面的代码示例演示如何创建此类的实例：

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

必须向提供 Cosmos DB Uri 和 primary key`DocumentClient`构造函数。 可以从 Azure 门户获取这些。 有关详细信息，请参阅[连接到 Azure Cosmos DB 帐户](/azure/cosmos-db/documentdb-dotnetcore-get-started#a-idconnectastep-3-connect-to-an-azure-cosmos-db-account)。

### <a name="creating-a-database"></a>创建数据库

文档数据库是文档集合和用户的逻辑容器，可以创建在 Azure 门户中，或以编程方式使用`DocumentClient.CreateDatabaseIfNotExistsAsync`方法：

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

`CreateDatabaseIfNotExistsAsync`方法指定`Database`对象作为参数，`Database`对象，它指定数据库名称作为其`Id`属性。 `CreateDatabaseIfNotExistsAsync`方法创建数据库，如果它不存在，或如果它已存在，则返回该数据库。 但是，示例应用程序会忽略返回任何数据`CreateDatabaseIfNotExistsAsync`方法。

> [!NOTE]
> `CreateDatabaseIfNotExistsAsync`方法返回`Task<ResourceResponse<Database>>`检查对象和响应的状态代码来确定是否已创建一个数据库，或返回现有数据库。

### <a name="creating-a-document-collection"></a>创建文档集合

文档集合是 JSON 文档的容器，可以创建在 Azure 门户中，或以编程方式使用`DocumentClient.CreateDocumentCollectionIfNotExistsAsync`方法：

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

`CreateDocumentCollectionIfNotExistsAsync`方法需要两个必需参数 – 数据库名称指定为`Uri`，和一个`DocumentCollection`对象。 `DocumentCollection`对象表示具有指定名称的文档集合`Id`属性。 `CreateDocumentCollectionIfNotExistsAsync`方法创建的文档集合，如果它不存在，或如果它已存在，则返回的文档集合。 但是，示例应用程序会忽略返回任何数据`CreateDocumentCollectionIfNotExistsAsync`方法。

> [!NOTE]
> `CreateDocumentCollectionIfNotExistsAsync`方法返回`Task<ResourceResponse<DocumentCollection>>`检查对象和响应的状态代码来确定是否已创建的文档集合，或返回现有的文档集合。

（可选）`CreateDocumentCollectionIfNotExistsAsync`方法还可以指定`RequestOptions`对象，封装到 Cosmos DB 帐户发出的请求可以指定的选项。 `RequestOptions.OfferThroughput`属性可用于定义的文档集合中，性能级别，并在此示例中应用程序中，设置为 400 每秒请求单位。 此值应增加或减少具体取决于是否将经常或不常访问集合。

> [!IMPORTANT]
> 请注意，`CreateDocumentCollectionIfNotExistsAsync`方法将创建一个新集合具有保留的吞吐量，吞吐量具有定价影响。

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>检索文档集合文档

可以通过创建和执行文档查询检索文档集合中的内容。 使用创建文档查询`DocumentClient.CreateDocumentQuery`方法：

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

此查询以异步方式从指定集合中检索所有文档，并将放在文档`List<TodoItem>`显示的集合。

`CreateDocumentQuery<T>`方法指定`Uri`表示应进行查询的文档的集合的自变量。 在此示例中，`collectionLink`变量是类级字段，它指示`Uri`，它表示要检索从文档的文档集合：

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

`CreateDocumentQuery<T>`方法创建一个查询，以同步执行，并返回`IQueryable<T>`对象。 但是，`AsDocumentQuery`方法将`IQueryable<T>`对象传递给`IDocumentQuery<T>`可以异步执行的对象。 执行异步查询时使用`IDocumentQuery<T>.ExecuteNextAsync`方法，与检索文档数据库中，从结果的下一步页`IDocumentQuery<T>.HasMoreResults`，该值指示是否有其他结果以从查询返回的属性。

文档可以是服务器端筛选通过包括`Where`在查询中，该筛选谓词应用于针对文档集合的查询子句：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

此查询检索所有文档集合中其`Done`属性等于`false`。

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>将文档插入到文档集合

文档是用户定义 JSON 内容和可以插入到文档集合与`DocumentClient.CreateDocumentAsync`方法：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

`CreateDocumentAsync`方法指定`Uri`表示文档应将插入到，该集合的自变量和`object`表示要插入的文档的自变量。

### <a name="replacing-a-document-in-a-document-collection"></a>替换文档集合中的文档

文档可以具有的文档集合中要替换`DocumentClient.ReplaceDocumentAsync`方法：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

`ReplaceDocumentAsync`方法指定`Uri`表示应替换为集合中的文档的自变量和`object`表示更新后的文档数据的自变量。

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>从文档集合中删除文档

可以从与文档集合中删除文档`DocumentClient.DeleteDocumentAsync`方法：

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

`DeleteDocumentAsync`方法指定`Uri`表示应删除的集合中的文档的自变量。

### <a name="deleting-a-document-collection"></a>删除文档集合

可以从具有的数据库中删除文档集合`DocumentClient.DeleteDocumentCollectionAsync`方法：

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

`DeleteDocumentCollectionAsync`方法指定`Uri`表示要删除的文档集合的自变量。 请注意，调用此方法也会删除集合中存储的文档。

### <a name="deleting-a-database"></a>删除数据库

可以从 Cosmos DB 数据库帐户中删除某个数据库`DocumentClient.DeleteDatabaesAsync`方法：

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

`DeleteDatabaseAsync`方法指定`Uri`表示要删除的数据库的自变量。 请注意，调用此方法也会删除存储在数据库中，文档集合和文档集合中存储的文档。

## <a name="summary"></a>摘要

本文介绍了如何使用 Microsoft Azure DocumentDB 客户端库将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序。 Azure Cosmos DB 文档数据库是提供对 JSON 文档，提供快速、 高度可用、 可缩放数据库服务需要无缝缩放和全局复制的应用程序的较低的延迟访问的 NoSQL 数据库。


## <a name="related-links"></a>相关链接

- [TodoDocumentDB (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDB/)
- [Cosmos DB 文档](/azure/cosmos-db/)
- [DocumentDB 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn948556.aspx)
