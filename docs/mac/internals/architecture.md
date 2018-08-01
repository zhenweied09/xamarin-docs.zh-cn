---
title: Xamarin.Mac 体系结构
description: 本指南浏览到 OBJECTIVE-C 的较低级别 Xamarin.Mac 和及其之间的关系。 它还说明了概念，如编译、 选择器、 注册机构、 应用程序启动和生成器。
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: d6d7557fed5ea0ca0719dcbddbda316340645320
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30785551"
---
# <a name="xamarinmac-architecture"></a>Xamarin.Mac 体系结构

_本指南浏览到 OBJECTIVE-C 的较低级别 Xamarin.Mac 和及其之间的关系。它还说明了概念，如编译、 选择器、 注册机构、 应用程序启动和生成器。_

## <a name="overview"></a>概述

Xamarin.Mac 应用程序运行在单声道执行环境中，并使用 Xamarin 的编译器来编译到中间语言 (IL)，即然后中的实时 (JIT) 编译为本机代码在运行时。 这将运行的并行与 Objective C 运行时。 这两个运行时环境上类似于 UNIX 的内核，专门 XNU，运行的服务并公开到用户代码，从而允许开发人员访问基础本机或托管系统的各种 Api。

下图显示了这种体系结构的基本概述：

[![显示的体系结构的基本概述图示](architecture-images/mac-arch.png "显示体系结构的基本概述图示")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>本机和托管代码

在开发用于 Xamarin 时, 条款*本机*和*托管*通常使用代码。 托管的代码是由.NET Framework 公共语言运行时，或在 Xamarin 的情况下管理其执行的代码： Mono 运行时。

本机代码是将在特定平台 （例如，Objective C 或甚至 AOT 编译代码，在 ARM 芯片上） 本机运行的代码。 本指南介绍了如何在托管的代码编译为本机代码，并说明如何 Xamarin.Mac 应用程序配合使用，充分利用通过绑定，使用 Apple 的 Mac Api 时也有权访问。NET 的 BCL 和复杂的语言，如 C#。

## <a name="requirements"></a>要求

以下是通过 Xamarin.Mac 开发 macOS 应用程序所需的条件：

- Mac 运行 macOS Sierra (10.12) 或更高版本。
- Xcode 的最新版本 (从安装[应用商店](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- 最新版本的 Xamarin.Mac 和 Visual Studio for Mac

运行通过 Xamarin.Mac 创建的 Mac 应用程序具有以下系统要求：

- 运行 Mac OS X 10.7 或更高版本的 Mac。

## <a name="compilation"></a>编译

编译任何 Xamarin 平台应用程序时，Mono C# （或 F #） 编译器将运行，并且将 C# 和 F # 代码编译为 Microsoft 中间语言 （MSIL 或 IL）。 然后使用 Xamarin.Mac*就实时 (JIT)* 编译器在运行时编译本机代码，根据需要在正确的体系结构上执行。

这是使用 AOT 编译的 Xamarin.iOS 相反。 使用 AOT 编译器时，在生成时被编译所有程序集以及其中的所有方法。 使用 JIT，编译的过程仅为执行的方法按需。

与 Xamarin.Mac 应用程序，Mono 通常会嵌入到应用程序捆绑包 (称为**嵌入 Mono**)。 使用经典 Xamarin.Mac API 时，应用程序可以改为使用**系统 Mono**，但是，这不支持统一 API。 系统 Mono 是指已安装在操作系统中的 Mono。 应用程序启动，Xamarin.Mac 应用将使用它。

## <a name="selectors"></a>选择器

使用 Xamarin，我们有两个单独生态系统，.NET 和 Apple，我们需要将起来看起来尽可能简化尽可能，以确保最终目标是平滑的用户体验。 在上面的两个运行时通信的方式的一节中，我们已了解，你也可能很好地听说过这样本机的 Mac Api，要在 Xamarin 中使用的术语绑定。 在中深入解释了绑定[Objective C 绑定文档](~/mac/platform/binding.md)，因此现在，让我们研究一下 Xamarin.Mac 实质上工作原理。

首先，必须有一种方法来公开 OBJECTIVE-C 向 C#，这是通过选择器。 选择器是一条消息发送到的对象或类。 Objective C 达到此目的通过[objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html)函数。 有关使用选择器的详细信息，请参阅 iOS [OBJECTIVE-C 的选择器](~/ios/internals/objective-c-selectors.md)指南。 此外必须有一种方法向公开的托管的代码 OBJECTIVE-C，这是更复杂的因为这样一个事实： Objective C 不知道有关托管代码的任何信息。 若要获取解决此，我们使用[注册机构](~/mac/internals/registrar.md)。 这在下一部分中的更详细说明。

## <a name="registrar"></a>注册器

如上所述，注册机构是代码到目标 c。 该公开托管的代码 这是通过创建派生自 NSObject 每个托管类的列表：

- 对于不包装现有的 OBJECTIVE-C 类的所有类，它将创建一个新的 OBJECTIVE-C 类与 Objective C 成员镜像具有的所有托管的成员`[Export]`属性。
- 在每个目标 – C 成员的实现中，代码将自动添加到调用的镜像托管的成员。

下面的伪代码演示如何完成此操作的示例：

**C# （托管代码）：**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**Objective-c （本机代码）：**

```objc
@interface MyViewController : UIViewController
 - (void)myFunc;
@end 

@implementation MyViewController
- (void)myFunc {
    // Code to call the managed C# MyFunc method in MyViewController
}
@end
```

托管的代码可以包含的属性，`[Register]`和`[Export]`，注册机构用于知道需要向目标 C.公开的对象 [注册] 属性用于在生成的默认名称是不适合的情况下指定生成的 OBJECTIVE-C 类的名称。 从 NSObject 派生的所有类都自动都注册目标 c。 必需的 [导出] 特性包含一个字符串，即使用生成的 OBJECTIVE-C 类中的选择器。

有两种类型的注册机构中 Xamarin.Mac – 动态和静态使用：

- 动态注册机构 – 这是所有 Xamarin.Mac 生成的默认注册机构。 动态注册机构未在运行时程序集中的所有类型的注册。 通过使用 OBJECTIVE-C 的运行时 API 提供的函数来执行此操作。 动态注册机构因此有慢启动，但更快地生成时间。 使用动态注册机构时，调用 trampolines 的本机函数 （通常在 C 中)，使用作为方法实现。 它们有所不同不同的体系结构。
- 静态注册机构 – 静态注册机构期间生成，也不能是然后编译到静态库链接到可执行文件中生成 Objective C 的代码。 这使得更快启动，但在生成期间所用时间。

## <a name="application-launch"></a>应用程序启动

使用系统 Mono 或 Xamarin.Mac 的启动逻辑将与不同，具体取决于是否嵌入。 若要查看的代码和 Xamarin.Mac 应用程序启动的步骤，请参阅[启动标头](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h)xamarin macios 公共存储库中的文件。

## <a name="generator"></a>Generator

Xamarin.Mac 包含的每个 Mac API 的定义。 你可以浏览其中任何上[MaciOS github 存储库](https://github.com/xamarin/xamarin-macios/tree/master/src)。 这些定义包含具有属性、 接口以及任何所需的方法和属性。 例如，下面的代码是用于定义在 NSBox [AppKit 命名空间](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526)。 请注意，它是与大量的方法和属性的接口：

```csharp
[BaseType (typeof (NSView))]
public interface NSBox {

        …

        [Export ("borderRect")]
        CGRect BorderRect { get; }

        [Export ("titleRect")]
        CGRect TitleRect { get; }

        [Export ("titleCell")]
        NSObject TitleCell { get; }

        [Export ("sizeToFit")]
        void SizeToFit ();

        [Export ("contentViewMargins")]
        CGSize ContentViewMargins { get; set; }

        [Export ("setFrameFromContentFrame:")]
        void SetFrameFromContentFrame (CGRect contentFrame);

        …

}
```

生成器中，调用`bmac`在 Xamarin.Mac，采用这些定义文件并使用.NET 工具将其编译到临时程序集。 但是，此临时程序集不可用来调用 Objective C 的代码。 然后，该生成器读取临时程序集，并生成可以在运行时使用的 C# 代码。 这是为什么，例如，如果将随机属性添加到你定义的.cs 文件，它不会显示在输出的代码。 生成器不知道它，因此`bmac`不知道要在其输出的临时程序集中查找。

Xamarin.Mac.dll 创建后，使用 packager `mmp`，将绑定的所有组件在一起。

在高级别，它来实现此目标通过执行以下任务：

- 创建应用捆绑包结构。
- 将复制你的托管程序集中。
- 如果启用了链接，运行托管链接器来通过删除未使用的部分优化你的程序集。
- 创建启动器应用程序，以及如果处于静态模式 registar 代码讨论了启动器代码中的链接。

这是以用户的一部分运行生成过程，从而将用户代码编译到程序集，该引用 Xamarin.Mac.dll 和运行然后`mmp`以使其包

有关更多详细信息链接器以及如何使用它，请参阅 iOS[链接器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>总结

本指南讨论过 Xamarin.Mac 应用和浏览的 Xamarin.Mac 和到目标 c。 其关系的编译
