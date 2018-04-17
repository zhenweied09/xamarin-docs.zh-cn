---
title: 类型注册机构
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 9d4d2e5f3e1c2dc1233b466c00dd60340552fed0
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="type-registrar"></a>类型注册机构

本文档介绍 Xamarin.iOS 由使用的类型注册系统。

## <a name="registration-of-managed-classes-and-methods"></a>托管的类和方法的注册

在启动期间，将注册 Xamarin.iOS:

  - 类与[[注册]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) OBJECTIVE-C 的类的特性。
  - 类与[[类别]](https://developer.xamarin.com/api/type/CRuntime.CategoryAttribute)作为 OBJECTIVE-C 的类别的属性。
  - 与接口[[协议]](https://developer.xamarin.com/api/type/Foundation.ProtocolAttribute/)视为 Objective C 的协议的属性。

和中与每个情况下成员[[导出]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)属性导出到目标。 这允许托管的类为创建和管理从 Objective C 调用方法的 C# world 和一个 Objective C 之间链接方法和属性的方式。

一个非常简单的示例是每个应用程序具有的 AppDelegate 类。 您将重新调用托管的 Main 方法具有与此类似的行：

    UIApplication.Main (args, null, "AppDelegate");

这将告知 Objective C 运行时来创建作为应用程序的委托类的调用"AppDelegate"的类型。  若要了解如何创建以 C# 编写"AppDelegate"类的实例 Objective C 运行时，必须注册此类。

Xamarin.iOS 运行时将会负责处理注册为你在内部，可以在运行时 （动态注册） 完全完成此注册或可以在编译时 （静态注册）。  动态方法涉及在启动时使用反射来查找所有类和方法注册并传递到 Objective C 运行时。  静态方法检查应用程序在编译时使用的程序集。  它确定的类和方法来注册 Objective C 并生成一个代码图它将嵌入到你的二进制文件。  然后，在启动时，我们注册地图 Objective C 运行时。

### <a name="categories"></a>类别

启动 Xamarin.iOS 8.10 后它将是可以创建使用 C# 语法的 OBJECTIVE-C 的类别。

这是使用 [类别] 特性，指定要扩展作为该属性的自变量的类型。
例如，下面的示例将扩展 NSString:

    [Category (typeof (NSString))]

每个类别方法正在使用的正常机制将方法导出到 Objective C 使用 [导出] 特性：

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

所有托管的扩展方法必须是静态的但可以创建在 C# 中的扩展方法使用标准语法的 OBJECTIVE-C 的实例方法：

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

扩展方法的第一个参数将在其调用的方法的实例。

完整的示例：

    [Category (typeof (NSString))]
    public static class MyStringCategory
    {
        [Export ("toUpper")]
        static string ToUpper (this NSString self)
        {
            return self.ToString ().ToUpper ();
        }
    }

此示例将本机 toUpper 实例方法添加到 NSString 类，该类可从目标 C.调用

    [Category (typeof (UIViewController))]
    public static class MyViewControllerCategory
    {
        [Export ("shouldAutoRotate")]
        static bool GlobalRotate ()
        {
            return true;
        }
    }

### <a name="protocols"></a>协议

从 Xamarin.iOS 8.10 [协议] 属性具有接口将被导出到 Objective C 作为协议。

示例:

    [Protocol ("MyProtocol")]
    interface IMyProtocol
    {
        [Export ("method")]
        void Method ();
    }

    class MyClass : IMyProtocol
    {
        void Method ()
        {
        }
    }

这将导出到 OBJECTIVE-C 的协议 (MyProtocol)，以及实现协议的类 (MyClass)。

 **动态注册**

用作为模拟器生成，因此加快了生成/debug 周期。  这是生成的类映射和编译到应用程序启动程序的此映射表，每次启动应用程序，并改为每次使用常规用途的启动器消除一些步骤的结果。  台式计算机具有足够的能力来执行运行时扫描类的速度快，因此性能不成问题。

 **静态注册**

旨在设备生成的移动设备慢于台式计算机，并且执行运行时扫描速度较慢。  由于设备生成将始终需要创建新的二进制文件，生成/debug 周期不受注册映射的创建。

## <a name="new-registration-system"></a>新的注册系统

从稳定 6.2.6 版本和我们添加了新的静态注册机构的 beta 6.3.4 版本。 在 7.2.1 版本我们进行了新的注册机构默认值。

此新的注册系统提供了以下新功能：

- 编译时检测到的程序员错误：
    - 要注册具有相同名称的两个类。
    - 多个方法导出响应相同的选择器



- 可以删除未使用的本机代码
    - 新的注册系统将添加到使用静态库中的代码允许本机链接器删除其中的未使用的本机代码，从生成的二进制文件的强引用。
      在 Xamarin 的示例绑定上大多数应用程序会成为至少 300 k 较小。

- 支持的 NSObject 泛型子类。 请参阅[NSObject 泛型](~/ios/internals/api-design/nsobject-generics.md)有关详细信息。 此外在新的注册系统将捕获不支持泛型构造这以前可能会导致在运行时随机行为。

这些是由新 registar 捕获的错误的一些示例：

同一类中不止一次导出相同的选择器。

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo:")]
    void Foo (NSString str);
    [Export ("foo:")]
    void Foo (string str)
}
```

导出多个具有相同的 OBJECTIVE-C 的名称的托管的类。

```csharp
[Register ("Class")]
class MyClass : NSObject {}

[Register ("Class")]
class YourClass : NSObject {}
```

导出泛型方法。

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo")]
    void Foo<T> () {}
}
```



需要有关新的注册机构记住以下事项：
- 某些第三方库需要进行更新以使用新的注册系统，请参阅明[需要修改以下](#required_modifications)有关详细信息。
- 短期缺点还是如果使用的帐户 framework，必须使用 Clang （这是因为 Clang 可以仅编译 Apple 的 accounts.h 标头）。 添加<code>--compiler:clang</code>到其他 mtouch 自变量，以使用 Clang，如果你正在使用 Xcode 4.6 或更早版本 （Xamarin.iOS 将自动选择 Clang Xcode 5.0 或更高版本。）

    <li>如果 Xcode 4.6 （或更早版本） 是使用，则 GCC / G + + 如果必须选择导出的类型名称包含非 ascii 字符 （这是因为使用 Xcode 4.6 附带的 Clang 的版本不支持在 Objective C 代码中的标识符内的非 ascii 字符）。 添加<code>--compiler:gcc</code>到要使用 GCC 的其他 mtouch 自变量。


## <a name="selecting-a-registrar"></a>选择注册机构

可以通过将以下选项之一添加到项目的 iOS 生成选项中的其他 mtouch 参数来选择其他注册机构：

-  `--registrar:static` ： 默认为设备版本
-  `--registrar:dynamic` ： 默认为模拟器版本
-  `--registrar:legacystatic` ： 默认为设备版本之前 Xamarin.iOS 7.2.1
-  `--registrar:legacydynamic` ： 默认为模拟器版本之前 Xamarin.iOS 7.2.1


## <a name="shortcomings-in-the-old-registration-system"></a>旧的注册系统中的不足之处

旧的注册系统带来以下缺点：

-  时未对 OBJECTIVE-C 的类和第三方本机库，这意味着我们无法获得本机链接器来删除第三方 （因为所有内容都将被删除） 不实际使用的本机代码中的方法 （本机） 的静态引用。 这是原因"-force_load libNative.a"每个第三方绑定所要做 (或等效 ForceLoad = true [LinkWith] 属性中的)。
-  你无法导出两个具有相同的 OBJECTIVE-C 的名称，不显示任何警告的托管的类型。 少见的方案是结尾 （在不同的命名空间） 的两个 AppDelegate 类。 在运行时它将是完全随机的哪一个已选取 （实际上，它运行的应用程序即使未重新生成的从而使非常让令人费解且令人沮丧调试体验之间有所不同，例如）。
-  无法导出具有相同的 OBJECTIVE-C 的签名的两个方法。 再次将从 Objective C 调用哪一个已随机 （但主要是因为实际遇到此错误的唯一方法是重写 unlucky 托管的方法，此问题不太常见与之前）。
-  已导出的方法集是动态和静态生成之间略有不同。
-  它不会无法正常工作时导出泛型类 （在运行时执行的确切的通用实现是随机的这有效地促成不确定的行为）。


 <a name="required_modifications" />


## <a name="new-registrar-required-changes-to-bindings"></a>绑定到新的注册机构： 所需的更改


可能需要更新，以使用新 registar 现有 OBJECTIVE-C 的绑定。

下面是需要完成的更改的列表。

### <a name="protocols-must-have-the-protocol-attribute"></a>协议都必须具有 [协议] 属性

协议实现现在必须应用于它们的 [协议] 属性。  如果不这样做，你将与此类似本机的链接器错误：

```csharp
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>选择器必须具有有效数量的参数

所有选择器必须正确指示参数的数目。  以前，这些错误被忽略，并且可能会导致运行时问题。

简单地说，冒号数目必须与匹配参数的数目。

例如：

-  没有参数: foo
-  一个参数: foo:
-  两个参数: foo:parameterName2:


以下是不正确的使用：

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>在导出中使用 IsVariadic 参数

可变参数函数必须允许，否则在自己导出属性中 （这是因为选择器是错误的数量的自变量，即点 2。 从上面违反了）：

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>必须链接到现有的符号

不可能将绑定本机库中不存在的类。

如果您尝试绑定非现有类，将获取本机链接器错误。

这通常发生在绑定已经存在了一段时间，并在该时间段，以便为特定的本机类已删除或重命名，而不更新绑定已修改的本机代码。

