---
title: Xamarin.Forms 中处理的文件
description: 可以使用代码，在标准.NET 库中，或通过使用嵌入的资源来实现与 xamarin.forms 结合处理的文件。
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 0be441a7be9777698212e690aca95fdd75e5050f
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310148"
---
# <a name="file-handling-in-xamarinforms"></a>Xamarin.Forms 中处理的文件

_可以使用代码，在标准.NET 库中，或通过使用嵌入的资源来实现与 xamarin.forms 结合处理的文件。_

## <a name="overview"></a>概述

Xamarin.Forms 代码在多个平台上运行 - 每个平台都有自己的文件系统。 以前，这意味着，读取和写入文件时非常方便地执行每个平台上使用本机文件 Api。 或者，嵌入的资源是更简单的解决方案，用于将与应用的数据文件。 但是，.NET 标准 2.0 是可以共享.NET 标准库中的文件访问代码。

有关处理图像文件的信息，请参阅[处理映像](~/xamarin-forms/user-interface/images.md)页。

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>保存和加载文件

`System.IO`类可以用于访问每个平台上的文件系统。 `File`类，可以创建、 删除和读取文件，和`Directory`类可以创建、 删除或枚举目录的内容。 你还可以使用`Stream`子类，它可以提供更大程度上控制文件操作 （如文件中压缩或位置搜索）。

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

使用的一个值，可以从.NET 标准库确定的每个平台上的文件路径[ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder)枚举的第一个参数为`Environment.GetFolderPath`方法。 这可以然后与结合使用的文件名`Path.Combine`方法：

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

这些操作所示示例应用，其中包括将保存并加载文本的页：

[![保存和加载文本](files-images/saveandload-sml.png "保存和加载在应用程序中的文件")](files-images/saveandload.png#lightbox "保存和加载在应用程序中的文件")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>加载文件作为资源嵌入

若要嵌入到一个文件 **.NET 标准**程序集，创建或添加文件，并确保**生成操作： EmbeddedResource**。

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

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>组织资源

上面的示例假定文件嵌入的用例的资源 ID 的形式的标准.NET 库项目根**Namespace.Filename.Extension**，如`WorkingWithFiles.PCLTextResource.txt`和`WorkingWithFiles.iOS.SharedTextResource.txt`。

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

## <a name="summary"></a>总结

本文介绍了一些简单的文件操作来保存和加载在设备上，文本和用于加载嵌入的资源。 使用.NET 标准 2.0 就可能共享.NET 标准库中的文件访问代码。

## <a name="related-links"></a>相关链接

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
- [使用 Xamarin.iOS 中的文件系统](~/ios/app-fundamentals/file-system.md)
- [文件工作簿](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
