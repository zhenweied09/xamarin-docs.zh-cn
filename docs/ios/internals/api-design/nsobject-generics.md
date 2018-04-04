---
title: 泛型的 NSObject 的子类
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 89df751d74b9b54ae8138d2e1b24c61d82c3cac8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="generic-subclasses-of-nsobject"></a>泛型的 NSObject 的子类

## <a name="using-generics-with-nsobjects"></a>将泛型与 NSObjects

启动 Xamarin.iOS 7.2.1 后，可以使用泛型的子类中`NSObject`(例如[UIView](https://developer.xamarin.com/api/type/UIKit.UIView/))。

你现在可以创建泛型类，如下所示：

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

由于对象该子类`NSObject`注册与 Objective C 运行时有一些限制，并可能采用的泛型子类`NSObject`类型。
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>NSObject 个泛型子类注意事项

本文档详细介绍中对的泛型子类的支持有限的限制`NSObjects`Xamarin.iOS 7.2.1 引入。
    
### <a name="generic-type-arguments-in-member-signatures"></a>对于成员签名中的泛型类型参数

在公开给 Objective C 的成员签名中的所有泛型类型参数必须具有`NSObject`约束。

**良好**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**： 泛型类型参数是`NSObject`，因此的选择器签名`myMethod:`可以安全地公开给 Objective C (它将始终为`NSObject`或它的子类)。

**错误**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**： 不能为导出 Objective C 代码可以调用，因为签名将与不同，具体取决于泛型类型的确切类型的成员创建 Objective C 签名`T`。

**良好**:

```csharp
class Generic<T> : NSObject
{
    T storage;

    [Export ("myMethod:")]
    public void MyMethod (NSObject value)
    {
    }
}
```

**原因**： 可以只要它们不会导出的成员签名的一部分不受约束泛型类型参数。

**良好**:

```csharp
class Generic<T, U> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
        Console.WriteLine (typeof (U));
    }
}
```

**原因**: `T` Objective C 中的参数导出`MyMethod`约束为不可`NSObject`，不受约束的类型`U`不是签名的一部分。
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>从 Objective C 的泛型类型的实例化

不允许从 Objective C 的泛型类型的实例化。 这通常发生在 xib 中使用托管的类型时。

请考虑此类定义中，公开的构造函数的`IntPtr`（Xamarin.iOS 构造的本机 OBJECTIVE-C 的实例中的 C# 对象的方式）：
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

上述构造时没有什么问题，在运行时，这将引发异常时，如果 Objective C 尝试创建它的实例。

这是因为 Objective C 的泛型类型，没有概念，它不能指定要创建的确切的泛型类型，会发生情况。

通过创建泛型类型的一个专门的子类，可以解决此问题。   例如：
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

现在没有歧义，类`GenericUIView`可以在 xibs 中使用。

## <a name="no-support-for-generic-methods"></a>不支持泛型方法

### <a name="generic-methods-are-not-allowed"></a>不允许使用泛型方法。

下面的代码将不进行编译：

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**原因**： 这不允许的因为 Xamarin.iOS 不知道哪种类型为类型自变量使用`T`从 Objective C 中时调用的方法。

一种替代方法是创建专用的方法，并改为导出的：

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyUIViewMethod (UIView argument)
    {
        MyMethod<UIView> (argument);
    }
    public void MyMethod<T> (T argument)
    {
    }
}
```

### <a name="no-exported-static-members-allowed"></a>允许任何导出静态成员

如果承载在泛型的子类，可以不公开到 OBJECTIVE-C 的静态成员`NSObject`。

不受支持的方案的示例：

```csharp
class Generic<T> : NSObject where T : NSObject
{
    [Export ("myMethod:")]
    public static void MyMethod ()
    {
    }

    [Export ("myProperty")]
    public static T MyProperty { get; set; }
}
```

**原因：**就像泛型方法，需要能够知道使用什么类型为泛型类型参数 t。 使用该 Xamarin.iOS 运行时

对实例使用的实例本身的成员 (因为永远不会将实例泛型<T>，它将始终为泛型<SomeSpecificClass>)，但对于静态成员不存在此信息。

请注意，这适合即使问题的成员不以任何方式使用类型参数 T。

在这种情况下是创建一个专门的子类替代项：

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIVIewProperty {
        get { return MyProperty; }
        set { MyProperty = value; }
    }
}

class Generic<T> : NSObject where T : NSObject
{
    public static void MyMethod () {}
    public static T MyProperty { get; set; }
}
```

### <a name="requires-new-static-registrar"></a>需要新的静态注册机构

泛型支持需要新[注册系统](~/ios/internals/registrar.md)。

如果你尝试使用旧遇到 （中添加不会生成正确的代码，从而导致未定义的行为） 的泛型类型时，旧注册系统将显示警告。
    
## <a name="performance"></a>性能

静态注册机构不能在生成时解决泛型类型中的导出的成员，像通常那样，它具有要查找在运行时。 这意味着调用从 Objective C 这样的方法是调用从非泛型类的成员比稍慢。

