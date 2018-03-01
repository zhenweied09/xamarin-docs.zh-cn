---
title: "API 设计"
description: "对 Xamarin.iOS API 设计的透视"
ms.topic: article
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 5fab7579be256e478c69b76b5e41b8c1b0568ba6
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="api-design"></a>API 设计

除了核心属于 Mono、 基类库[Xamarin.iOS](http://www.xamarin.com/iOS)附带的各种 iOS Api 允许开发人员使用 Mono 创建本机 iOS 应用程序的绑定。

Xamarin.iOS 的核心，没有桥接 Objective C 世界作为使用 C# world 互操作引擎，以及适用于 iOS 的绑定基于 C 的 Api 喜欢 CoreGraphics 和[OpenGLES](#OpenGLES)。

低级别的运行时与 Objective C 代码通信处于[MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime)。 在此，绑定之上[Foundation](#MonoTouch.Foundation)，CoreFoundation 和[UIKit](#MonoTouch.UIKit)提供。

## <a name="design-principles"></a>设计原则

以下是一些 （这些也适用于 Xamarin.Mac，适用于 OBJECTIVE-C OS X 上的单声道绑定） Xamarin.iOS 绑定我们设计原则：


- 请按照 Framework 设计准则
- 允许开发人员子类 Objective C 类：

  - 从现有类派生
  - 链接到调用基构造函数
  - 重写方法应处理 C# 重写系统

- 子类应适用于 C# 标准结构
- 不会公开到 OBJECTIVE-C 的选择器的开发人员
- 提供一种机制来调用任意 Objective C 库
- 使常见 OBJECTIVE-C 的任务变得简单，和硬 OBJECTIVE-C 的任务可能
- 为 C# 属性公开 OBJECTIVE-C 的属性
- 公开一个强类型的 API:
- 提高类型安全性
- 最大程度减少运行时错误
- 获取返回类型上的 IDE intellisense
- 允许 IDE 弹出文档
- 鼓励 IDE 中浏览的 api:
- 本机 C# 类型：

    - 示例： 而不是公开弱类型化数组如下：
        ```
        NSArray *getViews
        ```
        我们将它们公开具有强类型，如下：
    
        ```
        NSView [] Views { get; set; }
        ```
    
    这使 Visual Studio for Mac 能够浏览 API 时，不要自动完成，并允许的所有`System.Array`操作上返回值可用并允许要参与 LINQ 的返回值

- [NSString 变为字符串](~/ios/internals/api-design/nsstring.md)
- 打开 int 和 uint 应已枚举作为枚举 C# 和 C# 枚举具有 [标志] 特性的参数
- 而不是以非特定于类型的 NSArray 对象将公开为强类型化数组的数组。
- 事件和通知，让用户之间进行选择：

    - 强类型的版本是默认设置
    - 对于高级用例的弱类型化的版本

- 支持 Objective C 委托模式：

    - C# 事件系统
    - 将 C# 委托 (lambda，匿名方法和 System.Delegate) 对 Objective C Api 公开为"块"

### <a name="assemblies"></a>程序集

Xamarin.iOS 包含组成程序集的大量*Xamarin.iOS 配置文件*。 [程序集](~/cross-platform/internals/available-assemblies.md)页提供了更多信息。

### <a name="major-namespaces"></a>主要命名空间 

 <a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

[ObjCRuntime](https://developer.xamarin.com/api/namespace/ObjCRuntime/)命名空间，开发人员可以桥接是世界上之间 C# 和目标。
这是一种新的绑定，专供 iOS，基于从 Cocoa # 和 Gtk # 的体验。

 <a name="MonoTouch.Foundation" />


#### <a name="foundation"></a>Foundation

[Foundation](https://developer.xamarin.com/api/namespace/Foundation/)命名空间提供的基本数据类型旨在与属于 iOS Objective C Foundation framework 互操作和它是面向对象的目标 C.中编程的基础

在 C# 中从目标 C.类的层次结构的 Xamarin.iOS 镜像 例如，Objective C 基类[NSObject](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html)不能从 C# 通过[Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)。

虽然此命名空间提供的基础的 OBJECTIVE-C 的基础类型的绑定，但在少数情况下我们已映射的基础类型到.NET 类型。 例如:

- 而不是处理[NSString](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html)和[NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html)，运行时公开这些 C# 项目称为[字符串](https://developer.xamarin.com/api/type/System.String/)s 的强类型[数组](https://developer.xamarin.com/api/type/System.Array/)整个 sAPI。

- 此处公开各种帮助器 Api，允许开发人员将绑定第三方 OBJECTIVE-C 的 Api，其他 iOS Api 或当前不受 Xamarin.iOS 的 Api。


绑定 Api 的详细信息，请参阅[Xamarin.iOS 绑定生成器](~/cross-platform/macios/binding/binding-types-reference.md)部分。

 <a name="NSObject" />


##### <a name="nsobject"></a>NSObject

[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)类型是 Objective C 的所有绑定的基础。 Xamarin.iOS 类型镜像来自 iOS CocoaTouch Api 的类型的两个类： C 类型 （通常称为 CoreFoundation 类型） 和 Objective C 类型 （这些都派生自 NSObject 类）。

对于每个镜像非托管的类型的类型，它是可能获取本机对象，通过[处理](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/)属性。

Mono 将提供的所有对象，垃圾回收而`Foundation.NSObject`实现[System.IDisposable](https://developer.xamarin.com/api/type/System.IDisposable/)接口。 这意味着您显式释放的任何给定 NSObject 资源而无需等待垃圾回收器 kick 中。 当你使用大量 NSObjects，例如，可能会持有到大型数据块的指针的 UIImages，这是重要的。

如果你的类型需要执行确定性终止，重写[NSObject.Dispose(bool) 方法](https://developer.xamarin.com/api/type/Foundation.NSObject/%2fM%2fDispose)释放的参数"bool 释放"，如果设置为 true 则意味着，因为正在调用 Dispose 方法和用户在对象上的显式调用的 Dispose （)。 如果值为 false，这意味着，你 （bool 释放） 的 Dispose 方法是从终结器线程上调用终结器。 []()

<a name="Categories" />

##### <a name="categories"></a>类别

启动 Xamarin.iOS 8.10 它可从 C# 创建 OBJECTIVE-C 的类别。

这是使用`Category`属性，指定要扩展作为该属性的自变量的类型。 例如，下面的示例将扩展 NSString。

    [Category (typeof (NSString))]

每个类别方法就使用正常的机制用于 Objective C 使用导出方法`Export`属性：

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

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

此示例将本机 toUpper 实例方法添加到 NSString 类，该类可从目标 C.调用

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAudoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

这是有用的一种情况对整个组的基本代码中的类添加方法，例如，这将使所有`UIViewController`实例报告，它们可以旋转：

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```

<a name="PreserveAttribute" />

##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute 时处理应用程序以减少其大小的阶段是用于通知 mtouch – Xamarin.iOS 部署工具 – 若要保留为类型或类型的成员的自定义特性。

未以静态方式链接应用程序的每个成员都受制于删除。 因此，此属性用于将成员的未静态引用，但仍由你的应用程序需要的标记。

例如，如果动态实例化类型，你可能想要保留你的类型的默认构造函数。 如果使用 XML 序列化时，你可能想要保留你的类型的属性。

一种类型，每个成员上或在该类型本身上，你可以应用此属性。 如果你想要保留的整个类型，你可以使用语法 [保留 (AllMembers = true)] 的类型。

 <a name="MonoTouch.UIKit" />


#### <a name="uikit"></a>UIKit

[UIKit](https://developer.xamarin.com/api/namespace/UIKit/)命名空间包含到的所有用户界面组件构成 CocoaTouch C# 类形式的一对一映射。 已修改 API 为遵循 C# 语言中使用的约定。

对常见操作提供了 C# 委托。 请参阅[委托](#Delegates)部分以了解更多信息。

 <a name="OpenGLES" />


#### <a name="opengles"></a>OpenGLES

为了 OpenGLES，我们分发[修改版本](https://developer.xamarin.com/api/namespace/OpenTK/)的[OpenTK](http://www.opentk.com/) API，为已修改为使用 CoreGraphics 数据类型和结构，OpenGL 的面向对象的绑定，以及仅公开可在 iOS 的功能。

OpenGLES 1.1 功能都可通过 ES11.GL 类型，记录[此处](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES11.GL/)类型。

OpenGLES 2.0 功能是可通过记录的 ES20.GL 类型[此处](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES20.GL/)类型。

OpenGLES 3.0 功能是可通过记录的 ES30.GL 类型[此处](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES30.GL/)类型。

 <a name="Binding_Design" />


### <a name="binding-design"></a>绑定设计

Xamarin.iOS 不只是一种绑定到基础 OBJECTIVE-C 的平台。 它所扩展的.NET 类型系统和调度系统到更好的 blend C# 和目标。

就像 P/Invoke 有用工具，用于调用在 Windows 和 Linux 上的本机库或作为 IJW 支持可用于在 Windows 上的 COM 互操作，Xamarin.iOS 扩展运行时以支持绑定 C# 对象与 OBJECTIVE-C 的对象。

讨论在接下来的几节不是用户要创建 Xamarin.iOS 应用程序，但将有助于开发人员需要了解如何操作已完成，并创建更复杂的应用程序时将会帮助它们。


 <a name="Types" />


#### <a name="types"></a>类型

发生意义上，而不是低级别的基础类型，到 C# universe 公开 C# 类型。  这意味着， [API 使用 C#"string"类型，而不是 NSString](~/ios/internals/api-design/nsstring.md)并使用强类型化的 C# 数组而不是公开 NSArray。

一般情况下，在 Xamarin.iOS 和 Xamarin.Mac 设计中，基础`NSArray`对象不公开。 相反，运行时自动转换`NSArray`到强类型化数组的一些`NSObject`类。 因此，Xamarin.iOS 未公开 GetViews 返回 NSArray 类似的弱类型方法：

```csharp
NSArray GetViews ();
```

相反，该绑定将公开一个强类型的返回值，如下：

```csharp
UIView [] GetViews ();
```

有几个方法中公开`NSArray`，你可能想要使用的角用例`NSArray`直接，但其使用的 API 绑定中建议不要这样做。

此外，在**经典 API**而不是公开`CGRect`，`CGPoint`和`CGSize`从 CoreGraphics API 中，替换具有`System.Drawing`实现`RectangleF`， `PointF`和`SizeF`开发人员因为它们将保留现有使用 OpenTK 的 OpenGL 代码。 使用新的 64 位时**统一 API**，应使用 CoreGraphics API。

 <a name="Inheritance" />


#### <a name="inheritance"></a>继承

Xamarin.iOS API 设计允许开发人员相同的方式，它们将会扩展 C# 类型，在派生类中，使用"替代"关键字，以及使用 C#"base"关键字的基实现链接扩展本机 OBJECTIVE-C 的类型。

此设计允许开发人员使用 OBJECTIVE-C 的选择器作为其开发过程中，一部分避免处理，因为整个 OBJECTIVE-C 的系统已包装在 Xamarin.iOS 库。

 <a name="Types_and_Interface_Builder" />


#### <a name="types-and-interface-builder"></a>类型和接口生成器

当你创建是由接口生成器创建的类型的实例的.NET 类时，你需要提供的构造函数的单个`IntPtr`参数。
这被必需绑定具有非托管对象的托管的对象实例。
代码包含单个行，如下：

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```


 <a name="Delegates" />


#### <a name="delegates"></a>委托

Objective C 和 C# 中每种语言具有不同的含义，对于 word 委托。

Objective C 世界中，并且你将找到有关 CocoaTouch 联机文档中，委托是类的通常将响应一组方法的实例。 在于方法并不总是必需，这是非常类似于 C# 接口，但存在差异。

这些委托扮演重要角色 UIKit 和其他 CocoaTouch Api。 它们用于完成各项任务：

-  若要向你的代码 （C# 或 Gtk + 中的事件传送类似） 提供通知。
-  若要实现数据可视化效果控件的模型。
-  驱动器控件的行为。


编程模式旨在最大程度减少创建派生的类，以更改控件的行为。 此解决方案是在设计理念上类似于其他 GUI 工具包做了什么多年： Gtk 的发出信号，Qt 槽、 Winforms 事件、 WPF/Silverlight 事件，依此类推。 若要避免具有数百个接口 （一个用于每个操作），也不需要开发人员来实现他们并不需要太多的方法，Objective C 支持可选的方法定义。 此函数不同于 C# 接口需要实现的所有方法。

在 OBJECTIVE-C 的类中，你将看到使用此编程模式的类公开的属性，通常称为`delegate`，即需要实现必需部分接口和零个或多个可选部分。

在 Xamarin.iOS 提供三个互相排斥的机制，以将绑定到这些委托：

1.  [通过事件](#Events)。
2.  [通过强类型`Delegate`属性](#StrongDelegate)。
3.  [通过松散类型化`WeakDelegate`属性](#WeakDelegate)。


例如，考虑[UIWebView](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html)类。 这将调度到[UIWebViewDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html)实例，该值将赋给[委托](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate)属性。

 <a name="Via_Events" />


##### <a name="via-events"></a>通过事件

对于许多类型，Xamarin.iOS 将自动创建适当的委托，这将转发`UIWebViewDelegate`调用到 C# 事件。 对于 `UIWebView`：

-  [WebViewDidStartLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:)方法映射到[UIWebView.LoadStarted](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadStarted/)事件。
-  [WebViewDidFinishLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:)方法映射到[UIWebView.LoadFinished](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadFinished/)事件。
-  [WebView:didFailLoadWithError](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:)方法映射到[UIWebView.LoadError](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadError/)事件。


例如，此简单程序记录时加载 web 查看开始和结束时间：

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

 <a name="Via_Properties" />


##### <a name="via-properties"></a>通过属性

可能有多个订阅服务器到事件时，事件非常有用。 此外，事件限于情况下，将代码中的没有返回值的情况。

对于其中的代码都应返回值的情况，我们选择改为属性。 这意味着只有一个方法，可以设置在给定时间的对象中。

例如，可以使用此机制可消除键盘上的处理程序的屏幕上`UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

`UITextField`的`ShouldReturn`属性在此情况下将作为参数的委托，以返回 bool 值并确定是否文本字段应执行其他操作正在按下返回按钮。 在我们方法中，我们会返回*true*给调用方，但我们还从屏幕中删除键盘 (发生这种情况是当文本字段调用`ResignFirstResponder`)。


##### <a name="strongly-typed-via-a-delegate-property"></a>强类型通过委托属性

如果您希望使用不是使用事件，你可以提供自己[UIWebViewDelegate](https://developer.xamarin.com/api/type/UIKit.UIWebViewDelegate/)子类并将其分配给[UIWebView.Delegate](https://developer.xamarin.com/api/property/UIKit.UIWebView.Delegate/)属性。 一旦已分配 UIWebView.Delegate，UIWebView 事件调度机制将不再起作用，并且将对应的事件发生时被调用 UIWebViewDelegate 方法。

例如，此简单类型记录的时间来加载 web 视图：

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

上述代码中使用了如下：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

上述操作将创建 UIWebViewer 和它将指示它将消息发送到通知程序，我们创建响应消息的类的实例。

此模式还用于控制的某些控件，例如在 UIWebView 情况下，行为[UIWebView.ShouldStartLoad](https://developer.xamarin.com/api/property/UIKit.UIWebView.ShouldStartLoad/)属性允许`UIWebView`到控件的实例是否`UIWebView`将加载或不页。

模式也用于为几个控件按需提供的数据。 例如， [UITableView](https://developer.xamarin.com/api/type/UIKit.UITableView/)控件是一个功能强大的表呈现控件 – 和查找范围和内容的实例由驱动[UITableViewDataSource](https://developer.xamarin.com/api/type/UIKit.UITableView/DataSource)


@### 松散类型化通过 WeakDelegate 属性

除了外的强类型的属性，我们还允许开发人员如果需要以不同方式绑定操作弱类型化的委托。
强类型化 everywhere`Delegate`属性公开在 Xamarin.iOS 的绑定，相应`WeakDelegate`属性名称还公开。

使用时`WeakDelegate`，你将负责正确修饰类 using[导出](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)特性来指定选择器。 例如:

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

请注意该一次`WeakDelegate`属性已分配，`Delegate`不会使用属性。 此外，如果你想 [导出] 继承基类中实现方法，你必须进行了它的公共方法。


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>映射的 OBJECTIVE-C 的委托将模式与 c&#35;

当你看到如下所示的 OBJECTIVE-C 的示例：

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

这会指示要创建和构造类"SomethingDelegate"的实例并将值分配给 foo 变量的委托属性的语言。 此机制受 Xamarin.iOS 和 C# 语法是：

```csharp
foo.Delegate = new SomethingDelegate ();
```

在 Xamarin.iOS 我们提供了映射到 Objective C 的强类型类委托类。 若要使用它们，你将是子类化，而重写由 Xamarin.iOS 的实现定义的方法。 有关它们如何工作的详细信息，请参阅部分"模型"下面。


##### <a name="mapping-delegates-to-c35"></a>将委托映射到 c&#35;

UIKit 在两种形式中通常使用 OBJECTIVE-C 的委托。

第一种形式提供给组件的模型的接口。 例如，作为为视图中，如用于列表视图数据存储设施按需提供数据的机制。  在这些情况下，应始终创建适当的类的实例并将变量分配。

在以下示例中，我们提供了`UIPickerView`与使用字符串的模型的实现：

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

第二种形式是提供的事件通知。 在这些情况下，尽管我们仍公开上述，窗体中的 API，但我们还提供 C# 事件，它应该是简单要用于快速操作和集成的匿名委托和 C# 中的 lambda 表达式。

例如，你可以订阅`UIAccelerometer`事件：

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

Where 它们很有用，但程序员必须选择一个或另一个，可以使用两个选项。 如果你创建的强类型的响应程序/委托实例，并将其分配，C# 事件将不会有效。 如果你使用 C# 事件，则绝不会调用响应方/委托类中的方法。

前面的示例使用`UIWebView`可以使用如下的 C# 3.0 lambda 编写：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>对事件作出响应

在 Objective C 代码中，有时多个控件和提供程序的信息的多个控件的事件处理程序将托管在同一个类。 这是可能的因为类响应消息，以及，只要类响应消息，可以将对象链接在一起。

为以前详细 Xamarin.iOS 支持这两个 C# 基于事件的编程模型，和 Objective C 委托模式，其中你可以创建的新类实现委托和替代所需的方法。

它还可支持 OBJECTIVE-C 的模式对多个不同操作的响应者的所有托管位置类的同一实例中。 若要通过执行此操作，你将需要使用 Xamarin.iOS 绑定的低级别的功能。

例如，如果您希望您的类以响应同时`UITextFieldDelegate.textFieldShouldClear`： 消息和`UIWebViewDelegate.webViewDidStartLoad`： 在类的同一个实例，你必须使用 [导出] 特性声明：

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

C# 名称的方法并不重要;所有重要的是传递给 [导出] 特性的字符串。

在使用此样式编程，请确保 C# 参数与匹配的运行时引擎将传递的实际类型。


#### <a name="models"></a>模型

在 UIKit 存储设施，或响应程序使用的帮助器类实现中，这些内容通常称为 Objective C 代码中的代理，而和它们并作为协议实现。

Objective C 协议就像接口，但它们支持可选方法 – 不是所有方法，即需要为要使用的协议实现。

有两种方法的实现模型。 你可以手动实现，或使用现有的强类型的定义。


当你尝试实现一个尚未绑定通过 Xamarin.iOS 的类时，手动机制是必需的。 它是非常轻松地完成：

-  标记注册到运行时类
-  你想要重写每个方法应用的实际选择器名称的 [导出] 属性
-  实例化类，并将其传递。

例如，以下可选的方法之一在中实现 UIApplicationDelegate 协议定义：

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

OBJECTIVE-C 的选择器名称 ("applicationDidFinishLaunching:") 使用导出特性声明并类注册的`[Register]`属性。

Xamarin.iOS 提供强类型的声明，可供使用，不需要手动绑定。 若要支持此编程模型，Xamarin.iOS 运行时，请在类声明中支持 [型号] 属性。 这可告知其应不连接在类中，所有方法除非方法都是运行时显式实现。

这意味着，在 UIKit，编写使用可选方法表示一种协议的类如下：

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

如果你想要实现模型仅实现的一些方法，你所要做是重写你感兴趣，并忽略其他方法的方法。 运行时仅将挂钩覆盖方法，不向 Objective C 领域的原始方法。

与前面的手动示例的等价内容是：

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

优点是没有无需深入探究 Objective C 标头文件，以了解选择器、 的自变量或对 C# 中，映射的类型，并且，你会得到 intellisense 从 Visual Studio for Mac，以及强类型


#### <a name="xib-outlets-and-c35"></a>XIB 插座和 c&#35;

> [!IMPORTANT]
> 使用 XIB 文件时，本部分将介绍与容器的 IDE 集成。 在时使用 for iOS Xamarin 设计器，这所有替换输入下的一个名称**标识 > 名称**IDE，如下所示的属性部分中：
>
> [![](images/designeroutlet.png "在 iOS 设计器中输入的项名称")](images/designeroutlet.png)
>
>有关 iOS 设计器的详细信息，请查看[iOS 设计器简介](~/ios/user-interface/designer/introduction.md#how-it-works)文档。

这是如何使用 C# 集成插座的低级别说明，供的 Xamarin.iOS 的高级用户。 使用 Visual Studio for Mac 映射完成时自动在后台使用为您生成此航班的代码。

当设计用户界面使用接口生成器时，你仅将设计应用程序的外观，并且将建立一些默认连接。 如果你想要以编程方式提取信息、 更改在运行时控件的行为或修改在运行时控制，则需要将某些控件绑定到你的托管代码。

在几个步骤完成此操作：

1.  添加**outlet 声明**到你**文件的所有者**。
1.  连接到控件**文件的所有者**。
1.  存储 UI 以及到 XIB/NIB 文件连接。
1.  加载 NIB 文件在运行时。
1.  访问 outlet 变量。


用于构建具有接口生成器接口 Apple 的文档中介绍 (3) 通过步骤 (1)。

当使用 Xamarin.iOS，你的应用程序将需要创建派生自 UIViewController 的类。 它实现它如下：

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

然后从 NIB 文件加载你 ViewController，你执行此操作：

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

这将加载 NIB 从用户界面。 现在，若要访问的容器，它是必需的以通知运行时，我们想要对其进行访问。 若要这样做，`UIViewController`子类需要声明的属性并对其进行批注具有 [连接] 属性。 如：

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

此属性实现是实际提取和存储的实际的本机类型的值。

不需要担心这适用于 Mac 和 InterfaceBuilder 使用 Visual Studio 时。 适用于 Mac 的 visual Studio 自动镜像与代码编译为你项目的一部分的分部类中的所有声明的 outlet。

#### <a name="selectors"></a>选择器

Objective C 编程的核心概念是选择器。 通常将出现在要求您传递一个选择器，或需要你的代码以响应选择器的 Api。

在 C# 中创建新的选择器是非常简单 – 只需创建的新实例`ObjCRuntime.Selector`类并在需要它的 API 中的任何位置使用结果。 例如:

```csharp
var selector_add = new Selector ("add:plus:");
```

对于 C# 方法响应的选择器调用，则它必须继承自`NSObject`必须以使用选择器名称修饰类型和 C# 方法`[Export]`属性。 例如:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

请注意该选择器名称**必须**完全匹配，包括所有中间和尾随冒号 (":")，如果存在。

#### <a name="nsobject-constructors"></a>NSObject 构造函数

大多数类中派生自的 Xamarin.iOS`NSObject`将公开特定于功能的对象的构造函数，但它们还将公开各种不是显而易见的构造函数。

构造函数的用法如下：

```csharp
public Foo (IntPtr handle)
```

此构造函数用于在运行时需要将你的类映射到非托管类时实例化您的类。 加载 XIB/NIB 文件时，将发生这种情况。  此时，Objective C 运行时将已创建一个对象在非托管领域中，并且将调用此构造函数来初始化托管的一方。

通常，你需要做是调用基构造函数使用句柄参数，并在正文中，执行任何所需的初始化。

```csharp
public Foo ()
```

这是默认构造函数的类和 Xamarin.iOS 提供类，这在此期间，将初始化 Foundation.NSObject 类和的所有类，并在结束时，链接这到 Objective C`init`类上的方法。

```csharp
public Foo (NSObjectFlag x)
```

此构造函数用于初始化该实例，但阻止代码在最终调用 Objective C"init"方法。 通常使用此命令时已注册以进行初始化 (当你使用`[Export]`上您的构造函数) 或当你具有已完成通过另一个平均值你初始化。

```csharp
public Foo (NSCoder coder)
```

此构造函数提供的用例，该对象从 NSCoding 实例初始化的位置。 有关详细信息，请参阅 Apple 的[存档和序列化编程指南。](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>异常

Xamarin.iOS API 设计不会为 C# 异常引发 Objective C 异常。 没有垃圾回收将首先发送到 Objective C world 强制设计和不断无效的数据之前必须生成的任何异常生成的绑定本身传递到 Objective C 世界。

#### <a name="notifications"></a>通知

在 iOS 和 OS X 中，开发人员可以订阅广播由基础平台的通知。 这可通过使用`NSNotificationCenter.DefaultCenter.AddObserver`方法。 `AddObserver`方法采用两个参数之一是你想要订阅的通知; 另一种是引发通知时要调用的方法。

在 Xamarin.iOS 和 Xamarin.Mac，各种通知的密钥将托管在触发通知的类。 例如，通知引发`UIMenuController`作为托管`static NSString`中的属性`UIMenuController`"通知"的名称结尾的类。

### <a name="memory-management"></a>内存管理

Xamarin.iOS 具有将会负责为你释放资源，它们将不再使用时的垃圾回收器。 除了垃圾回收器中，所有对象都派生自`NSObject`实现`System.IDisposable`接口。

#### <a name="nsobject-and-idisposable"></a>NSObject 和 IDisposable

公开`IDisposable`接口是为开发人员提供帮助中释放可能封装较大的内存块的对象的一种简便方式 (例如，`UIImage`可能如下所示只是一个无害的指针，但无法指向 2 兆字节映像) 和其他重要和有限的资源 （如视频解码缓冲区）。

NSObject 实现 IDisposable 接口以及[.NET 释放模式](http://msdn.microsoft.com/en-us/library/fs2xkftw.aspx)。 这样，开发人员该子类重写释放行为并释放其自己的资源按需 NSObject。 例如，考虑解决多个映像保留此视图控制器：

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

当释放的托管的对象时，它不再有用。 你仍可以对这些对象的引用，但该对象是出于何种目的此时无效。 一些.NET Api 通过引发 ObjectDisposedException，如果你尝试访问任何方法对已释放的对象，例如确保这一点：

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

即使你仍可以访问变量"映像"，实际上是一个无效的引用和不再指向保存图像的 OBJECTIVE-C 的对象。

但是，释放 C# 中的对象并不意味着一定销毁对象。 您只是释放该 C# 了对对象的引用。 很可能 Cocoa 环境可能已保存围绕供自己使用的引用。 例如，如果将 UIImageView 的映像属性设置为一个映像，然后释放该映像，则基础 UIImageView 将好像执行它自己的引用并将保持对此对象的引用，直到它完成后使用它。

#### <a name="when-to-call-dispose"></a>何时调用 Dispose

当你需要 Mono 中获取 rid 的你的对象时，应调用释放。 Mono 程序不知道你 NSObject 实际还保持对重要资源如内存或信息池的引用时，可能用例。 在这些情况下，应调用释放，以立即释放到内存中，而不是等待 Mono 执行垃圾回收周期的引用。

在内部，当 Mono 创建[NSString 引用从 C# 字符串](~/ios/internals/api-design/nsstring.md)，它将释放它们立即减少的垃圾回收器必须执行的工作。 将运行更少的对象以处理，得越快 GC。

#### <a name="when-to-keep-references-to-objects"></a>何时保留对对象的引用

自动内存管理器的一个端作用是，只要有没有对其的引用，将获取 GC rid 的未使用的对象。 这有时可能会有令人惊讶副作用，例如，如果创建一个本地变量以保存你的顶层视图控制器，或你的顶级窗口，然后让那些项消失后面备份。

请勿在你的对象保留的引用中你静态或实例变量，如果 Mono 将不假思索地调用 dispose （） 方法，并将会发布对对象的引用。 因为这可能是仅未完成的引用，则 Objective C 运行时将为你销毁对象。

## <a name="related-links"></a>相关链接

- [绑定字段](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
