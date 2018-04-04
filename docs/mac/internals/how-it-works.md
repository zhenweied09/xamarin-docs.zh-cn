---
title: Xamarin.Mac 的工作原理
description: 本文档介绍 Xamarin.Mac 的内部工作原理。 具体而言，它会查看构造函数、 内存管理，之前实时编译和注册机构。
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/25/2017
ms.openlocfilehash: baa60d177a7d7d070a218108b2f6779eeaf94f78
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="how-xamarinmac-works"></a>Xamarin.Mac 的工作原理

大多数情况下开发人员将永远不必担心内部"神奇"的 Xamarin.Mac，但是，具有的内容实质上的工作原理将如何帮助解释现有文档使用 C# 镜头和调试问题中有大致了解时发生。

应用程序中 Xamarin.Mac，桥接两个领域： 没有包含本机类的实例基于 Objective C 运行时 (`NSString`，`NSApplication`等)，并且没有包含托管类的实例的 C# 运行时 (`System.String`， `HttpClient`等)。 这两个领域之间 Xamarin.Mac 创建双向桥，以便应用程序可以 Objective C 中的调用方法 （选择器） (如`NSApplication.Init`) 和 Objective C 可以调用返回 （如应用程序委托的方法） 的应用程序的 C# 方法。 一般情况下，到 Objective C 的调用以透明方式处理通过**P/Invokes**和 Xamarin 提供了一些运行时代码。

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>公开 C# 类 / Objective C 的方法

但是，对于 Objective C 将回调到应用的 C# 对象，它们需要 Objective C 可以理解的方式公开。 这是通过`Register`和`Export`属性。 请参见以下示例：

```csharp
[Register ("MyClass")]
public class MyClass : NSObject
{
   [Export ("init")]
   public MyClass ()
   {
   }

   [Export ("run")]
   public void Run ()
   {
   }
}
```

在此示例中，Objective C 运行时将现在了解有关名为的类信息`MyClass`与选择器调用`init`和`run`。

在大多数情况下，这是实现详细信息，开发人员可以忽略，因为大多数应用程序接收的回调将是指在通过重写方法上`base`类 (如`AppDelegate`， `Delegates`， `DataSources`) 或在**操作**传递到 Api。 在所有这些情况下，`Export`属性，则不需要在 C# 代码。

## <a name="constructor-runthrough"></a>构造函数浏览

在许多情况下，开发人员将需要公开应用程序的 C# 类构造 API 为 Objective C 运行时，以便可以将它实例化从位置如情节提要或 XIB 文件中调用时。 下面是在 Xamarin.Mac 应用中使用的最常见的五个构造函数：

```csharp
// Called when created from unmanaged code
public CustomView (IntPtr handle) : base (handle)
{
   Initialize ();
}

// Called when created directly from a XIB file
[Export ("initWithCoder:")]
public CustomView (NSCoder coder) : base (coder)
{
   Initialize ();
}

// Called from C# to instance NSView with a Frame (initWithFrame)
public CustomView (CGRect frame) : base (frame)
{
}

// Called from C# to instance NSView without setting the frame (init)
public CustomView () : base ()
{
}

// This is a special case constructor that you call on a derived class when the derived called has an [Export] constructor.
// For example, if you call init on NSString then you don’t want to call init on NSObject.
public CustomView () : base (NSObjectFlag.Empty)
{
}
```

一般情况下，开发人员应保留`IntPtr`和`NSCoder`创建如自定义某些类型时，将生成的构造函数`NSViews`单独。 如果 Xamarin.Mac 需要对 Objective C 运行时请求响应中调用这些构造函数之一，并且你已经将其删除，应用程序将崩溃本机代码内，则可能很难找出完全问题。

## <a name="memory-management-and-cycles"></a>内存管理和周期

在许多方面非常类似于 Xamarin.iOS 是 Xamarin.Mac 中的内存管理。 它也是复杂的主题，一个超出本文的讨论范围。 请阅读[内存和性能最佳实践](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

## <a name="ahead-of-time-compilation"></a>继续操作的实时编译

通常，在生成时没有.NET 应用程序编译到机器代码，而是将它们编译到调用获取的 IL 代码的中间层_中实时_应用启动时 (JIT) 编译为机器代码。

单声道运行时对 JIT 编译的时间此机器代码可能会降低 Xamarin.Mac 应用的启动最多 20%，取决于将必要的机器代码，要生成的时间。

由于 Apple 在 iOS 上施加的限制，不供 Xamarin.iOS 的 IL 代码的 JIT 编译。 因此，所有的 Xamarin.iOS 应用程序已满_Ahead 时间_(AOT) 在生成周期期间编译为机器代码。

新到 Xamarin.Mac 是 AOT 的功能的 IL 代码在应用程序生成周期中，就像 Xamarin.iOS 可以。 Xamarin.Mac 使用_混合_AOT 方法编译大部分所需的机器代码，但允许运行时来编译所需的 trampolines 和灵活地继续支持 Reflection.Emit （和其他使用案例的案例当前支持的 Xamarin.Mac）。

有两个主要区域 AOT 有助于 Xamarin.Mac 应用：

- **更好的"本机"崩溃日志**-如果 Xamarin.Mac 应用程序崩溃时在本机代码中，这是常见情况，在插入 Cocoa Api 的无效调用时 (例如发送`null`到不接受它的方法)、 本机崩溃 JIT 与日志帧难以分析。 由于 JIT 帧没有调试信息，将会以十六进制偏移量的多个行和发生了什么不知道。 AOT 生成"real"命名的帧并跟踪变得更容易读取。 这也意味着 Xamarin.Mac 应用程序将更好地与交互本机工具如**lldb**和**检测**。
- **更好地启动时间性能**-对于大型 Xamarin.Mac 应用程序，使用多个第二个启动时间，JIT 编译的所有代码可能需要很长时间。 AOT 执行此操作，提前。

### <a name="enabling-aot-compilation"></a>启用 AOT 编译

在 Xamarin.Mac 中双击启用 AOT**项目名称**中**解决方案资源管理器**、 导航至**Mac 生成**和添加`--aot:[options]`到**其他 mmp 自变量：**字段 (其中`[options]`是一个或多个选项，若要控制 AOT 类型，请参阅下文)。 例如：

![向其他 mmp 自变量添加 AOT](how-it-works-images/aot01.png "添加 AOT 到其他 mmp 自变量")

> [!IMPORTANT]
> 启用 AOT 编译显著增加生成时，有时是最多达几分钟时间，但它可以提高应用程序启动时间的 20%的平均值。 因此，AOT 编译仅都应上启用**版本**Xamarin.Mac 应用的版本。

### <a name="aot-compilation-options"></a>Aot 编译选项

有几个不同的选项，可以启用在 Xamarin.Mac 应用上的 AOT 编译时调整：

- `none` -无 AOT 编译。 此为默认设置。
- `all` -AOT 编译 MonoBundle 中的每个程序集。
- `core` -AOT 编译`Xamarin.Mac`，`System`和`mscorlib`程序集。
- `sdk` -AOT 编译`Xamarin.Mac`和基库 (BCL) 程序集。
- `|hybrid` -添加到上述选项之一启用混合 AOT 可提供最小化 IL，但将导致再编译时间。
- `+` -包括到 AOT 编译为一个。
- `-` -从 AOT 编译中删除单个文件。

例如，`--aot:all,-MyAssembly.dll`将启用所有 MonoBundle 中的程序集上 AOT 编译_除_`MyAssembly.dll`和`--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll`将启用混合，代码 AOT 包含`MyOtherAssembly.dll`和排除`mscorlib.dll`.

## <a name="partial-static-registrar"></a>部分静态注册机构

开发 Xamarin.Mac 应用，则会变为重要对满足开发的截止时间将完成更改和对其进行测试之间的时间降至最低。 策略如模块化以外的基本代码和单元测试可帮助以减少编译时间，因为它们降低的次数，应用将需要在昂贵的完全重新生成。

此外，和新手 Xamarin.Mac，_部分静态注册机构_（如通过 Xamarin.iOS 率先推出了） 可以大大减少中的 Xamarin.Mac 应用的启动时间**调试**配置。 了解如何使用分部静态注册机构可以挤压出几乎 5 倍的调试启动将需要花费一些注册机构是的区别是静态和动态，之间，此"部分静态"版本的用途的背景。

### <a name="about-the-registrar"></a>有关注册机构

任何 Xamarin.Mac 揭秘应用程序存在于从 Apple 和 Objective C 运行时的 Cocoa framework。 生成此"本机 world"和"托管的 world"的 C# 之间的桥梁是 Xamarin.Mac 的主要责任。 此任务的一部分由内执行注册`NSApplication.Init ()`方法。 这是在 Xamarin.Mac Cocoa Api 的任何使用需要的一个原因`NSApplication.Init`第一次调用。

注册机构的作业是通知 Objective C 运行时应用的 C# 类，派生自类诸如存在的`NSApplicationDelegate`， `NSView`， `NSWindow`，和`NSObject`。 这要求应用程序中的所有类型，以确定什么需要注册，并且每个类型设置为报表上的哪些元素的扫描。

此扫描可以来完成操作**动态**，在使用反射，在应用程序启动或**静态**，作为生成时间步骤。 时选取注册类型，开发人员应注意以下内容：

- 静态注册可以大大减少启动时间，但可以生成时间大大减慢 （通常多个双精度调试生成时间）。 这将是默认为**版本**配置生成。
- 动态注册它延迟此工作，直到应用程序启动并跳过代码生成，但这一附加工作可以创建明显暂停 （至少 2 秒） 在应用程序启动。 这一点尤为明显在调试配置版本，其默认为动态注册和其反射较慢。

部分 Xamarin.iOS 8.13 中首次引入的静态注册为开发人员提供了这两个选项的最佳。 通过预先计算中每个元素的注册信息`Xamarin.Mac.dll`和传送此信息与静态库 （即只需在生成时链接到） 中的 Xamarin.Mac，Microsoft 已删除大部分动态反射时间注册机构时不影响生成时间。

### <a name="enabling-the-partial-static-registrar"></a>启用部分的静态注册机构

部分静态注册机构在 Xamarin.Mac 中启用通过双击**项目名称**中**解决方案资源管理器**、 导航至**Mac 生成**和添加`--registrar:static`到**其他 mmp 自变量：**字段。 例如：

![将部分静态注册机构添加到其他 mmp 参数](how-it-works-images/psr01.png "将部分静态注册机构添加到其他 mmp 自变量")

## <a name="additional-resources"></a>其他资源

下面是一些更详细的解释的功能的工作方式内部：

- [Objective-C 的选择器](~/ios/internals/objective-c-selectors.md)
- [注册器](~/ios/internals/registrar.md)
- [对于 iOS 和 OS X 的 Xamarin 统一 API](~/cross-platform/macios/unified/index.md)
- [Theading 基础知识](~/ios/app-fundamentals/threading.md)
- [委托、 协议和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [有关 `newrefcount`](~/ios/internals/newrefcount.md)

