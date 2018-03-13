---
title: "案例研究： Tasky"
description: "本文档介绍如何将生成跨平台应用程序的原则应用 Tasky 可移植的示例应用程序中。 它涉及移动应用程序设计、 编写供重复使用的常见代码和实施面向 iOS、 Android 和 Windows Phone 平台的特定于平台的项目。"
ms.topic: article
ms.prod: xamarin
ms.assetid: B581B2D0-9890-C383-C654-0B0E12DAD5A6
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 5b16fe1d6dab776af31e0aff97a00065517550ec
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="case-study-tasky"></a>案例研究： Tasky

_本文档介绍如何将生成跨平台应用程序的原则应用 Tasky 可移植的示例应用程序中。它涉及移动应用程序设计、 编写供重复使用的常见代码和实施面向 iOS、 Android 和 Windows Phone 平台的特定于平台的项目。_

*Tasky* *可移植*是一个简单的待办事项列表应用程序。 本文档讨论了如何设计和生成，以下的指导[生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档。 讨论涵盖以下几个方面：

<a name="Design_Process" />

## <a name="design-process"></a>设计过程

建议你创建的路线图为你想要实现之前开始编码。 这是尤其适用于跨平台开发，其中，您构建了将采用多种方式公开的功能。 开头清楚什么你正在生成节省了时间和工作量在开发周期的更高版本。

 <a name="Requirements" />

### <a name="requirements"></a>惠?

设计应用程序的第一步是确定所需的功能。 这些类型可以是高级目标或详细的用例。 Tasky 具有简单的功能要求：

 -  查看任务列表
 -  添加、 编辑和删除任务
 -  设置任务的状态设置为完成

你应该考虑特定于平台的功能的使用。  Tasky 可以充分利用 iOS 地理围栏或 Windows Phone 动态磁贴？ 即使不使用中的第一个版本的特定于平台的功能，你应提前规划以确保您的业务和数据层可以适应这些变化。

 <a name="User_Interface_Design" />

### <a name="user-interface-design"></a>用户界面设计

开头，可以实现跨目标平台的高级设计。 注意到注意平台特定 UI 限制。 例如，`TabBarController`在 iOS 中可以显示多包含五个按钮，而 Windows Phone 等效项可以显示最多四个。
绘制屏幕流使用你选 （纸适用） 的工具。

 [![](case-study-tasky-images/taskydesign.png "绘制屏幕流使用你选择的纸张工作原理的工具")](case-study-tasky-images/taskydesign.png#lightbox)

 <a name="Data_Model" />

### <a name="data-model"></a>数据模型

了解哪些数据需要存储将帮助确定要使用的持久性机制。 请参阅[跨平台数据访问](~/cross-platform/app-fundamentals/index.md)有关可用存储机制和决定它们之间的帮助信息。 对于此项目，我们将使用 SQLite.NET。

Tasky 需要为每个 TaskItem 存储三个属性：

 -  **名称**– 字符串
 -  **说明**– 字符串
 -  **完成**– 布尔值

 <a name="Core_Functionality" />

### <a name="core-functionality"></a>核心功能

请考虑的 API 的用户界面将需要使用以满足的要求。 待办事项列表需要以下函数：

 -   **列出的所有任务**– 以显示所有可用的任务的主屏幕都列表
 -  **获取一个任务**– 如果接触任务行
 -  **保存一个任务**– 时编辑任务
 -  **删除一个任务**– 当任务被删除
 -  **创建空任务**– 时创建一个新的任务

若要实现代码重用，此 API 应实现一次在*可移植类库*。

 <a name="Implementation" />

### <a name="implementation"></a>实现

一旦已同意应用程序设计，请考虑如何它可能会作为一个跨平台应用程序实现。 这将成为应用程序的体系结构。 中的指南[生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档中，应用程序代码应被破坏向下插入以下部分：

 -   **公共代码**– 常见项目包含重新可用的代码，以便存储的任务数据; 公开模型类和一个 API，用于管理保存和加载的数据。
 -   **特定于平台的代码**– 实现本机 UI，对于每个操作系统，请使用作为后端的公共代码的特定于平台的项目。

 [![](case-study-tasky-images/taskypro-architecture.png "特定于平台的项目实现本机 UI，对于每个操作系统，请使用作为后端的常见代码")](case-study-tasky-images/taskypro-architecture.png#lightbox)

以下各节所述这两部分。

 <a name="Common_(PCL)_Code" />

## <a name="common-pcl-code"></a>常见 (PCL) 代码

Tasky 可移植为共享通用代码使用可移植类库的策略。 请参阅[共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)代码共享选项的说明的文档。

所有常见的代码，包括数据访问层、 数据库代码和协定，放置在类库项目中。

完整的 PCL 项目如下所示。 所有可移植库中的代码适用于每个目标平台。 在部署时，每个本机应用程序将引用该库。

![](case-study-tasky-images/portable-project.png "每个本机应用程序部署时，将引用该库")

类关系图显示按层进行分组的类。 `SQLiteConnection`类是从 Sqlite 的网络包的样板文件代码。 类的其余部分是用于 Tasky 自定义代码。 `TaskItemManager`和`TaskItem`类表示向特定于平台的应用程序公开的 API。

 [![](case-study-tasky-images/classdiagram-core.png "TaskItemManager 和 TaskItem 类表示向特定于平台的应用程序公开的 API")](case-study-tasky-images/classdiagram-core.png#lightbox)

使用命名空间到单独的层可帮助管理每个层之间的引用。 特定于平台的项目应只需包括`using`业务层的语句。 应通过公开的 API 封装的数据访问层和数据层`TaskItemManager`在业务层。

 <a name="References" />

### <a name="references"></a>参考资料

可移植类库需要能够跨多个平台，每个都有不同的平台和框架的功能的支持级别都可用。 正因如此，则会在其上的包和 framework 库可的限制。 例如，Xamarin.iOS 不支持 c#`dynamic`关键字，因此可移植类库不能使用的任何包都依赖于动态代码，即使此类代码将在 Android 上工作。 适用于 Mac 的 visual Studio 将妨碍你在添加不兼容的程序包和引用，但你将想要记住，若要在以后避免意外事件保留限制。

注意： 你将看到你的项目引用尚未使用的 framework 库。 这些引用是 Xamarin 项目模板的一部分包括。 链接的过程后应用会被编译，将请删除未引用的代码，因此，即使`System.Xml`已被引用，它将不会包含在最终应用程序中因为我们不使用任何 Xml 的函数。

 <a name="Data_Layer_(DL)" />

### <a name="data-layer-dl"></a>数据层 (DL)

数据层包含执行数据 – 的物理存储到一个数据库、 平面文件或其他机制的代码。 Tasky 数据层由两部分组成： SQLite NET 库和添加来连接它的自定义代码。

Tasky Sqlite net nuget 包 （由 Frank Kreuger 已发布） 为依赖嵌入提供了一个对象关系映射 (ORM) 数据库接口的 SQLite NET 代码。 `TaskItemDatabase`类继承自`SQLiteConnection`并添加所需的创建、 读取、 更新、 删除 (CRUD) 方法来读取和写入 SQLite 的数据。 很可能是重复使用其他项目中的泛型 CRUD 方法的简单样本实现。

`TaskItemDatabase`是单一实例，确保所有访问都发生针对同一个实例。 使用锁来防止从多个线程并发访问。

 <a name="SQLite_on_WIndows_Phone" />

#### <a name="sqlite-on-windows-phone"></a>在 Windows Phone 上的 SQLite

在 iOS 和 Android 同时提供作为操作系统的一部分的 SQLite，Windows Phone 不包括兼容的数据库引擎。 若要在所有三个平台间共享代码的 SQLite 的 Windows phone 本机版本是必需的。 请参阅[处理本地数据库](~/xamarin-forms/app-fundamentals/databases.md)有关 for Sqlite Windows Phone 项目的设置的详细信息。

 <a name="Using_an_Interface_to_Generalize_Data_Access" />

#### <a name="using-an-interface-to-generalize-data-access"></a>使用接口配合使用来通用化数据访问

数据层采用依赖`BL.Contracts.IBusinessIdentity`，以便它可以实现需要具有主键的抽象数据访问方法。 实现接口的任何业务层类可以随后将保存在数据层中。

接口只需指定一个整数属性以充当为主键：

```csharp
public interface IBusinessEntity {
    int ID { get; set; }
}
```

基类实现接口，并添加要将其标记为自动递增的主键的 SQLite 的网络属性。 实现此基类业务层中的任何类可以随后将保存在数据层：

```csharp
public abstract class BusinessEntityBase : IBusinessEntity {
    public BusinessEntityBase () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
}
```

使用接口的数据层中的泛型方法的一个示例是这`GetItem<T>`方法：

```csharp
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

 <a name="Locking_to_prevent_Concurrent_Access" />

#### <a name="locking-to-prevent-concurrent-access"></a>锁定以防止并发访问

A[锁](http://msdn.microsoft.com/en-us/library/c5kehkcz(v=vs.100).aspx)内实施`TaskItemDatabase`类以防止并发访问数据库。 这是为了确保已序列化从不同的线程并发访问 （否则 UI 组件可能会尝试以数据库读取后台线程正在更新一次）。 如何实施该锁的示例所示：

```csharp
static object locker = new object ();
public IEnumerable<T> GetItems<T> () where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return (from i in Table<T> () select i).ToList ();
    }
}
public T GetItem<T> (int id) where T : BL.Contracts.IBusinessEntity, new ()
{
    lock (locker) {
        return Table<T>().FirstOrDefault(x => x.ID == id);
    }
}
```

大多数的数据层代码无法在其他项目中重新使用。 在层中的仅特定于应用程序代码是`CreateTable<TaskItem>`调用`TaskItemDatabase`构造函数。

 <a name="Data_Access_Layer_(DAL)" />

### <a name="data-access-layer-dal"></a>数据访问层 (DAL)

`TaskItemRepository`类封装一个强类型的 API，用于与数据存储机制`TaskItem`对象要创建、 删除、 检索和更新。

 <a name="Using_Conditional_Compilation" />

#### <a name="using-conditional-compilation"></a>使用条件编译

类使用条件编译以设置文件位置-这是一种实现平台分歧。 返回的路径的属性将编译为每个平台上的不同代码。 此处显示的代码和特定于平台的编译器指令：

```csharp
public static string DatabaseFilePath {
    get {
        var sqliteFilename = "TaskDB.db3";
#if SILVERLIGHT
        // Windows Phone expects a local path, not absolute
        var path = sqliteFilename;
#else
#if __ANDROID__
        // Just use whatever directory SpecialFolder.Personal returns
        string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
        // we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder
#endif
        var path = Path.Combine (libraryPath, sqliteFilename);
                #endif
                return path;
    }
}
```

根据所用平台，则输出将为"<app
path>/Library/TaskDB.db3"对于 iOS，"<app
path>/Documents/TaskDB.db3"为 Android 或只是"TaskDB.db3"为 Windows Phone。

### <a name="business-layer-bl"></a>业务层 (BL)

业务层实现模型类和外观对其进行管理。
模型处于 Tasky`TaskItem`类和`TaskItemManager`实现以提供一个用于管理 API 的外观模式`TaskItems`。

 <a name="Façade" />

#### <a name="faade"></a>外观

 `TaskItemManager` 包装`DAL.TaskItemRepository`提供 Get，保存和删除的应用程序和 UI 层将引用的方法。

业务规则和逻辑将此处如果放置必需 – 例如任何保存一个对象之前必须满足的验证规则。

 <a name="API_for_Platform-Specific_Code" />

### <a name="api-for-platform-specific-code"></a>特定于平台的代码的 API

一旦已编写的常见代码，则必须生成用户界面收集和显示由它公开的数据。 `TaskItemManager`类实现的外观模式提供应用程序代码的一个简单的 API 来访问。

每个特定于平台的项目中编写的代码通常将对该设备的本机 SDK 紧密耦合，并且只能访问使用通过定义的 API 的常见代码`TaskItemManager`。 这包括方法和业务类，它公开，如`TaskItem`。

映像将不在平台之间共享，而独立添加到每个项目。 这非常重要，因为每个平台映像以不同方式处理，使用不同的文件名、 目录和解决方法。

其余各节讨论 Tasky UI 的特定于平台的实现详细信息。

 <a name="iOS_App" />

## <a name="ios-app"></a>iOS App

有只有几种实现 iOS Tasky 使用常见的 PCL 项目来存储和检索数据的应用程序所需的类。 完成 iOS Xamarin.iOS 项目所示：

 ![](case-study-tasky-images/taskyios-solution.png "此处显示的 iOS 项目")

类是显示在此图中，分组到层。

 [![](case-study-tasky-images/classdiagram-android.png "在此图中，为层分组显示类")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>参考资料

IOS 应用程序引用特定于平台的 SDK 库 – 例如。 Xamarin.iOS 和 MonoTouch.Dialog-1。

它还必须引用`TaskyPortableLibrary`PCL 项目。
引用列表如下所示：

 ![](case-study-tasky-images/taskyios-references.png "引用列表如下所示")

在使用这些引用此项目中实现的应用程序层和用户界面层。

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>应用程序层 (AL)

应用程序层包含绑定到 UI PCL 所公开的对象所需的特定于平台的类。 特定于 iOS 的应用程序具有两个类来帮助显示任务：

 -   **EditingSource** – 此类用于绑定到该用户接口的任务的列表。 因为`MonoTouch.Dialog`使用对于任务列表中，我们需要实施此帮助器以启用轻扫删除功能在`UITableView`。 轻扫删除是 iOS，但不是 Android 或 Windows Phone 上常见的因此 iOS 特定项目是唯一一个执行它。
 -   **TaskDialog** – 此类用于将一项任务绑定到 UI。 它使用`MonoTouch.Dialog`反射 API 包装`TaskItem`与包含要允许具有正确的格式输入的屏幕的正确特性的类的对象。

`TaskDialog`类使用`MonoTouch.Dialog`属性来创建屏幕基于类的属性。 类类似如下所示：

```csharp
public class TaskDialog {
    public TaskDialog (TaskItem task)
    {
        Name = task.Name;
        Notes = task.Notes;
        Done = task.Done;
    }
    [Entry("task name")]
    public string Name { get; set; }
    [Entry("other task info")]
    public string Notes { get; set; }
    [Entry("Done")]
    public bool Done { get; set; }
    [Section ("")]
    [OnTap ("SaveTask")]    // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Save;
    [Section ("")]
    [OnTap ("DeleteTask")]  // method in HomeScreen
    [Alignment (UITextAlignment.Center)]
    public string Delete;
}
```

请注意`OnTap`属性需要方法名称 – 这些方法必须在类中存在其中`MonoTouch.Dialog.BindingContext`创建 (在这种情况下，`HomeScreen`下一节所述的类)。

 <a name="User_Interface_Layer_(UI)" />

### <a name="user-interface-layer-ui"></a>用户界面层 (UI)

用户界面层的以下类组成：

1.   **AppDelegate** – 包含对外观 API 调用，以设置字体和颜色在应用程序中使用的样式。 Tasky 是一个简单应用程序不没有在运行任何其他初始化任务以便`FinishedLaunching`。
2.   **屏幕**– 的子类`UIViewController`，用于定义每个屏幕和其行为。 屏幕将绑定在一起使用应用程序层类 UI 和通用 API ( `TaskItemManager` )。 在此示例创建屏幕了在代码中，但它们无法设计使用 Xcode 的接口生成器或情节提要设计器。
3.   **映像**– 可视元素是每个应用程序的重要组成部分。 Tasky 具有初始屏幕和图标图像，它适用于 iOS 中必须提供在常规模式和 Retina 解析。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主屏幕

主页屏幕是`MonoTouch.Dialog`显示的 SQLite 数据库中的任务列表的屏幕。 它继承自`DialogViewController`和实现代码以将设置`Root`以包含一套`TaskItem`显示的对象。

 [![](case-study-tasky-images/ios-taskylist.png "它从 DialogViewController 继承并实现设置要包含的显示的 TaskItem 对象的集合的根的代码")](case-study-tasky-images/ios-taskylist.png#lightbox)

与显示和任务列表与交互相关的两个主要方法是：

1.   **PopulateTable** – 使用业务层`TaskManager.GetTasks`方法来检索的集合`TaskItem`要显示的对象。
2.   **选择**– 时都需要某一行，在新的屏幕中显示该任务。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>任务详细信息屏幕

任务详细信息是允许要编辑或删除的任务的输入的屏幕。

Tasky 使用`MonoTouch.Dialog`的反射 API，以显示该屏幕中，因此，没有`UIViewController`实现。 相反，`HomeScreen`类实例化，并显示`DialogViewController`使用`TaskDialog`从应用程序层的类。

此屏幕快照显示演示了一个空屏幕`Entry`属性设置的水印文本**名称**和**说明**字段：

 [![](case-study-tasky-images/ios-taskydetail.png "此屏幕快照显示空屏幕演示条目属性中设置的名称和说明字段中的水印文本")](case-study-tasky-images/ios-taskydetail.png#lightbox)

功能**任务详细信息**必须中实现 （如保存或删除任务） 的屏幕`HomeScreen`类，因为这就是`MonoTouch.Dialog.BindingContext`创建。 以下`HomeScreen`方法支持任务详细信息屏幕：

1.   **ShowTaskDetails** – 创建`MonoTouch.Dialog.BindingContext`呈现屏幕。 它会创建使用反射来检索属性名称的输入的屏幕并从类型`TaskDialog`类。 具有属性的属性上实现的其他信息，例如，输入框中的水印文本。
2.   **SaveTask** – 此方法引用中`TaskDialog`类通过`OnTap`属性。 它时，将调用**保存**按下时，并使用`MonoTouch.Dialog.BindingContext`在保存所做的更改使用之前检索用户输入数据`TaskItemManager`。
3.   **DeleteTask** – 此方法引用中`TaskDialog`类通过`OnTap`属性。 它使用`TaskItemManager`删除使用主键 （ID 属性） 的数据。

 <a name="Android_App" />

## <a name="android-app"></a>Android App

完整的 Xamarin.Android 项目是如下图所示：

 ![](case-study-tasky-images/taskyandroid-solution.png "此处显示的 android 项目")

类关系图中，使用按层进行分组的类：

 [![](case-study-tasky-images/classdiagram-android.png "包含按层进行分组的类的类关系图")](case-study-tasky-images/classdiagram-android.png#lightbox)

 <a name="References" />

### <a name="references"></a>参考资料

Android 应用程序项目必须从 Android SDK，访问的类引用特定于平台的 Xamarin.Android 程序集。

它还必须引用 PCL 项目中 （例如。 TaskyPortableLibrary) 来访问公共数据和业务层代码。

 ![](case-study-tasky-images/taskyandroid-references.png "TaskyPortableLibrary 访问公共数据和业务层代码")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>应用程序层 (AL)

类似于我们讨论过的 iOS 版本更早版本，应用程序层中的 Android 版本包含绑定到 UI 的核心所公开的对象所需的特定于平台的类。

 **TaskListAdapter** -显示列表<T>我们需要实现一个适配器来显示中的自定义对象的对象`ListView`。 适配器控制在列表中每个项的使用哪种布局 – 在此情况下，该代码使用 Android 的内置布局`SimpleListItemChecked`。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>用户界面 (UI)

Android 应用程序的用户界面层是代码和 XML 标记的组合。

 -   **资源/布局**– 屏幕布局和行的单元格作为 AXML 文件实现的设计。 AXML 可以进行手动编写的或在布局的直观地使用适用于 Android 的 Xamarin UI 设计器。
 -   **资源/Drawable** – 图像 （图标） 和自定义按钮。
 -   **屏幕**– 活动的子类，可定义每个屏幕及其行为。 使用应用程序层类 UI 和通用 API，将联系在一起 (`TaskItemManager`)。

 <a name="Home_Screen" />

#### <a name="home-screen"></a>主屏幕

在主页屏幕包含活动子类`HomeScreen`和`HomeScreen.axml`文件用于定义布局 （按钮和任务列表的位置）。 屏幕如下所示：

 [![](case-study-tasky-images/android-taskylist.png "屏幕如下所示")](case-study-tasky-images/android-taskylist.png#lightbox)

主页屏幕代码定义的处理程序单击的按钮和单击项在列表中，以及填充在该列表`OnResume`方法，（以便它将反映在任务详细信息屏幕中所做的更改）。 使用业务层的加载数据`TaskItemManager`和`TaskListAdapter`从应用程序层。

 <a name="Task_Details_Screen" />

#### <a name="task-details-screen"></a>任务详细信息屏幕

任务详细信息屏幕也包括`Activity`子类并重 AXML 布局文件。 布局确定输入控件的位置和 C# 类定义的行为，用于加载和保存`TaskItem`对象。

 [![](case-study-tasky-images/android-taskydetail.png "类定义的行为，用于加载和保存 TaskItem 对象")](case-study-tasky-images/android-taskydetail.png#lightbox)

所有引用 PCL 库都均通过`TaskItemManager`类。

 <a name="Windows_Phone_App" />

## <a name="windows-phone-app"></a>Windows Phone 应用
完整的 Windows Phone 项目：

 ![](case-study-tasky-images/taskywp7-solution.png "Windows Phone 应用程序完整的 Windows Phone 项目")

下图显示分组到层的类：

 [![](case-study-tasky-images/classdiagram-wp7.png "此图显示分组到层的类")](case-study-tasky-images/classdiagram-wp7.png#lightbox)

 <a name="References" />

### <a name="references"></a>参考资料

特定于平台的项目必须引用所需的特定于平台的库 (如`Microsoft.Phone`和`System.Windows`) 若要创建有效的 Windows Phone 应用程序。

它还必须引用 PCL 项目中 （例如。 `TaskyPortableLibrary`) 以利用`TaskItem`类和数据库。

 ![](case-study-tasky-images/taskywp7-references.png "TaskyPortableLibrary 为利用 TaskItem 类和数据库")

 <a name="Application_Layer_(AL)" />

### <a name="application-layer-al"></a>应用程序层 (AL)

同样，与 iOS 和 Android 版本中，应用程序层包含帮助将数据绑定到用户界面的非可视元素。

 <a name="ViewModels" />

#### <a name="viewmodels"></a>ViewModels

Viewmodel 从 PCL 中的数据换行 ( `TaskItemManager`)，并提供可供 Silverlight/XAML 数据绑定的方式。 （如跨平台应用程序文档中所述），这是行为的特定于平台的一个示例。

 <a name="User_Interface_(UI)" />

### <a name="user-interface-ui"></a>用户界面 (UI)

XAML 有一个唯一的数据绑定功能，可以在标记中声明并减少显示的对象所需的代码量：

1.   **页**– XAML 文件，而其代码隐藏文件定义用户界面和引用 Viewmodel 和 PCL 项目中，显示和收集数据。
2.   **映像**– 初始屏幕、 背景和图标图像是用户界面的重要组成部分。

 <a name="MainPage" />

#### <a name="mainpage"></a>MainPage

MainPage 类使用`TaskListViewModel`以显示使用 XAML 的数据绑定功能的数据。 该页面的`DataContext`被设置为视图模型，以异步方式填充该字段。 `{Binding}`在 XAML 中的语法确定如何显示数据。

 <a name="TaskDetailsPage" />

#### <a name="taskdetailspage"></a>TaskDetailsPage

通过绑定来显示每个任务`TaskViewModel`到 TaskDetailsPage.xaml 中定义的 XAML。 通过检索的任务数据`TaskItemManager`在业务层。

 <a name="Results" />

## <a name="results"></a>结果

生成的应用程序类似于每个平台上：

 <a name="iOS" />

#### <a name="ios"></a>iOS

应用程序使用 iOS 标准用户界面设计，例如添加按钮在导航栏中正在放置和使用内置**加号 （+）**图标。 它还使用默认值`UINavigationController`后退按钮行为和支持轻扫到删除表中的。

 [![](case-study-tasky-images/ios-taskylist.png "它还将使用默认 UINavigationController 后退按钮行为，并支持轻扫--删除表中")](case-study-tasky-images/ios-taskylist.png#lightbox) [ ![ ](case-study-tasky-images/ios-taskylist.png "它还使用默认 UINavigationController后退按钮行为和支持轻扫--删除表中")](case-study-tasky-images/ios-taskylist.png#lightbox)

 <a name="Android" />

#### <a name="android"></a>Android

Android 应用程序使用内置控件包括需要时间刻度显示的行的内置布局。 除了屏幕上的后退按钮支持的硬件/系统后行为。

 [![](case-study-tasky-images/android-taskylist.png "硬件/系统后行为支持除了屏幕上的后退按钮")](case-study-tasky-images/android-taskylist.png#lightbox)[![](case-study-tasky-images/android-taskylist.png "硬件/系统后行为支持除了屏幕上后退按钮")](case-study-tasky-images/android-taskylist.png#lightbox)

 <a name="Windows_Phone" />

#### <a name="windows-phone"></a>Windows Phone

Windows Phone 应用程序使用标准的布局，填充而不是在顶部导航栏屏幕底部应用栏。

 [![](case-study-tasky-images/wp-taskylist.png "Windows Phone 应用程序使用标准的布局，填充而不是在顶部导航栏屏幕底部应用栏")](case-study-tasky-images/wp-taskylist.png#lightbox) [ ![ ] (case-study-tasky-images/wp-taskylist.png "Windows Phone 应用程序使用标准布局，填充而不是在顶部导航栏屏幕底部应用栏")](case-study-tasky-images/wp-taskylist.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>摘要

本文档提供了如何已分层的应用程序设计的原则应用于一个简单的应用程序，为了实现代码重用三个移动平台的详细的说明： iOS、 Android 和 Windows Phone。

它具有描述用于设计应用程序层的过程和讨论哪些代码&amp;功能已在每个层中已实现。

可以从下载的代码[github](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)。

## <a name="related-links"></a>相关链接

- [生成跨平台应用程序 （主文档）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Tasky 可移植示例应用程序 (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
