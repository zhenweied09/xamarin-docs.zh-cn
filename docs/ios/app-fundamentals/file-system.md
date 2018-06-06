---
title: 使用 Xamarin.iOS 中的文件系统
description: 本文档介绍如何使用 Xamarin.iOS 中的文件系统。 它讨论了目录，读取文件、 XML 和 JSON 序列化、 应用程序沙盒，共享通过 iTunes，和的详细信息的文件。
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 5bcfac7911ac589e08cd6c5db8c8ea15a3497eca
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784236"
---
# <a name="working-with-the-file-system-in-xamarinios"></a>使用 Xamarin.iOS 中的文件系统

你可以使用 Xamarin.iOS 和`System.IO`中的类 *.NET 基类库 (BCL)* 访问 iOS 文件系统。 `File`类，可以创建、 删除和读取文件，和`Directory`类可以创建、 删除或枚举目录的内容。 你还可以使用`Stream`子类，它可以提供更大程度上控制文件操作 （如文件中压缩或位置搜索）。

iOS 有一定的应用程序可以使用文件系统来保留应用程序的数据的安全性并保护用户免受恶意应用执行一些限制。 这些限制属于*应用程序沙盒*– 限制对文件、 首选项、 网络资源、 硬件、 等应用程序的访问的一组规则。应用程序仅限于读取和写入其主目录 （安装位置）; 中的文件它无法访问另一个应用程序的文件。

iOS 还具有一些文件特定于系统的功能： 某些特定目录需要特殊处理与备份和升级，并且应用程序还可以共享通过 iTunes 文件。

本文讨论的功能和限制的 ios 文件系统中的详细信息，并包括的示例应用程序演示如何使用 Xamarin.iOS 来执行一些简单的文件系统操作：

 [![](file-system-images/05-sampleapp.png "IOS 执行一些简单的文件系统操作的示例")](file-system-images/05-sampleapp.png#lightbox)

 <a name="General_File_Access" />

## <a name="general-file-access"></a>常规文件访问

Xamarin.iOS 允许你使用.NET`System.IO`在 iOS 上的文件系统操作的类。

下面的代码段演示了一些常见的文件操作。 你将找到它们所有下面集中在`SampleCode.cs`文件，请在本文的示例应用程序。

<a name="Working_with_directories" />

### <a name="working-with-directories"></a>使用目录

此代码枚举当前目录中的子目录 (指定的"。 /"参数)，这是你的应用程序可执行文件的位置。
你的输出将是所有文件和部署与应用程序 （显示在控制台窗口进行调试时） 的文件夹的列表。

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

 <a name="Reading_files" />


### <a name="reading-files"></a>读取文件

若要读取的文本文件，您只需要单个行的代码。 此示例将在应用程序输出窗口中显示的文本文件的内容。

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

 <a name="XML_Serialization" />


### <a name="xml-serialization"></a>XML 序列化

尽管使用完整`System.Xml`命名空间是超出了本文的范围，你可以轻松地进行反序列化中的文件系统提供的 XML 文档使用 StreamReader 如下：

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

请参阅 MSDN 文档以了解[System.Xml](http://msdn.microsoft.com/library/system.xml.aspx)有关详细信息的命名空间[序列化](http://msdn.microsoft.com/library/system.xml.serialization.aspx)。 你还应查看[Xamarin.iOS 文档](~/ios/deploy-test/linker.md)在链接器 – 通常你将需要添加`[Preserve]`属性设为你想要序列化的类。

 <a name="Creating_Files_and_Directories" />


### <a name="creating-files-and-directories"></a>创建文件和目录

此示例演示如何使用`Environment`类来访问我们可以在其中创建文件和目录的文档文件夹。

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

使用 Json 在 Xamarin.iOS 应用程序中的数据是非常便于使用[Json.NET](http://www.newtonsoft.com/json) for.NET NuGet 程序包的高性能 JSON framework。 只需将 NuGet 包添加到你的应用程序项目： 

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
请参阅 Json.NET[文档](http://www.newtonsoft.com/json/help)有关使用.NET 应用程序中的 json 数据的详细信息。

<a name="Special_Considerations" />

## <a name="special-considerations"></a>特殊的注意事项

尽管 Xamarin.iOS 和.NET 文件操作之间的相似之处，iOS 和 Xamarin.iOS 不同于.NET 在一些重要方面。

 <a name="runtimeaccessible" />


### <a name="making-project-files-accessible-at-runtime"></a>使项目文件可以在运行时访问

默认情况下，如果将文件添加到你的项目，它不包含在最终的程序集中，并因此不会提供给你的应用程序。 若要在程序集包含一个文件，必须将其标记具有特殊的生成操作，调用内容。

要将标记包含的文件，右键单击文件，然后选择**生成操作&gt;内容**在 Visual Studio for mac。 你还可以更改**生成操作**中文件的**属性**表。

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>区分大小写

务必了解 iOS 文件系统是*区分大小写*。 这意味着你的文件和目录名称必须完全匹配 – README.txt 和 readme.txt 将被视为不同的文件名。

这可能是让.NET 开发人员更熟悉 Windows 文件系统，这是感到困惑*区分大小写*–"文件"、"文件"和"文件"将所有引用同一个目录。

因此，但 iOS 设备区分大小写，并应记住与编写代码，的 iOS 模拟器是不区分大小写的默认值。 这意味着如果 filename 大小写文件本身和在代码中对它的引用之间存在不同，你的代码可能仍正常工作在模拟器中，但在真实设备上将会失败。 这是为什么很重要，将部署到早期和通常在 iOS 开发过程的实际设备的原因之一。

 <a name="Path_Separator" />


### <a name="path-separator"></a>路径分隔符

iOS 使用正斜杠 / 作为路径分隔符 (即不同于 Windows，使用反斜杠 \)。

因为此令人困惑的区别，所以最好使用`System.IO.Path.Combine`方法，以针对当前平台，而不是进行硬编码调整特定路径分隔符。 这是一个简单的步骤，用于使代码更易于移植到其他平台。

 <a name="Application_Sandbox" />


## <a name="application-sandbox"></a>应用程序沙盒

出于安全原因限制到文件系统 （和诸如网络和硬件功能的其他资源） 的应用程序的访问。 此限制称为*应用程序沙盒*。 文件系统中，根据你的应用程序被限制为创建和删除其主目录中文件和目录。

主目录是你的应用程序和所有数据的存储位置的文件系统中的一个唯一的位置。 你无法选择 （或更改） 你的应用程序; 的主目录的位置但是 iOS 和 Xamarin.iOS 提供属性和方法来管理的文件和目录内。

 <a name="The_Application_Bundle" />


## <a name="the-application-bundle"></a>应用程序捆绑包

*应用程序捆绑包*是包含你的应用程序的文件夹。
它是通过让.app 后缀添加到的目录名称区分开来其他文件夹。 应用程序捆绑包包含可执行文件和所有内容 （文件、 图像、 等） 需要为你的项目。

当您浏览到你在 Mac OS 中的应用程序捆绑包时，它将显示与不同的图标不是你在其他目录中看到 （和隐藏.app 后缀）;但是，它是只是一个以不同方式显示操作系统的正则的目录。

若要查看示例代码应用程序捆绑包，右键单击该项目在 Visual Studio 中针对 Mac 和选择**打开所在的文件夹**。 然后导航到**bin/Debug/** 其中你应该看到应用程序图标 （类似于下面的屏幕截图）。

 [![](file-system-images/40-bundle.png "导航到 bin/Debug 来查找应用程序图标类似于此屏幕截图")](file-system-images/40-bundle.png#lightbox)

右键单击此图标，然后选择**查看包内容**若要浏览应用程序捆绑包目录的内容。 内容显示一样的正则目录中，内容如下所示：

 [![](file-system-images/45-bundle.png "应用程序捆绑包的内容")](file-system-images/45-bundle.png#lightbox)

应用程序捆绑包是在测试期间，安装在模拟器或设备上，最后，什么用于包含在应用商店提交到 Apple。

 <a name="Application_Directories" />


## <a name="application-directories"></a>应用程序目录

当你的应用程序安装到设备上时，操作系统创建其主目录，并将放在你应用程序捆绑包。 你的代码可以访问应用程序捆绑包来读取数据，但不是应写入到该根目录中，以及对其进行签名的任何修改将使你的应用程序无效，导致无法启动。

因此，尽管执行任何操作应写入到根目录， <b>iOS 7 及更早版本中存在</b>创建多个可用于在应用程序根目录下的目录。 <b>在 iOS 8 中，用户可访问的目录是<a href="https://developer.apple.com/library/ios/technotes/tn2406/_index.html" target="_blank">不位于</a>应用程序根目录内</b>。

下面列出了这些目录和它们的用途：

&nbsp;

|目录|描述|
|---|---|
|[ApplicationName].app/|**在 iOS 7 及更早版本中**这是`ApplicationBundle`存储应用程序可执行文件的目录。 在应用中创建的目录结构存在于此目录 （例如，图像和其他文件类型，在你 Mac 项目的 Visual Studio 中已标记为资源）。<br /><br />如果你需要访问在你的应用程序捆绑包的内容文件，此目录的路径是可通过`NSBundle.MainBundle.BundlePath`属性。|
|文档 /|使用此目录来存储用户文档和应用程序数据文件。<br /><br />此目录的内容可以可供用户通过 iTunes 文件共享 （尽管这默认情况下禁用）。 添加`UIFileSharingEnabled`布尔密钥到 Info.plist 文件，以便允许用户访问这些文件。<br /><br />即使应用程序不会立即启用文件共享，应避免放置应从你在此目录中的用户隐藏的文件 (如数据库文件，除非您想要共享它们)。 只要保持隐藏敏感文件，这些文件没有公开 （和可能移出、 已修改，或已删除通过 iTunes） 如果的未来版本中启用文件共享。<br /><br /> 你可以使用`Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)`方法来获取你的应用程序的文档目录的路径。<br /><br />通过 iTunes 备份此目录的内容。|
|库 /|库目录是存储不直接由用户，如数据库或其他应用程序生成的文件创建的文件的良好开端。 此目录的内容将永远不会面临用户通过 iTunes。<br /><br />你可以在库; 中创建您自己的子目录但是，已有的某些系统中创建目录此处，你应注意，包括首选项和缓存。<br /><br />通过 iTunes 备份此目录 （除外缓存子目录中） 的内容。 将备份在库中创建的自定义目录。|
|首选项库 / /|特定于应用程序首选项文件存储在此目录。 不要直接创建这些文件。 请改用`NSUserDefaults`类。<br /><br />通过 iTunes 备份此目录的内容。|
|缓存库 / /|缓存目录是存储数据文件，可帮助你的应用程序的好时机运行，但如果需要，可以轻松地重新创建。 应用程序应创建和删除这些文件，根据需要可以重新创建这些文件，如有必要。 但是，不会运行该应用程序时，iOS 5 可能还会删除 （在下极低的存储的情况下），这些文件。<br /><br />此目录的内容不通过 iTunes，这意味着它们将不会显示，如果用户将设备还原，备份，它们可能不是安装你的应用程序的更新的版本后存在。<br /><br />例如，以防你的应用程序无法连接到网络，你可能使用的缓存目录来存储数据文件或文件，以提供很好的脱机体验。 应用程序可以保存并在等待网络响应时快速检索此数据，但它不需要进行备份和可以轻松地为恢复或重新创建后还原或版本更新。|
|tmp /|应用程序可以仅需要在此目录在短时间的临时文件存储。 为了节省空间，应在不再需要时删除文件。 应用程序未运行时，系统也可以从该目录删除文件。<br /><br />此目录的内容不会备份通过 iTunes 所造成。<br /><br />例如，tmp 目录可能用于存储临时文件的下载显示给用户 （如 Twitter 虚拟形象或电子邮件附件），但他们已查看 （并后重新下载，如有必要在将来，无法删除的).|

在查找工具窗口中，此屏幕截图中显示的目录结构：

 [![](file-system-images/08-library-directory.png "此屏幕截图中显示的目录结构中查找工具窗口")](file-system-images/08-library-directory.png#lightbox)

 <a name="Accessing_Other_Directories_Programmatically" />

### <a name="accessing-other-directories-programmatically"></a>以编程方式访问其他目录

访问更早版本的目录和文件示例`Documents`目录。 要写入到另一个目录必须构造路径使用".."语法如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

创建目录是非常相似：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

路径`Caches`和`tmp`目录可用于构造如下：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

 <a name="Sharing_Files_with_the_User_through_iTunes" />


## <a name="sharing-files-with-the-user-through-itunes"></a>与用户通过 iTunes 共享文件

用户可以访问你的应用程序的文档目录中的文件，通过编辑`Info.plist`和创建**应用程序支持 iTunes 共享**(`UIFileSharingEnabled`) 中的条目**源**视图中，为此处所示：

 [![](file-system-images/09-uifilesharingenabled-plist.png "添加应用程序支持 iTunes 共享属性")](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

可以在 iTunes 中访问这些文件，当连接设备，并且用户选择`Apps`选项卡。例如，下面的屏幕截图显示所选应用程序通过 iTunes 共享中的文件：

 [![](file-system-images/10-itunes-file-sharing.png "此屏幕截图中显示所选应用程序通过 iTunes 共享中的文件")](file-system-images/10-itunes-file-sharing.png#lightbox)

用户只能访问通过 iTunes 此目录中的顶级项。 无法查看任何子目录的内容 （尽管它们可以将它们复制到其计算机，或删除它们）。 例如，与 GoodReader，PDF 和 EPUB 文件可与共享应用程序，以使用户可以在其 iOS 设备上读取它们。

如果不小心，修改其文档文件夹的内容的用户可能会导致问题。 你的应用程序应考虑这一点，并在恢复的文档文件夹的破坏性更新。

本文的示例代码在文档文件夹中创建文件和文件夹 (在**SampleCode.cs**)，并使文件共享中**Info.plist**文件。 此屏幕截图显示这些在 iTunes 中显示的方式：

 [![](file-system-images/15-itunes-file-sharing-example.png "此屏幕快照显示文件在 iTunes 中的显示方式")](file-system-images/15-itunes-file-sharing-example.png#lightbox)

请参阅[处理映像](~/ios/app-fundamentals/images-icons/index.md)文章为你创建的有关如何设置图标为应用程序和任何自定义文档类型的信息。

如果`UIFileSharingEnabled`密钥是 false 或不存在，则文件共享，默认情况下是禁用，并且用户将无法与你 Documentsdirectory 进行交互。

 <a name="Backup_and_Restore" />

## <a name="backup-and-restore"></a>备份和还原

当设备由 iTunes 备份时，将除以下保存在应用程序的主目录中创建的所有目录：

-   **[ApplicationName].app** – 应用程序捆绑包*未*备份的信息，但仅限于时捆绑已更改 （应用程序更新安装时，例如）。 仍，因为它已签名，因此在安装后必须保持不变，不应修改此目录。 
-   **库/缓存**– 缓存目录适用于不需要备份的工作文件。 
-   **tmp** – 临时文件创建和删除不再需要时，使用此目录或文件该 iOS 时删除它需要的空间。 


备份大量数据可能需要很长时间。 如果你决定你需要备份任何特定文档或数据，文档和库，你的应用程序应仅使用此文件夹。 对于暂时性数据或可以轻松地从网络检索的文件，使用缓存或 tmp 目录。

请注意，iOS 将清除文件系统上磁盘空间严重不足设备，运行时。 此过程将从库/缓存和 tmp 中删除所有文件的当前未运行的应用程序的文件夹。

 <a name="Complying_with_iOS5_iCloud_Backup_Restrictions" />

## <a name="complying-with-ios5-icloud-backup-restrictions"></a>遵守 iOS5 iCloud 备份限制

Apple 引入*iCloud 备份*ios 5 的功能。 当启用 iCloud 备份，应用程序的主目录中的所有文件 (排除目录不会正常备份，例如，应用程序捆绑包，`Caches`和`tmp`) 是备份到 iCloud 服务器。 在其设备丢失、 被盗或已损坏的情况下，此功能将提供具有完整备份的用户。

因为 iCloud 仅向每个用户提供 5 Gb 的免费空间，并避免不必要地使用带宽，Apple 期望到仅备份基本用户生成的数据的应用程序。 若要符合 iOS 数据存储准则应限制通过遵循以下各项获取备份的数据量：

-  仅存储用户生成的数据或不能为在文档目录 （这是备份） 中重新创建的数据。 
-  存储以便可以轻松地重新创建或重新下载中的任何其他数据`Library/Caches`或`tmp`（这不是备份，并且无法将其清除）。 
-  如果你有可能适用于的文件`Library/Caches`或`tmp`文件夹，而你不想要清理，将它们存储在其他位置 (如`Library/YourData`) 并应用不能反向向上属性，以防止文件占用 iCloud 备份 bandwidth 和存储空间。 此数据仍使用了空间在设备上，因此你应小心管理，则删除它在可能的情况。 

不备份了使用设置属性`NSFileManager`类。 确保你的类`using Foundation`并调用`SetSkipBackupAttribute`如下所示：

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

当`SetSkipBackupAttribute`是`true`备份，而不考虑它存储在目录将不会对文件 (甚至`Documents`目录)。 您可以查询属性使用`GetSkipBackupAttribute`方法，并且你可以重置它通过调用`SetSkipBackupAttribute`方法替换`false`，如下所示：

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>共享数据之间 iOS 应用和应用程序扩展

因为作为主机应用程序 （而不是其包含的应用） 中运行应用程序扩展，共享数据不会自动包括以便额外工作是必需的。 应用程序组是机制 iOS 使用以允许不同的应用程序共享数据。 如果应用程序是否已正确配置与正确授权和设置，它们可以访问在其正常 iOS 沙盒之外的共享的目录。

### <a name="configure-an-app-group"></a>配置应用组

通过[应用组](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)配置共享位置，这是在 [iOS 开发中心](https://developer.apple.com/devcenter/ios/)的**证书、标识符和描述文件**部分配置的。 还必须在每个项目的“Entitlements.plist”中引用此值。

有关创建和配置应用程序组的信息，请参阅[应用组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)指南。

### <a name="files"></a>文件

IOS 应用程序和扩展插件还可以共享文件使用的公共文件路径 (给定已正确配置与正确的授权，并且设置):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```
> [!IMPORTANT]
> 如果组路径返回为`null`，检查权利和预配配置文件的配置，并确保它们正确无误。


<a name="Application_Version_Updates" />

## <a name="application-version-updates"></a>应用程序的版本更新

下载你的应用程序的新版本时，iOS 将创建新的主目录，并将新的应用程序捆绑包存储在它。 iOS 然后将新主目录的以下文件夹移动应用程序捆绑包的先前版本中：

-  **文档**
-  **Library**


其他目录还可以跨复制并放置于你新的主目录，但它们不能保证要复制，因此你的应用程序不应依赖于此系统的行为。

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍了文件系统操作是使用 Xamarin.iOS 与任何其他.NET 应用程序一样简单。 它还引入了应用程序沙盒，并检查它会导致的安全隐患。 接下来，它还介绍了一个应用程序捆绑包的概念。 最后，它枚举可用于你的应用程序的专用的目录，并在应用程序升级以及备份过程的说明及其角色。


## <a name="related-links"></a>相关链接

- [FileSystemSampleCode](https://developer.xamarin.com/samples/FileSystemSampleCode/)
- [文件系统编程指南](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [你的应用程序支持的注册文件类型](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
