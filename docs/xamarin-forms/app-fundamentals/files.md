---
title: 在 Xamarin.Forms 中处理的文件
description: 可以使用代码在.NET Standard 库中，或通过使用嵌入的资源来实现处理用于 Xamarin.Forms 的文件。
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 87084a0ccc2970f56e7ef7a6d2f4c59c49032aa0
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527360"
---
# <a name="file-handling-in-xamarinforms"></a>在 Xamarin.Forms 中处理的文件

_可以使用代码在.NET Standard 库中，或通过使用嵌入的资源来实现处理用于 Xamarin.Forms 的文件。_

## <a name="overview"></a>概述

Xamarin.Forms 代码在多个平台上运行 - 每个平台都有自己的文件系统。 以前，这意味着，读取和写入文件非常轻松地执行每个平台上使用本机文件 Api。 或者，嵌入的资源是更简单的解决方案，用于将数据文件的应用。 但是，使用.NET Standard 2.0 有可能共享.NET Standard 库中的文件访问代码。

有关处理图像文件的信息，请参阅[处理图像](~/xamarin-forms/user-interface/images.md)页。

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>保存和加载文件

`System.IO`类可用于访问每个平台上的文件系统。 `File`类，可以创建、 删除和读取文件，和`Directory`类可以创建、 删除或枚举目录的内容。 此外可以使用`Stream`子类，它可以提供更高的控制文件操作 （如文件内压缩或位置搜索）。

可以使用编写文本文件`File.WriteAllText`方法：

```csharp
File.WriteAllText(fileName, text);
```

可以使用读取文本文件`File.ReadAllText`方法：

```csharp
string text = File.ReadAllText(fileName);
```

此外，`File.Exists`方法确定是否存在指定的文件：

```csharp
bool doesExist = File.Exists(fileName);
```

可通过使用的值从.NET Standard 库确定的每个平台上的文件的路径[ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder)枚举的第一个参数作为`Environment.GetFolderPath`方法。 这可组合使用的文件名`Path.Combine`方法：

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

示例应用，其中包括一个页面，将保存并加载文本中演示了这些操作：

[![保存和加载文本](files-images/saveandload-sml.png "保存和加载应用程序中的文件")](files-images/saveandload.png#lightbox "保存和加载应用程序中的文件")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>加载文件作为资源嵌入

若要嵌入到一个文件 **.NET Standard**程序集，创建或添加文件，并确保**生成操作： EmbeddedResource**。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![配置嵌入资源生成操作](files-images/vs-embeddedresource-sml.png "设置 EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "设置 EmbeddedResource BuildAction")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![在 PCL 中，配置嵌入的资源生成操作中嵌入的文本文件](files-images/xs-embeddedresource-sml.png "设置 EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "设置 EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` 用于访问嵌入的文件使用其**资源 ID**。 默认情况下的资源 ID 是带有-中嵌入的项目的默认命名空间前缀的文件名在这种情况下该程序集是**WorkingWithFiles** ，文件名为**PCLTextResource.txt**，因此资源 ID 是`WorkingWithFiles.PCLTextResource.txt`。

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

`text`然后可以使用变量来显示文本或否则代码中使用它。 此屏幕截图[示例应用](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)将会显示在呈现文本`Label`控件。

 [![在 PCL 中嵌入的文本文件](files-images/pcltext-sml.png "嵌入文档中的应用程序中显示的 PCL")](files-images/pcltext.png#lightbox "嵌入文档中的应用程序中显示的 PCL")

加载和反序列化 XML 也同样简单。 下面的代码演示的 XML 文件加载并反序列化从一种资源，然后绑定到`ListView`进行显示。 XML 文件包含的数组`Monkey`（在示例代码中定义的类） 的对象。

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

 [![Xml 文件嵌入在 PCL 中，显示在 ListView](files-images/pclxml-sml.png "嵌入的 XML 文件在 ListView 中显示的 PCL")](files-images/pclxml.png#lightbox "PCL 显示在 ListView 中嵌入的 XML 文件")

<a name="Embedding_in_Shared_Projects" />

## <a name="embedding-in-shared-projects"></a>在共享项目中嵌入

共享项目可以作为嵌入资源，还包含文件，但共享项目的内容将编译到引用的项目，因为前缀用于嵌入的文件资源 Id 可以更改。 这意味着每个嵌入的文件的资源 ID 可能会因每个平台。

有两个解决方案共享的项目这一问题：

-  **同步项目**-编辑每个平台使用的项目属性**同一**程序集名称和默认命名空间。 此值然后可以作为嵌入资源共享项目中的 Id 的前缀为"硬编码"。
-  **#if 编译器指令**-使用编译器指令设置正确的资源 ID 前缀，并使用该值来动态构造正确的资源 id。


说明第二个选项的代码如下所示。 编译器指令用于选择的硬编码资源前缀 （这通常是与引用的项目的默认命名空间相同）。 `resourcePrefix`变量然后用于通过串联使用嵌入的资源文件名创建一个有效的资源 ID。

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>组织资源

上面的示例假定该文件嵌入.NET Standard 库项目，为资源 id 的形式的根目录**Namespace.Filename.Extension**，如`WorkingWithFiles.PCLTextResource.txt`和`WorkingWithFiles.iOS.SharedTextResource.txt`。

就可以组织文件夹中的嵌入的资源。 当嵌入的资源放置在文件夹中时，文件夹名称一部分的资源 ID （用句点分隔），以便资源 ID 格式变为**Namespace.Folder.Filename.Extension**。 将示例应用程序中使用到的文件夹的文件放**MyFolder**会使相应资源 Id`WorkingWithFiles.MyFolder.PCLTextResource.txt`和`WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`。

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>调试嵌入的资源

因为它是有时难以理解为什么不加载特定的资源，可以暂时将以下调试代码添加到应用程序以帮助确认正确配置了资源。 它会输出所有已知的资源嵌入到给定的程序集**错误**板来帮助调试加载问题的资源。

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

## <a name="summary"></a>总结

本文介绍了一些简单的文件操作来保存和加载在设备上的文本和用于加载嵌入的资源。 使用.NET Standard 2.0，则可以共享.NET Standard 库中的文件访问代码。

## <a name="related-links"></a>相关链接

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
- [使用 Xamarin.iOS 中的文件系统](~/ios/app-fundamentals/file-system.md)

