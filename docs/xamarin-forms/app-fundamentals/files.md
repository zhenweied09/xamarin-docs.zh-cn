---
title: "文件"
description: "使用嵌入的资源或针对本机文件系统 Api 编写，可以完成处理与 xamarin.forms 结合的文件。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/22/2017
ms.openlocfilehash: c6d10025ccc038ba160fe3c09f6ce92e97d916d2
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="files"></a>文件

_使用嵌入的资源或针对本机文件系统 Api 编写，可以完成处理与 xamarin.forms 结合的文件。_

## <a name="overview"></a>概述

Xamarin.Forms 代码在多个平台上运行 - 每个平台都有自己的文件系统。 这意味着读取和写入文件是大多数轻松地完成每个平台上使用本机文件 Api。 或者，嵌入的资源是更简单的解决方案，用于将与应用的数据文件。

本文档涵盖以下常见文件处理方案：

-  [ **文件作为资源嵌入**](#Loading_Files_Embedded_as_Resources) -文件可以作为应用程序并加载使用反射 API 的一部分提供。
-  [ **保存和加载文件**](#Loading_and_Saving_Files) -用户可写存储可以是本机实现，然后使用访问`DependencyService`。


第三方组件调用**PCLStorage**还可用于读取和写入到用户可访问存储 PCL 代码中的文件。

有关处理图像文件的信息，请参阅[处理映像](~/xamarin-forms/user-interface/images.md)页。

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>加载文件作为资源嵌入

若要嵌入到一个文件**PCL**程序集，创建或添加文件，并确保**生成操作： EmbeddedResource**。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![配置嵌入资源生成操作](files-images/vs-embeddedresource-sml.png "设置 EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "设置 EmbeddedResource BuildAction")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![文本文件嵌入在 PCL 中，配置嵌入的资源的生成操作](files-images/xs-embeddedresource-sml.png "设置 EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "设置 EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` 用于访问嵌入的文件使用其**资源 ID**。 默认情况下的资源 ID 是带有入-嵌入项目的默认命名空间前缀的文件名在这种情况下该程序集是**WorkingWithFiles** ，文件名为**PCLTextResource.txt**，因此，资源 ID 是`WorkingWithFiles.PCLTextResource.txt`。

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

`text`然后可以使用变量来显示文本或否则在代码中使用它。 此屏幕截图中的[示例应用程序](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)演示中呈现的文本`Label`控件。

 [![文本文件嵌入 PCL](files-images/pcltext-sml.png "嵌入文本文件中应用程序中显示的 PCL")](files-images/pcltext.png#lightbox "嵌入文本文件中应用程序中显示的 PCL")

加载和反序列化 XML 也同样简单。 下面的代码演示正在将 XML 文件加载并反序列化从资源，然后绑定到`ListView`进行显示。 XML 文件包含的数组`Monkey`（类的示例代码中定义） 的对象。

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![Xml 文件嵌入在 PCL 中，显示在 ListView 中](files-images/pclxml-sml.png "PCL 显示在 ListView 中嵌入的 XML 文件")](files-images/pclxml.png#lightbox "PCL 显示在 ListView 中嵌入的 XML 文件")

<a name="Embedding_in_Shared_Projects" />

### <a name="embedding-in-shared-projects"></a>在共享项目中嵌入

共享项目可以为嵌入的资源，还包含文件，但使用的前缀共享项目的内容编译到引用的项目，因为嵌入 Id 可以更改的文件资源。 这意味着每个嵌入文件的资源 ID 可能会为每个平台的不同。

有两种解决办法与共享项目的此问题：

-  **同步项目**-编辑每个平台使用的项目属性**相同**程序集名称和默认命名空间。 此值然后可将"硬编码"作为嵌入资源 Id 在共享项目中的前缀。
-  **#if 编译器指令**-使用编译器指令以设置正确的资源 ID 前缀，并使用该值来动态构造正确的资源 id。


说明第二个选项的代码所示。 编译器指令用于选择的硬编码资源前缀 （这通常是引用的项目的默认命名空间相同）。 `resourcePrefix`变量然后用于通过串联为嵌入的资源文件名创建有效的资源 ID。

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif
#if WINDOWS_PHONE
var resourcePrefix = "WorkingWithFiles.WinPhone.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>组织资源

上面的示例假定文件嵌入 PCL 项目，用例的资源 ID 的形式的根目录**Namespace.Filename.Extension**，如`WorkingWithFiles.PCLTextResource.txt`和`WorkingWithFiles.iOS.SharedTextResource.txt`。

就可以组织文件夹中的嵌入的资源。 嵌入的资源放入文件夹中，文件夹名称将成为一部分的资源 ID （用句点分隔），以便资源 ID 格式变为**Namespace.Folder.Filename.Extension**。 放置到一个文件夹中的示例应用中使用的文件**myfolder 文件夹**会使相应资源 Id`WorkingWithFiles.MyFolder.PCLTextResource.txt`和`WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`。

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>调试嵌入的资源

因为它是有时难以理解为何不加载特定的资源，可以暂时将下面的调试代码添加到应用程序，以帮助确认资源正确配置。 它将输出嵌入到到给定程序集中的所有已知的资源**错误**pad 来帮助调试加载问题的资源。

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>保存和加载文件

Xamarin.Forms 运行在多个平台上，每个都有其自己的文件系统，因为没有单个方法用于加载和保存用户创建的文件。 为了演示如何保存和加载示例应用程序包含一个屏幕，将保存并加载一些用户输入的文本文件完成的屏幕如下所示：

 [![保存和加载文本](files-images/saveandload-sml.png "保存和加载在应用程序中的文件")](files-images/saveandload.png#lightbox "保存和加载在应用程序中的文件")

每个平台都有一个略有不同的目录结构和不同的文件系统功能-例如 Xamarin.iOS 和 Xamarin.Android 支持大多数`System.IO`功能，而 Windows Phone 仅支持`IsolatedStorage`和[`Windows.Storage` ](http://msdn.microsoft.com/library/windowsphone/develop/jj681698(v=vs.105).aspx) Api。

若要获取解决此问题，示例应用程序时，可定义一个接口在 Xamarin.Forms PCL 中用于加载和保存文件。 它提供了一个简单的 API，用于加载和保存文本文件将存储在设备上。

```csharp
public interface ISaveAndLoad {
    void SaveText (string filename, string text);
    string LoadText (string filename);
}
```

PCL 代码然后使用[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)以获得对接口的本机实现的引用。 这允许可移植代码委托的加载和保存的文件复制到每个特定于平台的项目中编写的类。 在示例中，**保存**和**负载**按钮编写如下所示：

```csharp
var saveButton = new Button {Text = "Save"};
saveButton.Clicked += (sender, e) => {
    DependencyService.Get<ISaveAndLoad>().SaveText("temp.txt", input.Text);
};
var loadButton = new Button {Text = "Load"};
loadButton.Clicked += (sender, e) => {
    output.Text = DependencyService.Get<ISaveAndLoad>().LoadText("temp.txt");
};
```

然后实现需要文件实际上可以保存和加载之前要添加到每个特定于平台的项目。

### <a name="ios-and-android"></a>iOS 和 Android

对于 Xamarin.iOS 和 Xamarin.Android 项目接口的实现可以是完全相同。 代码如下所示，包括`[assembly: Dependency (typeof (SaveAndLoad))]`属性所需的`DependencyService`工作。

```csharp
[assembly: Dependency (typeof (SaveAndLoad))]
namespace WorkingWithFiles {
    public class SaveAndLoad : ISaveAndLoad {
        public void SaveText (string filename, string text) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            System.IO.File.WriteAllText (filePath, text);
        }
        public string LoadText (string filename) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            return System.IO.File.ReadAllText (filePath);
        }
    }
}
```

### <a name="universal-windows-platform-uwp-windows-81-and-windows-phone-81"></a>通用 Windows 平台 (UWP)、 Windows 8.1 和 Windows Phone 8.1

这些平台都具有不同的文件系统 API — [ `Windows.Storage` ](/windows/uwp/files/quickstart-reading-and-writing-files/) –，它是用于保存和加载文件。
`ISaveAndLoad`可以实现接口，如下所示：

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using WinApp;
using WorkingWithFiles;
using Xamarin.Forms;

[assembly: Dependency(typeof(SaveAndLoad_WinApp))]

namespace WindowsApp
{
    // https://msdn.microsoft.com/library/windows/apps/xaml/hh758325.aspx
    public class SaveAndLoad_WinApp : ISaveAndLoad
    {
        public async Task SaveTextAsync(string filename, string text)
        {
            StorageFolder localFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await localFolder.CreateFileAsync(filename, CreationCollisionOption.ReplaceExisting);
            await FileIO.WriteTextAsync(sampleFile, text);
        }
        public async Task<string> LoadTextAsync(string filename)
        {
            StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await storageFolder.GetFileAsync(filename);
            string text = await Windows.Storage.FileIO.ReadTextAsync(sampleFile);
            return text;
        }
    }
}
```


<a name="Saving_and_Loading_in_Shared_Projects" />

### <a name="saving-and-loading-in-shared-projects"></a>保存和加载在共享项目中

由于共享项目支持编译器指令所以有可能在共享项目中的单个类文件中包括所有特定于平台的代码 (而不使用`DependencyService`)。
单个`SaveAndLoad`类无法编写包含这两个实现更高版本，有选择地编译到引用的项目使用类似的编译器指令`#if WINDOWS_PHONE`， `#if __IOS__`，和`#if __ANDROID__`。

## <a name="additional-information"></a>其他信息

基于 PCL Xamarin.Forms 项目还可以充分利用[PCLStorage NuGet](http://www.nuget.org/packages/pclstorage) ([代码&amp;文档](https://pclstorage.codeplex.com/)) 来帮助实施方式跨平台的文件操作。


## <a name="summary"></a>摘要

本文档介绍了用于加载嵌入的资源和保存和加载设备上的文本的某些简单文件操作。 开发人员可以实现自己的本机文件 Api 使用`DependencyService`，使其很复杂，需要处理其文件操作要求。


## <a name="related-links"></a>相关链接

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [创建、 写入和读取文件 (UWP)](/windows/uwp/files/quickstart-reading-and-writing-files/)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
- [文件工作簿](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
