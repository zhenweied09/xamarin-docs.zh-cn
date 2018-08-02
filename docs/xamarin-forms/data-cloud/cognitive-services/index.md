---
title: 添加智能与认知服务
description: 本文提供了演示如何调用的一些 Microsoft 认知服务 Api 的示例应用程序的介绍。
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: c577aaf1991082baaf34248ee31899902f692d0b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241518"
---
# <a name="adding-intelligence-with-cognitive-services"></a>添加智能与认知服务

_Microsoft 认知服务是一套 Api、 Sdk 和供开发人员可以通过添加功能，如面部识别、 语音识别和语言理解，使其应用程序更智能的服务。本文提供的示例应用程序演示如何调用的一些 Microsoft 认知服务 Api 的简介。_

## <a name="overview"></a>概述

随附的示例是 todo 列表应用程序提供了以下功能：

- 查看任务的列表。
- 添加和编辑通过软键盘，或使用 Microsoft 语音 API 的语音识别中执行的任务。 有关执行语音识别的详细信息，请参阅[语音识别使用 Microsoft 语音 API](speech-recognition.md)。
- 拼写检查任务使用必应拼写检查 API。 有关详细信息，请参阅[拼写检查使用必应拼写检查 API](spell-check.md)。
- 将转换为德语使用转换器 API 从英语的任务。 有关详细信息，请参阅[文本转换使用转换器 API](text-translation.md)。
- 删除任务。
- 设置任务的状态设置为完成。
- 速率表情识别，使用表面 API 的应用程序。 有关详细信息，请参阅[表情识别使用表面 API](emotion-recognition.md)。

任务存储在本地的 SQLite 数据库。 有关使用本地的 SQLite 数据库的详细信息，请参阅[处理本地数据库](~/xamarin-forms/app-fundamentals/databases.md)。

`TodoListPage`启动应用程序时显示。 此页显示存储在本地数据库中，任何任务的列表，并允许用户创建新任务或来对应用程序：

![](images/sample-application-1.png "TodoListPage")

可以通过单击创建新项*+* 按钮，导航到`TodoItemPage`。 此页还可以通过选择一项任务导航：

![](images/sample-application-2.png "TodoItemPage")

`TodoItemPage`允许任务要创建、 编辑、 拼写检查转换，保存，并将删除。 语音识别可用来创建或编辑任务。 这被实现通过按麦克风按钮以开始记录，并通过第二次按相同的按钮停止录制，这将记录发送到 Bing 语音识别 API。

单击 smilies 按钮`TodoListPage`导航到`RateAppPage`，用于执行情感识别的面部表情图像上：

![](images/sample-application-3.png "RateAppPage")

`RateAppPage`允许用户来执行其字面，显示返回表情与提交到表面 API 的照片。

## <a name="understanding-the-application-anatomy"></a>了解应用程序剖析

示例应用程序的可移植类库 (PCL) 项目包含五个主要文件夹：

|文件夹|目标|
|--- |--- |
|模型|包含应用程序的数据模型类。 这包括`TodoItem`类，该类建模的数据应用程序使用单个项。 该文件夹还包括用于从不同 Microsoft 认知服务 Api 返回的模型 JSON 响应的类。|
|存储库|包含`ITodoItemRepository`接口和`TodoItemRepository`用于执行数据库操作的类。|
|服务|包含的接口和用于访问不同 Microsoft 认知服务 Api，并使用的接口的类`DependencyService`类用来定位在平台项目中实现的接口的类。|
|Utils|包含`Timer`类，该类由`AuthenticationService`类若要续订一个 JWT 访问令牌每隔 9 分钟。|
|视图|包含应用程序的页。|

PCL 项目中还包含一些重要的文件：

|文件|目标|
|--- |--- |
|Constants.cs|`Constants`类，从而为 Microsoft 认知服务 Api 调用中指定的 API 密钥和终结点。 API 密钥常量需要更新访问不同的认知服务 Api。|
|App.xaml.cs|`App`类负责实例化的这两个将显示的每个平台上的应用程序的第一页和`TodoManager`用于调用数据库操作的类。|

### <a name="nuget-packages"></a>NuGet 包

示例应用程序使用以下 NuGet 包：

- `Newtonsoft.Json` –.NET 提供的 JSON 框架。
- `PCLStorage` – 提供了一套跨平台本地文件 IO Api。
- `sqlite-net-pcl` – 提供 SQLite 数据库存储。
- `Xam.Plugin.Media` – 提供跨平台照片拍摄和 Api。

此外，这些 NuGet 包还会安装其自己的依赖关系。

### <a name="modeling-the-data"></a>对数据进行建模

示例应用程序使用`TodoItem`类模型显示和本地的 SQLite 数据库中存储的数据。 以下代码示例演示 `TodoItem` 类：

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

`ID`属性用于唯一标识每个`TodoItem`实例，并使用 SQLite 数据库中自动递增的主键将该属性的属性修饰。

### <a name="invoking-database-operations"></a>调用数据库操作

`TodoItemRepository`类实现的数据库操作，并可以通过访问类的实例`App.TodoManager`属性。 `TodoItemRepository`类提供下列方法来调用数据库操作：

- **GetAllItemsAsync** – 从本地的 SQLite 数据库中检索的所有项。
- **GetItemAsync** – 从本地的 SQLite 数据库中检索指定的项。
- **SaveItemAsync** – 创建或更新本地的 SQLite 数据库中的项。
- **DeleteItemAsync** – 从本地的 SQLite 数据库中删除指定的项。

### <a name="platform-project-implementations"></a>平台项目实现

`Services` PCL 项目中的文件夹包含`IFileHelper`和`IAudioRecorderService`使用的接口`DependencyService`类用来定位在平台项目中实现的接口的类。

`IFileHelper`接口由实现`FileHelper`每个平台项目中的类。 此类包含单个方法`GetLocalFilePath`，这将返回一个用于存储 SQLite 数据库的本地文件路径。

`IAudioRecorderService`接口由实现`AudioRecorderService`每个平台项目中的类。 此类包含`StartRecording`， `StopRecording`，和支持的方法，这在平台 Api 中使用来自设备的麦克风录制音频，并将其存储为 wav 文件。 在 iOS 上`AudioRecorderService`使用`AVFoundation`录制音频的 API。 在 Android 上，`AudioRecordService`使用`AudioRecord`录制音频的 API。 在通用 Windows 平台 (UWP)，`AudioRecorderService`使用`AudioGraph`录制音频的 API。

### <a name="invoking-cognitive-services"></a>调用认知服务

示例应用程序将调用以下 Microsoft 认知服务：

- Microsoft 语音 API。 有关详细信息，请参阅[语音识别使用 Microsoft 语音 API](speech-recognition.md)。
- 必应拼写检查 API。 有关详细信息，请参阅[拼写检查使用必应拼写检查 API](spell-check.md)。
- 将转换 API。 有关详细信息，请参阅[文本转换使用转换器 API](text-translation.md)。
- 表面 API。 有关详细信息，请参阅[表情识别使用表面 API](emotion-recognition.md)。

## <a name="related-links"></a>相关链接

- [Microsoft 认知服务文档](https://www.microsoft.com/cognitive-services/documentation)
- [Todo 认知服务 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
