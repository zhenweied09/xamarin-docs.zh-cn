---
title: 使用 Xamarin.iOS 中的文件系统
description: 本文档介绍如何使用 Xamarin.iOS 中的文件系统。 它讨论了目录，读取文件、 XML 和 JSON 序列化、 应用程序沙盒，共享文件通过 iTunes 和的详细信息。
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: cf595b57906cf1c47acdcdbcddf04bfbdc963393
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113425"
---
# <a name="working-with-the-file-system-in-xamarinios"></a>使用 Xamarin.iOS 中的文件系统

可以使用 Xamarin.iOS 和`System.IO`中的类 *.NET 基类库 (BCL)* 访问 iOS 文件系统。 `File`类，可以创建、 删除和读取文件，和`Directory`类可以创建、 删除或枚举目录的内容。 此外可以使用`Stream`子类，它可以提供更高的控制文件操作 （如文件内压缩或位置搜索）。

iOS 限制某些应用程序可以使用文件系统来保持应用程序的数据的安全性和保护用户免受恶意应用执行的操作。 这些限制是一部分*应用程序沙盒*– 一组规则，用于限制对文件、 首选项、 网络资源、 硬件、 等的应用程序的访问。应用程序仅限于读取和写入 （已安装位置）; 其主目录中的文件它不能访问另一个应用程序的文件。

iOS 还具有某些文件特定于系统的功能： 某些目录需要特殊处理方式与备份和升级，并且应用程序还可以共享通过 iTunes 文件。

本文讨论的功能和限制的 ios 文件系统中详细信息，并包括演示如何使用 Xamarin.iOS 执行一些简单的文件系统操作的示例应用程序：

 [![](file-system-images/05-sampleapp.png "一个示例 iOS 执行一些简单的文件系统操作")](file-system-images/05-sampleapp.png#lightbox)

 <a name="General_File_Access" />

## <a name="general-file-access"></a>常规文件访问

Xamarin.iOS 允许您使用.NET`System.IO`用于在 iOS 上的文件系统操作的类。

下面的代码段演示了一些常见的文件操作。 您会发现其所有下面传入`SampleCode.cs`文件，请在本文的示例应用程序。

<a name="Working_with_directories" />

### <a name="working-with-directories"></a>与处理目标目录

此代码枚举当前目录中的子目录 (通过指定"。 /"参数)，这是应用程序的可执行文件的位置。
输出将是所有文件和与 （显示在控制台窗口中进行调试时） 应用程序部署的文件夹的列表。

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

 <a name="Reading_files" />


### <a name="reading-files"></a>读取文件

要读取的文本文件，只需一行代码。 此示例会在应用程序输出窗口中显示文本文件的内容。

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

 <a name="XML_Serialization" />


### <a name="xml-serialization"></a>XML 序列化

尽管使用完整`System.Xml`命名空间不在本文的范围内，你可以轻松地进行反序列化文件系统中提供的 XML 文档使用 StreamReader 如下：

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

请参阅的 MSDN 文档[System.Xml](http://msdn.microsoft.com/library/system.xml.aspx)有关详细信息的命名空间[序列化](http://msdn.microsoft.com/library/system.xml.serialization.aspx)。 此外应查看[Xamarin.iOS 文档](~/ios/deploy-test/linker.md)链接器 – 通常需要添加`[Preserve]`所要序列化的类的属性。

 <a name="Creating_Files_and_Directories" />


### <a name="creating-files-and-directories"></a>创建文件和目录

此示例演示如何使用`Environment`类来访问我们可以在其中创建文件和目录文档文件夹。

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

创建一个目录是一个非常类似的过程：

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

System.IO 命名空间的详细信息，请参阅[MSDN 文档](http://msdn.microsoft.com/library/system.io.aspx)。


### <a name="serializing-json"></a>序列化 Json

使用 Json 的 Xamarin.iOS 应用程序中的数据是非常容易使用[Json.NET](http://www.newtonsoft.com/json) for.NET NuGet 程序包的高性能 JSON 框架。 只需将 NuGet 包添加到应用程序的项目： 

[![](file-system-images/json01.png "将 NuGet 包添加到应用程序项目")](file-system-images/json01.png#lightbox)

接下来，添加一个类来充当序列化/反序列化的数据模型 (在这种情况下`Account.cs`):

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        #region Computed Properties
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }
        #endregion

        #region Constructors
        public Account() {

        }
        #endregion
    }
}
```

最后，创建的实例`Account`类，其序列化为 json 数据并将其写入到文件：

```csharp
// Create a new record
var account = new Account(){
    Email = "monkey@xamarin.com",
    Active = true,
    CreatedDate = new DateTime(2015, 5, 27, 0, 0, 0, DateTimeKind.Utc),
    Roles = new List<string> {"User", "Admin"}
};

// Serialize object
var json = JsonConvert.SerializeObject(account, Newtonsoft.Json.Formatting.Indented);

// Save to file
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "account.json");
File.WriteAllText(filename, json);
```
请参阅 Json.NET[文档](http://www.newtonsoft.com/json/help)有关如何使用.NET 应用程序中的 json 数据的详细信息。

<a name="Special_Considerations" />

## <a name="special-considerations"></a>特别注意的事项

尽管 Xamarin.iOS 和.NET 的文件操作之间的相似之处，iOS 和 Xamarin.iOS 不同于.NET 在一些重要方面。

 <a name="runtimeaccessible" />


### <a name="making-project-files-accessible-at-runtime"></a>使项目文件可在运行时访问

默认情况下，如果将文件添加到你的项目，其不包含在最终的程序集，并因此不会提供给应用程序。 若要包含在程序集中一个文件，必须将其标记具有特殊的生成操作，称为内容。

要将标记包含的文件，右键单击文件，然后选择**生成操作&gt;内容**在 Visual Studio for mac。 此外可以更改**生成操作**中的文件**属性**工作表。

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>区分大小写

务必要了解 iOS 文件系统*区分大小写*。 这意味着你的文件和目录名称必须完全匹配 – README.txt 和 readme.txt 将被视为不同的文件名。

这可能是面向.NET 开发人员更熟悉 Windows 文件系统，这是令人困惑*不区分大小写*–"文件"、"文件"和"文件"将所有引用相同的目录。

因此，但 iOS 设备区分大小写，并应记住使用，编写代码，的 iOS 模拟器是不区分大小写的默认值。 这意味着如果文件名大小写不同文件本身和代码中的对其引用之间，你的代码可能仍工作在模拟器中，但在实际设备上将会失败。 这是为什么务必要部署到实际设备早期和 iOS 开发过程通常的原因之一。

 <a name="Path_Separator" />


### <a name="path-separator"></a>路径分隔符

iOS 使用正斜杠 / 作为路径分隔符 (这是不同于 Windows，使用反斜杠 \)。

由于此令人困惑的差异，很好的做法使用`System.IO.Path.Combine`方法，它可调整为当前平台，而不是硬编码特定的路径分隔符。 这是一个简单的步骤，使代码更易于移植到其他平台。

 <a name="Application_Sandbox" />


## <a name="application-sandbox"></a>应用程序沙盒

到文件系统 （和其他资源，例如网络和硬件功能） 的应用程序的访问权限仅限于出于安全原因。 此限制称为*应用程序沙盒*。 文件系统中，根据你的应用程序被限制为创建和删除其主目录中文件和目录。

主目录在你的应用程序及其所有数据的存储位置的文件系统中是唯一的位置。 你不能选择 （或更改） 应用程序的主目录的位置但是 iOS 和 Xamarin.iOS 提供属性和方法来管理文件和目录。

 <a name="The_Application_Bundle" />


## <a name="the-application-bundle"></a>应用程序捆绑包

*应用程序捆绑包*是包含你的应用程序的文件夹。
通过让.app 后缀添加到的目录名称，它被区分从其他文件夹。 应用程序捆绑包包含可执行文件和所有内容 （文件、 图像等） 需要为你的项目。

当您浏览到你在 Mac OS 中的应用程序捆绑包时，它将显示使用不同的图标不是其他目录中查看 （和隐藏.app 后缀）;但是，它是只需以不同方式显示操作系统的常规目录。

若要查看示例代码应用程序捆绑包，右键单击该项目在 Visual Studio 中适用于 Mac 和选择**打开所在文件夹**。 然后导航到**bin/Debug/** 应从何处您查找应用程序图标 （类似于下面的屏幕截图）。

 [![](file-system-images/40-bundle.png "导航到 bin/Debug 若要查找应用程序图标类似于以下屏幕截图")](file-system-images/40-bundle.png#lightbox)

右键单击此图标，然后选择**查看包内容**浏览应用程序捆绑包目录的内容。 内容显示就像一个常规目录的内容，如下所示：

 [![](file-system-images/45-bundle.png "应用程序捆绑包的内容")](file-system-images/45-bundle.png#lightbox)

应用程序捆绑包是什么在测试期间，安装在模拟器或设备上，最后，什么提交到 Apple 以包含在 App Store。

 <a name="Application_Directories" />


## <a name="application-directories"></a>应用程序目录

在设备上安装应用程序时，操作系统将创建其主目录，并将在您应用程序捆绑包。 你的代码可以访问应用程序捆绑包，读取数据，但不是应写入到该根目录中，对其进行签名并进行任何修改将使你的应用程序无效，导致无法启动。

这样，则应写入到根目录，尽管<b>在 iOS 7 及更早版本中</b>会产生大量的目录，应用程序根目录中可供使用。 <b>用户可访问的目录，在 iOS 8<a href="https://developer.apple.com/library/ios/technotes/tn2406/_index.html" target="_blank">不位于</a>内的应用程序根目录</b>。

下面列出了这些目录和它们的用途：

&nbsp;

|目录|描述|
|---|---|
|[ApplicationName].app/|**在 iOS 7 及更早版本中**这是`ApplicationBundle`存储应用程序可执行文件目录。 在应用中创建的目录结构存在此目录 （例如，图像和其他文件类型，在 Visual Studio for Mac 项目中已标记为资源） 中。<br /><br />如果需要访问内部应用程序捆绑包的内容文件，此目录的路径是可通过`NSBundle.MainBundle.BundlePath`属性。|
|文档 /|使用此目录来存储用户文档和应用程序数据文件。<br /><br />此目录的内容可以是供用户通过 iTunes 文件共享 （尽管默认情况下禁用）。 添加`UIFileSharingEnabled`要允许用户访问这些文件的 Info.plist 文件的布尔型关键字。<br /><br />即使应用程序不会立即启用文件共享，应避免将置于应隐藏此目录中用户的文件 (如数据库文件，除非您想要进行共享)。 只要保持隐藏状态的敏感文件，这些文件将不会公开 （和可能移动，已修改，或通过 iTunes 删除） 如果的未来版本中启用文件共享。<br /><br /> 可以使用`Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)`方法以获取有关你的应用程序的文档目录的路径。<br /><br />通过 iTunes 备份此目录的内容。|
|库 /|库目录是存储不直接由用户，如数据库或其他应用程序生成的文件创建的文件的好时机。 通过 iTunes 向用户永远不会公开此目录的内容。<br /><br />可以在 Library; 中创建您自己的子目录但是，已有的某些系统创建目录下面，您应该注意，包括首选项和缓存。<br /><br />通过 iTunes 备份 （除了缓存子目录中） 此目录的内容。 将备份在库中创建的自定义目录。|
|库/首选项 /|特定于应用程序首选项文件存储在此目录中。 不要直接创建这些文件。 请改用`NSUserDefaults`类。<br /><br />通过 iTunes 备份此目录的内容。|
|缓存库 / /|缓存目录是存储数据文件，可帮助你的应用程序的好时机运行，但如果需要，可以轻松地重新创建。 应用程序应创建并根据需要删除这些文件并将无法重新创建这些文件，如有必要。 iOS 5 还可能会删除这些文件 （在极低的存储情况下），但它不会执行操作，应用程序运行时。<br /><br />此目录的内容不会备份通过 iTunes，这意味着它们将不会显示，如果用户将设备还原，并且它们可能不会安装您的应用程序的更新的版本后显示。<br /><br />例如，如果你的应用程序无法连接到网络，可能使用的缓存目录来存储数据或文件，以提供良好的脱机体验。 应用程序可以保存并等待网络响应时快速检索此数据，但它不需要进行备份，可轻松地恢复或还原或版本更新后重新创建。|
|tmp /|应用程序可以存储在此目录中在短时间内，才需要的临时文件。 为了节省空间，应在不再需要时删除文件。 应用程序未运行时，操作系统也可以从该目录删除文件。<br /><br />此目录的内容不备份通过 iTunes。<br /><br />例如，可能会使用 tmp 目录来存储临时文件的下载 （如 Twitter 头像或电子邮件附件），用户的显示，但后他们已被查看 （并且如有必要在将来重新下载无法删除该).|

此屏幕截图显示在查找器窗口中的目录结构：

 [![](file-system-images/08-library-directory.png "此屏幕截图显示在查找器窗口中的目录结构")](file-system-images/08-library-directory.png#lightbox)

 <a name="Accessing_Other_Directories_Programmatically" />

### <a name="accessing-other-directories-programmatically"></a>以编程方式访问其他目录

访问在前面的目录和文件示例`Documents`目录。 要写入到另一个目录，必须构造路径使用".."语法如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

创建一个目录是非常相似：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

路径`Caches`和`tmp`目录可构造如下：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

 <a name="Sharing_Files_with_the_User_through_iTunes" />


## <a name="sharing-files-with-the-user-through-itunes"></a>与通过 iTunes 用户共享文件

用户可以访问应用程序的文档目录中的文件，通过编辑`Info.plist`并创建**应用程序支持 iTunes 共享**(`UIFileSharingEnabled`) 中的条目**源**视图中，为如下所示：

 [![](file-system-images/09-uifilesharingenabled-plist.png "添加应用程序支持 iTunes 共享属性")](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

可以在 iTunes 中访问这些文件，当连接设备并在用户选择`Apps`选项卡。例如，下面的屏幕截图显示了通过 iTunes 共享所选应用程序中的文件：

 [![](file-system-images/10-itunes-file-sharing.png "此屏幕截图显示在所选应用程序中通过 iTunes 共享文件")](file-system-images/10-itunes-file-sharing.png#lightbox)

用户只能访问通过 iTunes 此目录中的顶级项。 （尽管它们可以将它们复制到他们的计算机，或将其删除），它们不能看到的任何子目录的内容。 例如，使用 GoodReader，PDF 和 EPUB 文件可与共享应用程序，以便用户可以在其 iOS 设备上阅读它们。

如果不小心修改他们的文档文件夹的内容的用户可能导致问题。 你的应用程序应考虑这一点，并从破坏性更新的文档文件夹中复原。

这篇文章的示例代码在文档文件夹中创建文件和文件夹 (在**SampleCode.cs**)，并启用文件共享中**Info.plist**文件。 此屏幕截图显示了这些在 iTunes 中显示的方式：

 [![](file-system-images/15-itunes-file-sharing-example.png "此屏幕截图显示了如何在 iTunes 中显示文件")](file-system-images/15-itunes-file-sharing-example.png#lightbox)

请参阅[处理图像](~/ios/app-fundamentals/images-icons/index.md)一文，了解有关如何设置的图标为应用程序和任何自定义文档类型的信息创建。

如果`UIFileSharingEnabled`密钥是 false 或不存在，则文件共享，默认情况下为禁用，并且用户将无法与你 Documentsdirectory 进行交互。

 <a name="Backup_and_Restore" />

## <a name="backup-and-restore"></a>备份和还原

通过 iTunes 备份设备，除以下将保存在应用程序的主目录中创建的所有目录：

-   **[ApplicationName].app** – 应用程序捆绑包*does*备份，但仅限于时此捆绑包进行了哪些更改 （例如，应用程序更新已安装）。 不管怎样，因为对其进行签名，因此在安装后必须保持不变，不应修改此目录。 
-   **库/缓存**– 缓存目录适用于不需要备份的工作文件。 
-   **tmp** – 此目录用于创建和删除不再需要时，临时文件或文件的 iOS 删除需要空间时。 


备份大量数据可能需要长时间。 如果你决定需要备份的任何特定文档或数据，文档和库，应用程序应仅使用此文件夹。 对于暂时性数据或可以轻松地从网络检索的文件，使用缓存或 tmp 目录。

请注意，iOS 将 clean 文件系统上的磁盘空间严重不足设备在运行时。 此过程将从库/缓存和 tmp 中删除所有文件的当前未运行的应用程序的文件夹。

 <a name="Complying_with_iOS5_iCloud_Backup_Restrictions" />

## <a name="complying-with-ios5-icloud-backup-restrictions"></a>遵守 iOS5 iCloud 备份限制

Apple 引入*iCloud 备份*iOS 5 的功能。 启用 iCloud 备份时，应用程序的主目录中的所有文件 (不包括不会正常备份，例如，应用程序捆绑的目录`Caches`和`tmp`) 是备份到 iCloud 服务器。 在其设备丢失、 被盗或损坏的情况下，此功能提供了具有完整备份的用户。

因为 iCloud 仅提供给每个用户 5 Gb 的免费空间，以避免不必要地使用带宽，Apple 要求应用程序到仅备份基本用户生成的数据。 若要符合 iOS 数据存储准则应限制通过遵守以下各项获取备份的数据量：

-  仅存储用户生成的数据或不能为文档目录 （即备份） 中重新创建的数据。 
-  以便可以轻松地重新创建或重新下载中的任何其他数据存储`Library/Caches`或`tmp`（这不是备份，且无法将其清除）。 
-  如果你有可能适用于的文件`Library/Caches`或`tmp`文件夹，但您不想要清除，将其存储在其他位置 (如`Library/YourData`) 并应用不能反向向上属性可防止他们使用 iCloud 备份 ba 阻止文件ndwidth 和存储空间。 因此，应该仔细管理并删除它在可能的情况，此数据仍将用尽设备上的空间。 

不重新启动使用设置属性`NSFileManager`类。 请确保您的类是`using Foundation`，并调用`SetSkipBackupAttribute`如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

当`SetSkipBackupAttribute`是`true`该文件不会备份，而不考虑其存储在目录 (甚至`Documents`目录)。 您可以查询属性使用`GetSkipBackupAttribute`方法，并且你可以重置它通过调用`SetSkipBackupAttribute`方法替换`false`，如下所示：

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>共享数据之间 iOS 应用和应用扩展

由于应用扩展主机应用程序 （而不是其包含的应用程序） 的一部分运行，共享数据不是自动包含因此需要额外的工作。 应用程序组是机制 iOS 使用以允许不同的应用共享数据。 如果应用程序都已正确配置了正确的权利和预配，他们可以访问其正常 iOS 沙箱之外的共享的目录。

### <a name="configure-an-app-group"></a>配置应用组

通过[应用组](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)配置共享位置，这是在 [iOS 开发中心](https://developer.apple.com/devcenter/ios/)的**证书、标识符和描述文件**部分配置的。 还必须在每个项目的“Entitlements.plist”中引用此值。

有关创建和配置应用程序组的信息，请参阅[应用程序组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)指南。

### <a name="files"></a>文件

IOS 应用程序和扩展插件还可以共享文件使用的公共文件路径 (给定它们已进行正确配置了正确的权利和预配):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```
> [!IMPORTANT]
> 如果组路径返回为`null`，检查的权利和预配配置文件配置并确保它们正确无误。


<a name="Application_Version_Updates" />

## <a name="application-version-updates"></a>应用程序版本更新

下载你的应用程序的新版本后，iOS 创建新的主目录，并将它存储新应用程序捆绑包。 iOS 然后将新主目录的以下文件夹移动从以前版本的应用程序捆绑包：

-  **文档**
-  **Library**


其他目录还可能跨复制并将放在新的主目录下，但不是保证要复制，因此你的应用程序不应依赖于此系统的行为。

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍了文件系统操作是使用 Xamarin.iOS 如同任何其他.NET 应用程序一样简单。 它还引入了应用程序沙盒并检查它会导致的安全隐患。 接下来，探讨了应用程序捆绑包的概念。 最后，它枚举可用于应用程序的专用的目录，并在应用程序升级和备份期间介绍他们的角色。


## <a name="related-links"></a>相关链接

- [FileSystemSampleCode](https://developer.xamarin.com/samples/FileSystemSampleCode/)
- [文件系统编程指南](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [注册文件类型应用支持](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
