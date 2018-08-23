---
title: 在 Xamarin.iOS 封送处理的异常
description: 本文档介绍如何使用 Xamarin.iOS 应用程序中的本机和托管异常。 它讨论可能发生的问题以及这些问题的解决方案。
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/05/2017
ms.openlocfilehash: dcf1074aacb6d139d107dac01fa86f459831d5f9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786738"
---
# <a name="exception-marshaling-in-xamarinios"></a>在 Xamarin.iOS 封送处理的异常

_Xamarin.iOS 包含新的事件，以帮助响应异常，尤其是在本机代码。_

托管的代码和 Objective C 都支持运行时异常 （try/catch/finally 子句）。

但是，其实现是不同，这意味着在他们需要处理的异常，然后运行用其他语言编写的代码时，运行时库 （单声道运行时和 Objective C 运行时库） 都将出现问题。

本文档说明可以发生的问题和可能的解决方案。

它还包括一个示例项目，[异常封送处理](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)，这可以用于测试不同的方案和其解决方案。

## <a name="problem"></a>问题

异常引发，并在堆栈展开某个帧期间遇到与引发的异常的类型不匹配时发生问题。

本机 API 引发 Objective C 异常，并且然后该 Objective C 异常必须以某种方式进行处理时在堆栈展开过程达到托管的帧时 Xamarin.iOS 或 Xamarin.Mac 典型的例子。

默认操作是不执行任何操作。 对于上面的示例中，这意味着让 Objective C 运行时托管的展开帧。 这会出现问题，因为 Objective C 运行时不知道如何展开托管的帧;例如，它不会执行任何`catch`或`finally`该框架中的子句。

### <a name="broken-code"></a>中断的代码

请考虑下面的代码示例：

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

此操作将引发 Objective C NSInvalidArgumentException 本机代码中：

    NSInvalidArgumentException *** setObjectForKey: key cannot be nil

和堆栈跟踪将类似如下内容：

    0   CoreFoundation          __exceptionPreprocess + 194
    1   libobjc.A.dylib         objc_exception_throw + 52
    2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
    3   libobjc.A.dylib         objc_msgSend + 102
    4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
    5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
    6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()

帧 0-3 是本机框架和堆栈展开器 Objective C 运行时中的_可以_展开这些帧。 具体而言，它会执行任何 Objective C`@catch`或`@finally`子句。

但是，Objective C 堆栈开卷机是_不_能够正确展开托管的帧 （帧 4-6），因为的帧都将展开，但将不会执行托管的异常的逻辑。

这意味着，它通常是不可能按以下方式捕捉这些异常：

```csharp
try {
    var dict = new NSMutableDictionary ();
    dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero);
} catch (Exception ex) {
    Console.WriteLine (ex);
} finally {
    Console.WriteLine ("finally");
}
```

这是因为 Objective C 堆栈开卷机不知道托管`catch`子句，并且既不将`finally`执行子句。

在上面的代码示例_是_有效，是因为 Objective C 的未经处理的 Objective C 异常通知方法有 [`NSSetUncaughtExceptionHandler`][2]，后者Xamarin.iOS 和 Xamarin.Mac 使用，在该点尝试将任何 Objective C 异常转换为托管异常。

## <a name="scenarios"></a>方案

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>方案 1-捕获 Objective C 异常与托管的 catch 处理程序

在以下方案中，它有可能是捕获 Objective C 异常使用托管`catch`处理程序：

1. Objective C 将引发异常。
2. Objective C 运行时遍历堆栈，这时 （但不是展开它），寻找本机`@catch`能够处理该异常的处理程序。
3. Objective C 运行时找不到任何`@catch`处理程序，调用`NSGetUncaughtExceptionHandler`，并调用由 Xamarin.iOS/Xamarin.Mac 安装处理程序。
4. Xamarin.iOS/Xamarin.Mac's 处理程序将将 Objective C 异常转换为托管异常，并引发它。 由于 Objective C 运行时未展开堆栈 （仅遍历它），当前帧是同一个引发 Objective C 异常的位置。

因为单声道运行时不知道如何正确展开 OBJECTIVE-C 的帧，在这里，出现另一个问题。

当调用 Xamarin.iOS 的未捕获的 Objective C 异常回调时，堆栈是如下：

     0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "*** setObjectForKey: key cannot be nil")
     1 CoreFoundation           __handleUncaughtException + 809
     2 libobjc.A.dylib          _objc_terminate() + 100
     3 libc++abi.dylib          std::__terminate(void (*)()) + 14
     4 libc++abi.dylib          __cxa_throw + 122
     5 libobjc.A.dylib          objc_exception_throw + 337
     6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
     7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
     8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
     9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
    10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]

在这里，仅托管的帧帧 8-10，但在帧 0 中引发托管的异常。 这意味着，单声道运行时必须展开本机框架 0-7，这会导致问题等效于上面讨论的问题： 虽然单声道运行时将展开本机框架，它不会执行任何 Objective C`@catch`或`@finally`子句.

代码示例：

```objc
-(id) setObject: (id) object forKey: (id) key
{
    @try {
        if (key == nil)
            [NSException raise: @"NSInvalidArgumentException"];
    } @finally {
        NSLog (@"This will not be executed");
    }
}
```

与`@finally`子句将不会执行，因为展开此帧单声道运行时不知道它。

此变体是引发托管的异常中的托管的代码和通过本机框架，以获取然后展开第一个托管`catch`子句：

```csharp
class AppDelegate : UIApplicationDelegate {
    public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
    {
        throw new Exception ("An exception");
    }
    static void Main (string [] args)
    {
        try {
            UIApplication.Main (args, null, typeof (AppDelegate));
        } catch (Exception ex) {
            Console.WriteLine ("Managed exception caught.");
        }
    }
}
```

托管`UIApplication:Main`方法将调用本机`UIApplicationMain`方法，，然后选择 iOS 将执行在最终调用托管之前的本机代码执行很多`AppDelegate:FinishedLaunching`方法替换仍大量的托管的异常时在堆栈上的本机框架引发：

     0: TestApp                 ExceptionMarshaling.IOS.AppDelegate:FinishedLaunching (UIKit.UIApplication,Foundation.NSDictionary)
     1: TestApp                 (wrapper runtime-invoke) <Module>:runtime_invoke_bool__this___object_object (object,intptr,intptr,intptr) 
     2: libmonosgen-2.0.dylib   mono_jit_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
     3: libmonosgen-2.0.dylib   do_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
     4: libmonosgen-2.0.dylib   mono_runtime_invoke [inlined] mono_runtime_invoke_checked(method=<unavailable>, obj=<unavailable>, params=<unavailable>, error=0xbff45758)
     5: libmonosgen-2.0.dylib   mono_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>)
     6: libxamarin-debug.dylib  xamarin_invoke_trampoline(type=<unavailable>, self=<unavailable>, sel="application:didFinishLaunchingWithOptions:", iterator=<unavailable>), context=<unavailable>)
     7: libxamarin-debug.dylib  xamarin_arch_trampoline(state=0xbff45ad4)
     8: libxamarin-debug.dylib  xamarin_i386_common_trampoline
     9: UIKit                   -[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:]
    10: UIKit                   -[UIApplication _callInitializationDelegatesForMainScene:transitionContext:]
    11: UIKit                   -[UIApplication _runWithMainScene:transitionContext:completion:]
    12: UIKit                   __84-[UIApplication _handleApplicationActivationWithScene:transitionContext:completion:]_block_invoke.3124
    13: UIKit                   -[UIApplication workspaceDidEndTransaction:]
    14: FrontBoardServices      __37-[FBSWorkspace clientEndTransaction:]_block_invoke_2
    15: FrontBoardServices      __40-[FBSWorkspace _performDelegateCallOut:]_block_invoke
    16: FrontBoardServices      __FBSSERIALQUEUE_IS_CALLING_OUT_TO_A_BLOCK__
    17: FrontBoardServices      -[FBSSerialQueue _performNext]
    18: FrontBoardServices      -[FBSSerialQueue _performNextFromRunLoopSource]
    19: FrontBoardServices      FBSSerialQueueRunLoopSourceHandler
    20: CoreFoundation          __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
    21: CoreFoundation          __CFRunLoopDoSources0
    22: CoreFoundation          __CFRunLoopRun
    23: CoreFoundation          CFRunLoopRunSpecific
    24: CoreFoundation          CFRunLoopRunInMode
    25: UIKit                   -[UIApplication _run]
    26: UIKit                   UIApplicationMain
    27: TestApp                 (wrapper managed-to-native) UIKit.UIApplication:UIApplicationMain (int,string[],intptr,intptr)
    28: TestApp                 UIKit.UIApplication:Main (string[],intptr,intptr)
    29: TestApp                 UIKit.UIApplication:Main (string[],string,string)
    30: TestApp                 ExceptionMarshaling.IOS.Application:Main (string[])

框架 0-1 和 27 30 进行管理，虽然两者之间的所有这些都是本机。 如果通过这些帧，没有 Objective C 展开 Mono`@catch`或`@finally`将执行子句。

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>方案 2-无法捕获 Objective C 异常

在以下方案中，它是_不_可以捕获 Objective C 异常使用托管`catch`处理程序由于另一种方法中处理了该 Objective C 异常：

1. Objective C 将引发异常。
2. Objective C 运行时遍历堆栈，这时 （但不是展开它），寻找本机`@catch`能够处理该异常的处理程序。
3. Objective C 运行时查找`@catch`处理程序中，展开堆栈，并开始执行`@catch`处理程序。

这种情况下通常位于 Xamarin.iOS 应用程序，因为主线程上没有通常如下代码：

``` objective-c
void UIApplicationMain ()
{
    @try {
        while (true) {
            ExecuteRunLoop ();
        }
    } @catch (NSException *ex) {
        NSLog (@"An unhandled exception occured: %@", exc);
        abort ();
    }
}

```

这意味着在主线程没有永远不会真正未经处理的 Objective C 异常，并因此永远不调用我们将 Objective C 异常转换为托管异常的回调。

这也是很常见调试 Xamarin.Mac 早期 macOS 版本比 Xamarin.Mac 支持因为检查在调试器中的大多数 UI 对象将尝试，提取属性相对应的正在执行的平台 （不存在的选择器上的应用程序时因为 Xamarin.Mac 包括支持更高版本的 macOS 版本）。 调用此类选择器将引发`NSInvalidArgumentException`（"无法识别的选择器发送到..."），这最终将导致进程崩溃。

总之，具有 Objective C 运行时或单声道运行时展开帧它们不编程到句柄可能会导致未定义的行为，例如崩溃，内存泄漏和其他类型的不可预测 (mis) 行为。

## <a name="solution"></a>解决方案

在 Xamarin.iOS 10 和 Xamarin.Mac 2.10 中，我们添加了支持捕获托管和 Objective C 异常在任何托管本机边界，以及将该异常转换为另一种类型。

在伪代码中，查找类似如下内容：

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

P/Invoke 到 objc_msgSend 已截获，并改为调用：

``` objective-c
void
xamarin_dyn_objc_msgSend (id obj, SEL sel)
{
    @try {
        objc_msgSend (obj, sel);
    } @catch (NSException *ex) {
        convert_to_and_throw_managed_exception (ex);
    }
}
```

和类似的内容出于反之亦然 （封送到 Objective C 异常的托管的异常）。

在托管本机边界上捕获异常不是成本释放，因此它不始终处于启用状态默认情况下：

- Xamarin.iOS/tvOS： 模拟器中启用了截获的 OBJECTIVE-C 的异常。
- Xamarin.watchOS： 截获会强制执行在所有情况下，因为让托管 Objective C 运行时展开帧将混淆垃圾回收器中，并且挂起或崩溃，请。
- Xamarin.Mac： 支持截获 Objective C 异常进行调试版本。

[生成时标志](#build_time_flags)部分介绍如何启用截获时默认不启用。

## <a name="events"></a>事件

有两个新的事件后截获异常引发：`Runtime.MarshalManagedException`和`Runtime.MarshalObjectiveCException`。

这两个事件传递`EventArgs`对象，其中包含原始抛出的异常 (`Exception`属性)，和`ExceptionMode`属性来定义应如何封送异常。

`ExceptionMode`来更改属性在事件处理程序以更改根据完成处理程序中的任何自定义处理行为。 一个示例将中止该进程，如果发生某些异常。

更改`ExceptionMode`属性将应用到单个事件，它不会影响将来截获任何异常。

可以使用以下方式：

- `Default`： 默认值因平台而异。 它是`ThrowObjectiveCException`如果 GC 在协作模式下 (watchOS) 和`UnwindNativeCode`否则为 (iOS / watchOS / macOS)。 默认值可能会在将来更改。
- `UnwindNativeCode`： 这是以前 （未定义） 的行为。 这不可用时在协作模式下使用 GC （即 watchOS 上的唯一选项; 因此，这不是 watchOS 上的有效选项），但它是所有其他平台的默认选项。
- `ThrowObjectiveCException`： 将托管的异常转换为 Objective C 异常和引发 Objective C 异常。 这是 watchOS 上的默认值。
- `Abort`： 中止该进程。
- `Disable`： 禁用异常截获，因此它没有任何意义，若要将此值设置事件处理程序中，但后引发事件时已晚以禁用它。 在任何情况下，如果设置，它将充当`UnwindNativeCode`。

为了封送到托管代码的 Objective C 异常，提供了以下模式：

- `Default`： 默认值因平台而异。 它是`ThrowManagedException`如果 GC 在协作模式下 (watchOS) 和`UnwindManagedCode`否则为 (iOS / tvOS / macOS)。 默认值可能会在将来更改。
- `UnwindManagedCode`： 这是以前 （未定义） 的行为。 这不可用时在协作模式下使用 GC （即上 watchOS 唯一有效的 GC 模式，因此这不是 watchOS 上的有效选项），但它是所有其他平台的默认值。
- `ThrowManagedException`： 将 Objective C 异常转换为托管异常和引发托管的异常。 这是 watchOS 上的默认值。
- `Abort`： 中止该进程。
- `Disable`： 禁用异常截获，因此它没有任何意义，若要设置此值在事件处理程序，但一次事件引发中时，它为时已晚以禁用它。 在任何情况下如果设置，它将中止该进程。

因此，若要查看每次异常封送处理时，可以执行此操作：

``` csharp
Runtime.MarshalManagedException += (object sender, MarshalManagedExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling managed exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
    
};
Runtime.MarshalObjectiveCException += (object sender, MarshalObjectiveCExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling Objective-C exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
};
```

<a name="build_time_flags" />

## <a name="build-time-flags"></a>生成时标志

可以传递到以下选项**mtouch** （有关 Xamarin.iOS 应用程序） 和**mmp** （对于 Xamarin.Mac 应用程序），以确定是否启用了异常截获，并设置的默认操作应发生：

- `--marshal-managed-exceptions=`
  - `default`
  - `unwindnativecode`
  - `throwobjectivecexception`
  - `abort`
  - `disable`

- `--marshal-objectivec-exceptions=`
  - `default`
  - `unwindmanagedcode`
  - `throwmanagedexception`
  - `abort`
  - `disable`

除`disable`，这些值是等于`ExceptionMode`值传递给`MarshalManagedException`和`MarshalObjectiveCException`事件。

`disable`选项将_主要_禁用截获，但我们仍将截获异常时不会添加任何执行开销。 封送处理的事件则仍会引发这些异常，与正在执行的平台的默认模式的默认模式。

## <a name="limitations"></a>限制

我们仅截获 P/Invoke 移动到`objc_msgSend`函数尝试捕获 Objective C 异常时系列。 这意味着到另一个 C 函数，然后引发任何 Objective C 异常，该函数使用 P/Invoke 仍将运行旧的和未定义行为 （这可以提高在将来）。

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>相关链接

- [异常封送 （示例）](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
