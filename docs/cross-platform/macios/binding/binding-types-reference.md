---
title: 绑定类型参考指南
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: e064eda3db9aa0156869cf1c7392823553af9bd2
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="binding-types-reference-guide"></a>绑定类型参考指南

本文档介绍可用于批注 API 协定文件驱动器绑定并生成代码的特性列表

Xamarin.iOS 和 Xamarin.Mac API 协定用 C# 编写多半以定义的方式的接口定义为 C# 中加以表示 Objective C 代码。 此过程涉及接口声明以及 API 约定可能需要某些基本类型定义的组合。 绑定类型的简介，请参阅我们的助理指南[绑定 Objective C 库](~/cross-platform/macios/binding/objective-c-libraries.md)。

## <a name="type-definitions"></a>类型定义

语法：

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

你具有的协定定义的每个接口[ `[BaseType]` ](#BaseTypeAttribute)声明生成对象的基类型的属性。 在上面的声明中`MyType`将绑定到 Objective C 类型调用生成类的 C# 类型`MyType`。

如果在类型名称后指定任何类型 (上面的示例中`Protocol1`和`Protocol2`) 使用接口继承语法的内容这些接口将内联如果它们过的协定的一部分`MyType`。
是一种类型采用一种协议的 Xamarin.iOS 曲面的方式内联所有的方法和已转换的类型本身的协议中声明的属性。

下面演示如何的 Objective C 声明`UITextField`都 Xamarin.iOS 协定中定义：

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

将作为 C# API 约定编写如下：

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

你可以通过将其他特性应用于接口，以及配置控制的代码生成的许多其他方面[ `[BaseType]` ](#BaseTypeAttribute)属性。


### <a name="generating-events"></a>生成事件

Xamarin.iOS 和 Xamarin.Mac API 设计的一个功能是，我们将 OBJECTIVE-C 的委托类映射为 C# 事件和回调。 用户可以选择在按实例逐一是否采用 Objective C 编程模式，通过将分配给属性，例如`Delegate`实现 Objective C 运行时将调用的各种方法的类或实例选择 C# 的样式事件和属性。

让我们看到如何使用 OBJECTIVE-C 的模型的一个示例：

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

在上述示例中，你可以看到，我们已选择覆盖两种方法，一个通知，滚动事件已位，并且第二个，是应返回布尔值指示回调`scrollView`是否应该滚动到排名靠前的或不。

C# 模型允许你的库的用户来侦听通知使用 C# 事件语法或属性语法挂接都应该返回值的回调。

这是如何相同功能的 C# 代码如下所示使用 lambda:

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

由于事件不会返回的值 （它们具有 void 返回类型），因此你可以连接多个副本。 `ShouldScrollToTop`不是某个事件，它是改为使用类型属性`UIScrollViewCondition`具有此签名：

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

它将返回`bool`值，在这种情况下 lambda 语法使我们可以仅返回的值从`MakeDecision`函数。

支持的绑定生成器的生成的事件和属性链接类，如`UIScrollView`与其`UIScrollViewDelegate`（我们称这些模型类），这通过批注你[ `[BaseType]` ](#BaseTypeAttribute)定义替换`Events`和`Delegates`（如下所述） 的参数。 除了批注[ `[BaseType]` ](#BaseTypeAttribute)利用这些参数就需要通知的几个更多组件的生成器。

需要多个参数的事件 （在 Objective C 约定为委托类中的第一个参数是发件人对象的实例） 必须为生成提供你想要的名称`EventArgs`的类。 这通过完成[ `[EventArgs]` ](#EventArgsAttribute)模型类中在方法声明上的属性。 例如：

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

以上声明将生成`UIImagePickerImagePickedEventArgs`派生自的类`EventArgs`和包这两个参数，`UIImage`和`NSDictionary`。 生成器是生成此警告：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然后，它公开中的以下`UIImagePickerController`类：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

返回值的模型方法绑定以不同的方式。 需要生成的 C# 委托 （方法的签名） 和也选择默认值以返回在用户不提供实现自己的情况下这两个的名称。 例如，`ShouldScrollToTop`定义这是：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

上述操作将创建`UIScrollViewCondition`委托具有签名的已显示更高版本，并且如果用户不提供实现，则返回值将为 true。

除了[ `[DefaultValue]` ](#DefaultValueAttribute)属性中，你还可以使用[ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute)将定向以返回调用或指定参数的值的生成器的属性[ `[NoDefaultValue]` ](#NoDefaultValueAttribute)指示生成器没有默认值的参数。

<a name="BaseTypeAttribute" />

### <a name="basetypeattribute"></a>BaseTypeAttribute

语法：

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

#### <a name="basetypename"></a>BaseType.Name

你使用`Name`属性来控制此类型将绑定到在 OBJECTIVE-C 的领域中的名称。 这通常用于为 C# 类型提供一个名称，符合.NET Framework 设计准则，但它映射到不遵循此约定的 Objective C 中的名称。

示例中，在下面的示例中，我们将映射 Objective C`NSURLConnection`键入到`NSUrlConnection`，如.NET Framework 设计准则使用而不是"URL"的"Url":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

指定的名称用作值生成`[Register]`在绑定中的属性。 如果`Name`未指定，则该类型的短名称用作的值`[Register]`中生成的输出属性。

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events 和 BaseType.Delegates

这些属性用于驱动的 C# 生成的设置事件中生成的类的样式。 它们用于将具有其 Objective C 委托类的给定的类的链接。 你将会遇到许多情况下，其中一个类用于委托类发送通知和事件。 例如`BarcodeScanner`者必须随`BardodeScannerDelegate`类。 `BarcodeScanner`类通常具有`Delegate`分配的实例的属性`BarcodeScannerDelegate`以，而此工作原理，你可能想要向你的用户公开的 C#-如样式事件接口，并在这些情况下将使用`Events`和`Delegates`属性[ `[BaseType]` ](#BaseTypeAttribute)属性。

这些属性始终在一起设置和必须具有相同数量的元素以及保持同步。`Delegates`数组包含一个字符串，对于你想要自动换行，每个弱类型的委托和`Events`数组包含你想要与之关联的每个类型的一种类型。

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```


#### <a name="basetypekeeprefuntil"></a>BaseType.KeepRefUntil

如果创建此类的新实例时，将此特性的应用，该对象的实例将保留围绕到引用的方法`KeepRefUntil`已调用。 这是提高您的 api，可用性，当你不想让用户保持对对象的引用，以使用你的代码时很有用。 此属性的值是中的方法的名称`Delegate`类，因此你必须与结合使用这`Events`和`Delegates`以及属性。

下面的示例演示如何通过使用此`UIActionSheet`Xamarin.iOS 中：

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```


### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

当此属性应用于接口定义它将阻止生成器生成默认构造函数。

当你需要要使用的类中的其他构造函数之一进行初始化的对象时，请使用此属性。


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

当此属性应用于接口定义它将标记为私有的默认构造函数。 这意味着，可以仍实例化此类的对象内部从您扩展的文件，但它只是不是用户可在类的访问。

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

使用此属性的类型定义，可将绑定 OBJECTIVE-C 的类别以及公开这三个 C# 扩展方法来镜像 Objective C 公开的功能的方式。

类别是用于扩展的类中可用方法和属性集的 OBJECTIVE-C 的机制。   在实践中，它们用于扩展基本类的功能 (例如`NSObject`) 采用特定框架中的链接时 (例如`UIKit`)，使其方法可用，但仅当新的 framework 链接中。   在某些其他情况下，它们用于组织类中的功能的功能。   它们是 C# 扩展方法的设计理念相似。

这是什么类别将如下所示在目标 c:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上面的示例中找到在将扩展的实例使用库`UIView`方法`makeBackgroundRed`。

若要将这些绑定，可以使用[ `[Category]` ](#CategoryAttribute)接口定义的属性。   使用时[ `[Category]` ](#CategoryAttribute)特性的含义[ `[BaseType]` ](#BaseTypeAttribute)属性将更改从用于指定要扩展，为要扩展的类型的基类。

下面演示如何`UIView`扩展都是绑定，转变为 C# 扩展方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

上述操作将创建`MyUIViewExtension`包含的类`MakeBackgroundRed`扩展方法。   这意味着，现在可以调用`MakeBackgroundRed`上任何`UIView`子类化，请向您提供相同的功能，你会收到一个目标 C

在某些情况下，你将找到**静态**类别内的成员如在下面的示例：

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

这将导致**不正确**类别 C# 接口定义：

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

这是不正确因为用于`BoolMethod`需要的实例扩展`FooObject`但绑定 ObjC**静态**扩展，这是由于的 C# 扩展方法的实现方式事实的副作用。

使用上面定义的唯一方法是通过以下繁琐代码：

```csharp
(null as FooObject).BoolMethod (range);
```

要避免这种情况条建议是内联`BoolMethod`内的定义`FooObject`接口定义本身，这将允许您才能调用此扩展，如它旨在`FooObject.BoolMethod (range)`。

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

我们将发出警告 (BI1117)，每当我们找到[ `[Static]` ](#StaticAttribute)成员内部[ `[Category]` ](#CategoryAttribute)定义。 如果你确实想要[ `[Static]` ](#StaticAttribute)成员内的你[ `[Category]` ](#CategoryAttribute)定义可以通过使用抑制警告`[Category (allowStaticMembers: true)]`或的修饰你成员或[`[Category]` ](#CategoryAttribute)接口定义与[ `[Internal]` ](#InternalAttribute)。

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

此属性应用于类时它只会产生静态类，一个不是派生自`NSObject`，因此[ `[BaseType]` ](#BaseTypeAttribute)忽略属性。 静态类用于承载 C 你想要公开的公共变量。

例如：

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

将生成具有以下 API 的 C# 类：

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>协议/模型定义

模型通常用于通过协议的实现。
它们不同，因为运行时将仅向注册 Objective C 实际上已被覆盖的方法。
否则，该方法将不会注册。

这通常意味着当子类已标记为与类`ModelAttribute`，不应调用基方法。   调用该方法将引发异常，你应该在你子类重写任何方法上实现的整个行为。

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

默认情况下，是一种协议的一部分的成员不是必需的。 这允许用户创建的一个子类`Model`只需从 C# 中的类派生，并仅将他们所关心的方法重写的对象。 有时 OBJECTIVE-C 的协定要求用户提供此方法的实现 (那些被标记为`@required`指令 Objective C 中)。 在这些情况下，您应标记使用这些方法`[Abstract]`属性。

`[Abstract]`特性可以应用于方法或属性，并导致生成器生成的成员标记为抽象类和类是抽象类。

以下是取自 Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute" />

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

指定的默认值，如果用户不提供此特定方法中的模型对象的方法要返回的模型方法

语法：

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

例如，在下面的虚部委托类的`Camera`类，我们提供`ShouldUploadToServer`将上的作为属性公开`Camera`类。 如果用户`Camera`类未显式设置到 lambda 可以响应 true 或 false 的值，返回的默认值在此情况下将为 false，我们在指定的值`DefaultValue`属性：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

如果用户虚部类中设置一个处理程序，则将忽略此值：

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

另请参阅： [ `[NoDefaultValue]` ](#NoDefaultValueAttribute)， [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute)。

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

语法：

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

此属性时提供的模型类返回一个值的方法上将指示要返回指定的参数的值，如果用户未提供其自己的方法或 lambda 的生成器。

示例:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

在上面的事例如果的用户`NSAnimation`类选择了要使用的任何 C# 事件/属性，并且未设置`NSAnimation.ComputeAnimationCurve`到方法或 lambda，返回值将为进度参数中传递的值。

另请参阅： [ `[NoDefaultValue]` ](#NoDefaultValueAttribute)， [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

有时，有必要，不能公开事件或从模型类到主机类委派属性，以便添加此属性将指示以避免任何使用它修饰的方法生成的生成器。

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```

### <a name="delegatenameattribute"></a>DelegateNameAttribute

返回值来设置要使用的委托签名的名称的模型方法中使用此属性。

示例:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

使用上面的定义，生成器将生成以下公共声明：

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

此属性用于允许生成器更改在主机类中生成的属性的名称。 有时很有用时 FooDelegate 类方法的名称适合委托类，但将会在主机类中作为属性不正常。

这也是真正的有用 （和所需） 时有两个或有意义的多个重载方法保留它们以在 FooDelegate 类名为但你想要在具有更好的给定名称的 host 类中公开它们。

示例:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

使用上面的定义，生成器将生成以下公共主机类中的声明：

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

需要多个参数的事件 （在 Objective C 约定为委托类中的第一个参数是发件人对象的实例） 必须提供您要用于生成 EventArgs 该类是的名称。 这通过完成`[EventArgs]`属性中的方法声明你`Model`类。

例如：

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

以上声明将生成`UIImagePickerImagePickedEventArgs`类，该类派生自 EventArgs 并包这两个参数，`UIImage`和`NSDictionary`。 生成器是生成此警告：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然后，它公开中的以下`UIImagePickerController`类：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

此属性用于允许若要更改的事件或在类中生成的属性名称的生成器。 有时很有用时模型类方法的名称对模型类中，有意义，但将看起来很奇怪原始类中的事件或属性。

例如，`UIWebView`使用从以下位`UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

上述公开`LoadingFinished`中的方法为`UIWebViewDelegate`，但`LoadFinished`为事件中的最多将挂钩`UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

当你将`[Model]`属性设置为您的协定 API，运行时中的类型定义将生成将仅到类中方法呈现调用，如果用户已覆盖类中的方法的特殊代码。 此属性通常应用于包装 Objective C 委托类的所有 Api。

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

指定对模型的方法不提供默认返回值。

这适用于 OBJECTIVE-C 的运行时通过响应`false`到 Objective C 运行时请求，以确定是否此类中实现的指定选择器。

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

另请参阅： [ `[DefaultValue]` ](#DefaultValueAttribute)， [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>协议

Objective C 协议概念中 C# 实际上不存在。 协议则类似于 C# 接口，但它们的区别在于采用它的类必须实现不是所有的方法和在协议中声明的属性。 相反的一些方法和属性是可选的。

某些协议通常用作模型类，应使用绑定那些[ `[Model]` ](#ModelAttribute)属性。

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
```

已合并开头 Xamarin.iOS 7.0 新增和改进的协议绑定功能。  包含任意定义`[Protocol]`属性将实际生成大大地提高你使用这些协议的方法的三个支持类：

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

**类实现**提供完整的抽象类，你可以重写的各个方法并获取完整的类型安全。 但由于 C# 中不支持多重继承，有一些的情形，其中可能需要不同的基类，但仍想要实现接口。

这就是生成**接口定义**传入。  它是具有协议的所有所需的方法的接口。  这样，开发人员想要实现您的协议，只是实现该接口。  运行时将自动注册为采用协议的类型。

请注意接口仅列出所需的方法，并且确实公开了可选的方法。   这意味着采用协议的类会获得完整的类型检查所需的方法，而将需要采用弱键入 （手动使用导出特性和匹配的签名） 的可选的协议方法。

要更加方便地使用使用协议的 API，请绑定工具还将生成一个扩展方法类，它公开所有的可选方法。   这意味着，只要你使用的 API，则你将能够将视作具有所有方法的协议。

如果你想要在你的 API 中使用的协议定义，你将需要在你的 API 定义中编写主干空接口。   如果你想要使用 MyProtocol API 中，你将需要执行此操作：

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

### <a name="adopting-protocol-generated-interfaces"></a>采用协议生成接口

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

### <a name="protocol-inlining"></a>内联的协议

尽管绑定已声明为采用一种协议的现有 Objective C 类型时，你需要把内联协议直接。 若要执行此操作，只是将声明你协议不含任何接口[ `[BaseType]` ](#BaseTypeAttribute)属性，并列出为你的接口的基接口的列表中的协议。

示例:

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```


## <a name="member-definitions"></a>成员定义

本部分中的特性应用于一种类型的各个成员： 属性和方法声明。


### <a name="alignattribute"></a>AlignAttribute

用于指定属性的返回类型的对齐值。 某些属性采用指向必须某些边界上对齐的地址 (在 Xamarin.iOS 发生这种情况例如一些`GLKBaseEffect`属性必须是 16 字节对齐)。 你可以使用此属性修饰 getter，并使用对齐值。 这通常用于`OpenTK.Vector4`和`OpenTK.Matrix4`类型时与 OBJECTIVE-C 的 Api 集成在一起。

示例:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

`[Appearance]`特性被限制到 iOS 5，其中引入的外观管理器。

`[Appearance]`特性可以应用于任何方法或属性参与`UIAppearance`framework。 当此属性应用于方法或类中的属性时，它将直接绑定生成器能创建一个强类型外观类，用于设置样式的此类的所有实例或与特定条件匹配的实例。

示例:

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

上述也会生成下面的代码在 UIToolbar 中：

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (Xamarin.iOS 5.4)

使用`[AutoReleaseAttribute]`方法和属性来包装方法调用到中的方法上`NSAutoReleasePool`。

Objective C 中有一些方法，返回值添加到默认`NSAutoReleasePool`。 默认情况下，这些将转到你的线程`NSAutoReleasePool`，但由于 Xamarin.iOS 还保留了对对象的引用，只要托管的对象存在，可能不想要保留的额外的引用`NSAutoReleasePool`这仅获取耗尽你的线程之前返回控制转移到下一个线程，或你返回到主循环。

例如在大量的属性上应用此特性 (例如`UIImage.FromFile`) 返回已添加到默认值的对象`NSAutoReleasePool`。 如果没有此特性，只要你的线程不未将控制权返回给主循环，将保留的图像。 如果你的线程已某种形式的背景始终处于活动状态的程序下载程序并等待工作时，图像将永远不会释放。

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]`用于强制在创建托管类型，即使返回的非托管的对象与绑定定义中所述的类型不匹配。

标头中所述的类型与本机方法的返回的类型不匹配时，这是很有用，例如需要从以下 Objective C 定义`NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

明确指出它将返回`NSURLSessionDownloadTask`实例，但尚未它**返回** `NSURLSessionTask`，这是超类并因此不转换为`NSURLSessionDownloadTask`。 由于我们是类型安全上下文中`InvalidCastException`将发生这种情况。

若要符合标头说明和避免`InvalidCastException`、`[ForcedTypeAttribute]`使用。

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`[ForcedTypeAttribute]`还接受一个名为的布尔值`Owns`即`false`默认情况下`[ForcedType (owns: true)]`。 拥有参数用于按照[所有权策略](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html)为**核心 Foundation**对象。

`[ForcedTypeAttribute]`仅适用于参数、 属性和返回值。

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]`允许绑定`NSNumber`，`NSValue`和`NSString`（枚举） 到更准确的 C# 类型。 该属性可以用于创建更好且更准确，通过本机 API 的.NET API。

（在返回值） 的方法、 参数和属性与可修饰`BindAs`。 唯一限制是，您的成员**必须不**内`[Protocol]`或[ `[Model]` ](#ModelAttribute)接口。

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

当前支持的封装类型包括：

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

支持以下 C# 数据类型从/到要封装`NSValue`:

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint / PointF
* CGRect / RectangleF
* CGSize / SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

支持以下 C# 数据类型从/到要封装`NSNumber`:

* bool
* byte
* double
* float
* short
* int
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* nint
* nuint
* 枚举

#### <a name="nsstring"></a>NSString

[`[BindAs]`](#BindAsAttribute) 适用于与 conjuntion[枚举作为后盾 NSString 常量](#enum-attributes)因此你可以创建更好的.NET API，例如：

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

将输出：

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

我们将处理`enum`  <->  `NSString`转换才提供的枚举类型到[ `[BindAs]` ](#BindAsAttribute)是[NSString 常量作为后盾](#enum-attributes)。

#### <a name="arrays"></a>数组

[`[BindAs]`](#BindAsAttribute) 此外支持数组的任何支持的类型，你可以作为示例的以下 API 定义：

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

将输出：

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

`rects`参数将封装到`NSArray`包含`NSValue`每个`CGRect`，因而您将收到的数组`CAScroll?`其使用返回的值创建`NSArray`包含`NSStrings`。

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

`[Bind]`特性有两个用途一个当应用于方法或属性声明中，并当应用于单个 getter 或 setter 的属性中的另一个。

当用于方法或属性、 影响`[Bind]`特性将生成调用指定的选择器的方法。 但结果生成的方法不用修饰[ `[Export]` ](#ExportAttribute)属性，这意味着，它不能参与方法重写。 这通常与结合使用`[Target]`实现 OBJECTIVE-C 的扩展方法的属性。

例如：

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

如果用中 getter 或 setter，`[Bind]`属性用于更改默认值生成属性的 getter 和 setter OBJECTIVE-C 的选择器名称时由代码生成器推断。 默认情况下，标记具有名称的属性时`fooBar`，生成器将生成`fooBar`导出的 getter 和`setFooBar:`setter。 在少数情况下，Objective C 未遵循此约定，它们通常更改要 getter 名称`isFooBar`。
将使用此属性以通知此生成器。

例如：

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute" />

### <a name="asyncattribute"></a>AsyncAttribute

仅在 Xamarin.iOS 6.3 上可用和更高版本。

此特性可以应用于其最后一个自变量作为完成处理程序的方法。

你可以使用`[Async]`其最后一个自变量为回调的方法的属性。  如果你将此应用于方法时，绑定生成器将生成了后缀，该方法的版本`Async`。  如果回调不带任何参数，返回值将为`Task`，如果回调采用参数，则结果将为`Task<T>`。

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

以下将生成此异步方法：

```csharp
Task LoadFileAsync (string file);
```

如果回调采用多个参数，则应设置`ResultType`或`ResultTypeName`指定所需生成的类型，它将保存的所有属性的名称。

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

以下将生成此异步方法，其中`FileLoading`包含属性同时访问`files`和`byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

如果回调的最后一个参数是`NSError`，然后生成`Async`方法将检查如果值不为 null，并且，如果是这种情况，生成的异步方法将设置的任务异常。

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

上述生成以下的异步方法：

```csharp
Task<string> UploadAsync (string file);
```

和错误时，该结果任务将具有设置为异常`NSErrorException`包装生成`NSError`。

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

此属性用于指定返回的值`Task`对象。   此参数采用现有类型，因此它需要在你的核心 api 定义之一中定义。

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

此属性用于指定返回的值`Task`对象。   此参数采用你所需的类型名称的名称，生成器将会产生一系列属性，一个用于回调会获取每个参数。

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

此属性用于自定义生成的异步方法的名称。   默认值是使用方法的名称，并将文本"Async"追加，可以使用此更改此默认设置。

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

此特性应用于字符串参数或字符串属性和指示不使用对此参数，封送处理的零复制字符串的代码生成器，并改为从 C# 字符串创建的新 NSString 实例。
此属性仅需要对字符串，如果指示生成器将使用零复制字符串封送处理使用`--zero-copy`命令行选项或设置程序集级别属性`ZeroCopyStringsAttribute`。

这是在 OBJECTIVE-C 要在其中声明该属性的情况下必需`retain`或`assign`属性而不是`copy`属性。 这些通常发生在第三方库已错误地"优化"由开发人员。 一般情况下，`retain`或`assign``NSString`属性不正确自`NSMutableString`或用户派生类`NSString`可能会更改的库代码，稍重大的不知情的情况下字符串的内容应用程序。 通常这种情况由于过早优化。

下面两个此类属性显示在目标 c:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

当你将`[DisposeAttribute]`为类，提供代码片段将添加到`Dispose()`类的方法实现。

由于`Dispose`方法自动生成的`bmac-native`和`btouch-native`工具，你需要使用`[Dispose]`属性将在生成的某些代码注入`Dispose`方法实现。

例如：

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

`[Export]`属性用于标记的方法或属性将公开为 Objective C 运行时。 此属性绑定工具和实际的 Xamarin.iOS 和 Xamarin.Mac 运行时之间共享。 对于方法，参数被传递到生成的代码中，原义属性、 getter 和 setter 的导出将生成基于与基声明的 (请参阅的部分[ `[BindAttribute]` ](#BindAttribute)有关如何更改的信息绑定工具的行为）。

语法：

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

[选择器](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html)表示基础 OBJECTIVE-C 的方法或属性被绑定的名称。

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

此属性用于将 C 全局变量公开为按需加载并向 C# 代码公开的字段。 通常这必需获取值的常量 C 或 Objective C 中定义和，可能是在某些 Api 中使用任一令牌或其值是不透明和必须用作-是由用户代码。

语法：

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName`是 C 符号将其与链接。 默认情况下这将加载从库从命名空间中推断出其名称定义该类型。 如果这不是其中查找符号的库，则应传递`libraryName`参数。 如果你正在链接静态库，请使用`__Internal`作为`libraryName`参数。

生成的属性始终是静态的。

被标记为字段特性的属性可以是以下类型：

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* 枚举

不支持 setter[枚举作为后盾 NSString 常量](#enum-attributes)，但是它们也可以将其手动绑定，如果需要。

示例:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

`[Internal]`特性可以应用于方法或属性，它起标记生成的代码与`internal`C# 关键字在生成的程序集代码仅可以访问。 这是通常用于隐藏太低级别的 Api 或提供一个并非最佳的公共 API，你想要提高时或不支持由生成器的 api 并需要某些手工编码。

当设计绑定，通常会隐藏方法或使用此属性的属性，并提供不同的名称，为方法或属性，然后对您的 C# 互补的支持文件，将添加公开的强类型包装器基础功能。

例如：

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

然后，在支持文件中，可以具有一些类似下面的代码：

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```

<a name="IsThreadStaticAttribute" />

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

此属性标记为要使用.NET 进行批注的属性支持字段`[ThreadStatic]`属性。 这是该字段是一个线程静态变量的情况下很有用。

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin.iOS 6.0.6)

此属性将使方法支持本机 (OBJECTIVE-C) 异常。
而不是调用`objc_msgSend`直接，将调用经过自定义 trampoline 捕获 ObjectiveC 异常并将它们封送到托管异常。

当前只有几`objc_msgSend`支持签名 (您将了解到签名不受支持，如果使用此绑定的应用程序的本机链接失败且缺少 monotouch_*_objc_msgSend*符号)，但的详细信息可以是在请求中添加了。


### <a name="newattribute"></a>NewAttribute

此特性应用于方法和属性，以拥有生成器生成`new`关键字声明的前面。

它用于避免编译器警告时相同的方法或属性名称在基类中已存在的子类中引入。

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

你可以将此特性应用于字段具有生成器生成强类型的帮助器 Notifications 类中。

此特性可用于无需不执行任何负载的通知的自变量也可以指定`System.Type`引用另一个接口在 API 定义中，通常与以"EventArgs"结尾的名称。 生成器会变为接口类子类`EventArgs`并且将包括所有列出的属性。 [ `[Export]` ](#ExportAttribute)属性应在`EventArgs`类列出用于查找要提取值的 OBJECTIVE-C 的字典键的名称。

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
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

你的代码用户然后可以轻松地订阅通知发布到[NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/)通过使用如下代码：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

或设置要观察特定对象。 如果你通过`null`到`objectToObserve`此方法将行为就像其其他对等一样。

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

中的返回的值`ObserveDidStart`可用于轻松地停止接收通知，如下：

```csharp
token.Dispose ();
```

也可以调用[NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject//)并将令牌传递。 如果你的通知中包含参数，则应指定一个帮助程序`EventArgs`接口，如下：

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

上述将生成`MyScreenChangedEventArgs`类，该类具有`ScreenX`和`ScreenY`将提取中的数据的属性[NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/)字典使用键`ScreenXKey`和`ScreenYKey`分别并应用正确的转换。 `[ProbePresence]`属性为生成器用来探测如果此键设置`UserInfo`，而不是尝试提取的值。 这适用于的情况下，项的状态 （通常为布尔值） 的值。

此选项，可以编写如下代码：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

在某些情况下，没有任何字典上传递的值相关联的常量。  Apple 有时使用公共符号常量和有时使用字符串常量。  默认情况下[ `[Export]` ](#ExportAttribute)中你提供属性`EventArgs`类将使用指定的名称作为公共符号要查找在运行时。  如果这不是这种情况，并改为它应为字符串常量查找然后传递`ArgumentSemantic.Assign`到导出的属性的值。

**Xamarin.iOS 8.4 中的新增功能**

有时，通知便可生命不带任何参数，因此使用[ `[Notification]` ](#NotificationAttribute)没有自变量是可接受。  但有时，将引入对通知的参数。  若要支持这种情况下，可以多次应用该属性。

如果你在开发一个绑定，并且你想要避免破坏现有的用户代码，则将打开从现有通知：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

到如下两次，列出通知属性的版本：

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute" />

### <a name="nullallowedattribute"></a>NullAllowedAttribute

这应用于属性时它可以标记为允许值的属性`null`要分配给它。 此操作才有效对于引用类型。

当此应用于它指示指定的参数可以为 null，并且不检查，应执行用于传递方法签名中的参数`null`值。

如果引用类型没有此特性，绑定工具将生成然后再将它传递到 Objective C 所赋的值的检查，并且将生成的检查，将引发`ArgumentNullException`分配的值是否`null`。

例如：

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>OverrideAttribute

使用此属性可指示绑定生成器，应将此特定方法的绑定标记与`override`关键字。

### <a name="presnippetattribute"></a>PreSnippetAttribute

你可以使用此属性将一些代码以验证输入的参数后、 但在到目标。 该代码调用之前插入

示例:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

你可以使用此属性将一些代码以之前的任何参数验证中生成的方法插入。

示例:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

指示要调用来自此类，以从它获取值的指定的属性的绑定生成器。

此属性通常用于刷新指向保留引用的对象图的引用对象的缓存。 通常它显示在代码中添加/删除等操作。 使用此方法，以便添加或删除，更新的内部缓存来确保元素后会记录到实际上正在使用的对象的托管的引用。 这可能是因为该绑定工具在给定的绑定生成的所有引用对象的支持字段。

示例:

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

在这种情况下，`Dependencies`属性将添加或删除从的依赖关系后调用`NSOperation`加载对象，确保我们具有表示实际的关系图的对象，防止内存泄漏以及内存损坏。

### <a name="postsnippetattribute"></a>PostSnippetAttribute

你可以使用此属性将某些 C# 源代码要插入后的代码调用，其基础的 OBJECTIVE-C 的方法

示例:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

此属性是应用于返回值标记为正在代理对象。 不从用户绑定区别在于某些 Objective C Api 返回的代理对象。 此属性的效果是标记为对象`DirectBinding`对象。 对于中 Xamarin.Mac 方案，你可以看到[讨论此 bug](https://bugzilla.novell.com/show_bug.cgi?id=670844)。

### <a name="retainlistattribute"></a>RetainListAttribute

指示要保留对参数的托管的引用或移除对参数的内部引用的生成器。 这用来保留对象引用。

语法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果值`doAdd`为 true，则将参数添加到`__mt_{0}_var List<NSObject>;`。 其中`{0}`将替换为给定`listName`。 你必须在对 API 互为补充分部类中声明此支持字段。

有关示例请参阅[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Xamarin.iOS 6.0)

这可以应用于返回类型以指示应调用生成器`Release`上在返回之前的对象。 这仅需要一种方法可保留对象 （而不是一个 autoreleased 对象，它是最常见的方案） 时

示例:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

此外此属性将传播到生成的代码，以便 Xamarin.iOS 运行时知道它必须保留在此类函数从返回 Objective C 时的对象。


### <a name="sealedattribute"></a>SealedAttribute

指示标记为已密封生成的方法的生成器。 如果未指定此属性，默认值是生成的虚拟方法 （虚拟方法、 一个抽象方法或重写，具体取决于如何使用其他属性）。

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

当`[Static]`属性应用于方法或属性时，这将生成的静态方法或属性。 如果未指定此属性，然后生成器生成的实例方法或属性。


### <a name="transientattribute"></a>TransientAttribute

使用此属性与标志属性，即是暂时的其值由暂时 iOS 而不是长生存期对象。 当此属性应用于一个属性时，生成器将不创建此属性，这意味着托管的类不会保留对对象的引用的支持字段。

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

在设计中 Xamarin.iOS/Xamarin.Mac 的绑定，`[Wrap]`属性用于包装具有强类型对象的弱类型对象。 这派上用场主要使用 OBJECTIVE-C 的委托对象通常声明为类型的`id`或`NSObject`。 使用 Xamarin.iOS 和 Xamarin.Mac 的约定是公开为类型这些委托或数据源`NSObject`和使用的约定"Weak"+ 公开的方法的名称命名。 `id delegate`从 Objective C 的属性将公开为`NSObject WeakDelegate { get; set; }`API 协定文件中的属性。

但是通常分配给此委托的值的强类型，因此我们外围的强类型并应用`[Wrap]`属性，这意味着，用户可以选择使用弱类型，它们需要一些精细控制，或者如果他们需要采用低级别 tric对其工作大多数情况下，k，也可以使用强类型属性。

示例:

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

这是用户将如何使用委托的弱类型版本：

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

这是如何使用强类型版本，请注意，并且用户采用 C# 类型系统的优点，并使用替代关键字来声明其意图，他不需要手动用户用修饰的方法和`[Export]`，因为我们这样做了用户的绑定中的工作：

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

另一个用途`[Wrap]`属性是支持的方法的强类型版本。  例如：

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

由生成的成员`[Wrap]`不`virtual`默认情况下，如果你需要`virtual`成员可以设置为`true`可选`isVirtual`参数。

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

## <a name="parameter-attributes"></a>Parameter 特性

本部分介绍你可以将应用于方法定义中的参数的属性以及`[NullAttribute]`适用于作为一个整体的属性。

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

此属性应用于在 C# 委托声明中的参数类型相关参数符合调用约定的 OBJECTIVE-C 的块，并应封送它以这种方式通知联编程序。

这通常用于在目标 c： 中定义如下的回调

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另请参阅： [CCallback](#CCallback)。

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

此属性应用于在 C# 委托声明中的参数类型相关参数符合 C ABI 函数指针调用约定，并应封送它以这种方式通知联编程序。

这通常用于在目标 c： 中定义如下的回调

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另请参阅： [BlockCallback](#BlockCallback)。

### <a name="params"></a>Params

你可以使用`[Params]`上要具有注入"params"定义中的生成器的方法定义的最后一个数组参数的属性。   这样的绑定，以便轻松地为可选参数。

例如，以下定义：

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

允许编写以下代码：

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

这有额外的优势，它不需要用户创建的传递元素数组。

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

你可以使用`[PlainString]`前面字符串参数，以便指示将字符串作为 C 字符串，而不是传递作为参数传递的绑定生成器的属性`NSString`。

大多数 OBJECTIVE-C 的 Api 使用`NSString`参数，但是少量的 Api 公开`char *`API，用于传递的字符串，而不`NSString`变体。
使用`[PlainString]`在这些情况下。

例如，以下的 OBJECTIVE-C 的声明：

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

应绑定如下：

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

指示要保持对指定的参数的引用的生成器。 生成器将为此字段提供的后备存储，或者你可以指定一个名称 ( `WrapName`) 来存储位置处的值。 这可用于保存对托管对象到 Objective C 作为参数传递，并且当您知道 Objective C 将只保留对象的此副本的引用。 例如，如 API`SetDisplay (SomeObject)`将使用此属性，因为它是可能 SetDisplay 一次可以仅显示一个对象。 如果你需要能够跟踪多个对象 （例如，对于一个类似于堆栈的 API) 将使用`[RetainList]`属性。

语法：

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

指示要保留对参数的托管的引用或移除对参数的内部引用的生成器。 这用来保留对象引用。

语法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果值`doAdd`为 true，则将参数添加到`__mt_{0}_var List<NSObject>`。 其中`{0}`将替换为给定`listName`。 你必须在对 API 互为补充分部类中声明此支持字段。

有关示例请参阅[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

此特性应用于参数，并从 Objective C 转换为 C# 时才使用。  在这些转换各种 OBJECTIVE-C`NSObject`参数进行包装到托管对象的表示形式。

运行时将采用对本机对象的引用，并保留引用，直到最后一个托管的引用的对象是消失，并且 GC 具有可能运行。

在少数情况下，很重要的 C# 运行时不保留对本机对象的引用。  这有时发生时的基础的本机代码已附加到该参数的生命周期的特殊行为。  例如： 参数的析构函数将执行一些清理操作，或释放某些非常宝贵的资源。

此属性通知运行时所需的对象，如有可能从覆盖方法将返回到 Objective C 时释放。

规则很简单： 如果运行时必须创建新的托管表示形式，从本机对象，然后在函数结束时，将删除的本机对象的保留计数，并托管对象的句柄属性将被清除。   这意味着，如果你对托管对象的引用，该引用将变得毫无用处 （调用上它的方法将引发异常）。

如果未创建传递的对象，或如果已有未完成托管的对象表示形式，强制的释放不会发生。 


## <a name="property-attributes"></a>属性特性

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

此属性用于支持 OBJECTIVE-C 的惯用语法，其中具有 getter 的属性在基类中引入，可变子类引入了 setter。

由于 C# 不支持此模型，基类必须具有 setter 和 getter，和可以使用子类[OverrideAttribute](#OverrideAttribute)。

此属性仅在属性 setter 中使用，并用于在目标 C.支持可变惯用语法

示例:

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

<a name="enum-attributes" />

## <a name="enum-attributes"></a>Enum 特性

映射`NSString`于枚举值的常量是一个简单的方式来创建更好的.NET API。 它：

* 允许要通过显示更有用的代码完成**仅**API; 的正确值
* 添加类型安全，不能使用其他`NSString`常量中的不正确的上下文; 和
* 允许隐藏某些常量，进行而不会丢失功能显示 API 一个较短列表的代码完成。

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
```

从上面的绑定定义生成器将创建`enum`本身，并且还将创建`*Extensions`包括两个方式转换方法枚举值之间的静态类型和`NSString`常量。 这意味着常量保持可供开发人员，即使它们不是 API 的一部分。

示例：

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

### <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

你可以按照声明**一个**使用此特性的枚举值。 这将成为所返回的枚举值未知的常量。

上面的示例中：

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

如果没有枚举值进行了修饰则`NotSupportedException`将引发。

### <a name="errordomainattribute"></a>ErrorDomainAttribute

错误代码是作为枚举值绑定。 通常没有为它们的错误域，它并不总是容易哪一个适用的查找 （或如果即使存在）。

此属性可用于将错误域与枚举本身相关联。

示例:

```csharp
    [Native]
    [ErrorDomain ("AVKitErrorDomain")]
    public enum AVKitError : nint {
        None = 0,
        Unknown = -1000,
        PictureInPictureStartFailed = -1001
    }
```

然后，你可以调用扩展方法`GetDomain`获取域常量的任何错误。

### <a name="fieldattribute"></a>FieldAttribute

这是相同的`[Field]`用于内部类型的常量的属性。 它还可在枚举映射具有特定常量的值。

A`null`值可用来指定应返回的枚举值，如果`null``NSString`指定常量。

上面的示例中：

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

如果没有`null`值不存在则`ArgumentNullException`将引发。

## <a name="global-attributes"></a>全局特性

全局特性可以应用使用`[assembly:]`特性修饰符，如[ `[LinkWithAttribute]` ](#LinkWithAttribute) ，也可以使用任何位置，如[ `[Lion]` ](#SinceAndLionAttributes)和[ `[Since]`](#SinceAndLionAttributes)属性。

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

这是允许开发人员指定的链接而不进行强制库的使用者，若要手动配置 gcc_flags 重用绑定的库所需的标志和额外 mtouch 自变量传递到库的程序集级别属性。

语法：

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

程序集级别应用此特性，例如，这是什么[CorePlot 绑定](https://github.com/mono/monotouch-bindings/tree/master/CorePlot)使用：

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

当你使用`[LinkWith]`属性，指定`libraryName`嵌入到生成的程序集，允许用户将包含的非托管的依赖关系以及正确使用所需的命令行标志的单个 DLL从 Xamarin.iOS 的库。

还有可能不提供`libraryName`，在这种情况下`LinkWith`属性可以用于仅指定其他链接器标志：

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>LinkWithAttribute 构造函数

这些构造函数，可以指定要与链接和嵌入到你生成程序集，支持此库支持的目标和所需与库链接任何可选库标志的库。

请注意，`LinkTarget`自变量推断的 Xamarin.iOS，而不需要设置。

示例：

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```

#### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

`ForceLoad`属性用于决定是否`-force_load`链接标志用于链接本机库。 现在，这始终应为 true。

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

要绑定的库如果任何框架上具有硬要求 (以外`Foundation`和`UIKit`)，应设置`Frameworks`属性设置为包含所需的平台框架的用空格分隔列表的字符串。 例如，如果你正在绑定库需要`CoreGraphics`和`CoreText`，则会将设置`Frameworks`属性`"CoreGraphics CoreText"`。

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

设置此属性设置为 true，如果需要进行编译而不默认情况下，这是一个 C 编译器中使用 c + + 编译器生成的可执行文件。 如果你正在绑定的库在 c + + 编写的请使用此选项。

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

要将捆绑的非托管库的名称。 这是具有扩展名".a"的文件，它可以包含多个平台 （例如，ARM 和 x86 对于模拟器） 的对象代码。

早期版本的 Xamarin.iOS 检查`LinkTarget`属性来确定平台你的库支持，但这是现在自动检测和`LinkTarget`忽略属性。

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

`LinkerFlags`字符串都提供了一种绑定作者指定任何其他链接器标志时本机库链接到应用程序所需的方法。

例如，如果本机库需要 libxml2 和 zlib，你将设置`LinkerFlags`字符串以`"-lxml2 -lz"`。

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

早期版本的 Xamarin.iOS 检查`LinkTarget`属性来确定平台你的库支持，但这是现在自动检测和`LinkTarget`忽略属性。

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

设置此属性设置为 true，如果要链接的库需要的 GCC 异常处理的库 (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

`SmartLink`属性应设置为 true，以让 Xamarin.iOS 确定是否`ForceLoad`是否为必需。

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

`WeakFrameworks`属性相同的工作方式为`Frameworks`属性，但在链接时，`-weak_framework`说明符传递给 gcc 为每个列出的框架。

`WeakFrameworks` 它可以让库和平台框架针对弱链接到的应用程序，以便他们可以选择可以使用它们，如果它们可用，但不是会硬依赖于它们这很有用，如果你的库是为了，若要添加额外的功能在更高版本iOS 的版本。 弱链接的详细信息，请参阅 Apple 的文档上[弱链接](http://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html)。

最适合弱链接将`Frameworks`喜欢帐户， `CoreBluetooth`， `CoreImage`， `GLKit`，`NewsstandKit`和`Twitter`因为它们仅在 iOS 5 中可用。

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) 和 LionAttribute (macOS)

你使用`[Since]`属性设为 Api 标志为拥有及时引入某个特定点。 特性应仅用于标记类型和方法可能导致运行时问题，如果基础的类、 方法或属性不可用。

语法：

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

它应在一般情况下不会应用到枚举、 约束或新的结构为那些将不会导致运行时错误，如果装有操作系统的较旧版本的设备上执行。

当应用于类型的示例：

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

当应用于新的成员时的示例：

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

`[Lion]`方式相同但类型中引入了 Lion 应用特性。 若要使用的原因`[Lion]`与在 iOS 中使用的更具体版本号是主要 OS X 版本会很少发生而很容易地由通过其版本号比其 codename 记住操作系统非常频繁，修订该 iOS

### <a name="adviceattribute"></a>AdviceAttribute

使用此属性可为开发人员提供有关可能会更方便地使用其他 Api 的提示。   例如，如果你提供一个 API 的强类型版本，你无法上使用此特性弱类型属性来指示开发人员更好的 API。

此属性中的信息显示在文档中并可以开发工具为用户建议提供有关如何改进

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

只可用在 Xamarin.iOS 5.4 和更高版本。

此属性指示生成器，此特定的库的绑定 (如果适用于`[assembly:]`) 或类型应使用快速的零复制字符串封送处理。 此属性等效于传递的命令行选项`--zero-copy`到生成器。

生成器在零副本用作字符串时，作为 OBJECTIVE-C 的使用而不会产生的新创建的字符串的相同的 C# 字符串有效地使用`NSString`对象和避免将数据从 C# 字符串复制到 Objective C 字符串。 使用零复制字符串的唯一的缺点是，你必须确保任何字符串属性，则在包装碰巧被标记为`retain`或`copy`具有`[DisableZeroCopy]`属性设置。 这是需要因为零复制字符串的句柄在堆栈上分配且它是在函数返回时无效。

示例:

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

你还可以应用程序集级别的属性和它将应用于所有类型的程序集：

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>强类型的字典

使用 Xamarin.iOS 8.0 我们引入了对轻松地创建强类型类包装支持`NSDictionaries`。

虽然始终已经可以使用[DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/)数据类型以及手动 API，它现在是很简单，若要这样做。  有关详细信息，请参阅[提供强类型](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types)。

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

当此属性应用于接口时，生成器将生成具有相同名称作为派生自的接口的类[DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/)并将转换到强类型的接口中定义的每个属性getter 和 setter 字典。

这将自动生成可以实例化的类从现有`NSDictionary`或已新创建的。

此属性采用一个参数，其中包含用于访问上字典的元素的键的类的名称。   默认情况下具有该属性的接口中的每个属性将查找带有后缀"密钥"的名称的指定类型中的成员。

例如：

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

在上面的示例中，`MyOption`类将生成的字符串属性`Name`，将使用`MyOptionKeys.NameKey`作为要检索字符串的字典的键。   并将使用`MyOptionKeys.AgeKey`作为到要检索的字典键`NSNumber`其中包含 int

如果你想要使用不同的密钥，你可以使用导出属性属性，例如：

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

#### <a name="strong-dictionary-types"></a>强字典类型

中支持以下数据类型`StrongDictionary`定义：

|C# 接口类型|`NSDictionary` 存储类型|
|---|---|
|`bool`|`Boolean` 存储在 `NSNumber`|
|枚举值|整数存储在 `NSNumber`|
|`int`|32 位整数存储在 `NSNumber`|
|`uint`|存储在 32 位无符号的整数 `NSNumber`|
|`nint`|`NSInteger` 存储在 `NSNumber`|
|`nuint`|`NSUInteger` 存储在 `NSNumber`|
|`long`|64 位整数存储在 `NSNumber`|
|`float`|32 位整数存储为 `NSNumber`|
|`double`|64 位整数存储为 `NSNumber`|
|`NSObject` 和子类|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array`的 `NSObject`|`NSArray`|
|C#`Array`的枚举|`NSArray` 包含`NSNumber`值|
