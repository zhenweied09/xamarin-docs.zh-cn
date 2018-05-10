---
title: 生成优化
description: 本文档说明在 Xamarin.iOS 和 Xamarin.Mac 应用的生成时应用的各种优化。
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: bradumbaugh
ms.author: brumbaug
dateupdated: 04/16/2018
ms.openlocfilehash: abdd1223c0105156580b8f23fc2c020f2f45caa6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="build-optimizations"></a>生成优化

本文档说明在 Xamarin.iOS 和 Xamarin.Mac 应用的生成时应用的各种优化。

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>删除 UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

删除到调用[UIApplication.EnsureUIThread] [ 1] （针对 Xamarin.iOS) 或`NSApplication.EnsureUIThread`（对于 Xamarin.Mac)。

此优化将更改下列类型的代码：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

为以下：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

此优化需要链接器来启用，且仅应用到方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

默认情况下启用为发布生成。

可以通过传递重写默认行为`--optimize=[+|-]remove-uithread-checks`到 mtouch/mmp。

[1]: https://developer.xamarin.com/api/member/UIKit.UIApplication.EnsureUIThread/

## <a name="inline-intptrsize"></a>内联 IntPtr.Size

内联常数值的`IntPtr.Size`根据目标平台。

此优化将更改下列类型的代码：

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

为以下 （构建的 64 位平台） 时：

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

此优化需要链接器来启用，且仅应用到方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

默认情况下，如果面向的同一个体系结构，已启用或平台程序集 (**Xamarin.iOS.dll**， **Xamarin.TVOS.dll**， **Xamarin.WatchOS.dll**或**Xamarin.Mac.dll**)。

如果面向多个体系结构，此优化将创建为 32 位版本和 64 位版本的应用程序中，不同的程序集，这两个版本将需要包含在应用中，有效地增加而不减少最终应用程序大小它。

可以通过传递重写默认行为`--optimize=[+|-]inline-intptr-size`到 mtouch/mmp。

## <a name="inline-nsobjectisdirectbinding"></a>内联 NSObject.IsDirectBinding

`NSObject.IsDirectBinding` 是实例属性，用于确定某个特定实例是否包装类型的或不 (包装类型的托管的类型的映射到本机类型; 对于实例托管`UIKit.UIView`类型映射到本机`UIView`类型-相反是用户类型在这种情况下`class MyUIView : UIKit.UIView`将是用户类型)。

需要知道的值`IsDirectBinding`时调入 OBJECTIVE-C 的因为值确定哪个版本的`objc_msgSend`使用。

给定下面的代码：

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

我们可以确定在`UIView.SomeProperty`的值`IsDirectBinding`不是常量，不能为内联：

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

但是，很可能看一看应用程序中的所有类型，并确定没有类型继承自`NSUrl`，因此它可安全地内联`IsDirectBinding`为常量值`true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

具体而言，此优化将更改下列类型的代码 (这是绑定代码`NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

为以下 (时它可以确定不是否存在的任何子类`NSUrl`应用程序中):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

此优化需要链接器来启用，且仅应用到方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

它是始终启用 Xamarin.iOS，默认情况下，并且始终为 Xamarin.Mac 禁用默认情况下 （因为它是可能以动态方式加载 Xamarin.Mac 中的程序集，它是不可能以确定特定类永远不会进行了子类化）。

可以通过传递重写默认行为`--optimize=[+|-]inline-isdirectbinding`到 mtouch/mmp。

## <a name="inline-runtimearch"></a>内联 Runtime.Arch

此优化将更改下列类型的代码：

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

为以下 （构建的设备） 时：

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

此优化需要链接器来启用，且仅应用到方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

它始终会针对 Xamarin.iOS （它也无法供 Xamarin.Mac） 启用默认情况下。

可以通过传递重写默认行为`--optimize=[+|-]inline-runtime-arch`到 mtouch。

## <a name="dead-code-elimination"></a>死代码消除

此优化将更改下列类型的代码：

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

到：

```csharp
Console.WriteLine ("Doing this");
```

它还将评估常量比较，如下：

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

和确定表达式`8 == 8`是始终 true，并减少到：

```csharp
Console.WriteLine ("Doing this");
```

这是一个功能强大的优化内联优化，一起使用时，因为它可以转换以下类型的代码 (这是绑定代码`NFCIso15693ReadMultipleBlocksConfiguration.Range`):

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

到此 (对于 64 位设备，生成时，以及何时还能够确保有没有`NFCIso15693ReadMultipleBlocksConfiguration`在应用程序的子类):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

AOT 编译器已能够执行消除死代码如下，但在链接器，这意味着链接器能够看到有多个方法，不使用，并因此可能会删除 （除非使用其他位置） 内完成此优化:

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

此优化需要链接器来启用，且仅应用到方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

它始终会启用默认情况下，（如果已启用链接器）。

可以通过传递重写默认行为`--optimize=[+|-]dead-code-elimination`到 mtouch/mmp。

## <a name="optimize-calls-to-blockliteralsetupblock"></a>优化对 BlockLiteral.SetupBlock 调用

Xamarin.iOS/Mac 运行时需要知道委托的块签名创建的托管的 OBJECTIVE-C 的块时。 这可能是操作非常耗费资源。 此优化将在生成时，计算块签名和修改 IL 调用`SetupBlock`改为将作为参数的签名的方法。 执行此操作无需计算在运行时的签名。

基准显示这加快块调用了 10 至 15 倍。

它将转换以下[代码](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

到：

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

此优化需要链接器来启用，且仅应用到方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

使用静态的注册机构 （在静态注册机构启用默认情况下，对于设备生成，并在默认情况下，对于版本启用静态注册机构的 Xamarin.Mac 生成的 Xamarin.iOS) 时，它会启用默认情况下。

可以通过传递重写默认行为`--optimize=[+|-]blockliteral-setupblock`到 mtouch/mmp。

## <a name="optimize-support-for-protocols"></a>优化对协议的支持

Xamarin.iOS/Mac 运行时需要有关如何将托管类型实现 OBJECTIVE-C 的协议信息。 此信息存储在接口中 （并且这些接口上的特性），这不是非常高效的格式，也不是链接器友好。

一个示例就是这些接口存储有关中的所有协议成员信息`[ProtocolMember]`属性，它除了别的包含对这些成员的参数类型的引用。 这意味着，只需实现此类接口将使链接器保留在该界面中使用的所有类型，即使对于可选成员应用程序永远不会调用或实现。

此优化将使在高效使用既简单又快速在运行时查找内存少的格式中存储任何所需的信息的静态注册机构。

它还将讲解链接器，它不一定需要保留这些接口，或任何相关的属性。

此优化要求使用链接器和静态注册机构启用。

在 Xamarin.iOS 此优化时启用链接器和静态注册机构默认启用。

上 Xamarin.Mac 此优化是永远不会默认启用的因为 Xamarin.Mac 支持动态，加载程序集和这些程序集可能不没有在生成时已知 （并因此不优化）。

可以通过传递重写默认行为`--optimize=-register-protocols`到 mtouch/mmp。

## <a name="remove-the-dynamic-registrar"></a>删除动态注册机构

Xamarin.iOS 和 Xamarin.Mac 运行时包括对支持[注册托管的类型](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/)与 Objective C 运行时。 也可以在生成时或在运行时 （或部分生成时间和在运行时的其余部分），但如果它完全执行此操作在生成时，这也意味着可以删除在运行时执行它的支持代码。 这会导致显著缩短应用大小，尤其是较小的应用程序，例如扩展或 watchOS 应用。

此优化需要静态注册机构和链接器启用。

链接器将尝试确定它是否可以安全地删除动态注册机构，并且如果使尝试将其删除。

由于 Xamarin.Mac 动态支持在运行时 （这不在生成时已知） 的加载程序集，则无法确定在生成时，是否这是安全的优化。 这意味着永远不会将默认情况下，对于 Xamarin.Mac 应用程序启用了此优化。

可以通过传递重写默认行为`--optimize=[+|-]remove-dynamic-registrar`到 mtouch/mmp。

如果重写默认值来删除动态注册机构，链接器将发出警告，如果检测到不安全 （但动态注册机构仍将被删除）。

## <a name="inline-runtimedynamicregistrationsupported"></a>内联 Runtime.DynamicRegistrationSupported

内联值的`Runtime.DynamicRegistrationSupported`在生成时确定。

如果删除动态注册机构 (请参阅[删除动态注册机构](#remove-the-dynamic-registrar)优化)，这是一个常量`false`值，否则它是一个常数`true`值。

此优化将更改下列类型的代码：

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

为以下删除动态注册机构时：

```csharp
throw new Exception ("dynamic registration is not supported");
```

为时不会动态注册机构中删除以下：

```csharp
Console.WriteLine ("do something");
```

此优化需要链接器来启用，且仅应用到方法`[BindingImpl (BindingImplOptions.Optimizable)]`属性。

它始终会启用默认情况下，（如果已启用链接器）。

可以通过传递重写默认行为`--optimize=[+|-]inline-dynamic-registration-supported`到 mtouch/mmp。

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>预计算的方法来创建的 OBJECTIVE-C 的块的托管的委托

当 Objective C 调用选择器，采用一个块作为一个参数，然后托管的代码已经重写该方法中，Xamarin.iOS / Xamarin.Mac 运行时需要创建对该块的委托。

由绑定生成器生成的绑定代码将包括`[BlockProxy]`属性，指定具有的类型`Create`可以执行此操作的方法。

给定以下 Objective C 代码：

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

并添加以下绑定代码：

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

将生成生成器：

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }
    
    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...
        
    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;
        
        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }
    
    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }
        
        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

当 Objective C 调用`[ObjCBlockTester callClassCallback]`，Xamarin.iOS / Xamarin.Mac 运行时将查看`[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]`参数上的属性。 它将然后查找`Create`该类型的方法，调用该方法来创建委托。

此优化将查找`Create`在生成时和静态注册机构的方法将生成查找在运行时使用的属性和反射 （这是快得多，还允许链接器而不使用其元数据标记的方法的代码若要删除相应的运行时代码，使应用程序更小）。

找不到 mmp/mtouch 是否`Create`方法，然后将显示 MT4174/MM4174 警告，并查找将在运行时执行。
最可能的原因手动编写且没有所需的绑定代码`[BlockProxy]`属性。

此优化需要静态的注册机构以启用。

它始终会启用默认情况下，（只要启用了静态注册机构）。

可以通过传递重写默认行为`--optimize=[+|-]static-delegate-to-block-lookup`到 mtouch/mmp。
