---
title: 在 Xamarin iOS 和 Android 的 visual Basic.NET
description: 本演练演示如何生成本机的 Xamarin.iOS 和 Xamarin.Android 应用程序使用以 Visual Basic.NET 编写的业务逻辑。
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: a3e63f6c8d9b35a8158e2db6a48734b2205fe54c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782276"
---
# <a name="visual-basicnet-in-xamarin-ios-and-android"></a>在 Xamarin iOS 和 Android 的 visual Basic.NET

[TaskyPortable](/samples/mobile/VisualBasic/TaskyPortableVB/)示例应用程序演示如何通过 Xamarin 使用 Visual Basic 代码编译到可移植类库。 下面是在 iOS、 Android 和 Windows Phone 上运行的生成应用的一些屏幕快照：

 [![](native-apps-images/image5.png "iOS、 Android 和 Windows 手机上运行使用 Visual Basic 创建的应用程序")](native-apps-images/image5.png#lightbox)

IOS、 Android 和 Windows Phone 在示例中的项目用 C# 编写。 每个应用程序的用户界面使用本机技术生成的 (情节提要、 Xml 和 Xaml 分别)，而`TodoItem`management 由 Visual Basic 可移植类库提供使用`IXmlStorage`由提供的实现本机项目。

## <a name="sample-walkthrough"></a>示例演练

本指南介绍如何将 Visual Basic 实现中[TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)用于 iOS 和 Android 的 Xamarin 示例。

> [!NOTE]
> 在查看说明[Visual Basic.NET Pcl](/guides/cross-platform/application_fundamentals/pcl/portable_visual_basic_net/)然后再继续进行本指南。

## <a name="visualbasicportablelibrary"></a>VisualBasicPortableLibrary

仅可以在 Visual Studio 中创建 Visual Basic 可移植类库。
示例库包含我们四个 Visual Basic 文件中的应用程序的基础知识：

-  IXmlStorage.vb
-  TodoItem.vb
-  TodoItemManager.vb
-  TodoItemRepositoryXML.vb


### <a name="ixmlstoragevb"></a>IXmlStorage.vb

由于文件访问行为平台之间变化太大，因此不提供可移植类库`System.IO`文件存储 Api 任何配置文件中的。 这意味着，如果我们想要直接与我们的可移植代码中的文件系统进行交互，我们需要回叫我们本机项目每个平台上。  通过编写针对每个平台可以在 C# 中实现一个简单的界面我们 Visual Basic 代码，我们可以仍有权访问文件系统的可共享 Visual Basic 代码。

示例代码使用此非常简单的界面，其中包含仅使用两个方法： 读取和写入序列化的 Xml 文件。

```vb
Public Interface IXmlStorage
    Function ReadXml(filename As String) As List(Of TodoItem)
    Sub WriteXml(tasks As List(Of TodoItem), filename As String)
End Interface
```

iOS、 Android 和 Windows Phone 实现此接口将显示在指南的后面。

### <a name="todoitemvb"></a>TodoItem.vb

此类包含用于在整个应用程序的业务对象。 它将在 Visual Basic 中定义，并与 iOS、 Android 和 Windows Phone 共享用 C# 编写的项目。

类定义如下所示：

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

此示例使用 XML 序列化和反序列化来加载和保存的 TodoItem 对象。

### <a name="todoitemmanagervb"></a>TodoItemManager.vb

管理器类提供了可移植代码的 API。 它提供了有关的基本 CRUD 操作`TodoItem`类，但这些操作没有实现。

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String, storage As IXmlStorage)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

构造函数采用 IXmlStorage 的实例作为参数。 这可让每个平台提供其自己的工作实现，但仍会允许描述可共享的其他功能的可移植代码。

### <a name="todoitemrepositoryvb"></a>TodoItemRepository.vb

存储库类包含用于管理的 TodoItem 对象的列表的逻辑。 完整的代码所示 – 逻辑存在主要是为了跨 TodoItems 管理一个唯一的 ID 值，如添加和从集合中移除。

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String, storage As IXmlStorage)
        _filename = filename
        _storage = storage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> 此代码是一种非常基本的数据存储机制的一个示例。
> 提供它是为了演示如何可移植类库可以编写代码，针对一个访问特定于平台的功能 （在此情况下，加载和保存 Xml 文件） 的接口。 但它并不是一个生产质量数据库的替代方案。

## <a name="ios-android-and-windows-phone-application-projects"></a>iOS、 Android 和 Windows Phone 应用程序项目

本部分包含 IXmlStorage 接口的特定于平台的实现，并演示如何使用每个应用程序中。 所有是用 C# 编写的应用程序项目。

### <a name="ios-and-android-ixmlstorage"></a>iOS 和 Android IXmlStorage

Xamarin.iOS 和 Xamarin.Android 为提供的完全`System.IO`功能以便你可以轻松地加载和保存 Xml 文件使用以下类：

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        if (File.Exists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new FileStream(filename, FileMode.Open))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(filename))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

在 iOS 应用程序中`TodoItemManager`和`XmlStorageImplementation`中创建**AppDelegate.cs**文件中此代码段所示。 前四行仅生成数据将存储位置; 文件的路径最后两行显示两个类实例化。

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

在 Android 应用程序中`TodoItemManager`和`XmlStorageImplementation`中创建**Application.cs**文件中此代码段所示。 前三行只构建数据将存储位置; 文件的路径最后两行显示两个类实例化。

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new AndroidTodo.XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

应用程序代码的其余部分是主要关心的用户界面和使用`TaskMgr`类来加载和保存`TodoItem`类。

### <a name="windows-phone-ixmlstorage"></a>Windows Phone IXmlStorage

Windows Phone 不提供使用改为公开 IsolatedStorage API 的到设备的文件系统的完全访问权限。 `IXmlStorage` Windows Phone 实现如下所示：

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        if (fileStorage.FileExists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new StreamReader(new IsolatedStorageFileStream(filename, FileMode.Open, fileStorage)))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(new IsolatedStorageFileStream(filename, FileMode.OpenOrCreate, fileStorage)))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

`TodoItemManager`和`XmlStorageImplementation`中创建**App.xaml.cs**文件中此代码段所示。

```csharp
var filename = "TodoList.xml";
var xmlStorage = new XmlStorageImplementation();
TodoMgr = new TodoItemManager(filename, xmlStorage);
```

Windows Phone 应用程序的其余部分包含的 Xaml 和 C# 创建的用户界面和使用`TodoMgr`类来加载和保存`TodoItem`对象。

## <a name="visual-basic-pcl-in-visual-studio-for-mac"></a>Visual Basic 在 Visual Studio 中适用于 Mac 的 PCL

适用于 Mac 的 visual Studio 不支持 Visual Basic 语言-无法创建或为 mac。 编译使用 Visual Studio 的 Visual Basic 项目

Visual Studio for Mac 的可移植类库的支持意味着它可以引用了 PCL 从 Visual Basic 生成的程序集。

本部分介绍如何 PCL 程序集在 Visual Studio 中的编译，然后确保将是存储在版本控制系统和其他项目引用的。

### <a name="keeping-the-pcl-output-from-visual-studio"></a>从 Visual Studio 保持 PCL 输出

默认情况下将配置 （包括 TFS 和 Git） 的大多数版本控制系统为忽略 **/bin/** 不会存储这意味着编译的 PCL 程序集的目录。 这意味着你将需要手动将其复制到运行 Visual Studio for Mac 以添加对它的引用的任何计算机。

若要确保版本控制系统可以存储 PCL 程序集输出，可以创建将它复制到项目根目录的后期生成脚本。 此生成后步骤有助于确保可以轻松地添加到源代码管理和与其他项目共享程序集。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

1. 右键单击项目并选择**属性 > 生成事件**部分。

2. 添加_后期生成_将输出 DLL 中此项目复制到项目根目录下的脚本 (即之外 **/bin/**)。 根据您的版本控制配置，该 DLL 现在应该能够添加到源代码管理。

  [![](native-apps-images/image6-vs-sml.png "生成后生成脚本，以便将 VB DLL 复制的事件")](native-apps-images/image6-vs.png#lightbox)

#### <a name="visual-studio-2015"></a>Visual Studio 2015

1.  右键单击项目并选择**属性 > 编译**，然后，确保在左上角梳理框中选择所有配置。 单击**生成事件...** 右下角的按钮。

    [![](native-apps-images/image6.png "项目属性编译部分")](native-apps-images/image6.png#lightbox)

1.  添加后期生成脚本，将输出 DLL 中此项目复制到项目根目录下 (即之外 **/bin/** )。 根据您的版本控制配置，该 DLL 现在应该能够添加到源代码管理。

    [![](native-apps-images/image7.png "生成事件窗口")](native-apps-images/image7.png#lightbox)

#### <a name="all-versions"></a>所有版本

下一次生成项目时，可移植类库程序集将被复制到项目根目录位置，并且你检查中/提交/推送所做的更改 DLL 将存储 （以便适用于 Mac，可以使用 Visual Studio 的 Mac 上下载它）。

  [![](native-apps-images/image8-sml.png "输出 Visual 基本程序集的文件位置")](native-apps-images/image8.png#lightbox)


此程序集可以然后添加到 Visual Studio 中的 Xamarin 项目用于 Mac，即使 Xamarin iOS 或 Android 项目中不支持 Visual Basic 语言本身也是如此。

### <a name="referencing-the-pcl-in-visual-studio-for-mac"></a>引用在 Visual Studio 中的 PCL for Mac

由于 Xamarin 不支持 Visual Basic，因此它不能加载 PCL 项目中 （或 Windows Phone 应用），此屏幕截图中所示：

 [![](native-apps-images/image9.png "Mac 解决方案的 visual Studio")](native-apps-images/image9.png#lightbox)

我们仍可以在 Xamarin.iOS 和 Xamarin.Android 项目中包括 Visual Basic PCL 程序集 DLL:

1.  右键单击**引用**节点，然后选择**编辑引用...**

    [![](native-apps-images/image10.png "项目编辑引用菜单")](native-apps-images/image10.png#lightbox)

1.  选择 **.Net 程序集**选项卡上，并导航到 Visual Basic 项目目录中的输出 DLL。 即使适用于 Mac 的 Visual Studio 无法打开项目，所有文件应都在那里从源代码管理。 单击**添加**然后**确定**将此程序集添加到 iOS 和 Android 应用程序。

    [![](native-apps-images/image11-sml.png "单击添加，然后确定将此程序集添加到 iOS 和 Android 应用程序")](native-apps-images/image11.png#lightbox)

1.  IOS 和 Android 应用程序现在可以包含由 Visual Basic 可移植类库的应用程序逻辑。 此屏幕截图中显示的 iOS 应用程序引用 Visual Basic PCL，而且具有从该库中使用的功能的代码。

    [![](native-apps-images/image12-sml.png "编辑引用添加.NET 程序集窗口")](native-apps-images/image12.png#lightbox)


如果更改到 Visual Studio 中的 Visual Basic 项目记住若要生成项目，请在源代码管理中存储生成的程序集 DLL，然后拉取到你的 Mac 的源控件从该新 DLL，以便适用于 Mac 的 Visual Studio 将生成包含最新功能。


## <a name="summary"></a>总结

本文演示了如何使用 Visual Basic 代码中使用 Visual Studio 和可移植类库的 Xamarin 应用程序。 即使 Xamarin 不直接支持 Visual Basic，将 Visual Basic 编译成 PCL 允许使用要包括在 iOS 和 Android 应用的 Visual Basic 编写的代码。

## <a name="related-links"></a>相关链接

- [TaskyPortableVB （示例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [使用.NET Framework (Microsoft) 的跨平台开发](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
