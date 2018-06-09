---
title: 使用 Azure 移动应用程序中同步脱机数据
description: 此文章介绍了如何将脱机同步功能添加到的 Xamarin.Forms 应用程序使用的 Azure Mobile Apps 后端。
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: e8b0eeeb4f0033fccd7a61b4acb286bb8457e6c2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243592"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>使用 Azure 移动应用程序中同步脱机数据

_脱机同步允许用户交互与移动应用程序、 查看、 添加或修改数据，甚至在没有网络连接。更改将存储在本地数据库中，并且一旦设备处于联机状态，可以与 Azure Mobile Apps 实例同步所做的更改。此文章介绍了如何向 Xamarin.Forms 应用程序添加脱机同步功能。_

## <a name="overview"></a>概述

[Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供`IMobileServiceTable`接口，表示可以在表存储在 Azure Mobile Apps 实例执行的操作。 这些操作直接连接到 Azure Mobile Apps 实例，并且如果移动设备没有相应的网络连接将失败。

若要支持脱机同步，Azure 移动客户端 SDK 支持*同步表*，这由`IMobileServiceSyncTable`接口。 此接口可提供相同的创建、 读取、 更新、 删除 (CRUD) 操作作为`IMobileServiceTable`接口，但操作读取或写入本地存储。 本地存储区不填充使用从 Azure Mobile Apps 实例的新数据对的调用之前*请求*数据。 同样，数据对的调用之前未发送到 Azure Mobile Apps 实例*推送*本地更改。

脱机同步还包括支持在 Azure Mobile Apps 实例和自定义冲突解决这两个本地存储区中和已更改同一记录时检测冲突。 在本地存储中，或 Azure Mobile Apps 实例中，也可以处理冲突。

有关脱机同步的详细信息，请参阅[Azure Mobile Apps 中的脱机数据同步](/azure/app-service-mobile/app-service-mobile-offline-data-sync/)和[启用 Xamarin.Forms 移动应用程序的脱机同步](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/)。

## <a name="setup"></a>安装

将集成 Xamarin.Forms 应用程序与 Azure Mobile Apps 实例之间的脱机同步的过程如下所示：

1. 创建 Azure Mobile Apps 实例。 有关详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 添加[Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) Xamarin.Forms 解决方案中的所有项目的 NuGet 包。
1. （可选）启用 Azure Mobile Apps 实例和 Xamarin.Forms 应用程序中的身份验证。 有关详细信息，请参阅[进行身份验证的用户与 Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)。

以下部分提供有关配置通用 Windows 平台 (UWP) 项目的其他安装说明使用 Microsoft.Azure.Mobile.Client.SQLiteStore NuGet 包。 没有其他安装程序需要在 iOS 和 Android 使用 Microsoft.Azure.Mobile.Client.SQLiteStore NuGet 包。

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要使用 SQLite 通用 Windows 平台 (UWP)，请按照下列步骤：

1. 安装[的通用 Windows 平台的 SQLite](http://sqlite.org/2016/sqlite-uwp-3120200.vsix)在开发环境中的 Visual Studio 扩展。
1. 在 Visual Studio 中的 UWP 项目，右键单击**引用 > 添加引用**，导航到**扩展**并添加**的通用 Windows 平台的 SQLite**和**通用 Windows 平台应用的 visual c + + 2015年运行时**扩展到 UWP 项目。

## <a name="initializing-the-local-store"></a>初始化线程本地存储区

可以执行任何同步表操作之前，必须初始化本地存储。 这是在可移植类库 (PCL) 项目中的 Xamarin.Forms 解决方案来实现的：

```csharp
using Microsoft.WindowsAzure.MobileServices;
using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
using Microsoft.WindowsAzure.MobileServices.Sync;

namespace TodoAzure
{
    public partial class TodoItemManager
    {
        static TodoItemManager defaultInstance = new TodoItemManager();
        IMobileServiceClient client;
        IMobileServiceSyncTable<TodoItem> todoTable;

        private TodoItemManager()
        {
            this.client = new MobileServiceClient(Constants.ApplicationURL);
            var store = new MobileServiceSQLiteStore("localstore.db");
            store.DefineTable<TodoItem>();
            this.client.SyncContext.InitializeAsync(store);
            this.todoTable = client.GetSyncTable<TodoItem>();
        }
        ...
  }
}
```

通过创建一个新的本地 SQLite 数据库`MobileServiceSQLiteStore`类，前提是它尚不存在。 然后，`DefineTable<T>`方法中的字段相匹配的本地存储中创建一个表`TodoItem`类型，前提是它尚不存在。

A*同步上下文*与关联`MobileServiceClient`实例，并使用同步表进行的跟踪更改。 同步上下文维护的队列中保留有序列的表的创建、 更新和删除 (CUD) 操作将发送到 Azure Mobile Apps 实例更高版本。 `IMobileServiceSyncContext.InitializeAsync()`方法用于将本地存储与同步上下文相关联。

`todoTable`字段是`IMobileServiceSyncTable`，因此，所有 CRUD 操作都使用本地存储。

## <a name="performing-synchronization"></a>执行同步

与 Azure Mobile Apps 同步本地存储区实例时`SyncAsync`调用方法：

```csharp
public async Task SyncAsync()
{
  ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

  try
  {
    await this.client.SyncContext.PushAsync();

    // The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
    // Use a different query name for each unique query in your program.
    await this.todoTable.PullAsync("allTodoItems", this.todoTable.CreateQuery());
  }
  catch (MobileServicePushFailedException exc)
  {
    if (exc.PushResult != null)
    {
      syncErrors = exc.PushResult.Errors;
    }
  }

  // Simple error/conflict handling.
  if (syncErrors != null)
  {
    foreach (var error in syncErrors)
    {
      if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
      {
        // Update failed, revert to server's copy
        await error.CancelAndUpdateItemAsync(error.Result);
      }
      else
      {
        // Discard local change
        await error.CancelAndDiscardItemAsync();
      }

      Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
    }
  }
}
```

`IMobileServiceSyncTable.PushAsync`方法对同步上下文中，而不是特定的表，并将所有 CUD 更改都发送自上次推送。

请求由执行`IMobileServiceSyncTable.PullAsync`对单个表的方法。 第一个参数`PullAsync`方法是仅在移动设备使用的查询名称。 提供在 Azure 移动客户端 SDK 执行名称结果的非 null 查询*增量同步*，其中每次请求操作返回的结果，最新`updatedAt`从结果的时间戳存储在局部变量系统表。 将后续请求操作然后只能检索记录后该时间戳。 或者，*完全同步*可以通过传递`null`作为查询名称，这会导致正在检索每个请求操作上的所有记录。 任何同步操作后，接收到的数据插入到本地存储中。

> [!NOTE]
> 如果针对具有挂起的本地更新的表执行请求，则请求将先执行推送对同步上下文。 这将减少已排入队列的更改和新数据从 Azure Mobile Apps 实例之间的冲突。

`SyncAsync`方法还包含用于处理冲突，这两个本地存储区中和在 Azure Mobile Apps 实例中更改同一记录时的基本实现。 在本地存储区和在 Azure Mobile Apps 实例中，数据已被更新冲突时`SyncAsync`方法将更新存储在 Azure Mobile Apps 实例中的数据从本地存储中的数据。 当任何其他冲突发生时，`SyncAsync`方法会放弃本地更改。 这将处理方案本地更改所在的 Azure Mobile Apps 实例从已删除的数据。

在生产应用程序，开发人员应编写自定义`IMobileServiceSyncHandler`适合其使用大小写的冲突处理实现。 有关详细信息，请参阅[使用开放式并发冲突解决](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency)在 Azure 门户中，和[深入了解上托管的客户端 SDK 中的脱机支持](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/)MSDN 博客。

## <a name="purging-data"></a>清除数据

本地存储中的表可以清除的数据与`IMobileServiceSyncTable.PurgeAsync`方法。 此方法支持删除应用程序不再需要的过时数据等方案。 例如，示例应用程序仅显示`TodoItem`未完成的实例。 因此，已完成的项目不再需要在本地存储。 清除本地存储中的已完成的项目完成，如下所示：

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

调用`PurgeAsync`还会触发推送操作。 因此，本地标记为已完成的任何项将从本地存储区中删除之前发送到 Azure Mobile Apps 实例中。 但是，如果有与 Azure Mobile Apps 实例同步挂起的操作，则清除将引发`InvalidOperationException`除非`force`参数设置为`true`。 使用替代策略是检查`IMobileServiceSyncContext.PendingOperations`属性，它返回挂起的操作尚未已推送到 Azure Mobile Apps 实例，并仅执行清除，如果属性为零的数。

> [!NOTE]
> 调用`PurgeAsync`与`force`参数设置为`true`将丢失任何挂起的更改。

## <a name="initiating-synchronization"></a>启动同步

在示例应用程序，`SyncAsync`通过调用方法`TodoList.OnAppearing`方法：

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

这意味着应用程序将尝试启动时与 Azure Mobile Apps 实例同步。

此外，同步可在 iOS 和 Android 由启动请求用于使用刷新和 Windows 平台上的数据，列表中**同步**用户界面上的按钮。 有关详细信息，请参阅[拉取到刷新](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh)。

## <a name="summary"></a>总结

本文介绍如何向 Xamarin.Forms 应用程序添加脱机同步功能。 脱机同步允许用户交互与移动应用程序、 查看、 添加或修改数据，甚至在没有网络连接。 更改将存储在本地数据库中，并且一旦设备处于联机状态，可以与 Azure Mobile Apps 实例同步所做的更改。


## <a name="related-links"></a>相关链接

- [TodoAzureAuthOfflineSync （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)
- [使用 Azure 移动应用程序的用户进行身份验证](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Azure 的移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
