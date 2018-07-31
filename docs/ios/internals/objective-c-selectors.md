---
title: 在 Xamarin.iOS OBJECTIVE-C 选择器
description: 本文档介绍如何与从 C# 的 OBJECTIVE-C 选择器进行交互。 它介绍了如何调用选择器和执行此操作时必须考虑在内的技术考虑因素。
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/12/2017
ms.openlocfilehash: 3083770fd2874eca317585b6bf949f3efe56f879
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351725"
---
# <a name="objective-c-selectors-in-xamarinios"></a>在 Xamarin.iOS OBJECTIVE-C 选择器

Objective C 语言基于*选择器*。 选择器是一条消息，可以发送到一个对象或*类*。 [Xamarin.iOS](~/ios/internals/api-design/index.md)映射实例为实例方法的选择器和类选择器对静态方法。

与普通 C 函数 （和类似 c + + 成员函数），您不能直接调用选择器使用[P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/)。
(*放在一边*： 理论上可以使用 P/Invoke，对于非虚拟的 c + + 成员函数，但需要担心每个编译器名称重整，这是更好地忽略对象的。)而是选择器发送到 OBJECTIVE-C 类或实例使用[`objc_msgSend`函数](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend)。

您可能会发现[Objective C 消息上的本帮助指南](http://developer.apple.com/iphone/library/documentation/cocoa/conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html)很有用。

<a name="Example" />

## <a name="example"></a>示例

假设你想要调用[-[NSString sizeWithFont:forWidth:lineBreakMode:]](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:)选择器。
（从 Apple 的文档） 声明为：

```csharp
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

-  返回类型是*CGSize*统一 api。
-  *字体*参数是[UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (和从 （间接） 派生的类型[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) )，并因此映射到[System.IntPtr](xref:System.IntPtr) 。
-  *宽度*参数， *CGFloat* ，映射到*nfloat*。
-  *LineBreakMode*参数， *UILineBreakMode* ，已在作为 Xamarin.iOS 绑定[UILineBreakMode 枚举](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/)。


将其全部放和我们想要匹配的 objc_msgSend 声明：

```csharp
CGSize objc_msgSend(IntPtr target, IntPtr selector,
    IntPtr font, nfloat width, UILineBreakMode mode);
```

我们将需要将其声明：

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

一旦声明后，我们可以调用它，一旦我们有适当的参数：

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode);
```

返回的值曾经是大小小于 8 个字节的结构 (如较早`SizeF`切换到统一的 Api 之前，使用) 方式但在设备上崩溃模拟器上运行上面的代码。 因此，若要调用一个选择器，返回一个值小于 8 位的大小，因此我们*还*需要声明`objc_msgSend_stret()`函数：

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

然后将变为调用：

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode);
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode);
```


<a name="Invoking_a_Selector" />

## <a name="invoking-a-selector"></a>调用一个选择器

调用一个选择器包含三个步骤：

1.  获取选择器目标。
1.  获取选择器名称。
1.  使用适当的参数调用 objc_msgSend()。


<a name="Selector_Targets" />

### <a name="selector-targets"></a>选择器目标

选择器目标是 OBJECTIVE-C 类或对象实例。 如果目标是一个实例，来自绑定的 Xamarin.iOS 类型，使用[ObjCRuntime.INativeObject.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/)属性。

如果目标是一个类，使用[ObjCRuntime.Class](https://developer.xamarin.com/api/type/ObjCRuntime.Class/)若要获取对类实例的引用，然后使用[Class.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/)属性。


<a name="Selector_Names" />

### <a name="selector-names"></a>选择器名称

Apple 的文档中列出了选择器名称。 例如， [UIKit NSString 扩展方法](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html)包括[sizeWithFont:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:)并[sizeWithFont:forWidth:lineBreakMode:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:)。 嵌入和尾随冒号都很重要，并且是选择器名称的一部分。

选择器名称后，可以创建[ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/)为它的实例。


<a name="Calling_objc_msgSend()" />

### <a name="calling-objcmsgsend"></a>调用 objc_msgSend()

 `objc_msgSend()` 用于将一条消息 （选择器） 发送到一个对象。 此系列函数采用至少两个必需的参数： 选择器目标 （实例或处理的类） 的选择器本身，，然后所需的特定选择器的任何参数。 实例，并选择器参数必须为`System.IntPtr`，并且所有其余参数必须匹配的类型选择器要求，例如`nint`有关`int`，或`System.IntPtr`所有`NSObject`-派生类型。 使用[NSObject.Handle](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/)属性来获取`IntPtr`Objective C 类型实例。

遗憾的是，有是多个`objc_msgSend()`函数。

使用[ `objc_msgSend_stret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret)的选择器可返回一个结构。
为了使"有趣"，ARM 上此内容包括所有返回类型包含*不*枚举或任何 C 内置类型 (char、 short、 int、 long、 float、 double)。 在 x86 （模拟器），这需要使用的所有结构大于 8 个字节的大小。 (CGSize-上述-示例中使用正是 8 个字节，并因此不会使用`objc_msgSend_stret()`在模拟器中。)

使用[ `objc_msgSend_fpret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_fpret)选择器返回的浮点值点仅在 x86 上。 此函数不需要在 ARM; 上使用请改用`objc_msgSend()`。

主[objc_msgSend()](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend)函数用于所有其他选择器。

一旦您决定这`objc_msgSend()`的功能，需要调用 （和可能会超过一个，例如，对于模拟器和设备），可以使用普通[ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute)方法声明为更高版本调用此函数。

一组预制`objc_msgSend()`中找不到声明[ `ObjCRuntime.Messaging` ](https://developer.xamarin.com/api/type/ObjCRuntime.Messaging/)。


<a name="ugly" />

## <a name="the-ugly"></a>费解之处

Objective C 有三种类型的`objc_msgSend`方法： 一个用于常规调用，返回浮点值 (仅限 x86) 的调用，调用返回结构值。 后者包含后缀`_stret`在`ObjCRuntime.Messaging`。

如果调用将返回某些结构 （规则是下面所述） 的方法，必须调用的方法的返回值作为输出值，此类的第一个参数：

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

事情此处会难以理解，因为必须使用时的规则_stret_不同在 X86 和 ARM 上，如果您希望您的绑定适用于模拟器和设备，需要将添加如下所示的代码：

```csharp
if (Runtime.Arch == Arch.DEVICE){
    PointF ret;

    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);

    return ret;
} else
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
```

### <a name="using-the-objcmsgsendstret-method"></a>使用 objc\_msgSend\_stret 方法

若要使用的规则[ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret)如下所示的这**ARM**:

-  任何值不是枚举的基类型的任何类型 (int，byte、 short、 long、 双精度型、 浮点型)。


若要使用的规则[ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret)如下所示的这**X86**:

-  任何值不是枚举，或任何基类型枚举的类型 (int，byte、 short、 long、 双精度型、 浮点型) 和其本机大小大于 8 个字节。


### <a name="creating-your-own-signatures"></a>创建你自己的签名。

以下[要点](https://gist.github.com/rolfbjarne/981b778a99425a6e630c)可用于创建你自己的签名，如果所需。



## <a name="related-links"></a>相关链接

- [选择器示例](https://developer.xamarin.com/samples/mac-ios/Objective-C/Selectors/)
