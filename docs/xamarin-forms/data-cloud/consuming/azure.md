---
title: 使用 Azure 移动应用
description: 本文中，这仅适用于使用 Node.js 后端的 Azure Mobile Apps，说明如何查询、 插入、 更新和删除数据存储在 Azure Mobile Apps 实例中的表。
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 73d74b59ef6e59028eec7cad19feec21908b6329
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269039"
---
# <a name="consuming-an-azure-mobile-app"></a>使用 Azure 移动应用

_Azure 移动应用，可以使用可缩放的后端移动身份验证、 脱机同步和推送通知的支持与托管在 Azure App Service 中开发应用。本文中，这仅适用于使用 Node.js 后端的 Azure Mobile Apps，说明如何查询、 插入、 更新和删除数据存储在 Azure Mobile Apps 实例中的表。_

> [!NOTE]
> 从开始在 6 月 30 日，所有新的 Azure Mobile Apps 将创建使用 TLS 1.2 默认情况下。 此外，还建议你现有 Azure Mobile Apps 重新配置为使用 TLS 1.2。 有关如何强制 Azure 移动应用程序中的 TLS 1.2 的信息，请参阅[强制实施 TLS 1.2](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-1112)。 有关如何配置 Xamarin 项目以使用 TLS 1.2 的信息，请参阅[传输层安全 (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)。

有关如何创建可供 Xamarin.Forms Azure Mobile Apps 实例的信息，请参阅[创建 Xamarin.Forms 应用](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)。 按照这些说明操作之后, 的可下载示例应用程序可以将配置为使用 Azure Mobile Apps 实例通过设置`Constants.ApplicationURL`Azure Mobile Apps 实例的 url。 然后，运行示例应用程序时它将连接到 Azure Mobile Apps 实例，如下面的屏幕截图中所示：

![](azure-images/portal.png "示例应用程序")

对 Azure 移动应用的访问是通过[Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)，和从 Xamarin.Forms 示例应用程序到 Azure 的所有连接都均通过 HTTPS。

> [!NOTE]
> 在 iOS 9 及更高版本中，应用程序传输安全 (ATS) 强制实施安全连接之间 internet 资源 （如应用程序的后端服务器） 和应用程序，从而防止意外泄露的敏感信息。 由于默认情况下，生成的 ios 9 应用中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，则会失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议和安全的 internet 资源的通信。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-an-azure-mobile-app-instance"></a>使用 Azure 移动应用程序实例

[Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供`MobileServiceClient`类，用于通过 Xamarin.Forms 应用程序访问 Azure Mobile Apps 的实例，如下面的代码示例中所示：

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

当`MobileServiceClient`创建实例，必须指定应用程序 URL 以标识 Azure Mobile Apps 实例。 此值可以从移动应用中的仪表板获取[Microsoft Azure 门户](https://portal.azure.com/)。

对引用`TodoItem`可以在此表上执行操作之前，必须获取存储在 Azure Mobile Apps 实例中的表。 这通过调用实现`GetTable`方法`MobileServiceClient`实例，它将返回`IMobileServiceTable<TodoItem>`引用。

### <a name="querying-data"></a>查询数据

可通过调用检索表的内容`IMobileServiceTable.ToEnumerableAsync`以异步方式计算查询并返回结果的方法。 数据也可以通过包括筛选服务器端`Where`在查询中的子句。 `Where`子句应用一个行筛选谓词对表中，执行查询，如下面的代码示例中所示：

```csharp
public async Task<ObservableCollection<TodoItem>> GetTodoItemsAsync (bool syncItems = false)
{
  ...
  IEnumerable<TodoItem> items = await todoTable
              .Where (todoItem => !todoItem.Done)
              .ToEnumerableAsync ();

  return new ObservableCollection<TodoItem> (items);
}
```

此查询将返回中的所有项`TodoItem`表`Done`属性等于`false`。 查询结果随后会放在`ObservableCollection`进行显示。

### <a name="inserting-data"></a>插入数据

当在 Azure Mobile Apps 实例中插入数据，将自动生成新列的表中根据需要，提供在 Azure Mobile Apps 实例中启用该动态架构。 `IMobileServiceTable.InsertAsync`方法用于将新的数据行插入到指定的表中，如下面的代码示例中所示：

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

在发出一个插入请求，正在传递给 Azure Mobile Apps 实例的数据中必须未指定 ID。 如果插入请求包含 ID`MobileServiceInvalidOperationException`将引发。

后`InsertAsync`方法完成后，Azure Mobile Apps 实例中的数据的 ID 将在填充`TodoItem`Xamarin.Forms 应用程序中的实例。

### <a name="updating-data"></a>更新数据

当更新在 Azure Mobile Apps 实例中的数据，将自动生成新列的表中根据需要，提供在 Azure Mobile Apps 实例中启用该动态架构。 `IMobileServiceTable.UpdateAsync`方法用于更新现有数据，使用新信息，如下面的代码示例中所示：

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

在更新请求时，必须指定 ID，这样 Azure Mobile Apps 实例可以标识要更新的数据。 此 ID 值存储在`TodoItem.ID`属性。 如果更新请求不包含 ID 没有更新，以确定数据的 Azure Mobile Apps 实例方法，因此`MobileServiceInvalidOperationException`将引发。

### <a name="deleting-data"></a>删除数据

`IMobileServiceTable.DeleteAsync`方法用于删除数据从 Azure Mobile Apps 表，如下面的代码示例中所示：

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

在发出删除请求时，必须指定 ID，以便 Azure 移动应用程序 sinstance 可以确定要删除的数据。 此 ID 值存储在`TodoItem.ID`属性。 如果在删除请求不包含 ID，没有要删除，以确定数据的 Azure Mobile Apps 实例方法，因此`MobileServiceInvalidOperationException`将引发。

## <a name="summary"></a>总结

本文介绍了如何使用[Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)来查询、 插入、 更新和删除数据存储在 Azure 移动应用程序实例中的表。 该 SDK 提供`MobileServiceClient`Xamarin.Forms 应用程序用于访问 Azure Mobile Apps 实例的类。


## <a name="related-links"></a>相关链接

- [TodoAzure （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [创建 Xamarin.Forms 应用](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Azure 的移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
