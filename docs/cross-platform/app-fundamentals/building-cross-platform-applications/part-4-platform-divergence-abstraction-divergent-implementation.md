---
title: "第 4 部分 — 处理多个平台"
ms.topic: article
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 21cd08ad2eb9c78ba1bcd6b31400a38266c65e51
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="part-4---dealing-with-multiple-platforms"></a>第 4 部分 — 处理多个平台

## <a name="handling-platform-divergence-amp-features"></a>处理平台分歧&amp;功能

分歧不只是跨平台问题;在相同的平台上的设备具有不同的功能 （特别是各种可用的 Android 设备）。 最明显且最基本是屏幕的大小，但其他设备属性可以改变，并需要应用程序在检查某些功能和行为以不同的方式基于其存在 （或不存在）。

这意味着所有应用程序需要处理接受降级的功能，否则呈现毫无吸引力、 最低公分功能的一组。 Xamarin 的与本机 Sdk 的每个平台的深度集成允许应用程序利用特定于平台的功能，因此有必要设计应用程序以使用这些功能。

请参阅平台功能文档概述了平台之间的功能的差异。

 <a name="Examples_of_Platform_Divergence" />


### <a name="examples-of-platform-divergence"></a>平台分歧的示例

 <a name="Fundamental_elements_that_exist_across_platforms" />


#### <a name="fundamental-elements-that-exist-across-platforms"></a>存在跨平台的基本元素

有某些特性是通用的移动应用程序。
这些是设计的较高级别的概念通常适用于所有设备，因此可以形成应用程序的基础：

-  通过选项卡或菜单的功能选择
-  数据和滚动的列表
-  数据的单个视图
-  编辑数据的单个视图
-  向后导航


设计高级屏幕流时可以将一种通用的用户体验基于这些概念。

 <a name="platform-specific_attributes" />


#### <a name="platform-specific-attributes"></a>特定于平台的属性

除了在所有平台存在的基本元素，你将需要在设计中的地址密钥平台的差异。 你可能需要考虑 （和编写代码以处理专用） 这些差异：

-   **屏幕大小**– 某些平台 （如 iOS 和 Windows Phone 早期） 具有标准化就相对简单，以面向的屏幕大小。 Android 设备没有大量不同的屏幕尺寸，需要更多工作来支持你的应用程序中。
-   **导航隐喻**– 在平台 （如之间的区别 硬件后退按钮，全景 UI 控件） 和平台 (Android 2 和 4，iPhone 和 iPad) 中。
-   **键盘**– 某些 Android 设备没有物理键盘，而其他设备仅有软件键盘。 可检测电脑何时软键盘变得模糊屏幕的一部分的代码必须是敏感的这些差异。
-   **触控和手势**– 笔势识别的操作系统支持各不相同，尤其是在每个操作系统的较旧版本。 早期版本的 Android 具有非常有限的支持对于触摸操作，这意味着支持较旧的设备可能需要单独的代码
-   **推送通知**– 每个平台 （如上有不同的功能实现 动态磁贴在 Windows 上）。


 <a name="Device-specific_features" />


#### <a name="device-specific-features"></a>特定于设备的功能

确定应用程序所需的最小功能必须是的内容;或者，当决定以利用每个平台上的哪些其他功能。 将所需代码来检测功能和禁用功能或提供多种可选方案 （如。 地理位置的替代方法可以是以使用户能够键入位置，或从映射中选择）：

-   **照相机**– 在设备之间的功能有所不同： 某些设备没有照相机，其他人有两个前端和后端面向相机。 某些相机都能使用的视频录制。
-   **地理位置和地图**– 支持 GPS 或 Wi-fi 位置上不存在的所有设备。 应用还需要满足在不同级别的支持的每个方法的准确性。
-   **加速、 陀螺和指南针**– 使应用程序几乎总是时需要提供回退硬件不受支持，这些功能通常找到仅所选内容中的每个平台上的设备。
-   **Twitter 和 Facebook** – 仅内置 iOS5 和 iOS6 上分别。 在早期版本和其他平台上将需要提供您自己的身份验证函数和接口直接与每个服务的 API。
-   **近距离通信 (NFC)** – （某些） 仅在 Android 手机 （在撰写本文之际）。


 <a name="Dealing_with_Platform_Divergence" />


### <a name="dealing-with-platform-divergence"></a>处理平台分歧

有两个不同的方法相同的基本代码，各自具有自己的优点和缺点从支持多个平台。

-   **平台抽象**– 业务外观模式，跨平台提供的统一的访问和提取到单个、 统一的 API 的特定平台实现。
-   **同名的不同实现**– 通过同名的不同实现通过如接口和继承或条件编译的体系结构工具功能的特定平台的调用。


 <a name="Platform_Abstraction" />


## <a name="platform-abstraction"></a>平台抽象

 <a name="Class_Abstraction" />


### <a name="class-abstraction"></a>类抽象

使用接口或基类共享代码中定义和实现或扩展特定于平台的项目中。 编写和扩展与类抽象的共享的代码特别适合到可移植类库因为提供给他们的 framework 的有限的子集，并且不能包含编译器指令以支持特定于平台的代码分支。

 <a name="Interfaces" />


#### <a name="interfaces"></a>接口

使用接口可以实现仍可以传到你的共享库以利用通用代码的特定于平台的类。

接口定义在共享代码中，并将其作为参数或属性传递到共享的库。

特定于平台的应用程序可以然后实现接口，并且仍然可以利用共享代码以对其进行处理。

 **优点**

实现可以包含特定于平台的代码和甚至引用特定于平台的外部库。

 **缺点**

无需创建并将实现传递到共享的代码。 如果该接口用于深层中共享代码然后它最后传递多个方法参数或否则下推通过调用链。 如果共享的代码使用大量不同的接口然后它们必须全部创建并且某处的共享代码中设置。

 <a name="Inheritance" />


#### <a name="inheritance"></a>继承

共享的代码可以在一个或多个特定于平台的项目中实现抽象或虚拟类，无法进行扩展。 这是类似于使用接口，但有一些已实现的行为。 接口或继承是否更好的设计选择有不同的视角： 尤其是 C# 仅允许单一继承，因为它可以规定可以今后设计您的 Api 的方式。 请慎用继承。

优点和缺点接口同样适用于具有额外的优点，可以包含某些实现代码 （可能是整个平台不可知实现，可以根据需要扩展） 的基类继承。

<a name="Xamarin.Forms" />

### <a name="xamarinforms"></a>Xamarin.Forms

请参阅[Xamarin.Forms](~/xamarin-forms/get-started/index.md)文档。


### <a name="plug-in-cross-platform-functionality"></a>插件的跨平台功能

你还可以以一致的方式使用的插件来扩展跨平台应用。

从链接我们[插件 github](https://github.com/xamarin/plugins)，大多数插件是开放源代码项目 （通常适用于通过 Nuget 安装），可帮助你实现各种电池状态从设置到的特定于平台的功能轻松使用 Xamarin 平台和 Xamarin.Forms 应用程序中的公共 API。


<a name="Other_Cross-Platform_Libraries" />

### <a name="other-cross-platform-libraries"></a>其他的跨平台库

有大量的可用提供跨平台功能的第三方库：

-   **MvvmCross** -  [https://github.com/slodge/MvvmCross/](https://github.com/slodge/MvvmCross/)
-   **Vernacular** （用于本地化）- [https://github.com/rdio/vernacular/](https://github.com/rdio/vernacular/)
-   **MonoGame** （针对的 XNA 游戏）- [http://monogame.codeplex.com/](http://monogame.codeplex.com/)
-   **NGraphics** - [NGraphics](https://github.com/praeclarum/NGraphics)和其前提[https://github.com/praeclarum/CrossGraphics](https://github.com/praeclarum/CrossGraphics)


 <a name="Divergent_Implementation" />


### <a name="divergent-implementation"></a>同名的不同实现

 <a name="Conditional_Compilation" />


#### <a name="conditional-compilation"></a>条件编译

在某些情况下，其中你在共享的代码将仍需要在每个平台上，可能访问类或功能的行为不同的工作方式不同。 条件编译最适用于共享资产项目中，同一源文件在具有不同的符号定义的多个项目中引用的位置。

Xamarin 项目始终定义`__MOBILE__`适用于 iOS 和 Android 应用程序项目 （请注意双下划线前和在这些符号后修补程序）。

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

<a name="iOS" />

##### <a name="ios"></a>iOS

Xamarin.iOS 定义`__IOS__`可以用于检测 iOS 设备。

```csharp
#if __IOS__
// iOS-specific code
#endif
```

也有监视和电视特定的符号：

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

<a name="Android" />

##### <a name="android"></a>Android

可以使用下面的代码应仅编译到 Xamarin.Android 应用程序

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

每个 API 版本还定义了一个新的编译器指令，所以类似下面的代码将让你可以添加功能，如果针对较新的 API。 每个 API 级别包括所有低的级别符号。 此功能不支持多个平台; 真正发挥作用通常`__ANDROID__`符号就够用了。

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

##### <a name="mac"></a>Mac

当前没有 Xamarin.Mac，为内置符号，但你可以添加自己的 Mac 在应用程序项目**选项 > 生成 > 编译器**中**定义符号**框中，或编辑**.csproj**文件并添加存在 (例如`__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

<a name="Windows_Phone" />

##### <a name="windows-phone"></a>Windows Phone

Windows Phone 应用定义两个符号 –`WINDOWS_PHONE`和`SILVERLIGHT`– 可以用于目标平台的代码。 这些不具备执行其周围 Xamarin 平台符号如下划线。


<a name="Using_Conditional_Compilation" />

##### <a name="using-conditional-compilation"></a>使用条件编译

条件编译的一个简单的案例研究示例设置 SQLite 数据库文件的文件位置。 三个平台都具有指定的文件位置的略有不同要求：

-   **iOS** – Apple 首选非用户数据放入特定位置 （库目录），但没有为此目录没有系统常量。 不需要特定于平台的代码来生成正确的路径。
-   **Android** – 返回的系统路径`Environment.SpecialFolder.Personal`是可接受的位置来存储数据库文件。
-   **Windows Phone** – 独立的存储机制不允许完整的路径来指定，只是相对路径和文件名。
-   **通用 Windows 平台**– 使用`Windows.Storage`Api。

下面的代码使用条件编译以确保`DatabaseFilePath`为每个平台正确无误：

```csharp
public static string DatabaseFilePath {
        get {
    var filename = "TodoDatabase.db3";
#if SILVERLIGHT
    // Windows Phone 8
    var path = filename;
#else

#if __ANDROID__
    string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
#if __IOS__
        // we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library");
#else
        // UWP
        string libraryPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
#endif
#endif
        var path = Path.Combine (libraryPath, filename);
#endif
        return path;
}
```

结果是一个类可以生成和使用在所有平台上，将 SQLite 数据库文件放在每个平台上的其他位置。

