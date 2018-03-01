---
title: "使用 Azure 搜索中搜索数据"
description: "Azure 搜索是云服务，提供了索引和查询上载的数据的功能。 这将删除基础结构要求和传统上与应用程序中实现搜索功能关联的搜索算法复杂性。 本文演示如何使用 Microsoft Azure 搜索库将 Azure Search 集成到 Xamarin.Forms 应用程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: bf6b9f8aaa07e934a1e707b85ecaa24e4f3d99bf
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="searching-data-with-azure-search"></a>使用 Azure 搜索中搜索数据

_Azure 搜索是云服务，提供了索引和查询上载的数据的功能。这将删除基础结构要求和传统上与应用程序中实现搜索功能关联的搜索算法复杂性。本文演示如何使用 Microsoft Azure 搜索库将 Azure Search 集成到 Xamarin.Forms 应用程序。_

## <a name="overview"></a>概述

数据存储在 Azure 搜索为索引和文档。 *索引*是 Azure 搜索服务中，可以搜索的数据的存储和从概念上讲类似于数据库表。 A*文档*是在索引中，可搜索数据的单个单元，它从概念上讲类似于数据库行。 当将文档上载和提交对 Azure 搜索的搜索查询，对特定索引中搜索服务发出请求。

对 Azure 搜索所做的每个请求必须包含服务和 API 密钥名称。 有两种类型的 API 密钥：

- *管理密钥*授予对所有操作的完全权限。 这包括管理服务、 创建和删除索引和数据源。
- *查询密钥*授予对索引和文档，只读访问权限，应使用的应用程序发出搜索请求。

最常见的请求对 Azure 搜索是执行查询。 有两种类型的可以提交的查询：

- A*搜索*查询将搜索在索引中的所有可搜索字段中的一个或多个项。 搜索查询是使用简化的语法或 Lucene 查询语法生成的。 有关详细信息，请参阅[Azure 搜索中的简单查询语法](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)，和[Lucene Azure 搜索中的查询语法](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/)。
- A*筛选器*查询对索引中的所有可筛选字段计算布尔表达式。 使用 OData 筛选器语言的子集生成筛选器查询。 有关详细信息，请参阅[Azure 搜索的 OData 表达式语法](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/)。

单独或一起，可以使用搜索查询和筛选器查询。 筛选器查询一起使用时，到整个索引中，首先应用，然后搜索查询执行筛选器查询的结果。

Azure 搜索还支持基于搜索输入检索建议。 有关详细信息，请参阅[建议查询](#suggestions)。

## <a name="setup"></a>安装

将 Azure Search 集成到 Xamarin.Forms 应用程序的过程如下所示：

1. 创建 Azure 搜索服务。 有关详细信息，请参阅[创建 Azure 搜索服务使用 Azure 门户](/azure/search/search-create-service-portal/)。
1. 作为目标框架的 Silverlight 移除 Xamarin.Forms 解决方案可移植类库 (PCL)。 这可以通过将 PCL 配置文件更改为支持跨平台开发，但不支持 Silverlight，如配置文件 151 或 92 任何配置文件来实现。
1. 添加[Microsoft Azure 搜索库](https://www.nuget.org/packages/Microsoft.Azure.Search)到 PCL 项目中，Xamarin.Forms 解决方案的 NuGet 包。

执行这些步骤后，Microsoft 搜索库 API 可用来管理搜索索引和数据源、 上载和管理文档和执行查询。

## <a name="creating-the-azure-search-index"></a>创建 Azure 搜索索引

索引架构必须定义映射到要搜索的数据的结构。 这可以完成在 Azure 门户中，或以编程方式使用`SearchServiceClient`类。 此类管理连接到 Azure 搜索中，并可以用于创建索引。 下面的代码示例演示如何创建此类的实例：

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

`SearchServiceClient`构造函数重载采用一个搜索服务名称和一个`SearchCredentials`对象作为自变量，`SearchCredentials`对象包装*管理密钥*Azure 搜索服务。 *管理密钥*创建索引所需。

> [!NOTE]
>  单个`SearchServiceClient`实例应使用应用程序中，以避免打开太多连接到 Azure Search。

通过定义索引`Index`对象，如下面的代码示例中所示：

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

`Index.Name`属性应设置为索引，名称和`Index.Fields`属性应设置为的数组`Field`对象。 每个`Field`实例指定名称、 类型和任何属性，指定如何使用此字段。 这些属性包括：

- `IsKey` – 指示字段是否索引的键。 在索引中，类型的一个字段`DataType.String`，必须指定为键字段。
- `IsFacetable` – 指示是否可以对此字段执行分面导航。 默认值为 `false`。
- `IsFilterable` – 表示是否可以在筛选器查询中使用该字段。 默认值为 `false`。
- `IsRetrievable` – 指示是否可以在搜索结果中检索字段。 默认值为 `true`。
- `IsSearchable` – 指示是否在全文搜索中包含的字段。 默认值为 `false`。
- `IsSortable` – 表示是否可以在中使用字段`OrderBy`表达式。 默认值为 `false`。

> [!NOTE]
> 在部署后更改索引涉及重新生成并重新加载数据。

`Index`对象可以选择指定`Suggesters`属性，用于定义索引中要用于支持自动完成或搜索建议查询的字段。 `Suggesters`属性应设置为的数组`Suggester`定义用于生成建议结果的搜索的字段的对象。

在创建后`Index`对象，通过调用创建索引`Indexes.Create`上`SearchServiceClient`实例。

> [!NOTE]
> 当从应用程序创建索引必须响应保留时，则使用`Indexes.CreateAsync`方法。

有关详细信息，请参阅[创建 Azure Search 索引使用.NET SDK](/azure/search/search-create-index-dotnet/)。

## <a name="deleting-the-azure-search-index"></a>删除 Azure 搜索索引

可以通过调用删除索引`Indexes.Delete`上`SearchServiceClient`实例：

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>将数据上载到 Azure 搜索索引

定义索引之后, 可以将数据上载到它使用两种模式之一：

- **拉取模型**– 数据定期引入从 Azure DocumentDB、 Azure SQL 数据库、 Azure Blob 存储或 SQL Server 托管 Azure 虚拟机中。
- **推送模型**– 数据以编程方式发送到索引。 这是采用此文章中的模型。

A`SearchIndexClient`必须创建实例，以将数据导入索引。 这可以通过调用来实现`SearchServiceClient.Indexes.GetClient`方法，如下面的代码示例中所示：

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

要导入到索引数据打包为`IndexBatch`对象，封装的集合`IndexAction`对象。 每个`IndexAction`实例包含一个文档，以及指示要对文档执行的操作的 Azure 搜索的属性。 在上面的代码示例`IndexAction.Upload`指定，如果它是新的、 要插入到索引文档中的哪些结果或如果它已存在替换操作。 `IndexBatch`对象然后通过调用发送到索引`Documents.Index`方法`SearchIndexClient`对象。 有关其他索引操作的信息，请参阅[决定要使用的索引操作](/azure/search/search-import-data-dotnet#ii-decide-which-indexing-action-to-use)。

> [!NOTE]
> 仅 1000 个文档可以包含在单个索引请求。

请注意，在上面的代码示例`monkeyList`作为匿名对象的集合中创建集合`Monkey`对象。 这将创建数据`id`字段，并将其解析 Pascal 大小写的映射`Monkey`camel 大小写的属性名称搜索索引字段名称。 或者，此映射还可以通过添加`[SerializePropertyNamesAsCamelCase]`属性设为`Monkey`类。

有关详细信息，请参阅[将数据上载到 Azure 搜索中使用.NET SDK](/azure/search/search-import-data-dotnet/)。

## <a name="querying-the-azure-search-index"></a>查询 Azure 搜索索引

A`SearchIndexClient`必须创建实例查询的索引。 当应用程序执行查询时，则最好遵循最低特权原则，并创建`SearchIndexClient`直接传递*查询密钥*作为自变量。 这可确保用户具有对索引和文档的只读访问权限。 在下面的代码示例演示了这种方法：

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

`SearchIndexClient`构造函数重载采用搜索服务名称、 索引名称和一个`SearchCredentials`对象作为自变量，`SearchCredentials`对象包装*查询密钥*Azure 搜索服务。

### <a name="search-queries"></a>搜索查询

可以通过调用查询索引`Documents.SearchAsync`方法`SearchIndexClient`实例，如下面的代码示例中所示：

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SearchAsync`方法采用的搜索文本参数和可选`SearchParameters`可以用于进一步优化查询的对象。 可以通过将设置指定筛选器查询时，指定将搜索查询作为搜索文本自变量，`Filter`属性`SearchParameters`自变量。 下面的代码示例演示两种查询类型：

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

此筛选器查询应用于整个索引，从结果中移除文档其中`location`字段不是等于中国且不等于越南。 在筛选之后，将搜索查询执行筛选器查询的结果。

> [!NOTE]
> 若要筛选而无需搜索，请将传递`*`作为搜索文本参数。

`SearchAsync`方法返回`DocumentSearchResult`包含查询结果的对象。 此对象枚举，与每个`Document`对象创建为`Monkey`对象，并添加到`Monkeys``ObservableCollection`进行显示。 以下屏幕快照显示搜索查询返回的结果从 Azure 搜索：

![](azure-search-images/search.png "搜索结果")

有关搜索和筛选的详细信息，请参阅[查询你使用.NET SDK 的 Azure 搜索索引](/azure/search/search-query-dotnet/)。

<a name="suggestions" />

### <a name="suggestion-queries"></a>建议查询

Azure 搜索允许请求的建议基于搜索查询，通过调用`Documents.SuggestAsync`方法`SearchIndexClient`实例。 在下面的代码示例说明了这一点：

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SuggestAsync`方法采用一个搜索文本自变量，若要使用的建议器名称 （即定义索引中），和一个可选`SuggestParameters`可以用于进一步优化查询的对象。 `SuggestParameters`实例将设置以下属性：

- `UseFuzzyMatching` – 当设置为`true`，Azure 搜索会查找建议，即使搜索文本中没有替代或缺少字符。
- `HighlightPreTag` – 建议命中前面预置的标记。
- `HighlightPostTag` – 追加到建议命中数的标记。
- `MinimumCoverage` – 表示报告为成功，则必须通过建议查询查询要涵盖的索引的百分比。 默认值为 80。
- `Top` – 若要检索的建议的数量。 它必须是 1 到 100，默认值为 5 之间的整数。

总体效果是命中突出显示，并且结果将包括包括相似的拼写搜索词的文档，将会从索引前的 10 个结果返回与。

`SuggestAsync`方法返回`DocumentSuggestResult`包含查询结果的对象。 此对象枚举，与每个`Document`对象创建为`Monkey`对象，并添加到`Monkeys``ObservableCollection`进行显示。 以下屏幕截图显示了从 Azure 搜索返回的建议结果：

![](azure-search-images/suggest.png "建议结果")

请注意，在示例应用程序，`SuggestAsync`用户完成输入的搜索词时，才会调用方法。 但是，它还可支持自动完成搜索查询，通过在每个 keypress 上执行。

## <a name="summary"></a>摘要

这篇文章演示了如何使用 Microsoft Azure 搜索库将 Azure Search 集成到 Xamarin.Forms 应用程序。 Azure 搜索是云服务，提供了索引和查询上载的数据的功能。 这将删除基础结构要求和传统上与应用程序中实现搜索功能关联的搜索算法复杂性。


## <a name="related-links"></a>相关链接

- [Azure 搜索 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Azure 搜索文档](/azure/search/)
- [Microsoft Azure 搜索库](https://www.nuget.org/packages/Microsoft.Azure.Search/)
