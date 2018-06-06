---
title: 事件、 协议和在 Xamarin.iOS 的委托
description: 本文档介绍如何使用事件，协议，并在 Xamarin.iOS 委托。 这些基本的概念是无处不在 Xamarin.iOS 开发中。
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d0e4c23bffe689c9218da2f43b97d98f348513ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784005"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>事件、 协议和在 Xamarin.iOS 的委托

Xamarin.iOS 使用控件公开大多数用户交互的事件。
传统的.NET 应用程序一样，Xamarin.iOS 应用程序在基本相同的方法中使用这些事件。 例如，Xamarin.iOS UIButton 类具有调用 TouchUpInside 事件的事件，并且只需像此类和事件是在.NET 应用程序中使用此事件。

此.NET 方法时，除了 Xamarin.iOS 公开可用于更复杂的交互和数据绑定的另一个模型。 此方法使用 Apple 调用委托和协议。 委托是在概念上类似于 C# 中的委托，但而不是定义和调用单个方法，Objective C 中的委托是符合协议的整个类。 一种协议具有类似于在 C# 中，一个接口，只不过其方法可以是可选。 例如，若要填充数据 UITableView，将创建一个委托类以实现在 UITableView 将调用来填充本身 UITableViewDataSource 协议中定义的方法。

在本文中你将了解有关这些主题，为你提供坚实的基础处理 Xamarin.iOS 中的回调方案包括：

-  **事件**– 使用.NET UIKit 控件事件。
-  **协议**– 学习什么协议是否和如何使用它们，并创建一个示例为地图批注提供数据。
-  **委托**– 学习有关通过扩展映射示例以处理用户交互，包括批注，然后学习强弱委托以及何时使用每个之间的差异的 OBJECTIVE-C 的委托。

为了说明协议和委托，我们将构建的简单的映射应用程序向映射添加批注，如下所示：

 [![](delegates-protocols-and-events-images/01-map.png "向映射添加批注一个简单的映射应用程序的示例")](delegates-protocols-and-events-images/01-map.png#lightbox) [ ![ ](delegates-protocols-and-events-images/04-annotation-with-callout.png "示例批注添加到图")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

有关此应用程序之前, 让我们开始吧看下 UIKit.NET 事件。

<a name=".NET_Events_with_UIKit" />

## <a name="net-events-with-uikit"></a>UIKit.NET 事件

Xamarin.iOS 公开 UIKit 控件上的.NET 事件。 例如，UIButton 具有 TouchUpInside 事件，你处理像平时在.NET 中，如下面使用 C# lambda 表达式的代码中所示：

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```
你可以实现这与 C# 2.0 样式匿名方法如下所示：

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

前面的代码是有线连接上 UIViewContoller 的 ViewDidLoad 方法中。 AButton 变量引用一个按钮，可以添加 iOS 设计器中或使用代码。 下图显示了该按钮，因为在 iOS 设计器中，来自附带本文的示例中添加：

 [![](delegates-protocols-and-events-images/02-interface-builder-outlet.png "在 iOS 设计器中添加按钮")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin.iOS 还支持连接到发生与控件交互的代码的目标操作样式。 若要创建用于 Hello 按钮的目标操作，双击它在 iOS 设计器中。 将显示 UIViewController 的代码隐藏文件，并且开发人员将需要选择一个位置来插入连接的方法：

 [![](delegates-protocols-and-events-images/03-interface-builder-action.png "UIViewControllers 代码隐藏文件")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

选择位置后，新的方法是创建和有线向上到控件。 在下面的示例中，将消息写入到控制台这是单击该按钮：

 [![](delegates-protocols-and-events-images/05-interface-builder-action.png "单击该按钮时，将向控制台写入一条消息")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

有关 iOS 目标操作模式的更多详细信息，请参阅的目标操作部分"[适用于 iOS 的核心应用程序能力](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)"在 Apple 的 iOS 开发人员库中。

有关如何使用 Xamarin.iOS iOS 设计器的详细信息，请参阅" [iOS 设计器概述](~/ios/user-interface/designer/index.md)"文档。

 <a name="Events" />


## <a name="events"></a>事件

如果你想要截获来自 UIControl 事件，则必须一系列选项： 从使用 C# lambda 和委托功能到使用低级别的 OBJECTIVE-C 的 Api。

以下部分说明如何将捕获的按钮，具体取决于你需要多少控件上的 TouchDown 事件。

 <a name="C#_Style" />


## <a name="c-style"></a>C# 样式

使用委托的语法：

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {    
    Console.WriteLine ("Touched");
};
```

如果你喜欢的是 lambda:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

如果你想要具有多个按钮使用的同一处理程序共享相同的代码：

```csharp
void handler (object sender, EventArgs args)
{
   if (sender == button1)
      Console.WriteLine ("button1");
   else
      Console.WriteLine ("some other button");
}

button1.TouchDown += handler;
button2.TouchDown += handler;
```

<a name="Monitoring_more_than_one_kind_of_Event" />


## <a name="monitoring-more-than-one-kind-of-event"></a>监视多个一种类型的事件

UIControlEvent 标志的 C# 事件具有到各个标志的一对一映射。 如果想要具有相同的代码段处理两个或多个事件，请使用`UIControl.AddTarget`方法：

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

使用 lambda 语法：

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

如果你需要使用 OBJECTIVE-C，如挂钩到一个特定对象实例和调用特定的选择器的低级功能：

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

请注意，如果继承的基类中实现的实例方法，它必须是公共方法。

 <a name="Protocols" />


## <a name="protocols"></a>协议

一种协议是一种 Objective C 语言功能，提供的方法声明列表。 它的作用类似于在 C# 中，主要区别接口，是一种协议可以具有可选方法。 如果采用一种协议的类不实现它们不会调用可选方法。 此外，Objective C 中的单个类可以实现多个协议，就像 C# 类可以实现多个接口。

Apple 使用整个 iOS 协议定义协定的类采用，而抽象化从调用方，因此操作系统一样的 C# 接口实现的类。 使用协议不管是在非委托方案 (如与`MKAnnotation`示例所示下一步)，和使用委托 （按所提供更高版本在此文档中，在委托部分）。

 <a name="Protocols_with_Monotouch" />


### <a name="protocols-with-xamarinios"></a>使用 Xamarin.ios 的协议

让我们看看一个示例使用从 Xamarin.iOS OBJECTIVE-C 的协议。 对于此示例中，我们将使用`MKAnnotation`协议，它是一部分的`MapKit`framework。 `MKAnnotation` 是协议，允许采用它提供有关可以添加到图的批注信息的任何对象。 例如，一个对象，实现`MKAnnotation`提供批注和与其相关联的标题的位置。

这种方式，`MKAnnotation`协议用于提供附带批注的相关数据。 从中采用的对象的数据生成本身的批注的实际视图`MKAnnotation`协议。 例如，当用户点击在批注上 （如下面的屏幕截图中所示） 时出现的标注的文本来自`Title`的类中实现协议的属性：

 [![](delegates-protocols-and-events-images/04-annotation-with-callout.png "当用户点击在批注上的标注的示例文本")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

在下一步的部分中，协议深入了解，所述 Xamarin.iOS 会将协议绑定到抽象类。 有关`MKAnnotation`协议，名为绑定的 C# 类`MKAnnotation`以模拟该协议，和它的名称为的一个子类`NSObject`，CocoaTouch 根基类。 协议要求 getter 和 setter 的坐标; 实现但是，标题和副标题是可选的。 因此，在`MKAnnotation`类，`Coordinate`属性是*抽象*，要求在实现和`Title`和`Subtitle`属性标记*虚拟*使其可选的如下所示：

```csharp
[Register ("MKAnnotation"), Model ]
public abstract class MKAnnotation : NSObject
{
    public abstract CLLocationCoordinate2D Coordinate
    {
        [Export ("coordinate")]
        get;
        [Export ("setCoordinate:")]
        set;
    }

    public virtual string Title
    {
        [Export ("title")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }

    public virtual string Subtitle
    {
        [Export ("subtitle")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }
...
}
```

任何类均可通过只需从派生提供批注数据`MKAnnotation`，只要至少`Coordinate`属性实现。 例如，下面是示例类构造函数中采用坐标或返回标题字符串：

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string _title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        _title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return _title;
        }
    }
}
```

通过绑定到该协议，任何类子类`MKAnnotation`可以提供创建批注的视图时，映射将使用的相关数据。 若要向地图添加批注，只需调用`AddAnnotation`方法`MKMapView`实例，如下面的代码中所示：

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
new CLLocationCoordinate2D (42.3467512, -71.0969456);

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

这里的映射变量是的一个实例`MKMapView`，即表示本身的映射的类。 `MKMapView`将使用`Coordinate`数据派生自`SampleMapAnnotation`实例以放置在地图上的批注视图。

`MKAnnotation`协议提供一组已知的跨实现它的任何对象的功能，而无需使用者 （在此情况下图） 无需了解有关实现细节。 这简化了向地图添加各种可能的批注。

 <a name="Protocols_Deep_Dive" />


### <a name="protocols-deep-dive"></a>协议深入了解

由于 C# 接口不支持可选方法，Xamarin.iOS 将映射到抽象类的协议。 因此，采用 Objective C 中的协议是中完成 Xamarin.iOS 通过从绑定到协议抽象类派生并实现所需的方法。 这些方法将作为类中的抽象方法公开。 从协议的可选方法将绑定到 C# 类的虚拟方法。

例如，下面是部分`UITableViewDataSource`协议绑定中的 Xamarin.iOS 作为：

```csharp
public abstract class UITableViewDataSource : NSObject
{
    [Export ("tableView:cellForRowAtIndexPath:")]
    public abstract UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath);
    [Export ("numberOfSectionsInTableView:")]
    public virtual int NumberOfSections (UITableView tableView){...}
...
}
```

请注意，此类为抽象。 Xamarin.iOS 使类抽象以支持在协议中的可选/所需的方法。
但是，与不同的是 OBJECTIVE-C 的协议 （或 C# 接口），C# 类不支持多重继承。 这会影响使用协议，因而通常会导致嵌套类的 C# 代码的设计。 更多有关此问题是在后面介绍本文档中，在委托部分。

 `GetCell(…)` 是一个抽象方法，绑定到 Objective C*选择器*， `tableView:cellForRowAtIndexPath:`，这是所需的方法的`UITableViewDataSource`协议。 选择器是方法名称的 OBJECTIVE-C 的术语。 若要强制所需的方法，Xamarin.iOS 将它声明为抽象。 另一种方法， `NumberOfSections(…)`，绑定到`numberOfSectionsInTableview:`。 此方法是可选的协议，因此 Xamarin.iOS 声明为虚拟的它使其成为可选若要在 C# 中重写。

Xamarin.iOS 负责为你的所有 iOS 绑定。 但是，如果您需要手动将一种协议绑定 Objective C 中，你可以这样的修饰类`ExportAttribute`。 这是使用 Xamarin.iOS 本身的相同方法。

有关如何在 Xamarin.iOS 绑定 Objective C 类型的详细信息，请参阅文章[绑定 Objective C 类型](~/ios/platform/binding-objective-c/index.md)。

我们尚未通过使用协议，但。 它们是也用于在 iOS 中作为基础 OBJECTIVE-C 的委托，即下一步部分的主题。

 <a name="Delegates" />


## <a name="delegates"></a>委托

iOS 使用 OBJECTIVE-C 的委托来实现的委派模式，在其中一个对象将传递工作到另一个。 执行工作的对象是第一个对象的委托。 一个对象告知其委托来完成工作，通过发送其消息后发生某些事件。 Objective C 中发送类似下面这样的消息是功能上等效于在 C# 中调用的方法。 委托实现这些调用，响应中的方法，并因此提供给应用程序的功能。

委托使您能够扩展类的行为而无需创建子类。 一个类当回拨时到另一个重要的操作发生后，在 iOS 中的应用程序通常使用委托。 例如，`MKMapView`类返回到其委托的调用，当用户点击上一个代码图，批注为委托类的作者提供机会在应用程序内响应。 你可以通过这种类型的更高版本中本文中，在示例中使用的委托使用情况的示例工作 Xamarin.iOS 的委托。

此时，你可能想知道如何类确定何种方法来在其委托上调用。 这是在其中使用协议的另一个位置。 通常情况下，可用于委托的方法来自它们采用的协议。

 <a name="How_Protocols_are_used_with_Delegates" />


### <a name="how-protocols-are-used-with-delegates"></a>如何与委托一起使用的协议

我们已了解之前如何使用协议来支持向地图添加批注。
协议还用于提供一组已知的类来调用某些事件发生时，如用户点击地图上批注后，或选择表中的单元格后的方法。 实现这些方法的类称为调用它们的类的委托。

支持委派的类执行此操作通过公开的类实现委托分配到委托属性。 委托实现的方法将取决于特定的委托采用的协议。 有关`UITableView`实现的方法，`UITableViewDelegate`协议，为`UIAccelerometer`方法，你会实施`UIAccelerometerDelegate`，依次类推对于整个 iOS 的情况下，你将想到任何其他类公开的委托。

`MKMapView`我们在我们前面的示例中看到的类还具有一个名为委托，它将调用属性的各种事件发生之后。 委托`MKMapView`属于类型`MKMapViewDelegate`。
你将使用此很快中示例响应批注后选中该选项，但首先让我们讨论强弱委托之间的差异。

 <a name="Strong_Delegates_vs._Weak_Delegates" />


### <a name="strong-delegates-vs-weak-delegates"></a>强委托 vs。弱委托

到目前为止我们介绍了委托是强委托，这意味着它们都强类型。 Xamarin.iOS 绑定随附在 iOS 中每个委托协议的强类型类。 但是，iOS 还具有弱委托的概念。 而不是子类化的类为特定的委托绑定到 OBJECTIVE-C 的协议，iOS 还可以选择自己绑定的协议方法在你喜欢的任何类中派生自 NSObject，修饰 ExportAttribute，与你的方法，然后提供适当的选择器。
采用此方法时，但你会将你的类的一个实例分配给 WeakDelegate 属性而不是委托属性。 弱委托可灵活地采用不同的继承层次结构下你委托类。 让我们看一下使用强和弱委托的 Xamarin.iOS 示例。

 <a name="Example_using_a_Delegate_with_Xamarin.iOS" />


### <a name="example-using-a-delegate-with-xamarinios"></a>委托使用 Xamarin.iOS 的示例

若要执行的代码以响应用户点击批注在我们的示例，我们可以子类化`MKMapViewDelegate`并分配到实例`MKMapView`的`Delegate`属性。 `MKMapViewDelegate`协议仅包含可选方法。
因此，所有方法都是虚拟，绑定到此协议中 Xamarin.iOS`MKMapViewDelegate`类。 当用户选择批注，`MKMapView`实例将发送`mapView:didSelectAnnotationView:`其委托到的消息。 若要处理这在 Xamarin.iOS 中，我们需要重写`DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)`如下 MKMapViewDelegate 子类中的方法：

```csharp
public class SampleMapDelegate : MKMapViewDelegate
{
    public override void DidSelectAnnotationView (
        MKMapView mapView, MKAnnotationView annotationView)
    {
        var sampleAnnotation =
            annotationView.Annotation as SampleMapAnnotation;

        if (sampleAnnotation != null) {

            //demo accessing the coordinate of the selected annotation to
            //zoom in on it
            mapView.Region = MKCoordinateRegion.FromDistance(
                sampleAnnotation.Coordinate, 500, 500);

            //demo accessing the title of the selected annotation
            Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
        }
    }
}
```

作为包含的控制器中的嵌套类实现上面所示的 SampleMapDelegate 类`MKMapView`实例。 在目标-C 中，你通常将看到采用直接在类内的多个协议的控制器。 但是，由于协议绑定到在 Xamarin.iOS 的类，实现强类型的委托的类是嵌套类作为通常包含。

与委托就地类实现中，你只需以实例化委托在控制器中的一个实例并将其分配给`MKMapView`的`Delegate`属性如下所示：

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    SampleMapDelegate _mapDelegate;
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //set the map's delegate
        _mapDelegate = new SampleMapDelegate ();
        map.Delegate = _mapDelegate;
        ...
    }
    class SampleMapDelegate : MKMapViewDelegate
    {
        ...
    }
}
```

若要使用弱委托完成同样的操作，你需要自行 bind 方法中任何派生自的类`NSObject`和将其分配给`WeakDelegate`属性`MKMapView`。 由于`UIViewController`类最终都派生自`NSObject`（如 CocoaTouch 中每个 Objective C 类），我们只需可以实现的方法绑定到`mapView:didSelectAnnotationView:`直接在控制器中，并将分配到的控制器`MKMapView`的`WeakDelegate`，避免所需的额外嵌套类。 下面的代码演示此方法：

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        //assign the controller directly to the weak delegate
        map.WeakDelegate = this;
    }
    //bind to the Objective-C selector mapView:didSelectAnnotationView:
    [Export("mapView:didSelectAnnotationView:")]
    public void DidSelectAnnotationView (MKMapView mapView,
        MKAnnotationView annotationView)
    {
        ...
    }
}
```

当运行此代码时，应用程序的行为完全相同的强类型的委托版本运行时。 此代码的好处是类的弱委托不需要额外时，我们使用了强类型的委托创建的创建。 但是，这会降低类型安全。 如果你是选择器传递到中犯错`ExportAttribute`，直到运行时不会找出。

 <a name="Events_and_Delegates" />


### <a name="events-and-delegates"></a>事件和委托

委托用于在 iOS 类似于.NET 使用事件的方法中的回调。 若要使 iOS Api 和它们使用 OBJECTIVE-C 的委托的方法看起来更像.NET，Xamarin.iOS 公开.NET 事件在其中在 iOS 中使用委托的多个位置。

例如，早期的实现其中`MKMapViewDelegate`是否响应了所选的批注无法还在实现 Xamarin.iOS 通过使用.NET 事件。 在这种情况下，将在定义事件`MKMapView`和调用`DidSelectAnnotationView`。 它将具有`EventArgs`子类的类型`MKMapViewAnnotationEventsArgs`。 `View`属性`MKMapViewAnnotationEventsArgs`将为您提供对批注视图的引用，无法继续使用同一个实现其从你 d 更早版本，为所述此处：

```csharp
map.DidSelectAnnotationView += (s,e) => {
    var sampleAnnotation = e.View.Annotation as SampleMapAnnotation;
    if (sampleAnnotation != null) {
        //demo accessing the coordinate of the selected annotation to
        //zoom in on it
        mapView.Region = MKCoordinateRegion.FromDistance (
            sampleAnnotation.Coordinate, 500, 500);

        //demo accessing the title of the selected annotation
        Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
    }
};
```

 <a name="Summary" />


## <a name="summary"></a>总结

本文介绍如何在 Xamarin.iOS 中使用事件、 协议和委托。 我们已了解如何 Xamarin.iOS 公开控件的正常.NET 样式事件。
接下来我们所了解的有关 OBJECTIVE-C 的协议，包括如何，它们是不同于 C# 接口和 Xamarin.iOS 如何使用它们。 最后，我们探讨了 Xamarin.iOS 角度 OBJECTIVE-C 的委托。 我们已了解如何 Xamarin.iOS 同时支持强和弱类型化的委托，以及如何将绑定.NET 事件委托方法。


## <a name="related-links"></a>相关链接

- [协议、 委托和事件 （示例）](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Hello，iOS](~/ios/get-started/hello-ios/index.md)
- [绑定 Objective C 类型](~/ios/platform/binding-objective-c/index.md)
- [Objective C 编程语言](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [设计在 Xcode 4 中的用户界面](http://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [适用于 iOS 的核心应用程序能力](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
