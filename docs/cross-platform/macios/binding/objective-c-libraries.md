---
title: 绑定 Objective C 库
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: acffa2745737231d030af731d77ced8f57bfbaeb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="binding-objective-c-libraries"></a>绑定 Objective C 库

当使用 Xamarin.iOS 或 Xamarin.Mac，可能会出现你想要使用第三方 Objective C 库的情况。 在这些情况下，你可以使用 Xamarin 绑定项目创建 C# 绑定到本机的 OBJECTIVE-C 的库。 项目使用相同的工具，我们使用向 C# 使 iOS 和 Mac Api。

本文档介绍如何将绑定 OBJECTIVE-C 的 Api，如果你正在绑定仅 C Api，则应为此，使用标准.NET 机制[P/Invoke framework](http://www.mono-project.com/docs/advanced/pinvoke/)。
如何以静态方式链接 C 库的详细信息位于[链接本机库](~/ios/platform/native-interop.md)页。

请参阅我们助理[绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)。
此外，如果你想要了解有关实质上会发生什么情况的详细信息，请检查我们[绑定概述](~/cross-platform/macios/binding/overview.md)页。

可以针对 iOS 和 Mac 库生成绑定。
本页介绍如何在 iOS 绑定，但是 Mac 绑定非常相似。

**适用于 iOS 的示例代码**

你可以使用[iOS 绑定示例](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample)项目进行实验，以使用绑定。

<a name="Getting_Started" />

## <a name="getting-started"></a>入门

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

创建绑定的最简单方法是创建 Xamarin.iOS 绑定项目。
你可以从 Visual Studio for Mac 通过选择项目类型， **iOS > 库 > 绑定库**:

[![](objective-c-libraries-images/00-sml.png "通过执行此操作从 Visual Studio for Mac 选择的项目类型，iOS 库绑定库")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

创建绑定的最简单方法是创建 Xamarin.iOS 绑定项目。
你可以从 Visual Studio 在 Windows 上通过选择项目类型， **Visual C# > iOS > 绑定库 (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "iOS 绑定库 iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> 注意： 有关项目的绑定**Xamarin.Mac** mac。 仅支持在 Visual Studio 中

-----

生成的项目包含你可以编辑小模板，它包含两个文件：`ApiDefinition.cs`和`StructsAndEnums.cs`。

`ApiDefinition.cs`是要在其中定义 API 约定中，这是介绍如何基础 Objective C API 投影到 C# 的文件。 语法和此文件的内容是本文档的讨论主要主题，它的内容仅限于接口 C# 和 C# 委托声明。 `StructsAndEnums.cs`文件是你将接口和委托由输入所需的任何定义的文件。 这包括枚举值和你的代码可能使用的结构。

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>绑定 API

若要执行全面的绑定，你将想要了解 Objective C API 定义和熟悉的.NET Framework 设计准则。

若要将绑定你通常将开始使用 API 定义文件的库。 API 定义文件是只是 C# 源文件，其中包含绑定的 C# 接口已通过几种帮助驱动器的属性进行批注。  此文件是什么定义了 C# 和 Objective C 之间的协定的含义。

例如，这是一个库的普通 api 文件：

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

上面的示例定义一个名为类`Cocos2D.Camera`派生自`NSObject`基类型 (此类型是来自`Foundation.NSObject`) 和其定义的静态属性 (`ZEye`)，采用任何自变量和方法的两个方法采用三个自变量。

中介绍了 API 文件和可以使用的特性的格式的深度讨论[API 定义文件](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)下面一节。

若要生成完整的绑定，将通常处理与四个组件：

-  API 定义文件 (`ApiDefinition.cs`模板中)。
-  可选： 任何枚举类型、 所需的 API 定义文件的结构 (`StructsAndEnums.cs`模板中)。
-  可选： 额外源可能会展开所生成的绑定，或提供多个 C# 友好 API （任何 C# 文件添加到项目）。
-  你正在绑定本机库。

此图显示在文件之间的关系：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "此图显示在文件之间的关系")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

API 定义文件将仅包含命名空间和接口定义 （使用接口可以包含任何成员），并且不包含类、 枚举、 委托或结构。 API 定义文件是只是将用于生成 API 的协定。

任何额外的代码，你需要喜欢枚举或支持的类应托管在单独的文件，在"CameraMode"上面的示例是一个枚举值，CS 文件中不存在，应在单独的文件，例如承载`StructsAndEnums.cs`:

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

`APIDefinition.cs`文件结合`StructsAndEnum`类，用于生成核心绑定的库。 您可以使用作为生成的库的是，但通常情况下，你将想要优化生成的库中添加为了用户方便一些 C# 功能。 一些示例包括实现`ToString()`方法，提供 C# 索引器，将隐式转换添加到和从某些本机类型或提供强类型版本的一些方法。 在额外 C# 文件中存储这些改进。 仅将 C# 文件添加到你的项目和它们将包含在此生成过程。

下面的示例演示如何将实现中的代码你`Extra.cs`文件。 请注意，你将使用分部类如这些增加的分部类中从的组合生成`ApiDefinition.cs`和`StructsAndEnums.cs`核心绑定：

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

生成库将生成本机绑定。

若要完成此绑定，应将本机库添加到项目中。  你可以执行此操作通过右键单击项目并选择将本机库添加到项目中，通过从拖放本机库 Finder 到的项目中的解决方案资源管理器中，或**添加** > **添加文件**选择本机库。
按照约定的本机库以单词"lib"开头，是以扩展名".a"结尾。 适用于 Mac 的 Visual Studio 执行此操作时，将添加两个文件：.a 文件和一个自动填充的 C# 文件，其中包含有关本机库包含的信息：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "按照约定的本机库以 word lib 开头和结尾扩展.a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

内容`libMagicChord.linkwith.cs`文件包含有关如何使用此库的信息，并指示你 IDE 以打包到生成的 DLL 文件的此二进制文件：

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

有关如何使用的详细信息的完整[ `[LinkWith]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)属性均记录在[绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)。

现在生成项目时你最终将得到`MagicChords.dll`包含的绑定和本机库的文件。 你可以分发此项目或生成的 DLL 到其自己的其他开发人员使用。

有时你可能会发现你需要几个枚举值、 委托定义或其他类型。 不要将放那些在 API 定义文件中，因为这是只是一个协定

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>API 定义文件

API 定义文件包含数量的接口。 API 定义中的接口将打开到类声明，并且它们必须使用修饰[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)特性来指定类的基类。

你可能想知道为何我们未使用类而不是接口的协定定义。 因为它使我们能够编写方法的协定，而无需提供 API 定义文件中，在方法体或无需为其提供主体必须引发异常或返回有意义的值，我们选取了接口。

但由于我们将用作的主干使用接口来生成我们不得不求助于修饰的属性与要驱动器绑定的协定的各个部分的类。

<a name="Binding_Methods" />

### <a name="binding-methods"></a>绑定方法

你可以执行的最简单绑定是将方法绑定。 只需声明的 C# 命名约定接口中的方法和修饰的方法与[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)属性。 [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)特性是链接你 C# 名称与 Xamarin.iOS 运行时中的 OBJECTIVE-C 的名称。 参数[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)特性是 OBJECTIVE-C 的选择器的名称。 示例如下：

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

上面的示例演示如何将绑定的实例方法。 若要绑定的静态方法，必须使用[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)属性，如下：

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

这是接口的必需的因为协定一部分，以及接口已具有静态 vs 实例声明没有概念，因此需要再次需采用属性。 如果你想要隐藏从绑定的特定方法，你可以按照声明具有的方法[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)属性。

`btouch-native`命令将引入引用参数不为 null 的检查。 如果你想要允许某个特定参数的 null 值，使用[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)参数，如下属性：

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

与导出为引用类型、 时[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)关键字还可以指定分配语义。 这是必需的以确保不泄露任何数据。

<a name="Binding_Properties" />

### <a name="binding-properties"></a>绑定属性

只需像方法一样，使用绑定 Objective C 属性[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)属性和直接映射到 C# 属性。 只需像方法一样，可以使用修饰属性[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)和[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)属性。

当你使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)实际下介绍 btouch 本机属性上的属性将绑定的两种方法： getter 和 setter。 提供要导出的名称**basename**和 setter 计算通过预先计算单词"集"，打开的第一个字母**basename**到大写并使需要的选择器自变量。 这意味着，`[Export ("label")]`应用于属性实际绑定"标签"和"setLabel:"OBJECTIVE-C 的方法。

有时 OBJECTIVE-C 的属性不遵循前面所述的模式和名称，将手动覆盖。 在这些情况下，你可以控制绑定使用生成的方式[ `[Bind]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute)属性 getter 或 setter，例如：

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

稍后将绑定"isMenuVisible"和"setMenuVisible:"。 （可选） 可以使用以下语法绑定属性：

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

其中的 getter 和 setter 显式定义为在`name`和`setName`上面的绑定。

除了对使用的静态属性的支持[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)，你可以按照与线程静态属性声明[ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute)，例如：

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

就像方法允许被标记为某些参数[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)，你可以将应用[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)到一个属性以指示该 null 是有效的值对于属性，例如：

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)还可以直接在 setter 上指定参数：

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>自定义控件绑定的需要注意的问题

设置自定义控件的绑定时，应考虑以下几点：

1. **绑定属性必须是静态**-时定义的属性，绑定[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)必须使用属性。
 2. **属性名称必须完全匹配**-用于将属性绑定的名称必须与自定义控件中的属性的名称完全匹配。
3. **属性类型必须完全匹配**-用于将属性绑定的变量类型必须与自定义控件中的属性的类型完全匹配。
4. **断点和 getter/setter** -断点置于 getter 或 setter 方法的属性将永远不会命中。
5. **观察回调**-你将需要使用观察回调接收通知的自定义控件的属性值中的更改。

不观察任何上述列出需要注意的问题可以导致在运行时以静默方式失败的绑定。

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Objective C 可变模式和属性

Objective C 框架使用惯用语法，一些类是与可变子类不可变。 例如`NSString`是不可变的版本中，虽然`NSMutableString`是允许转变的子类。

在这些类是经常可以看到的不可变的基类，包含具有 getter，但没有 setter 的属性。 以及要引入 setter 的可变版本。 由于不使用 C# 真正做到这一点，我们必须将此习惯用法映射到处理 C# 惯用语法。

这将映射到 C# 的方法是将 getter 和 setter 添加基本的类，但标记与 setter [ `[NotImplemented]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)属性。

然后，可变子类化，请在你使用[ `[Override]` ](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute)以确保该属性实际重写父行为的属性上的属性。

示例:

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>绑定的构造函数

`btouch-native`工具将自动生成酣睡构造函数在类中，为给定类`Foo`，它会生成：

-  `Foo ()`： 默认构造函数 （映射到 OBJECTIVE-C 的"init"构造函数）
-  `Foo (NSCoder)`: NIB 文件的反序列化期间使用的构造函数 (映射到目标-C"initWithCoder:"构造函数)。
-  `Foo (IntPtr handle)`： 构造函数以基于句柄的创建，这由运行时时调用运行时需要公开托管的对象从非托管对象。
-  `Foo (NSEmptyFlag)`： 这使用由派生类以防止重复初始化。

对于你定义的构造函数，它们需要声明使用接口定义中的以下签名： 它们必须返回`IntPtr`值和方法的名称应是构造函数。 例如，绑定`initWithFrame:`构造函数，这是你将：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>绑定协议

在 API 设计文档中，部分中所述[讨论模型和协议](~/ios/internals/api-design/index.md#Models)，Xamarin.iOS 将 OBJECTIVE-C 的协议映射到已被标记为的类[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)属性。 这通常用来实现 Objective C 委托类。

正则的绑定的类和委托类之间最大的区别是，委托类可能具有一个或多个可选方法。

例如，请考虑`UIKit`类`UIAccelerometerDelegate`，这是如何在 Xamarin.iOS 绑定：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

由于这是上的定义的可选方法`UIAccelerometerDelegate`无需进行任何其他执行操作。 但如果在协议上没有所需的方法，则应添加[ `[Abstract]` ](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)属性设为该方法。 这将强制实现的用户才能实际提供的方法的正文。

一般情况下，协议将在响应消息的类中使用。 这是通常通过 Objective C 中将响应协议中的方法的对象的实例分配给"委托"属性。

在 Xamarin.iOS 的约定是在任何支持这两个的 OBJECTIVE-C 的松散耦合样式实例`NSObject`可以分配给委托，就还使它的强类型版本。 为此，我们通常提供`Delegate`强类型的属性和`WeakDelegate`，松散类型化。 我们通常将绑定的松散类型化的版本与[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)，并且我们使用了[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)属性以提供强类型版本。

下面的示例演示如何我们绑定`UIAccelerometer`类：

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**MonoTouch 7.0 中的新增功能**

已合并开头 MonoTouch 7.0 新增和改进的协议绑定功能。  此新的支持，使其采用给定的类中的一个或多个协议使用 OBJECTIVE-C 的惯例简单得多。

每个协议定义`MyProtocol`Objective C 中现在有了`IMyProtocol`列出该协议，从所有所需的方法的接口，以及提供所有可选的方法的扩展类。  在 Xamarin Studio 编辑器允许开发人员来实现协议方法而无需使用的以前的抽象模型类单独子类的新支持与上述组合。

包含任意定义[ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute)属性将实际生成大大地提高你使用这些协议的方法的三个支持类：

```csharp
    // Full method implementation, contains all methods
    class MyProtocol : IMyProtocol {
        public void Say (string msg);
        public void Listen (string msg);
    }

    // Interface that contains only the required methods
    interface IMyProtocol: INativeObject, IDisposable {
        [Export ("say:")]
        void Say (string msg);
    }

    // Extension methods
    static class IMyProtocol_Extensions {
        public static void Optional (this IMyProtocol this, string msg);
        }
    }
```

**类实现**提供完整的抽象类，你可以重写的各个方法并获取完整的类型安全。  但由于 C# 中不支持多重继承，存在很其中可能的方案需要具有不同的基类，但你仍想要实现一个接口，即位置

生成**接口定义**传入。  它是具有协议的所有所需的方法的接口。  这样，开发人员想要实现您的协议，只是实现该接口。  运行时将自动注册为采用协议的类型。

请注意接口仅列出所需的方法，并且确实公开了可选的方法。  这意味着采用协议的类将获得完整的类型检查所需的方法，但将需要采用弱键入 (手动使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)特性和匹配的签名) 为可选协议方法。

要更加方便地使用使用协议的 API，请绑定工具还将生成一个扩展方法类，它公开所有的可选方法。  这意味着，只要你使用的 API，则你将能够将视作具有所有方法的协议。

如果你想要在你的 API 中使用的协议定义，你将需要在你的 API 定义中编写主干空接口。  如果你想要使用 MyProtocol API 中，你将需要执行此操作：

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

上述必须的因为在绑定时间`IMyProtocol`将不存在，就是为什么你需要提供空接口。

#### <a name="adopting-protocol-generated-interfaces"></a>采用协议生成接口

任何时候要实现一个接口生成的协议，如下：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

为接口方法的实现将自动获取导出与正确的名称，因此等效于此：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

如果隐式或显式实现的接口，并不重要。

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>绑定类扩展

Objective C 中就可以使用新的方法，类似于 C# 的扩展方法中的设计理念扩展类。 当存在下列方法之一时，你可以使用[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)属性标记为 OBJECTIVE-C 的消息的接收方的方法。

例如，在 Xamarin.iOS 我们绑定定义的扩展方法`NSString`时`UIKit`作为方法导入`NSStringDrawingExtensions`，如下所示：

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>绑定 OBJECTIVE-C 的自变量列表

Objective C 支持可变参数自变量。 例如：

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

若要从 C# 调用此方法将想要创建的签名如下：

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

这会将声明为内部，该方法隐藏用户，从上面的 API，但将其公开到库。 然后，你可以编写类似下面的方法：

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>绑定字段

有时想要访问在库中声明的公共字段。

通常这些字段包含必须引用的字符串或整数值。 它们通常用于为字符串，其中表示特定通知和作为字典中的键。

若要绑定字段，将属性添加到接口定义文件中，和修饰的属性[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)属性。 此属性采用一个参数： 到查找符号的 C 名称。 例如：

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

如果你想要包装在不是派生自的静态类中的各个字段`NSObject`，你可以使用[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class)类，此类中的属性：

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

上述将生成`LonelyClass`这不是派生自`NSObject`并且将包含一种绑定到`NSSomeEventNotification` 
 `NSString`作为公开`NSString`。

[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)特性可以应用于以下数据类型：

-  `NSString` 引用 （仅限于只读属性）
-  `NSArray` 引用 （仅限于只读属性）
-  32 位整数 (`System.Int32`)
-  64 位整数 (`System.Int64`)
-  32 位浮点数 (`System.Single`)
-  64 位浮点数 (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

除了本机字段名称，还可以指定字段的位置，通过将库名称传递的库名称：

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

因此你需要使用如果要将静态链接，是连接要绑定到任何库`__Internal`名称：

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>绑定枚举

你可以添加`enum`直接在绑定中文件复制到便于在 API 定义的内部使用它们而无需使用不同的源文件 （即需要编译绑定和最后一个项目中）。

示例:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

还有可能创建你自己枚举替换`NSString`常量。 在这种情况下将生成器**自动**创建将枚举值和 NSString 常量转换为你的方法。

示例:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

在上面的示例中，可以决定修饰`void Perform (NSString mode);`与[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)属性。 这将**隐藏**绑定使用者基于常量的 API。

但是这会将限制子类的类型化不用去涉猎在 API 替代使用[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)属性。 这些生成的方法不是`virtual`，即你不会能够重写它们-或者不可能，是一个不错的选择。

替代方法是将标记与原始`NSString`-基于，与定义`[Protected]`。 这将允许子类化工作，在需要时，和 wrap'ed 版本仍将起作用，并调用重写方法。

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>绑定`NSValue`， `NSNumber`，和`NSString`为更好的类型

[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)属性允许绑定`NSNumber`，`NSValue`和`NSString`（枚举） 到更准确的 C# 类型。 该属性可以用于创建更好且更准确，通过本机 API 的.NET API。

（在返回值） 的方法、 参数和属性与可修饰[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)。 唯一限制是，您的成员**必须不**内[ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute)或[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)接口。

例如：

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

将输出：

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

我们将执行内部`bool?`  <->  `NSNumber`和`CGRect`  <->  `NSValue`转换。

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) 此外支持数组`NSNumber``NSValue`和`NSString`（枚举）。

例如：

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

将输出：

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` 是`NSString`备份枚举，我们将提取右侧`NSString`值和处理类型转换。

请参阅[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)文档，了解所支持的转换类型。

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>绑定通知

通知是发布到的消息`NSNotificationCenter.DefaultCenter`和作为一种机制，用来从一部分到另一个应用程序广播消息。 开发人员通常使用[NSNotificationCenter](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/)的[AddObserver](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/M/AddObserver/)方法。 当应用程序将消息发送到通知中心时，它通常包含存储在负载[NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/)字典。 此字典弱类型化，并获取退出的信息就是错误容易，因为用户通常需要读取的密钥都是可用字典类型和可以存储在字典中的值类型的文档中。 有时键存在用作一个的布尔值。

Xamarin.iOS 绑定生成器提供的开发人员将通知绑定的支持。 若要执行此操作，你将设置[ `[Notification]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)属性上一个属性，也能使用标记[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)属性 （它可以是公共或私有）。

此特性可用于无需不执行任何负载的通知的自变量也可以指定`System.Type`引用另一个接口在 API 定义中，通常与以"EventArgs"结尾的名称。 生成器会变为接口类子类`EventArgs`并且将包括所有列出的属性。 [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)属性应使用 EventArgs 类中，若要列出用于查找要提取值的 OBJECTIVE-C 的字典键的名称。

例如：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

上面的代码将生成嵌套的类`MyClass.Notifications`使用以下方法：

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

你的代码用户然后可以轻松地订阅通知发布到[NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/)通过使用如下代码：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

中的返回的值`ObserveDidStart`可用于轻松地停止接收通知，如下：

```csharp
token.Dispose ();
```

也可以调用[NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject/)并将令牌传递。 如果你的通知中包含参数，则应指定一个帮助程序`EventArgs`接口，如下：

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

上述将生成`MyScreenChangedEventArgs`类，该类具有`ScreenX`和`ScreenY`将提取中的数据的属性[NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/)字典使用键"ScreenXKey"和"ScreenYKey"分别并应用正确的转换。 `[ProbePresence]`属性为生成器用来探测如果此键设置`UserInfo`，而不是尝试提取的值。 这适用于的情况下，项的状态 （通常为布尔值） 的值。

此选项，可以编写如下代码：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>绑定类别

类别是用于扩展的类中可用方法和属性集的 OBJECTIVE-C 的机制。   在实践中，它们用于扩展基本类的功能 (例如`NSObject`) 采用特定框架中的链接时 (例如`UIKit`)，使其方法可用，但仅当新的 framework 链接中。   在某些其他情况下，它们用于组织类中的功能的功能。   它们是 C# 扩展方法的设计理念相似。这是什么类别将如下所示在目标 c:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上面的示例中如果在上找到库将会扩展的实例`UIView`方法`makeBackgroundRed`。

若要将这些绑定，可以使用[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)接口定义的属性。  使用时[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)特性的含义[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)属性将更改从用于指定要扩展，为要扩展的类型的基类。

下面演示如何`UIView`扩展都是绑定，转变为 C# 扩展方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

上述操作将创建`MyUIViewExtension`包含的类`MakeBackgroundRed`扩展方法。  对任何这意味着你现在可以调用"MakeBackgroundRed"`UIView`子类化，请向您提供相同的功能，你会收到一个目标 C 在某些其他情况下，不来扩展系统类，但能组织功能，仅仅是用于修饰使用类别。  如：

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

尽管可以使用[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)属性还对于声明此修饰样式，你可能也只是它们将全部添加到类定义。  这两种将实现同一目的：

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

它是只是短在这些情况下，合并类别：

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>绑定块

块是由 Apple 到目标 c。 将在功能上等效的 C# 匿名方法引入新结构 例如，`NSSet`类现在公开此方法：

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

上面的说明声明调用的方法`enumerateObjectsUsingBlock:`采用一个自变量名为`block`。 它对捕获当前环境"（this"指针，访问的本地变量和参数） 的支持，此块是类似于 C# 匿名方法。 中的上述方法`NSSet`时，将调用具有两个参数的块`NSObject`(`id obj`一部分) 和一个布尔值的指针 ( `BOOL *stop`) 部分。

若要将绑定与 btouch 这种类型的 API，你需要首先声明块类型签名，如 C# 委托，然后引用它从 API 入口点，如下：

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

并且你的代码现在从 C# 调用您的函数：

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

如果你愿意，你还可以使用 lambda:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>异步方法

绑定生成器可以将转换为异步友好的方法，某一类的方法 (返回的任务的方法&lt;T&gt;)。

你可以使用[ `[Async]` ](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute)属性，返回 void，并且其最后一个自变量为回调的方法。  如果你将此应用于方法时，绑定生成器将生成了后缀，该方法的版本`Async`。  如果回调不带任何参数，返回值将为`Task`，如果回调采用参数，则结果将为`Task<T>`。  如果回调采用多个参数，则应设置`ResultType`或`ResultTypeName`指定所需生成的类型，它将保存的所有属性的名称。

示例:

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

上面的代码将生成这两个 LoadFile 方法，以及：

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>提供强类型弱 NSDictionary 参数

于 OBJECTIVE-C 的 API 中的许多位置，传递的参数为弱类型`NSDictionary`与特定键和值，但这些 Api 是 （你可以传递无效的密钥，并获取不会出现警告; 你可以传递无效的值，并获取不会出现警告） 易出错和令人沮丧若要使用因为它们需要多个行程文档以查找可能的密钥名称和值。

解决方案是提供提供的 api 和在幕后的强类型版本映射的各种基础键和值的强类型版本。

因此，例如，如果 Objective C API 接受`NSDictionary`，并记录为采用密钥`XyzVolumeKey`此方法采用`NSNumber`其卷取值范围从 0.0 到 1.0 和`XyzCaptionKey`采用字符串形式，您将希望用户能够很好的 API如下所示：

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

`Volume`属性被定义为可以为 null float，因为 Objective C 中的约定不需要这些字典具有值，因此，其中可能未设置值的方案。

若要执行此操作，你需要做一些事情：

* 创建强类型类，该子类[DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/)并为每个属性提供的各种的 getter 和 setter。
* 声明为采用的方法的重载`NSDictionary`需要新的强类型版本。

你可以手动，或者创建强类型类，或使用生成器来为你执行的工作。  我们首先探索如何手动执行此操作以便了解发生了什么情况，然后自动方法。

你需要为此创建支持文件，不会进入您的协定 API。  这是您将不得不编写创建 XyzOptions 类：

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

然后，您应提供的包装器方法，它会显示高级 API，在低级别 API 之上。

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

如果你的 API 并不需要被覆盖，可以通过使用安全地隐藏 NSDictionary 基于 API [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)属性。

如你所见，我们使用[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)特性外围的新的 API 入口点，并且我们对其使用我们强类型显现`XyzOptions`类。  包装器方法还允许为 null，来传递。

现在，我们没有提到的一点是 where`XyzOptionsKeys`值来源。  通常将组 API 呈现在如静态类中的键`XyzOptionsKeys`，如下所示：

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

让我们看一下自动支持创建这些强类型的字典。  这可以避免很多样本，并可以直接在您的 API 协定，而不是使用外部文件中定义的字典。

若要创建强类型的字典，引入了一个 API 中的接口和修饰它与[StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)属性。  这将告知生成器，它应创建一个类，你将派生自的接口与同名`DictionaryContainer`将为其提供强类型化访问器。

[ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)属性采用一个参数，这是静态的类，它包含字典键的名称。  然后该接口的每个属性将成为强类型化访问器。  默认情况下，代码将使用属性的名称与在静态类中的"密钥"后缀创建访问器。

这意味着，外部文件，也无需手动创建 getter 和 setter 的所有属性，也无需手动查找密钥创建你的强类型化访问器不再需要自己。

这是整个绑定将如下所示：

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

在你需要在中引用的情况下你`XyzOption`成员不同的字段 (即不属性的名称与后缀`Key`)，可修饰的属性[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)特性名称与你想要使用。

<a name="Type_mappings" />

## <a name="type-mappings"></a>类型映射

本部分介绍如何将 OBJECTIVE-C 的类型映射到 C# 类型。

<a name="Simple_Types" />

### <a name="simple-types"></a>简单类型

下表显示应映射中的 OBJECTIVE-C 和 CocoaTouch world 向 Xamarin.iOS 领域的类型的方式：

|Objective C 类型名称|Xamarin.iOS 统一 API 类型|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([详细上绑定 NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (另请参阅： [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|CoreFoundation 类型 (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|基础类型 (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>数组

Xamarin.iOS 运行时自动将负责将转换到 C# 数组`NSArrays`并转换回，因此，例如虚部 Objective C 方法执行该操作返回`NSArray`的`UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

绑定如下：

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

思路是要使用的 C# 强类型化数组，因为这将使 IDE 提供适当的代码完成与实际类型，而不强制用户猜测，或查阅文档以找出数组中包含的对象的实际类型。

在其中您可以不跟踪数组中包含的实际大多数派生类型的情况下，你可以使用`NSObject []`作为返回值。

<a name="Selectors" />

### <a name="selectors"></a>选择器

Objective C api 作为特殊类型的选择器出现`SEL`。 当绑定选择器时，将映射到类型`ObjCRuntime.Selector`。  通常选择器都公开在与一个对象，该目标对象，以及选择器使用 API 调用目标对象中。 提供这两种基本上对应于 C# 委托： 封装要调用的方法以及要调用的方法中的对象的内容。

这是绑定如下所示：

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

这是如何方法通常都将在应用程序中使用：

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

为了使绑定不用去涉猎对于 C# 开发人员，你通常将提供采用的方法`NSAction`参数，它允许使用而不是要使用 C# 委托和 lambda `Target+Selector`。 若要这样做通常会隐藏`SetTarget`方法，从而将其与标记[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)属性，然后将公开一个新的帮助器方法，如下：

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

因此，现在你的用户代码可以编写如下：

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>字符串

当你正在绑定采用的方法`NSString`，你可以替换，使用 C# 字符串类型，两者都在返回类型和参数。

唯一的用例时你可能想要使用`NSString`直接时此字符串用作为令牌。 有关字符串的详细信息和`NSString`，请阅读[上 NSString API 设计](~/ios/internals/api-design/nsstring.md)文档。

在某些极少数情况下，API 可能会公开类似 C 的字符串 (`char *`) 而不是 OBJECTIVE-C 的字符串 (`NSString *`)。 在这些情况下，还可以批注与参数[ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)属性。

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out / ref 参数

某些 Api 在其参数中，返回值或按引用传递参数。

通常签名类似如下所示：

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

第一个示例演示常见的 OBJECTIVE-C 的惯用语法以返回错误代码，指向的指针`NSError`指针传递，并在返回时设置值。   第二种方法演示如何 OBJECTIVE-C 的方法可能的对象并修改其内容。   这将是一个引用，而不是纯输出值的处理过程。

绑定将如下所示：

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>内存管理属性

当你使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)特性，则进行双向调用的方法将保留的数据的传递，则可以通过将它作为第二个参数，例如传递指定的自变量的语义：

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

上述将标记为具有"保留"语义的值。 可用的语义是：

-  分配
-  复制
-  保留

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>样式准则

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>使用 [内部]

你可以使用[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)属性来隐藏一种方法从公共 API。 你可能想要在其中公开的 API 是太低级别，你想要提供一个基于此方法的单独文件中的高级实现的情况下执行此操作。

此外可以使用此时你会遇到绑定生成器中的限制，例如某些高级的方案可能公开未绑定的类型和你想要将您自己的方式中的绑定和你想要自己包装这些类型在您自己的方式。

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>事件处理程序和回调

Objective C 类通常将通知广播或请求通过代理类 （Objective C 委托） 上发送消息的信息。

此模型中，而完全支持，并显示由 Xamarin.iOS 有时会比较麻烦。 Xamarin.iOS 公开 C# 事件模式和可以在这些情况下使用的类上的方法的回调系统。 这样，运行如下代码：

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

绑定生成器是能够减少的键入需要映射到 C# 模式 OBJECTIVE-C 的模式。

启动 Xamarin.iOS 1.4 后它将有可能还指示生成器可以生成特定的 OBJECTIVE-C 的委托绑定和公开的委托将作为 C# 事件和主机类型上的属性。

有两个类参与此流程，主机类，它将是当前发出事件，将电子邮件发送到的一个`Delegate`或`WeakDelegate`和实际的代理类。

考虑以下设置：

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

若要包装类，你必须：

-  在主机类中，将添加到你 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   声明充当其委托和你公开的 C# 名称的类型。 在上述示例中这种`typeof (MyClassDelegate)`和`WeakDelegate`分别。
-  在委托类中，在每个方法都具有两个以上参数，你需要指定想要用于自动生成的 EventArgs 类的类型。

绑定生成器并不局限于包装仅单个事件目标，因此你将必须提供数组，以支持此安装程序很可能某些 Objective C 类发出消息到多个委托。 大多数设置将不需要它，但生成器是准备好支持这种情况。

将生成的代码：

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

`EventArgs`用于指定的名称`EventArgs`要生成类。 你应使用签名每一次 (在此示例中，`EventArgs`将包含`With`类型 nint 属性)。

与上面的定义中，生成器将生成生成 MyClass 中的以下事件：

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

因此，你现在可以使用如下代码：

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

回调事件调用一样，区别是，而不是让多个潜在的订阅服务器 (例如，多个方法可以挂接到`Clicked`事件或`DownloadFinished`事件) 回调只能具有每个订阅服务器。

该过程完全相同，唯一的区别是而不是公开的名称`EventArgs`类将生成 EventArgs 实际用于生成的 C# 委托名称来命名。

如果委托类中的方法返回一个值，则绑定生成器将映射这到中的父类，而不是事件的委托方法。 在这些情况下，你需要提供应返回由方法如果用户不挂钩到委托的默认值。 执行此操作使用[ `[DefaultValue]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)或[ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)属性。

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) 返回值，将进行硬编码时[ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)用于指定将返回输入自变量。

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>枚举和基类型

你还可以引用枚举或不直接支持 btouch 接口定义系统的基类型。 若要执行此操作，将你的枚举和核心类型放入单独的文件，并包括为其中一个额外文件提供给 btouch 的一部分。

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>链接的依赖关系

如果你正在绑定不是你的应用程序的一部分的 Api，你需要确保你可执行文件针对这些库的链接。

你需要告知如何链接你的库的 Xamarin.iOS，这可以通过更改你的生成配置，以调用`mtouch`具有一些附加的命令生成指定如何与使用的新库链接的参数"-gcc_flags"选项，后跟带引号的字符串，其中包含所有额外的库所需的程序，如下：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

上面的示例将链接`libMyLibrary.a`，`libSystemLibrary.dylib`和`CFNetwork`到最终的可执行文件的 framework 库。

你可以利用程序集级别或者[ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)，，您可以将它们嵌入在协定文件 (如`AssemblyInfo.cs`)。
当你使用[ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)，你将需要你在作为所做的绑定过程中，这将与你的应用程序中嵌入的本机库时可用的本机库。 例如：

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

你可能想知道，为什么您需要`-force_load`命令，以及的原因是-ObjC 标志虽然编译中的代码，则系统不保留需支持 （链接器/编译器死代码消除去除它） 的类别的元数据的您针对 Xamarin.iOS，需要在运行时。

<a name="Assisted_References" />

## <a name="assisted-references"></a>辅助的引用

操作表和警报框等某些暂时性对象很不方便跟踪的面向开发人员和绑定生成器可帮助一点点。

例如，如果你有一个类显示一条消息，然后生成`Done`事件，处理这的传统方法是：

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

在上述方案中开发人员需要保持对对象的引用，自己和任一泄漏或主动清除框自己的引用。  尽管绑定代码，生成器支持跟踪的引用为你和清除它的特殊方法调用时，上面的代码就会处于：

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

请注意如何就不再需要将该变量保留在情况下，它处理本地变量并且不需要时对象不活动时，请清除该引用。

若要利用此功能，你的类应具有在中设置的事件属性[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)声明以及`KeepUntilRef`变量设置为对象愿意完成其工作时调用的方法名称此：

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>继承协议

从 Xamarin.iOS 3.2 版开始，我们支持从已标记有的协议继承[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)属性。 这可在某些 API 模式，如 in`MapKit`其中`MKOverlay`协议、 继承自`MKAnnotation`协议，并采用了多个类继承的`NSObject`。

从历史上看我们需要将协议复制到每个实现，但在这些情况下现在我们可以获得`MKShape`类继承自`MKOverlay`协议，它会自动生成所需的所有方法。

## <a name="related-links"></a>相关的链接

- [绑定示例](https://developer.xamarin.com/samples/BindingSample/)
