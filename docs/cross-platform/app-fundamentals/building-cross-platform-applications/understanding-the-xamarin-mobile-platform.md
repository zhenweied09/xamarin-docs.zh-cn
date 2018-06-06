---
title: 第 1 – 了解 Xamarin 移动平台部分
description: 本文档介绍了 Xamarin 平台在高级别上，查看在编译过程，平台 SDK 访问、 代码共享、 创建用户界面、 可视化设计器，和的详细信息。
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 40183e5f18ee589adaf2ea903f6948293c8680f7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781889"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>第 1 – 了解 Xamarin 移动平台部分

Xamarin 平台包括允许你开发应用程序的 iOS 和 Android 的元素数目：

-   **C# 语言**– 允许你使用熟悉的语法和泛型、 LINQ 和并行任务库等高级的功能。
-   **单声道.NET framework** – 提供 Microsoft 的.NET framework 中的大量功能的跨平台实现。
-   **编译器**– 根据的平台，生成本机应用程序 （如。 iOS) 或集成的.NET 应用程序和运行时 （例如。 Android)。 编译器还为移动的部署，例如链接掉未使用的代码执行许多优化。
-   **IDE 工具**– 在 Mac 和 Windows 上的 Visual Studio 允许你创建、 生成和部署 Xamarin 项目。


此外，因为基础的语言是 C# 使用.NET framework，可以构造项目来共享还可以将部署到 Windows Phone 的代码。

 <a name="Under_the_Hood" />


## <a name="under-the-hood"></a>揭秘

尽管 Xamarin 允许你在 C# 中，编写应用并在多个平台之间共享相同的代码，但每个系统上的实际实现很大差异。

 <a name="Compilation" />


## <a name="compilation"></a>编译

C# 源所做每个平台上非常不同的方式的本机应用到自己的方式：

-   **iOS** – C# 是预时间的 (AOT) 编译为 ARM 程序集语言。 .NET framework 是包括在内，使用去掉在链接以减少应用程序的大小期间未使用的类。 Apple 不允许运行时代码生成在 iOS 中，因此某些语言功能将不可用 (请参阅[Xamarin.iOS 限制](~/ios/internals/limitations.md))。
-   **Android** – C# 编译到 IL 和与 MonoVM + JIT'ing 一起打包。 在链接过程中去掉 framework 中的未使用的类。 应用程序运行的并行使用 Java/画 （Android 运行时），与通过 JNI 的本机类型进行交互 (请参阅[Xamarin.Android 限制](~/android/internals/limitations.md))。
-   **Windows** – C# 编译为 IL 执行和由内置的运行时，而不需要 Xamarin 工具。 设计 Windows 应用程序以下 Xamarin 的指南使重新使用 iOS 和 Android 上的代码更简单。
  请注意，通用 Windows 平台还具有 **.NET Native**选项的行为类似于 Xamarin.iOS 的 AOT 编译它。


链接器文档[Xamarin.iOS](~/ios/deploy-test/linker.md)和[Xamarin.Android](~/android/deploy-test/linker.md)提供了有关这一部分的编译过程的详细信息。

运行时编译 – 生成代码动态与`System.Reflection.Emit`– 应当避免。

Apple 的内核可防止在 iOS 设备上的动态代码生成，因此发出代码上快速 Xamarin.iOS 中将不工作。 同样，动态语言运行时功能不能用于 Xamarin 工具。

某些反射功能完成的工作 （例如。 MonoTouch.Dialog 将其用于反射 API），只不过无法代码生成。

 <a name="Platform_SDK_Access" />


## <a name="platform-sdk-access"></a>平台 SDK 访问

Xamarin 使熟悉 C# 语法提供特定于平台的 sdk 可以轻松进行访问的功能：

-   **iOS** – Xamarin.iOS 作为可以从 C# 引用的命名空间中公开 Apple 的 CocoaTouch SDK 框架。 例如，包含所有用户界面控件 UIKit framework 可以包含在一个简单`using UIKit;`语句。
-   **Android** – Xamarin.Android 作为命名空间，以便您可以引用使用支持的任何的 SDK 部分都公开 Google Android SDK 语句，如`using Android.Views;`访问用户界面控件。
-   **Windows** – 生成在 Windows 上使用 Visual Studio 的 Windows 应用。 项目类型包括 Windows 窗体、 WPF、 WinRT、 和通用 Windows 平台 (UWP)。


 <a name="Seamless_Integration_for_Developers" />


## <a name="seamless-integration-for-developers"></a>开发人员的的无缝集成

Xamarin 的好处是，尽管实质上的差异，Xamarin.iOS 和 Xamarin.Android （结合 Microsoft 的 Windows Sdk） 提供有关编写 C# 代码可以在所有三个平台之间重复使用的无缝体验。

业务逻辑、 数据库使用情况、 网络访问权限和其他常用函数可以写入一次并在每个平台，用于查找并作为本机应用程序执行的特定于平台的用户界面中提供的基础上重新使用。

 <a name="Integrated_Development_Environment_(IDE)_Availability" />


## <a name="integrated-development-environment-ide-availability"></a>集成的开发环境 (IDE) 可用性

Xamarin 开发可在 Mac 或 Windows 上的 Visual Studio 中。 选择将由平台 IDE 你想要为目标。

由于 Windows 应用程序可以仅开发在 Windows 上，以创建针对 iOS，Android、_和_Windows 需要 Visual Studio for Windows。 但是，很可能共享项目和 Windows 和 Mac 计算机，以便可以在 Mac 上生成 iOS 和 Android 应用和更高版本无法将共享的代码添加到 Windows 项目之间的文件。

中更详细地讨论了每个平台的开发要求[要求](~/cross-platform/get-started/requirements.md)指南。


<a name="iOS" />

### <a name="ios"></a>iOS

开发 iOS 应用程序需要运行 macOS 的 Mac 计算机。 你可以使用 Visual Studio 编写和部署在 Visual Studio 中使用 Xamarin iOS 应用程序。 但是，仍然生成和许可目的需要 Mac。

必须安装 Apple 的 Xcode IDE 提供的编译器和用于测试的模拟器。 可以在你自己的设备上测试[免费](~/ios/get-started/installation/device-provisioning/free-provisioning.md)，但的分发 （如构建应用程序 应用商店中） 必须加入 Apple 开发人员计划 （99 美元每年）。 每次提交或更新的应用程序，它必须先查看并批准 apple 才能将其可供下载的客户。

使用 Visual Studio IDE 中编写代码和屏幕布局可以以编程方式生成或 Xamarin 的 iOS 任一 IDE 中的设计器上进行编辑。

请参阅[Xamarin.iOS 安装指南](~/ios/get-started/installation/index.md)有关获取设置的详细说明。

<a name="Android" />

### <a name="android"></a>Android

Android 应用程序开发需要 Java 和 Android Sdk 安装。 这提供了编译器、 仿真程序和生成，部署和测试所需的其他工具。 Java、 Google Android SDK 和 Xamarin 的工具可所有安装和运行上的以下配置：

-  Mac OS X El Capitan 和更高版本 （10.11 +） 与 Visual Studio for Mac
-  Windows 7 和更高版本与 Visual Studio 2015 或自 2017 年 1


Xamarin 提供了一个统一的安装程序，将配置您的系统必备的 Java，与 Android 和 Xamarin 的工具 （包括可视化设计器中的为屏幕布局）。 请参阅[Xamarin.Android 安装指南](~/android/get-started/installation/index.md)有关的详细说明。

你可以生成和测试任何许可证的情况下实际设备上的应用程序将来自 Google，但是若要分发通过应用商店应用程序 (如 Google Play、 Amazon 或 Barnes&amp;挺) 注册费用可能 payable 运算符。 Google Play 将此时，发布你的应用程序，而其他存储具有类似于 Apple 的审批过程。

 <a name="Windows_Phone" />


### <a name="windows"></a>Windows

Windows 应用 （WinForms，WPF 中或 UWP） 都是用 Visual Studio 生成的。 它们不直接使用 Xamarin。 但是，C# 代码可以共享多个 Windows、 iOS 和 Android。
请访问 Microsoft 的[开发人员中心](https://developer.microsoft.com/)若要了解有关所需的 Windows 开发的工具。

 <a name="Creating_the_User_Interface_(UI)" />


## <a name="creating-the-user-interface-ui"></a>创建用户界面 (UI)

使用 Xamarin 的主要优点是应用程序用户界面使用上创建的应用程序从 OBJECTIVE-C 或 Java 编写的应用程序时不可区分每个平台的本机控件 (iOS 和 Android 分别)。

在你的应用程序中构建屏幕，可以设置代码中的控件布局或创建的每个平台使用提供的设计工具的完整屏幕。

 <a name="Programmatically_Create_Controls" />


### <a name="create-controls-programmatically"></a>以编程方式创建控件

每个平台允许用户界面控件添加到屏幕上使用代码。 这一点可能会非常耗时，因为它可以很难直观显示已完成的设计时硬编码像素坐标控件位置和大小。

以编程方式创建控件也存在一些优点，特别是在用于生成调整大小或在 iPhone 和 iPad 的屏幕大小中以不同的方式呈现的视图的 iOS。

 <a name="Visual_Designer" />


### <a name="visual-designer"></a>可视化设计器

每个平台都有用于直观地布局屏幕的不同方法：

-   **iOS** – Xamarin 的 iOS 设计器有助于构建使用拖放功能和属性字段的视图。 这些视图共同构成情节提要，并可在访问 **。情节提要**项目中包含的文件。
-   **Android** – Xamarin for Visual Studio 提供的 Android 的拖放 UI 设计器。 Android 屏幕布局将另存为 **。AXML**文件时使用 Xamarin 工具。
-   **Windows** – Microsoft 提供了 Visual Studio 和 Blend 中的拖放 UI 设计器。 屏幕布局作为存储。XAML 文件。


这些屏幕截图显示每个平台上可用的可视化屏幕设计器：

 [ ![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "这些屏幕截图显示每个平台上可用的可视化屏幕设计器")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

在所有情况下可以在代码中引用以可视方式创建的元素。

 <a name="User_Interface_Considerations" />


### <a name="user-interface-considerations"></a>用户界面注意事项

使用 Xamarin 生成跨平台应用程序的主要优点是它们可以充分利用本机 UI 工具包，以向用户提供熟悉的界面。 UI 也将作为任何其他本机应用程序快速执行。

某些 UI 隐喻的工作在多个平台 （例如，所有三个平台使用的类似的滚动列表控件），但为了使你的应用程序认为右 UI 应充分利用特定于平台的用户界面元素在适当的时候。 特定于平台的 UI 隐喻的示例包括：

-   **iOS** – 与软后退按钮，层次结构导航选项卡上屏幕的底部。
-   **Android** – 硬件/系统软件返回按钮、 操作菜单、 在屏幕顶部的选项卡。
-   **Windows** – Windows 应用可以在台式计算机、 平板电脑 （如 Microsoft Surface) 和手机上运行。 Windows 10 设备可能包括硬件后按钮和动态磁贴，例如。


建议你阅读与你面向的平台相关的设计准则：

-   **iOS** – [Apple 的人机接口指南](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
-   **Android** – [Google 的用户界面指南](http://developer.android.com/guide/practices/ui_guidelines/index.html)
-   **Windows** –[适用于 Windows 的用户体验设计准则](https://developer.microsoft.com/windows/design)


 <a name="Library_and_Code_Re-use" />


## <a name="library-and-code-re-use"></a>库和代码重用

Xamarin 平台允许你重复使用现有 C# 代码的跨所有平台，以及为每个平台编写的本机库的集成。

 <a name="C#_Source_and_Libraries" />


### <a name="c-source-and-libraries"></a>C# 源代码和库

由于 Xamarin 产品使用 C# 和.NET framework，因此大量 （均是开放式源和内部项目） 的现有源代码可以 Xamarin.iOS 或 Xamarin.Android 项目中重复使用。 通常的源可以只需添加到 Xamarin 解决方案，它将立即运行。 如果已使用不受支持的.NET framework 功能，可能需要一些优化。

可以在 Xamarin.iOS 或 Xamarin.Android 中使用的 C# 源代码的示例包括： SQLite NET、 NewtonSoft.JSON 和 SharpZipLib。

 <a name="Objective-C_Bindings_+_Binding_Projects" />


### <a name="objective-c-bindings--binding-projects"></a>Objective C 绑定 + 绑定项目

Xamarin 提供了一种称为工具*btouch*帮助创建允许 Objective C 库在 Xamarin.iOS 项目中使用的绑定。 请参阅[绑定 Objective C 类型文档](~/cross-platform/macios/binding/binding-types-reference.md)有关如何完成此操作的详细信息。

可在 Xamarin.iOS 的 Objective C 库的示例包括： RedLaser 条形码扫描、 Google Analytics 和 PayPal 集成。 开放源代码 Xamarin.iOS 绑定都位于[github](https://github.com/mono/monotouch-bindings)。

 <a name=".jar_Bindings_+_Binding_Projects" />


### <a name="jar-bindings--binding-projects"></a>.jar 绑定 + 绑定项目

Xamarin 支持使用 Xamarin.Android 中的现有 Java 库。 请参阅[绑定 Java 库文档](~/android/platform/binding-java-library/index.md)有关如何使用的详细信息。从 Xamarin.Android 的 JAR 文件。

开放源代码 Xamarin.Android 绑定都位于[github](https://github.com/mono/monodroid-bindings)。

 <a name="C_via_PInvoke" />


### <a name="c-via-pinvoke"></a>通过 PInvoke C

"平台 Invoke"技术 (P/Invoke) 返回到托管代码中允许托管的代码 (C#) 来调用本机库中的方法，以及要调用的本机库的支持。

例如， [SQLite NET](https://github.com/praeclarum/sqlite-net)库使用如下语句：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

这将绑定到 iOS 和 Android 的本机 C 语言 SQLite 实施。
熟悉现有 C API 的开发人员可以构建一组的 C# 类将映射到本机 API，并利用现有的平台代码。 没有文档[链接本机库](~/ios/platform/native-interop.md)在 Xamarin.iOS，只类似的原则适用于 Xamarin.Android。

 <a name="C++_via_Cxxi" />


### <a name="c-via-cppsharp"></a>C + + 通过 CppSharp

Miguel 说明 CXXI (现在称为[CppSharp](https://github.com/mono/CppSharp)) 上其[博客](http://tirania.org/blog/archive/2011/Dec-19.html)。 与直接绑定到 c + + 库的替代方法是创建 C 包装器，并将绑定到的通过 P/Invoke。

