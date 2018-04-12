---
title: 使用 Amazon SimpleDB 服务
description: Amazon SimpleDB 是 web 服务，提供的功能来存储和查询在 Amazon 的云中的数据。 此文章介绍了如何使用 AWS SDK for.NET 来查询、 创建、 替换和删除 SimpleDB 服务中存储的数据。
ms.prod: xamarin
ms.assetid: 823819AA-15F9-4144-B355-78A10AD37513
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1602319dbf5a5d00ac5de75f2d438b9aea692699
ms.sourcegitcommit: 271d3f7ea4abfcf87734d2c747a68cb8114d743c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2018
---
# <a name="consuming-an-amazon-simpledb-service"></a>使用 Amazon SimpleDB 服务

_Amazon SimpleDB 是 web 服务，提供的功能来存储和查询在 Amazon 的云中的数据。此文章介绍了如何使用 AWS SDK for.NET 来查询、 创建、 替换和删除 SimpleDB 服务中存储的数据。_

SimpleDB 服务使用熟悉的 REST 服务的使用者的请求和响应模型。 通过发送请求，其中可能包含数据，SimpleDB 服务上调用操作。 在处理请求之后, SimpleDB 服务将返回包含任何结果的响应。 SimpleDB 服务必须以编程方式创建，不能通过创建[AWS 控制台](https://aws.amazon.com)。 但是，需要创建并访问任何 Amazon web 服务，AWS 帐户。

在 SimpleDB 服务中，数据被组织到域，在其中可以放置数据和对数据运行查询。 域包含描述了特性名称-值对的项。 域可以被认为是类似于表，具有正在类似于列和正在与行类似的项的属性。 有关 SimpleDB 数据模型的详细信息，请参阅[数据模型](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/DataModel.html)Amazon 的网站上。

附带的示例应用程序的自述文件中找不到设置所需的 Amazon 服务说明。 当运行示例应用程序时，它将将连接到 Amazon Cognito 标识池来授予访问权限 SimpleDB 服务，如下面的屏幕截图中所示：

![](aws-images/portal.png "示例应用程序")

> [!NOTE]
> 在 iOS 9 及更高版本中，应用程序传输安全 (ATS) 强制实施安全连接之间 internet 资源 （如应用程序的后端服务器） 和应用程序，从而防止意外泄露的敏感信息。 由于默认情况下，生成的 ios 9 应用中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，则会失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议和安全的 internet 资源的通信。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-a-simpledb-service"></a>使用 SimpleDB 服务

Amazon Cognito 标识允许 AWS 服务，例如 SimpleDB，要调用从没有硬编码 AWS 凭据到应用程序的应用程序。 相反，在创建唯一标识池[Amazon Cognito 控制台](https://console.aws.amazon.com/cognito/home)。 标识池包含角色用于指定资源，如 SimpleDB，标识可访问的标识。

[AWS SDK for.NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)提供`CognitoAWSCredentials`和`AmazonSimpleDBClient`类，用于通过 Xamarin.Forms 应用程序访问 SimpleDB 服务，如下面的代码示例中所示：

```csharp
AmazonSimpleDBClient client;
...

public SimpleDBStorage ()
{
  var credentials = new CognitoAWSCredentials (
                      Constants.CognitoIdentityPoolId,
                      RegionEndpoint.USEast1);
  var config = new AmazonSimpleDBConfig ();
  config.RegionEndpoint = RegionEndpoint.USWest2;
  client = new AmazonSimpleDBClient (credentials, config);
  ...
}
```

新实例`CognitoAWSCredentials`通过提供唯一标识池 id 和 Cognito 标识帐户的区域创建类。 在撰写本文之际，Cognito 标识才可用的 USEast1 和 EUWest1 区域中。 但是，它可以与这些区域之外的 Amazon 服务进行通信。

当`AmazonSimpleDBClient`创建实例，`CognitoAWSCredentials`实例必须提供，以及与`AmazonSimpleDBConfig`指定 SimpleDB 服务所在的地理区域的实例。 `CognitoAWSCredentials`实例可确保访问的 SimpleDB 服务是一个与在其中标识创建池时，同时可避免需要在应用程序中嵌入的 AWS 访问密钥和密钥 AWS 帐户关联。

通过调用创建 SimpleDB 服务域`AmazonSimpleDBClient.CreateDomainAsync`方法，如下面的代码示例中所示：

```csharp
string tableName = "Todo";
...

async Task CreateDomain ()
{
  ...
  await client.CreateDomainAsync (new CreateDomainRequest { DomainName = tableName });
  ...
}
```

`CreateDomainAsync`方法需要`CreateDomainRequest`作为参数的实例。 `CreateDomainRequest`实例初始化`DomainName`属性值用于标识域。 若要创建域，此值必须是在 AWS 帐户与关联的域之间唯一的。 否则为将不会创建域并且没有错误响应将发送，则指示。 针对现有的域中，而不是新创建的域，然后将发生与域名任何操作。

### <a name="creating-simpledb-objects"></a>创建 SimpleDB 对象

示例应用程序使用`TodoItem`模型数据的类。 若要存储`TodoItem`在必须首先将转换为 SimpleDB service 实例`List`的`ReplaceableAttribute`对象。 这通过实现`ToSimpleDBReplaceableAttributes`方法，如下面的代码示例中所示：

```csharp
List<ReplaceableAttribute> ToSimpleDBReplaceableAttributes (TodoItem item)
{
  return new List<ReplaceableAttribute> () {
    new ReplaceableAttribute () {
      Name = "Name",
      Value = item.Name,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Notes",
      Value = item.Notes,
      Replace = true
    },
    new ReplaceableAttribute () {
      Name = "Done",
      Value = item.Done.ToString (),
      Replace = true
    }
  };
}
```

此方法创建`List`的新`ReplaceableAttribute`情况下，与`List`表示单个`TodoItem`实例。 每个`ReplaceableAttribute`实例表示的单个属性从`TodoItem`实例。 有关详细信息`ReplaceableAttribute`类，请参阅[ReplaceableAttribute 类](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_ReplaceableAttribute.htm)Amazon 的网站上。

同样，从 SimpleDB 服务检索数据时，必须将它转换从`List`的`Attribute`实例到`TodoItem`实例。 这实现的`FromSimpleDBAttributes`方法，如下面的代码示例中所示：

```csharp
TodoItem FromSimpleDBAttributes (List<Amazon.SimpleDB.Model.Attribute> attributeList, string id)
{
  var todoItem = new TodoItem ();
  todoItem.ID = id;
  todoItem.Name = attributeList.Where (attr => attr.Name == "Name").FirstOrDefault ().Value;
  todoItem.Notes = attributeList.Where (attr => attr.Name == "Notes").FirstOrDefault ().Value;
  todoItem.Done = Convert.ToBoolean (attributeList.Where (attr => attr.Name == "Done").FirstOrDefault ().Value);
  return todoItem;
}
```

此方法只需检索每个`Attribute`实例从`List`并将其设置在新创建`TodoItem`实例。

有关详细信息`Attribute`类，请参阅[属性类](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_Model_Attribute.htm)Amazon 的网站上。

### <a name="querying-data"></a>查询数据

可通过调用检索域的内容`AmazonSimpleDBClient.SelectAsync`方法，如下面的代码示例中所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var request = new SelectRequest () {
      SelectExpression = string.Format ("SELECT * from {0}", tableName)
  };
  var response = await client.SelectAsync (request);
  foreach (var item in response.Items) {
    Items.Add (FromSimpleDBAttributes (item.Attributes, item.Name));
  }
  ...
}
```

`SelectAsync`方法接受`SelectRequest`实例作为一个参数，它指定`Select`查询中的表达式`SelectExpression`属性。 查询表达式的格式是类似于标准 SQL 的格式`SELECT`语句。 有关查询表达式的详细信息，请参阅[使用选择 to Create Amazon SimpleDB Queries](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/UsingSelect.html) Amazon 的网站上。

> [!NOTE]
> 小心地将其构造的查询表达式时遵循报价的规则。 有关详细信息，请参阅[选择用引号括起来规则](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/QuotingRulesSelect.html)Amazon 的网站上。

`SelectAsync`方法返回包含的项和关联的查询表达式匹配的属性集合的响应。 此集合然后转换为`List`的`TodoItem`显示的实例。

### <a name="creating-and-replacing-data"></a>创建并将数据

`AmazonSimpleDBClient.PutAttributesAsync`方法用于创建和替换数据在 SimpleDB 服务域中，如下面的代码示例中所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBReplaceableAttributes (todoItem);
  var request = new PutAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.PutAttributesAsync (request);
  ...
}
```

`PutAttributesAsync`方法接受`PutAttributesRequest`作为参数的实例。 `PutAttributesRequest`实例指定要在其创建为新项目，或在现有项中替换的属性名称-值对。 `List`的`ReplaceableAttribute`实例生成的`ToSimpleDBReplaceableAttributes`方法。 此方法还将设置`Replace`每个属性`ReplaceableAttribute`到`true`。 这将导致要替换现有属性值，如果数据将被替换的新属性值。 但是，尝试将不存在的属性值不会导致错误响应。

值`PutAttributesRequest.ItemName`属性控制是否将新项添加到域，或是否将替换现有项。 当应用程序创建新项时，它将设置`TodoItem.ID`到新的属性`Guid`。 这确保每个`TodoItem`实例都有一个唯一标识符。 因此，如果`PutAttributesRequest.ItemName`属性设置为一个值，不存在的域中、 SimpleDB 服务将创建包含指定的属性名称-值对的新项目。 如果`PutAttributesRequest.ItemName`属性设置为一个值，已存在的域中，SimpleDB 服务将使用指定的属性名称-值对更新项。

### <a name="deleting-data"></a>删除数据

`AmazonSimpleDBClient.DeleteAttributesAsync`方法用于删除数据与 SimpleDB 服务域，如下面的代码示例中所示：

```csharp
public async Task DeleteTodoItemAsync (TodoItem todoItem)
{
  ...
  var attributeList = ToSimpleDBAttributes (todoItem);
  var request = new DeleteAttributesRequest () {
      DomainName = tableName,
      ItemName = todoItem.ID,
      Attributes = attributeList
  };
  await client.DeleteAttributesAsync (request);
  ...
}
```

`DeleteAttributesAsync`方法接受`DeleteAttributesRequest`作为参数的实例。  `DeleteAttributesRequest`实例指定了该项目，从与要删除的特性`List`的`Attribute`实例要删除生成的`ToSimpleDBAttributes`方法。 提供，删除的项的所有特性，都删除的项目。

## <a name="summary"></a>总结

本文介绍了如何使用 AWS SDK for.NET 来查询、 创建和替换，并删除 SimpleDB 服务中存储的数据。 此 SDK 提供`CognitoAWSCredentials`和`AmazonSimpleDBClient`Xamarin.Forms 应用程序用于访问 SimpleDB 服务的类。


## <a name="related-links"></a>相关链接

- [TodoAWS （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWS/)
- [Amazon Web 服务 SDK Xamarin 开发人员指南](http://docs.aws.amazon.com/mobile/sdkforxamarin/developerguide/)
- [Amazon Cognito Identity](http://docs.aws.amazon.com/cognito/devguide/identity/)
- [Amazon SimpleDB 开发人员文档](http://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/Welcome.html)
- [AmazonSimpleDBClient 类](http://docs.aws.amazon.com/sdkfornet1/latest/apidocs/html/T_Amazon_SimpleDB_AmazonSimpleDBClient.htm)
- [Amazon Web Services SDK for.NET](https://www.nuget.org/packages?q=Tags%3A%22aws-sdk-v3%22)
