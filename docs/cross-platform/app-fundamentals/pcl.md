---
title: "可移植类库简介"
description: "本文介绍可移植类库 (PCL) 项目，并将指导完成创建和使用针对 Mac 和 Visual Studio 的 Visual Studio 中的 PCL 项目。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 5dd77232dc992880b18393c740b54c7784e4d049
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="introduction-to-portable-class-libraries"></a>可移植类库简介

_本文介绍可移植类库 (PCL) 项目，并将指导完成创建和使用针对 Mac 和 Visual Studio 的 Visual Studio 中的 PCL 项目。_


构建跨平台应用程序的关键组成部分能够在各种特定于平台的项目间共享代码。 但是，这被复杂，因为不同的平台通常使用另一子组的.NET 基类库 (BCL)，并因此实际上只会生成到不同的.NET 核心库配置文件。 这意味着，每个平台只能使用指向相同的配置文件，所以，将出现需要单独的类库项目的每个平台的类库。

有三个主要方法代码共享可解决此问题： **.NET 标准项目**，**可移植类库 (PCL) 项目**，和**共享资产项目**.

- **标准.NET 项目** [.NET 标准](~/cross-platform/app-fundamentals/net-standard.md)。
-  **PCL**项目以支持一组已知的 BCL 类/功能的特定配置文件为目标。 但是，为 PCL 缺点是，它们通常需要额外体系结构的工作量将配置文件的特定代码单独放入其自己的库。 有关这两种方法的更多详细讨论，请参阅[共享代码选项指导](~/cross-platform/app-fundamentals/code-sharing.md)。
-  **共享资产项目**使用快速而又简单方式来共享代码在解决方案内，通常使用条件编译指令来指定将使用它 （有关详细信息的各种平台的代码路径的文件，并提供一组信息，请参阅[共享项目文章](~/cross-platform/app-fundamentals/shared-projects.md)和[设置跨平台的 Xamarin 解决方案指南](~/cross-platform/app-fundamentals/code-sharing.md))。


此页说明如何创建**PCL**面向特定配置文件，然后可由多个特定于平台的项目引用的项目。


## <a name="what-is-a-portable-class-library"></a>可移植类库是什么？

当你创建一个应用程序项目或类库项目时，生成的 DLL 被限制为在为创建的特定平台上工作。 这样会妨碍你从编写 Windows 应用程序，程序集，然后将其重新使用 Xamarin.iOS 和 Xamarin.Android 上。

当你创建可移植类库时，但是，可以选择想要代码上运行的平台的组合。 创建可移植类库时进行的兼容性选择被转换为"配置文件"标识符，其中介绍了此库支持哪些平台。

下表显示了一些因.NET 平台而异的功能。 若要编写保证能运行，在特定的设备平台上的 PCL 程序集你只需选择你创建项目时需要的支持。

|功能|.NET Framework|UWP 应用|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|核心|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|序列化|Y|Y|Y|Y|Y|
|数据注释|4.0.3 +|Y|Y||Y|

Xamarin 列反映 Xamarin.iOS 和 Xamarin.Android 支持随 Visual Studio 一起提供的所有配置文件，并仅将有限你选择支持的其他平台的你创建的所有库中的功能可用性的情况。

这包括的组合的配置文件：

-  .NET 4 或.NET 4.5
-  Silverlight 5
-  Windows Phone 8
-  UWP 应用

你可以阅读有关不同的配置文件的功能上[Microsoft 网站](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)并查看其他社区成员[PCL 配置文件摘要](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY)framework 信息以及其他说明，其中包括支持。



创建要共享代码的 PCL 具有大量的利与弊与文件链接的替代项：


**权益**

1. 集中式的代码共享 – 编写并测试代码可以由其他库或应用程序使用的单个项目中。
1. 重构操作将影响所有的代码加载解决方案 （可移植类库和特定于平台的项目） 中。
1. PCL 项目中可以轻松地通过在解决方案中，其他项目引用或输出程序集可以共享以供他人在他们的解决方案中引用。


**缺点**

1. 特定于平台的库，因为多个应用程序之间共享相同的可移植类库，不能引用 （如。 Community.CsharpSqlite.WP7).
1. 可移植类库子集可能不包括才可用 MonoTouch 和 Mono 适用于 Android （如 DllImport 或 System.IO.File） 中的类。



在某种程度上两个缺点可以绕过使用的提供程序模式或依赖关系注入的代码在平台项目中，针对在可移植类库中定义一个接口或基类类的实际实现。



此图显示跨平台应用程序使用可移植类库来共享代码，但它还使用依赖关系注入以传入的平台相关的功能的体系结构：



[![](pcl-images/image1.png "此图显示跨平台应用程序使用可移植类库来共享代码，但它还使用依赖关系注入以传入的平台相关的功能的体系结构")](pcl-images/image1.png#lightbox)



# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)



## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 演练


本部分演练如何创建和使用可移植类库使用 Visual Studio for mac。 请参阅完整的实现中的 PCL 示例部分。



### <a name="creating-a-pcl"></a>创建 PCL


你的解决方案中添加可移植类库是非常类似于常规的类库项目中添加。


1. 在新项目对话框选择`Multiplatform > Library > Portable Library`选项


    ![](pcl-images/image2.png "多平台 > 库 > 可移植库")


1. PCL Visual Studio 中创建的 Mac 将自动配置适用于 Xamarin.iOS 和 Xamarin.Android 的配置文件。 PCL 项目中将显示此屏幕截图中所示：

    ![](pcl-images/image3.png "PCL 项目中将显示此屏幕截图中所示")

PCL 现已为要添加的代码。 它也可以由其他 （应用程序项目，库项目和甚至其他 PCL 项目） 的项目引用。



### <a name="editing-pcl-settings"></a>编辑 PCL 设置


若要查看和更改此项目的 PCL 设置，请右键单击该项目并选择**选项 > 生成 > 常规**若要查看此处所示的屏幕：



[![](pcl-images/image4.png "若要查看和更改此项目的 PCL 设置，请右键单击该项目并选择选项生成通用，请参阅此处所示的屏幕")](pcl-images/image4.png#lightbox)



在此屏幕上的设置控制此特定 PCL 适用于哪些平台。 更改这些选项的任何更改的配置文件正在使用此 PCL 中，这反过来控制的功能可能会在可移植代码中使用。



更改任意`Target Framework`选项自动更新`Current Profile`; 如果选择了不兼容的选项，屏幕还将显示一条警告。



[![](pcl-images/image5.png "更改的任何目标框架选项，将自动更新当前配置文件屏幕将还会显示一条警告，如果选择了不兼容的选项")](pcl-images/image5.png#lightbox)



如果代码已添加到 PCL 后更改配置文件，，则可能库将无法再编译，如果该代码引用不是新选择配置文件的一部分的功能。


## <a name="working-with-a-pcl"></a>使用 PCL


时 PCL 库中编写代码，Visual Studio for Mac 编辑器将识别所选配置文件的限制，并相应地调整自动完成选项。 例如，此屏幕截图显示了 System.IO 的自动完成选项使用在 Visual Studio for Mac 中使用的默认配置文件 (Profile136) – 请注意滚动条，该值指示是否显示大约一半的可用类 （实际上有仅 14可用类）。



[![](pcl-images/image6.png "使用默认配置文件在 Visual Studio for Mac 请注意滚动条，指示实际上有显示大约一半的可用类才可用的 14 类中使用的 Profile136 IO")](pcl-images/image6.png#lightbox)



比较，使用 Xamarin.iOS 或 Xamarin.Android 项目 – 中自动完成 System.IO 有 40 类可用包括通常使用的类，如`File`和`Directory`它们都不位于任何 PCL 配置文件。



[![](pcl-images/image7.png "有 40 可用包括通常用于文件和目录等类不是任何 PCL 配置文件中的类")](pcl-images/image7.png#lightbox)



这反映了使用 PCL 基础权衡 – 无缝地在许多平台间共享代码的能力意味着某些 Api 不可用于你，因为它们没有可比较实现跨所有可能的平台。



### <a name="using-pcl"></a>使用 PCL


一旦创建 PCL 项目后，你可以添加任何兼容的应用程序或库项目中对它的引用与通常添加引用的方式相同。 在 Visual Studio for Mac 中，右键单击引用节点，然后选择`Edit References…`然后切换到项目选项卡，如下所示：



[![](pcl-images/image8.png "在适用于 Mac 的 Visual Studio，右键单击引用节点并选择编辑引用，然后切换到项目选项卡上，如所示")](pcl-images/image8.png#lightbox)



以下屏幕截图显示有关 TaskyPortable 示例应用，显示在底部和对该 PCL 库的引用的 PCL 库 Xamarin.iOS 项目中的解决方案板。



[![](pcl-images/image9.png "TaskyPortable 示例应用程序解决方案填充")](pcl-images/image9.png#lightbox)



PCL 的输出 (ie。 生成的程序集 DLL) 还可以将其添加为对大多数项目的引用。 这使得 PCL 理想途径提供跨平台组件和库。




# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)




## <a name="visual-studio-walkthrough"></a>Visual Studio 演练


此部分将指导完成如何创建和使用可移植类库使用 Visual Studio。 请参阅完整的实现中的 PCL 示例部分。



### <a name="creating-a-pcl"></a>创建 PCL


将 PCL 添加到你在 Visual Studio 中的解决方案是将正则项目添加到略有不同。



1. 在添加新项目屏幕中，选择`Portable Class Library`选项


    ![](pcl-images/image10.png "可移植类库")


1. Visual Studio 将立即与以下对话框中提示，以便可以配置该配置文件。
 刻度的平台，你需要支持，并按确定。


    ![](pcl-images/image11.png "刻度的平台，你需要支持，并按确定")


1. PCL 项目中将显示解决方案资源管理器中所示。 引用节点将指示库使用.NET Framework （由 PCL 配置文件定义） 的子集。

    ![](pcl-images/image12.png "NET Framework 由 PCL 配置文件定义")

PCL 现已为要添加的代码。 它也可以由其他 （应用程序项目，库项目和甚至其他 PCL 项目） 的项目引用。



### <a name="editing-pcl-settings"></a>编辑 PCL 设置


PCL 设置都可以查看和更改通过右键单击项目并选择**属性 > 库**，此屏幕截图中所示：



[![](pcl-images/image13.png "可查看并更改通过右键单击项目并选择属性库，此屏幕截图中所示的 PCL 设置")](pcl-images/image13.png#lightbox)



如果代码已添加到 PCL 后更改配置文件，，则可能库将无法再编译，如果该代码引用不是新选择配置文件的一部分的功能。



### <a name="working-with-a-pcl"></a>使用 PCL


时 PCL 库中编写代码，Visual Studio 将识别所选配置文件的限制，并相应地调整 Intellisense 选项。 例如，此屏幕截图显示了 System.IO 的自动完成选项使用默认配置文件 (Profile136) – 请注意滚动条，该值指示是否显示大约一半的可用类 （实际上有仅 14 类可用）。



[![](pcl-images/image14.png "使用默认配置文件 Profile136 IO")](pcl-images/image14.png#lightbox)



比较，使用自动完成在正则项目 – System.IO 有 40 类可用包括通常使用的类，如`File`和`Directory`它们都不位于任何 PCL 配置文件。



[![](pcl-images/image15.png "在正则项目中自动完成")](pcl-images/image15.png#lightbox)



这反映了使用 PCL 基础权衡 – 无缝地在许多平台间共享代码的能力意味着某些 Api 不可用于你，因为它们没有可比较实现跨所有可能的平台。



### <a name="using-pcl"></a>使用 PCL


一旦创建 PCL 项目后，你可以添加任何兼容的应用程序或库项目中对它的引用与通常添加引用的方式相同。 在 Visual Studio 中，右键单击引用节点，然后选择`Add Reference...`然后切换到**解决方案： 项目**选项卡如下所示：



[![](pcl-images/image16.png "如所示，项目选项卡")](pcl-images/image16.png#lightbox)



下面的屏幕截图显示有关 TaskyPortable 示例应用，显示在底部和对该 PCL 库的引用的 PCL 库 Xamarin.iOS 项目中的解决方案窗格。



[![](pcl-images/image17.png "TaskyPortable 示例应用程序解决方案窗格")](pcl-images/image17.png#lightbox)



PCL 的输出 (ie。 生成的程序集 DLL) 还可以将其添加为对大多数项目的引用。
这使得 PCL 理想途径提供跨平台组件和库。




-----



## <a name="pcl-example"></a>PCL 示例


[TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/)示例应用程序演示如何通过 Xamarin 使用可移植类库。
下面是在 iOS、 Android 和 Windows Phone 上运行的生成应用的一些屏幕快照：



[![](pcl-images/image18.png "以下是一些在 iOS、 Android 和 Windows Phone 上运行的生成应用的屏幕截图")](pcl-images/image18.png#lightbox)



共享大量的数据和逻辑都是完全可移植代码的类，它还演示如何将依赖关系注入用于 SQLite 数据库实现的特定于平台的要求。




解决方案结构如下所示 (适用于 Mac 和 Visual Studio 的 Visual Studio 中分别):



[![](pcl-images/image19.png "解决方案结构如下所示在 Visual Studio 中针对 Mac 和 Visual Studio 分别")](pcl-images/image19.png#lightbox)



因为演示目的，它被重构为一个抽象类，可以编译到可移植类库 SQLite NET 代码均具有特定于平台的部分 （可使用 SQLite 实现每个不同的操作系统上） 和实际的代码实现为 iOS 和 Android 项目中的子类。



### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

可移植类库可以支持的.NET 功能方面的限制。 因为它已编译多个平台上运行，但不能使用`[DllImport]`在 SQLite 的网络中使用的功能。 而是 SQLite NET 是作为抽象类，实现，然后引用通过共享代码的其余部分。 抽象 API 提取如下所示：


```csharp
public abstract class SQLiteConnection : IDisposable {

    public string DatabasePath { get; private set; }
    public bool TimeExecution { get; set; }
    public bool Trace { get; set; }
    public SQLiteConnection(string databasePath) {
         DatabasePath = databasePath;
    }
    public abstract int CreateTable<T>();
    public abstract SQLiteCommand CreateCommand(string cmdText, params object[] ps);
    public abstract int Execute(string query, params object[] args);
    public abstract List<T> Query<T>(string query, params object[] args) where T : new();
    public abstract TableQuery<T> Table<T>() where T : new();
    public abstract T Get<T>(object pk) where T : new();
    public bool IsInTransaction { get; protected set; }
    public abstract void BeginTransaction();
    public abstract void Rollback();
    public abstract void Commit();
    public abstract void RunInTransaction(Action action);
    public abstract int Insert(object obj);
    public abstract int Update(object obj);
    public abstract int Delete<T>(T obj);

    public void Dispose()
    {
        Close();
    }
    public abstract void Close();

}
```


共享代码的其余部分以抽象类"存储"和""对象从数据库中检索。 在所有应用程序使用此抽象类中，我们必须通过在完整的实现，它提供实际的数据库功能。



### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid 和 TaskyiOS


IOS 和 Android 应用程序项目包含用户界面和用于网络型 PCL 中的共享的代码的其他特定于平台的代码。



这些项目还包含抽象数据库 API，适用于该平台的实现。 在 iOS 和 Android Sqlite 数据库引擎是内置于操作系统，因此实现可以使用`[DllImport]`提供数据库连接的具体实现，如上所示。 特定于平台的实现代码的一段摘录所示：


```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```


示例代码中，可以查看完整的实现。

### <a name="taskywinphone"></a>TaskyWinPhone


Windows Phone 应用程序使用 XAML 生成其 ui，并包含其他特定于平台的代码用于与用户界面连接的共享的对象。



必须将 Windows Phone 应用创建并使用的实例与用于 iOS 和 Android 的实现，相比`Community.Sqlite.dll`作为它的抽象的一部分数据库 API。 而不是使用`DllImport`，如打开的方法来实现针对中引用的 Community.Sqlite 程序集`TaskWinPhone`项目。 摘录所示，以便与 iOS 和 Android 版本更高版本进行比较


```csharp
public static Result Open(string filename, out Sqlite3.sqlite3 db)
{
    db = new Sqlite3.sqlite3();
    return (Result)Sqlite3.sqlite3_open(filename, ref db);
}

public static Result Close(Sqlite3.sqlite3 db)
{
    return (Result)Sqlite3.sqlite3_close(db);
}
```


## <a name="summary"></a>摘要


本文简要讨论可移植类库的优缺点，演示了如何创建和使用从 Pcl 在 Visual Studio 中针对 Mac 和 Visual Studio;和最后引入显示 PCL 中操作的完整示例应用程序 – TaskyPortable –。


## <a name="related-links"></a>相关链接

- [TaskyPortable （示例）](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可移植 Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [共享项目](~/cross-platform/app-fundamentals/shared-projects.md)
- [共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)
- [使用.NET Framework (Microsoft) 的跨平台开发](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)
