---
title: iOS 体系结构
description: 浏览较低级别的 Xamarin.iOS
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 930b52e5b2a532e71594f26af79035db2cc5fb25
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="ios-architecture"></a>iOS 体系结构

Xamarin.iOS 应用程序运行在单声道执行环境中，并使用完整继续操作的时间 (AOT) 编译编译到 ARM 程序集语言的 C# 代码。 这将运行的并行与[Objective C 运行时](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)。 这两个运行时环境上运行的服务类似 UNIX 的内核，专门[XNU](https://en.wikipedia.org/wiki/XNU)，公开到用户代码，从而允许开发人员访问基础本机或托管系统的各种 Api。

下图显示了这种体系结构的基本概述：

[ ![](architecture-images/ios-arch-small.png "此图显示了继续操作的时间 (AOT) 编译体系结构的基本概述")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>本机和托管代码： 说明

在开发用于 Xamarin 时条款*本机和托管*通常使用代码。 [托管代码](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/)是由其执行的代码[.NET Framework 公共语言运行时](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx)，或在 Xamarin 的情况下： Mono 运行时。 这是我们所说的中间语言。

本机代码是将在特定平台 （例如，Objective C 或甚至 AOT 编译代码，在 ARM 芯片上） 本机运行的代码。 本指南介绍了如何 AOT 编译到本机代码，托管的代码，并说明如何 Xamarin.iOS 应用程序配合使用，充分利用 Apple 的 iOS Api 通过使用的绑定，而同时还将访问权限。NET 的 BCL 和复杂的语言，如 C#。


## <a name="aot"></a>AOT

编译任何 Xamarin 平台应用程序时，Mono C# （或 F #） 编译器将运行，并将你 C# 和 F # 代码编译为 Microsoft 中间语言 (MSIL)。 如果你在模拟器中，运行 Xamarin.Android、 Xamarin.Mac 应用程序中或甚至 Xamarin.iOS 应用程序[.NET 公共语言运行时 (CLR)](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx)编译就使用实时 (JIT) 编译器中的 MSIL。 在此编译为本机代码的运行时，这可以在你的应用程序的正确体系结构上运行。

但是，没有对 iOS 设置 apple，不允许在设备上动态生成的代码执行的安全限制。
若要确保我们遵守这些安全协议，Xamarin.iOS 改为使用继续操作的时间 (AOT) 编译器编译托管的代码。 这将生成的本机 iOS 二进制，（可选） 使用 LLVM 优化的设备，可以部署到 Apple 的基于 ARM 的处理器。 这如何相互配合粗略示意图如下图所示：

[![](architecture-images/aot.png "这如何相互配合粗略示意图")](architecture-images/aot-large.png#lightbox)

使用 AOT 具有数限制，详见[限制](~/ios/internals/limitations.md)指南。 它还提供了大量改进通过 JIT 通过减少启动时间，以及各种性能优化

现在，我们已经学习了如何代码的编译方式从源到本机代码，我们看一下实质上，若要查看如何 Xamarin.iOS 使我们可以编写完全本机 iOS 应用程序

## <a name="selectors"></a>选择器

使用 Xamarin，我们有两个单独生态系统，.NET 和 Apple，我们需要将起来看起来尽可能简化尽可能，以确保最终目标是平滑的用户体验。 在上面的两个运行时通信的方式的一节中，我们已了解，你也可能很好地听说过这样本机 iOS Api 要在 Xamarin 中使用的术语绑定。 在中深入解释了绑定我们[Objective C 绑定](~/cross-platform/macios/binding/overview.md)文档，因此现在让我们研究一下实质上的 iOS 工作原理。

首先，必须有一种方法来公开 OBJECTIVE-C 向 C#，这是通过选择器。 选择器是一条消息发送到的对象或类。 Objective C 达到此目的通过[objc_msgSend](~/cross-platform/macios/binding/overview.md)函数。
有关使用选择器的详细信息，请参阅[OBJECTIVE-C 的选择器](~/ios/internals/objective-c-selectors.md)指南。 此外必须有一种方法向公开的托管的代码 OBJECTIVE-C，这是更复杂的因为这样一个事实： Objective C 不知道有关托管代码的任何信息。 若要获取解决此，我们使用*注册机构*。 在下一部分中的更详细地对它们进行了解释。

## <a name="registrars"></a>注册机构

如上所述，注册机构是代码到目标 c。 该公开托管的代码 这是通过创建派生自 NSObject 每个托管类的列表：

- 对于不包装现有的 OBJECTIVE-C 类的所有类，它将创建一个新的 OBJECTIVE-C 类与 Objective C 成员镜像具有的所有托管的成员 [`Export`] 属性。

- 在每个目标 – C 成员的实现中，代码将自动添加到调用的镜像托管的成员。

下面的伪代码演示如何完成此操作的示例：

**C# （托管代码）**

```csharp
 class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
```

**Objective-C:**

```objectivec
@interface MyViewController : UIViewController { }

    -(void)myFunc;
@end

@implementation MyViewController {}

    -(void) myFunc
    {
        /* code to call the managed MyViewController.MyFunc method */
    }
@end

```

托管的代码可以包含的属性，`[Register]`和`[Export]`，注册机构用于知道需要向目标 C.公开的对象
`[Register]`属性用于在生成的默认名称是不适合的情况下指定生成的 OBJECTIVE-C 类的名称。 从 NSObject 派生的所有类都自动都注册目标 c。
所需`[Export]`属性包含一个字符串，即使用生成的 OBJECTIVE-C 类中的选择器。

有两种类型的注册机构在 Xamarin.iOS – 动态和静态中使用：


- **动态注册机构**– 动态注册机构未在运行时程序集中的所有类型的注册。 通过使用提供的函数执行此[OBJECTIVE-C 的运行时 API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)。 动态注册机构因此有慢启动，但更快地生成时间。 这是默认值为 iOS 模拟器。 使用动态注册机构时，调用 trampolines 的本机函数 （通常在 C 中)，使用作为方法实现。 它们有所不同不同的体系结构。

- **静态注册机构**– 静态注册机构生成 Objective C 代码生成，也不能是然后编译到静态库链接到可执行文件期间。 这使得更快启动，但在生成期间所用时间。 这是默认情况下使用为设备版本。 静态注册机构还可与 iOS 模拟器通过传递`--registrar:static`作为`mtouch`属性在你的项目的生成选项中，如下所示：

    [![](architecture-images/image1.png "设置其他 mtouch 自变量")](architecture-images/image1.png#lightbox)

有关详细信息 iOS Xamarin.iOS 由使用的类型注册系统的详细信息，请参阅[类型注册机构](~/ios/internals/registrar.md)指南。

## <a name="application-launch"></a>应用程序启动

由调用的函数提供所有 Xamarin.iOS 可执行文件的入口点`xamarin_main`，其中初始化 mono。

具体取决于项目类型中，以下操作：

- 有关正则 iOS 和 tvOS 应用程序，调用的托管的 Main 方法，提供的 Xamarin 应用程序。 此管理 Main 方法然后调用`UIApplication.Main`，即的入口点为目标。 UIApplication.Main 是适用于 OBJECTIVE-C 的绑定`UIApplicationMain`方法。
- 为了扩展和本机函数中 –`NSExtensionMain`或 (`NSExtensionmain` WatchOS 扩展) – 提供由 Apple 调用库。 由于这些项目类库和而非可执行项目，没有托管的 Main 方法执行。

此启动序列的所有会编译成静态库，然后链接到你的最终可执行文件使你的应用程序知道如何获取完全关闭。

此时我们的应用程序已开始、 运行 Mono、 在托管代码和我们知道如何调用本机代码和回叫。 我们需要做的下一步操作是实际开始添加控件并使应用程序交互。


## <a name="generator"></a>Generator

Xamarin.iOS 包含每个单个 iOS API 的定义。 你可以浏览其中任何上[MaciOS github 存储库](https://github.com/xamarin/xamarin-macios/tree/master/src)。 这些定义包含具有属性、 接口以及任何所需的方法和属性。 例如，下面的代码是用于在 UIKit 中定义 UIToolbar[命名空间](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)。 请注意，它是与大量的方法和属性的接口：

```csharp
[BaseType (typeof (UIView))]
public interface UIToolbar : UIBarPositioning {
    [Export ("initWithFrame:")]
    IntPtr Constructor (CGRect frame);

    [Export ("barStyle")]
    UIBarStyle BarStyle { get; set; }

    [Export ("items", ArgumentSemantic.Copy)][NullAllowed]
    UIBarButtonItem [] Items { get; set; }

    [Export ("translucent", ArgumentSemantic.Assign)]
    bool Translucent { [Bind ("isTranslucent")] get; set; }

    // done manually so we can keep this "in sync" with 'Items' property
    //[Export ("setItems:animated:")][PostGet ("Items")]
    //void SetItems (UIBarButtonItem [] items, bool animated);

    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage ([NullAllowed] UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);

    ...
}
```

生成器中，调用[ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) Xamarin.iOS，在将这些定义文件，并使用.NET 工具[将它们编译到临时程序集](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318)。 但是，此临时程序集不可用来调用 Objective C 的代码。 然后，该生成器读取临时程序集，并生成可以在运行时使用的 C# 代码。
这是为什么，例如，如果将随机属性添加到你定义的.cs 文件，它不会显示在输出的代码。 生成器不知道它，因此`btouch`不知道要在其输出的临时程序集中查找。

一旦创建 Xamarin.iOS.dll 后，mtouch 将绑定的所有组件在一起。

在高级别，它来实现此目标通过执行以下任务：

-   创建应用捆绑包结构。
-   将复制你的托管程序集中。
-   如果启用了链接，运行托管链接器以优化你的程序集通过翻录出的未使用的部分。
-   AOT 编译。
-   创建本机可执行文件，输出一系列的静态库的链接到本机可执行文件，（一个用于每个程序集），因此本机可执行文件组成的启动器代码、 注册机构代码 （如果静态） 和 AOT 中的所有输出编译器


有关更多详细信息链接器以及如何使用它，请参阅[链接器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>总结

本指南介绍了 AOT 编译 Xamarin.iOS 应用程序和浏览的 Xamarin.iOS 以及它与 Objective C 深度中的关系。

## <a name="related-links"></a>相关链接

- [限制](~/ios/internals/limitations.md)
- [绑定 Objective-C](~/cross-platform/macios/binding/overview.md)
- [Objective-C 的选择器](~/ios/internals/objective-c-selectors.md)
- [类型注册机构](~/ios/internals/registrar.md)
- [链接器](~/ios/deploy-test/linker.md)
