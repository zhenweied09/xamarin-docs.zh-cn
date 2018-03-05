---
title: "了解示例"
description: "本主题提供的 Xamarin.Forms 示例应用程序演示如何与另一个 web 服务进行通信的演练。 而每个 web 服务使用单独的示例应用程序，它们都功能上相似，共享通用类。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 6625edc1f661e5f9769de82ec48367e9f900e567
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="understanding-the-sample"></a>了解示例

_本主题提供的 Xamarin.Forms 示例应用程序演示如何与另一个 web 服务进行通信的演练。而每个 web 服务使用单独的示例应用程序，它们都功能上相似，共享通用类。_

如下所述的示例待办事项列表应用程序用于演示如何访问不同类型的 web 服务后端使用 Xamarin.Forms。 它提供功能：

- 查看任务的列表。
- 添加、 编辑和删除任务。
- 设置任务的状态设置为完成。
- 该任务的名称和说明字段进行通信。

在所有情况下，这些任务存储在通过 web 服务访问后端。

当启动应用程序时，会显示页面，列出了从 web 服务检索任何任务，并允许用户创建新任务。 单击任务导航到第二个页上，其中该任务可以编辑、 保存、 删除和读出应用程序。 最终的应用程序如下所示：

![](walkthrough-images/app-example-1.png "Todo 应用程序的第一页")
![](walkthrough-images/app-example-2.png "Todo 应用程序的第二页")

本指南中的每个主题提供的下载链接*不同*演示特定类型的 web 服务后端的应用程序版本。 下载与每个 web 服务样式相关的页上的相关的示例代码。

## <a name="understanding-the-application-anatomy"></a>了解应用程序剖析

对于每个示例应用程序 PCL 项目中包含的三个主要文件夹：

<table>
    <thead>
        <tr><td><strong>Folder</strong></td><td><strong>用途</strong></td></tr>
    </thead>
    <tbody>
        <tr>
            <td><strong>Data</strong></td>
                        <td>包含的类和接口可用于管理数据项目，并与 web 服务通信。 在最低限度上，这包括<code>TodoItemManager</code>类，该类通过中的属性公开<code>App</code>类来调用 web 服务操作。</td>
        </tr>
        <tr>
            <td><strong>模型</strong></td>
                        <td>包含应用程序的数据模型类。 在最低限度上，这包括<code>TodoItem</code>类，该类建模的数据应用程序使用单个项。 该文件夹还可以包含用于对用户数据的任何其他类。</td>
        </tr>
        <tr>
            <td><strong>视图</strong></td>
                        <td>包含应用程序的页。 这通常包括<code>TodoListPage</code>和<code>TodoItemPage</code>类和任何其他类，用于身份验证目的。</td>
                </tr>
    </tbody>
</table>

PCL 项目中的每个应用程序还包含一个重要的文件数：

<table>
    <thead>
      <tr><td><strong>文件</strong></td><td><strong>用途</strong></td></tr>
    <thead>
    <tbody>
        <tr>
            <td><strong>Constants.cs</strong></td>
            <td><code>Constants</code>类，该类指定任何应用程序用于与 web 服务通信的常数。 这些常量要求更新以访问您的个人的后端服务提供程序上创建。
        </tr>
        <tr>
            <td><strong>ITextToSpeech.cs</strong></td>
            <td><code>ITextToSpeech</code>接口，它可以指定<code>Speak</code>方法必须由任何实现类。</td>
        </tr>
        <tr>
          <td><strong>Todo.cs</strong></td>
          <td><code>App</code>负责实例化的这两个将显示的每个平台上的应用程序的第一页的类和<code>TodoItemManager</code>用于调用 web 服务操作的类。</td>
        </tr>
    </tbody>
</table>

### <a name="viewing-pages"></a>查看页

大多数示例应用程序包含至少两个页：

- **TodoListPage** – 此页显示的列表`TodoItem`实例和对勾图标如果`TodoItem.Done`属性是`true`。 单击项导航到`TodoItemPage`。 此外，通过单击创建新项 *+* 符号。
- **TodoItemPage** – 此页显示所选的详细信息`TodoItem`，并允许编辑、 保存、 删除和读出。

此外，某些示例应用程序包含用于管理用户身份验证过程的其他页。

### <a name="modeling-the-data"></a>对数据进行建模

每个示例应用程序使用`TodoItem`类模型显示并发送到 web 服务进行存储的数据。 以下代码示例演示 `TodoItem` 类：

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

`ID`属性用于唯一标识每个`TodoItem`实例，并且每个 web 服务的情况下用于标识要更新或删除数据。

### <a name="invoking-web-service-operations"></a>调用 Web 服务操作

通过访问 web 服务操作`TodoItemManager`类和类的实例可以通过访问`App.TodoManager`属性。 `TodoItemManager`类提供下列方法来调用 web 服务操作：

- **GetTasksAsync** – 此方法用于填充`ListView`控制上`TodoListPage`与`TodoItem`从 web 服务检索的实例。
- **SaveTaskAsync** – 此方法用于创建或更新`TodoItem`web 服务的实例。
- **DeleteTaskAsync** – 此方法用于删除`TodoItem`web 服务的实例。

此外，某些示例应用程序包含中的其他方法`TodoItemManager`类，用于管理用户身份验证过程。

而不是直接调用 web 服务操作`TodoItemManager`方法调用注入的依赖类上方法`TodoItemManager`构造函数。 例如，一个示例应用程序插入`SimpleDBStorage`类到`TodoItemManager`构造函数提供调用针对 Amazon 的 SimpleDB 服务操作的实现。

### <a name="translating-text-to-speech"></a>将文本到语音转换

大多数示例应用程序包含用于进行通信的值的文本到语音转换 (TTS) 功能`TodoItem.Name`和`TodoItem.Notes`属性。 这通过实现`OnSpeakActivated`中的事件处理程序`TodoItemPage`类，如下面的代码示例中所示：

```csharp
void OnSpeakActivated (object sender, EventArgs e)
{
    var todoItem = (TodoItem)BindingContext;
    App.Speech.Speak(todoItem.Name + " " + todoItem.Notes);
}
```

此方法只需调用`Speak`由特定于平台的实现的方法`Speech`类。 每个`Speech`类实现`ITextToSpeech`接口，并特定于平台的启动代码创建的实例`Speech`类可以通过访问`App.Speech`属性。

## <a name="summary"></a>摘要

本主题提供用于演示如何与另一个 web 服务进行通信的 Xamarin.Forms 示例应用程序的演练。 而每个 web 服务使用单独的示例应用程序，它们都基于相同的用户界面和业务逻辑上文所述-仅 web 服务数据存储机制是不同。


## <a name="related-links"></a>相关链接

- [ASMX 版本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX)
- [WCF 版本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF)
- [REST 版本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST)
- [Azure 版本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure)
- [Amazon Web Services 版本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWS)
