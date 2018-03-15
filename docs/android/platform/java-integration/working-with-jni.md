---
title: "使用 JNI"
description: "Xamarin.Android 允许编写而不是 Java 的 C# 中的 Android 应用。 多个程序集附带了 Xamarin.Android 提供 Java 库，包括 Mono.Android.dll 和 Mono.Android.GoogleMaps.dll 的绑定。 但是，对于每个可能的 Java 库，不提供了绑定的并且每个 Java 类型和成员，可能不会绑定提供的绑定。 若要使用未绑定 Java 类型和成员，可能使用 Java 本机接口 (JNI)。 本文将演示如何使用 JNI 与 Java 类型和成员从 Xamarin.Android 应用程序进行交互。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: f14d456cba66142c51e0755cdfd3c6795bd1cf73
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2018
---
# <a name="working-with-jni"></a>使用 JNI

_Xamarin.Android 允许编写而不是 Java 的 C# 中的 Android 应用。多个程序集附带了 Xamarin.Android 提供 Java 库，包括 Mono.Android.dll 和 Mono.Android.GoogleMaps.dll 的绑定。但是，对于每个可能的 Java 库，不提供了绑定的并且每个 Java 类型和成员，可能不会绑定提供的绑定。若要使用未绑定 Java 类型和成员，可能使用 Java 本机接口 (JNI)。本文将演示如何使用 JNI 与 Java 类型和成员从 Xamarin.Android 应用程序进行交互。_


## <a name="overview"></a>概述

它并不总是需要或不可能创建托管可调用包装 (MCW) 来调用 Java 代码。 在许多情况下，"内联"JNI 是完全可以接受且用于一次性使用未绑定的 Java 成员。 它通常是更简单，以使用 JNI 调用比若要生成的整个.jar 绑定一个 Java 类上的单个方法。

Xamarin.Android 提供`Mono.Android.dll`程序集，其中提供了绑定适用于 Android 的`android.jar`库。 类型和成员中不存在`Mono.Android.dll`和中不存在类型`android.jar`可能通过手动将它们绑定使用。 若要将 Java 类型和成员的绑定，请使用**Java 本机接口**(**JNI**) 若要查找类型、 读取和写入字段，和调用方法。

在 Xamarin.Android JNI API 是从概念上讲非常类似于`System.Reflection`.NET 中的 API： 它可以用于查找类型和成员的名称，读取和写入字段值，调用方法和的详细信息，方便。 你可以使用 JNI 和`Android.Runtime.RegisterAttribute`自定义特性来声明可以绑定以支持重写虚拟方法。 可以将绑定接口，以便它们可以在 C# 中实现。

本文档说明：

-  如何 JNI 引用类型。
-  如何查找、 读取和写入的字段。
-  如何查找并调用的方法。
-  如何公开虚拟方法，以允许从托管代码重写。
-  如何公开的接口。



## <a name="requirements"></a>惠?

JNI，如通过公开[Android.Runtime.JNIEnv 命名空间](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)，可用于 Xamarin.Android 的每个版本。
若要绑定 Java 类型和接口，必须使用 Xamarin.Android 4.0 或更高版本。


## <a name="managed-callable-wrappers"></a>托管的可调用包装器

A**管理可调用包装器**(**MCW**) 是*绑定*Java 类或接口，以便客户端 C# 代码不需要担心以上所有 JNI 机械该接口JNI 基础复杂性。 大部分`Mono.Android.dll`包含托管可调用包装器。

托管的可调用包装器有两个用途：

1.  封装 JNI 使用，从而使客户端代码不需要了解的基础的复杂性。
1.  让可以子类 Java 类型和实现 Java 接口。

第一个用途是复杂性的纯粹是复杂性的为了方便而且封装，以便使用者具有一套简单、 托管的类来使用。 这需要使用各种[JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)更高版本在本文中所述的成员。 请记住，管理可调用包装器不需要严格&ndash;JNI 使用为"内联"是完全可以接受并可用于一次性使用未绑定的 Java 成员。 子类分类和接口的实现需要托管可调用包装的使用。



## <a name="android-callable-wrappers"></a>Android 可调用包装器

Android 的可调用包装器 (ACW) 所需，每当需要调用托管的代码; Android 运行时 （图片）需要使用这些包装，因为没有方法在运行时与艺术作品注册类。
(具体而言， [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) Android 运行时不支持 JNI 函数。 Android 可调用包装器从而弥补缺少的运行时类型注册支持。）

每当 Android 代码需要执行的虚拟或接口方法重写或实现是在托管代码中，Xamarin.Android 必须提供 Java 代理，因此，此方法获取分派给相应的托管类型。 这些 Java 代理类型都是与托管类型实现的相同的构造函数，并声明任何重写的基类和接口方法具有"相同"的基类和 Java 接口列表的 Java 代码。

Android 可调用包装器生成的**monodroid.exe**程序期间[生成过程](~/android/deploy-test/building-apps/build-process.md)，并且 （直接或间接） 继承的所有类型生成[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)。



### <a name="implementing-interfaces"></a>实现接口

有时你可能需要实现的 Android 接口 (如[Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/))。

所有 Android 类和接口扩展[Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)接口： 因此，所有 Android 类型必须实现`IJavaObject`。
Xamarin.Android 利用这一事实&ndash;它使用`IJavaObject`为给定的托管类型与 Java 代理 （Android 可调用包装） 提供 Android。 因为**monodroid.exe**仅查找`Java.Lang.Object`子类 (这必须实现`IJavaObject`)、 生成子类`Java.Lang.Object`为我们提供了一种方法在托管代码中实现接口。 例如:

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {
    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig) {
        // implementation goes here...
    }
    public void OnLowMemory () {
        // implementation goes here...
    }
}
```


### <a name="implementation-details"></a>实现详细信息

*此文章的剩余部分提供了实现详细信息，如有更改，恕不另行通知*（和开发人员可能想要知道发生了什么情况实质上，所以此处提供）。

例如，给定以下的 C# 源代码：

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

**Mandroid.exe**程序将生成以下 Android 可调用包装器：

```java
package mono.samples.helloWorld;

public class HelloAndroid extends android.app.Activity {
    static final String __md_methods;
    static {
        __md_methods =
            "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" +
            "";
        mono.android.Runtime.register (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                HelloAndroid.class,
                __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                "", this, new java.lang.Object[] { });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

请注意，保留了的基类，并在托管代码中被重写每个方法均提供本机方法声明。



### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute 和 ExportFieldAttribute

通常情况下，Xamarin.Android 自动生成包含 ACW; 的 Java 代码类派生自一个 Java 类并重写现有 Java 方法时，这一代取决于类和方法名称。 但是，在某些情况下，代码生成是不合适，按如下所述：

-   Android 支持的操作名称中的布局的 XML 属性，例如[android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) XML 属性。 当指定了该值时，膨胀的视图实例将尝试查找 Java 方法。

-   [Java.io.Serializable](http://developer.android.com/reference/java/io/Serializable.html)接口需要`readObject`和`writeObject`方法。 由于它们不是此接口的成员，我们相应的托管的实现未公开到 Java 代码的这些方法。

-   [Android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/)接口需要实现类必须具有一个静态字段`CREATOR`类型的`Parcelable.Creator`。 生成的 Java 代码需要某些显式字段。 与我们的标准方案，没有给 Java 代码中的输出字段方法从托管代码。


代码生成不提供解决方案，以生成具有任意名称的任意 Java 方法，因为从 Xamarin.Android 4.2 开始[ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)和[ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/)已引入了来提供上述方案的解决方案。 这两个属性驻留在`Java.Interop`命名空间：

-   `ExportAttribute` &ndash; 指定方法名称和其预期的异常类型 （若要为指定显式"引发"在 Java 中）。 方法使用时，该方法将"导出"生成到给托管方法的相应 JNI 调用调度代码 Java 方法。 这只能与使用`android:onClick`和`java.io.Serializable`。

-   `ExportFieldAttribute` &ndash; 指定字段名称。 它驻留的作用是作为字段初始值设定项的方法。 这只能与使用`android.os.Parcelable`。

[ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/)示例项目演示了如何使用这些属性。


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>ExportAttribute 和 ExportFieldAttribute 疑难解答

-   由于缺少打包失败**Mono.Android.Export.dll** &ndash;如果你使用`ExportAttribute`或`ExportFieldAttribute`在你的代码或依赖库中的某些方法，你必须添加**Mono.Android.Export.dll**。 此程序集是隔离，以支持通过 Java 回调代码。 它是从独立**Mono.Android.dll**因为它将其他大小添加到应用程序。

-   在发布版本`MissingMethodException`进行导出方法&ndash;中发布版本，`MissingMethodException`进行导出方法。 （此问题被固定的 Xamarin.Android 的最新版本。）



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` 和`ExportFieldAttribute`提供该 Java 运行时代码可以使用的功能。 此运行时代码访问这些属性由驱动的生成 JNI 方法通过托管的代码。 因此，没有现有 Java 方法，它将绑定的托管的方法;因此，从托管的方法签名生成 Java 方法。

但是，这种情况下不是完全决定的。 最值得注意的是，这是在托管的类型和 Java 类型，例如之间的一些高级映射 true:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

如果需要为导出方法，这些类型`ExportParameterAttribute`必须用于显式授予相应的参数或返回类型的值。



### <a name="annotation-attribute"></a>批注属性

在 Xamarin.Android 4.2 我们转换`IAnnotation`到属性 (System.Attribute) 和添加了对 Java 包装中的批注生成支持的实现类型。

这意味着以下方向更改：

-   绑定生成器生成`Java.Lang.DeprecatedAttribute`从`java.Lang.Deprecated`(时它应为`[Obsolete]`在托管代码中)。

-   这并不意味着该现有`Java.Lang.Deprecated`类就会消失。 这些基于 Java 的对象可能仍用作常用的 Java 对象 （如果存在这样的使用）。 将有`Deprecated`和`DeprecatedAttribute`类。

-   `Java.Lang.DeprecatedAttribute`类标记为`[Annotation]`。 从此继承的自定义特性时`[Annotation]`属性，msbuild 任务将生成该自定义属性的 Java 批注 (@Deprecated) 中 Android 可调用包装 (ACW)。

-   批注可以拖动到类，方法上生成和导出字段 （这是一种方法在托管代码中）。

如果未注册包含类 （批注的类本身或包含的带批注的成员的类），整个的 Java 类源则不生成，包括批注。 对于方法，可以指定`ExportAttribute`获取显式生成和批注的方法。 此外，它不用于"生成"Java 批注类定义的功能。 换而言之，如果你定义某些批注的自定义托管的属性，你将需要添加另一个包含对应的 Java 批注类的.jar 库。 添加定义批注类型的 Java 源文件是不够的。 Java 编译器不能与相同的方式**apt**。

此外，将应用以下限制：

-   此转换过程不会考虑`@Target`批注类型上的批注为止。

-   到属性的属性不起作用。 改为使用的属性 getter 或 setter 的属性。



## <a name="class-binding"></a>类绑定

绑定类意味着编写托管可调用包装器，以简化的基础的 Java 类型调用。

绑定虚拟和抽象方法，以便允许从 C# 重写需要 Xamarin.Android 4.0。 但是，任何版本的 Xamarin.Android 可以将非虚拟方法、 静态方法或虚方法绑定不支持替代情况下。

通常，绑定包含以下各项：

-  A[正在绑定的 Java 类型的句柄 JNI](#_Looking_up_Java_Types)。

-  [JNI 字段 Id 和每个绑定字段属性](#_Instance_Fields)。

-  [JNI 方法 Id 和每个方法绑定方法](#_Instance_Methods)。

-  如果子类分类是必需的需要具有类型[RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)上的类型声明自定义特性[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)设置为`true`。



### <a name="declaring-type-handle"></a>声明类型句柄

字段和方法查找方法要求其声明的类型引用的对象引用。 按照约定，这将保存在`class_ref`字段：

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

请参阅[JNI 类型引用](#_JNI_Type_References)部分以了解详细信息有关`CLASS`令牌。


### <a name="binding-fields"></a>绑定字段

Java 字段公开为 C# 属性，例如 Java 字段[java.lang.System.in](http://developer.android.com/reference/java/lang/System.html#in)绑定为 C# 属性[Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/)。
此外，由于 JNI 区分静态字段和实例字段，实现属性时使用不同的方法。

字段绑定会涉及到三个集的方法：

1.  *获取字段 id*方法。 *获取字段 id*方法负责返回字段处理*获取字段值*和*设置字段值*方法将使用。 获取字段 id 需要了解声明类型，该字段的名称和[JNI 类型签名](#_JNI_Type_Signatures)的字段。

1.  *获取字段值*方法。 这些方法需要字段句柄，负责通过 Java 读取字段的值。
    要使用的方法取决于字段的类型。

1.  *设置字段值*方法。 这些方法需要字段句柄，负责将编写在 Java 中的字段的值。 要使用的方法取决于字段的类型。


 [静态字段](#_Static_Fields)使用[JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/)， `JNIEnv.GetStatic*Field`，和[JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/)方法。

 [实例字段](#_Instance_Fields)使用[JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/)， `JNIEnv.Get*Field`，和[JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/)方法。

例如，静态属性`JavaSystem.In`可以作为实现：

```csharp
static IntPtr in_jfieldID;
public static System.IO.Stream In
{
    get {
        if (in_jfieldId == IntPtr.Zero)
            in_jfieldId = JNIEnv.GetStaticFieldID (class_ref, "in", "Ljava/io/InputStream;");
        IntPtr __ret = JNIEnv.GetStaticObjectField (class_ref, in_jfieldId);
        return InputStreamInvoker.FromJniHandle (__ret, JniHandleOwnership.TransferLocalRef);
    }
}
```

注意： 我们将使用[InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership))要转换到的 JNI 引用`System.IO.Stream`实例，然后我们将`JniHandleOwnership.TransferLocalRef`因为[JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/)返回的本地引用。

许多[Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/)类型具有`FromJniHandle`方法会将转换 JNI 引用到所需的类型。



### <a name="method-binding"></a>方法绑定

作为方法 C# 和 C# 属性公开 Java 的方法。 例如，Java 方法[java.lang.Runtime.runFinalizersOnExit](http://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean))方法绑定为[Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/)方法，与[java.lang.Object.getClass](http://developer.android.com/reference/java/lang/Object.html#getClass)方法绑定为[Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/)属性。

方法调用是一个两步过程：

1.  *获取方法 id*要调用的方法。 *获取方法 id*方法负责返回的方法调用方法将使用的方法句柄。 获取方法 id 需要了解声明类型，该方法的名称和[JNI 类型签名](#_JNI_Type_Signatures)的方法。

1.  调用方法。

就像使用字段，方法用于获取方法 id 并调用的方法之间的静态方法和实例方法的行为有所不同。

[静态方法](#_Static_Methods_1)使用[JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/)查找方法 id，并使用`JNIEnv.CallStatic*Method`系列的调用方法。

[实例方法](#_Instance_Methods)使用[JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/)查找方法 id，并使用`JNIEnv.Call*Method`和`JNIEnv.CallNonvirtual*Method`系列的调用方法。

方法绑定是可能不仅仅方法调用。 方法绑定还包括允许的方法 （对于抽象和非 final 方法） 中重写或实现 （为接口方法）。 [支持继承接口](#_Supporting_Inheritance,_Interfaces_1)部分介绍支持虚方法和接口方法的复杂性。

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>静态方法

绑定的静态方法涉及使用`JNIEnv.GetStaticMethodID`以获得方法句柄，然后使用相应`JNIEnv.CallStatic*Method`方法，具体取决于方法的返回类型。 下面是一个示例的绑定的[Runtime.getRuntime](http://developer.android.com/reference/java/lang/Runtime.html#getRuntime())方法：

```csharp
static IntPtr id_getRuntime;

[Register ("getRuntime", "()Ljava/lang/Runtime;", "")]
public static Java.Lang.Runtime GetRuntime ()
{
    if (id_getRuntime == IntPtr.Zero)
        id_getRuntime = JNIEnv.GetStaticMethodID (class_ref,
                "getRuntime", "()Ljava/lang/Runtime;");

    return Java.Lang.Object.GetObject<Java.Lang.Runtime> (
            JNIEnv.CallStaticObjectMethod  (class_ref, id_getRuntime),
            JniHandleOwnership.TransferLocalRef);
}
```

我们将方法句柄存储在静态字段中，注意`id_getRuntime`。 这是一种性能优化，以便方法句柄不需要在每次调用查找。 不需要缓存方法句柄以这种方式。 一旦获得方法句柄， [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/)用于调用方法。 `JNIEnv.CallStaticObjectMethod` 返回`IntPtr`其中包含返回的 Java 实例的句柄。
[Java.Lang.Object.GetObject&lt;T&gt;（IntPtr，JniHandleOwnership）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)用于将 Java 句柄转换为强类型化的对象实例。



#### <a name="non-virtual-instance-method-binding"></a>非虚拟实例方法绑定

绑定`final`实例方法或实例方法，不要求重写时，涉及到使用`JNIEnv.GetMethodID`以获得方法句柄，然后使用相应`JNIEnv.Call*Method`方法，具体取决于方法的返回类型。 下面是一个示例的绑定的`Object.Class`属性：

```csharp
static IntPtr id_getClass;
public Java.Lang.Class Class {
    get {
        if (id_getClass == IntPtr.Zero)
            id_getClass = JNIEnv.GetMethodID (class_ref, "getClass", "()Ljava/lang/Class;");
        return Java.Lang.Object.GetObject<Java.Lang.Class> (
                JNIEnv.CallObjectMethod (Handle, id_getClass),
                JniHandleOwnership.TransferLocalRef);
    }
}
```

我们将方法句柄存储在静态字段中，注意`id_getClass`。
这是一种性能优化，以便方法句柄不需要在每次调用查找。 不需要缓存方法句柄以这种方式。 一旦获得方法句柄， [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/)用于调用方法。 `JNIEnv.CallStaticObjectMethod` 返回`IntPtr`其中包含返回的 Java 实例的句柄。
[Java.Lang.Object.GetObject&lt;T&gt;（IntPtr，JniHandleOwnership）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)用于将 Java 句柄转换为强类型化的对象实例。


### <a name="binding-constructors"></a>绑定的构造函数

构造函数是具有名称的 Java 方法`"<init>"`。 只需与 Java 实例方法一样，`JNIEnv.GetMethodID`用于查找的构造函数句柄。 与 Java 方法不同[JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/)方法用于调用的构造函数方法句柄。 返回值`JNIEnv.NewObject`为 JNI 本地引用：


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

类绑定通常将子类[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)。
当子类化`Java.Lang.Object`、 其他语义派上用场：`Java.Lang.Object`实例保留到通过 Java 实例的全局引用`Java.Lang.Object.Handle`属性。

1.  `Java.Lang.Object`默认构造函数将分配的 Java 实例。

1.  如果该类型具有`RegisterAttribute`，和`RegisterAttribute.DoNotGenerateAcw`是`true`，然后实例`RegisterAttribute.Name`通过其默认构造函数创建类型。

1.  否则为[Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)(ACW) 对应于`this.GetType`通过使用默认构造函数实例化。 Android 可调用包装器生成的包创建期间每个`Java.Lang.Object`为其子类`RegisterAttribute.DoNotGenerateAcw`未设置为`true`。

类型不类绑定，这是预期语义： 实例化`Mono.Samples.HelloWorld.HelloAndroid`C# 实例应构造 Java`mono.samples.helloworld.HelloAndroid`实例，这是生成的 Android 可调用包装器。

对于类的绑定，这可能是正确的行为，如果 Java 类型包含一个默认构造函数和/或没有其他构造函数需要调用。 否则，必须提供一个构造函数的执行下列操作：

1.  调用[Java.Lang.Object （IntPtr，JniHandleOwnership）](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)而非默认`Java.Lang.Object`构造函数。 需要这些信息来避免创建新的 Java 实例。

1.  检查值[Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/)在创建任何 Java 实例之前。 `Object.Handle`属性将不具有值`IntPtr.Zero`如果 Android 的可调用包装器在 Java 代码中，构造和正在构建的类绑定以包含所创建的 Android 可调用包装器实例。 例如，当 Android 创建`mono.samples.helloworld.HelloAndroid`实例，将创建 Android 可调用包装器，第一个和 Java`HelloAndroid`构造函数将创建的相应实例`Mono.Samples.HelloWorld.HelloAndroid`类型，与`Object.Handle`属性设置为在构造函数执行之前的 Java 实例。

1.  如果当前的运行时类型不是与声明相同类型，则相应的 Android 可调用包装器的实例必须创建，并且使用[Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership))存储返回的句柄[JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/)。

1.  如果当前的运行时类型的声明类型相同，然后调用 Java 构造函数，并使用[Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership))存储返回的句柄`JNIEnv.NewInstance`。


例如，考虑[java.lang.Integer(int)](http://developer.android.com/reference/java/lang/Integer.html#Integer(int))构造函数。 此绑定为：

```csharp
// Cache the constructor's method handle for later use
static IntPtr id_ctor_I;

// Need [Register] for subclassing
// RegisterAttribute.Name is always ".ctor"
// RegisterAttribute.Signature is tye JNI type signature of constructor
// RegisterAttribute.Connector is ignored; use ""
[Register (".ctor", "(I)V", "")]
public Integer (int value)
    // 1. Prevent Object default constructor execution
    : base (IntPtr.Zero, JniHandleOwnership.DoNotTransfer)
{
    // 2. Don't allocate Java instance if already allocated
    if (Handle != IntPtr.Zero)
        return;

    // 3. Derived type? Create Android Callable Wrapper
    if (GetType () != typeof (Integer)) {
        SetHandle (
                Android.Runtime.JNIEnv.CreateInstance (GetType (), "(I)V", new JValue (value)),
                JniHandleOwnership.TransferLocalRef);
        return;
    }

    // 4. Declaring type: lookup &amp; cache method id...
    if (id_ctor_I == IntPtr.Zero)
        id_ctor_I = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
    // ...then create the Java instance and store
    SetHandle (
            JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value)),
            JniHandleOwnership.TransferLocalRef);
}
```

[JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/)方法是帮助器执行`JNIEnv.FindClass`， `JNIEnv.GetMethodID`， `JNIEnv.NewObject`，和`JNIEnv.DeleteGlobalReference`从返回的值上`JNIEnv.FindClass`。 有关详细信息，请参阅下一节。

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>支持继承，接口

子类化 Java 类型或实现 Java 接口要求生成[Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)(ACWs) 为生成每个`Java.Lang.Object`在打包过程中的子类。 通过控制 ACW 生成[Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)自定义属性。

对于 C# 类型，`[Register]`自定义特性构造函数需要一个自变量： [JNI 简化类型引用](#_Simplified_Type_References_1)键入相应的 Java。 这样，提供 Java 和 C# 之间的不同名称。

在 Xamarin.Android 4.0 之前`[Register]`自定义特性已向"别名"现有的 Java 类型不可用。 这是因为 ACW 生成过程会生成 ACWs 为每个`Java.Lang.Object`遇到的子类。

Xamarin.Android 4.0 引入了[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)属性。 此属性指示 ACW 生成过程到*跳过*的带批注的类型，允许新托管可调用包装，它不会导致 ACWs 在包创建时生成的声明。 这样，绑定现有 Java 类型。 例如，考虑以下简单的 Java 类`Adder`，其中包含一个方法， `add`，用于将添加到整数并返回结果：

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

`Adder`无法作为绑定类型：

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public partial class Adder : Java.Lang.Object {
    static IntPtr class_ref = JNIEnv.FindClass ( "mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }
}
partial class ManagedAdder : Adder {
}
```

在这里， `Adder` C# 类型*别名* `Adder` Java 类型。 `[Register]`属性用于指定的 JNI 名称`mono.android.test.Adder`Java 类型和`DoNotGenerateAcw`属性用来抑制 ACW 生成。 这将导致在生成中为 ACW`ManagedAdder`类型，可在正确子类`mono.android.test.Adder`类型。 如果`RegisterAttribute.DoNotGenerateAcw`属性未被使用，则 Xamarin.Android 生成过程将已生成新`mono.android.test.Adder`Java 类型。 这将导致编译错误，作为`mono.android.test.Adder`类型也会出现两次，在两个单独的文件。



### <a name="binding-virtual-methods"></a>绑定虚拟方法

`ManagedAdder` 子类 Java`Adder`类型，但它不是特别有趣： C#`Adder`类型未定义任何虚拟方法，因此`ManagedAdder`不能重写任何内容。

绑定`virtual`方法以允许由子类重写需要需要完成这分为以下两个类别的几件事情：

1.  **方法绑定**

1.  **方法注册**


#### <a name="method-binding"></a>方法绑定

方法绑定需要加入到 C# 的两个支持成员`Adder`定义： `ThresholdType`，和`ThresholdClass`。

##### <a name="thresholdtype"></a>ThresholdType

`ThresholdType`属性返回当前绑定的类型：

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` 用于的方法绑定在确定何时应执行虚拟与非虚拟方法调度。 它应始终返回`System.Type`实例对应于声明的 C# 类型。

##### <a name="thresholdclass"></a>ThresholdClass

`ThresholdClass`属性返回的绑定类型的 JNI 类引用：

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` 中使用的方法绑定时调用的非虚拟方法。

#### <a name="binding-implementation"></a>绑定实现

方法的绑定实现负责的 Java 方法的运行时调用。 它还包含`[Register]`是方法注册的一部分，将方法注册节中讨论的自定义特性声明：

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }
}
```

`id_add`字段包含 Java 方法要调用的方法 ID。 `id_add`值取自`JNIEnv.GetMethodID`，这要求声明的类 (`class_ref`)，Java 方法名称 (`"add"`)，以及该方法的 JNI 签名 (`"(II)I"`)。

一旦获得方法 ID，`GetType`与相比`ThresholdType`以确定是否需要虚拟机或非虚拟的调度。 虚拟调度，则需要时`GetType`匹配`ThresholdType`，作为`Handle`可能引用 Java 分配子类化，这会重写该方法。

当`GetType`不匹配`ThresholdType`，`Adder`子类别 (例如通过`ManagedAdder`)，和`Adder.Add`如果子类调用将仅调用实现`base.Add`。 这是非虚拟调度大小写形式，这正是`ThresholdClass`传入。 `ThresholdClass` 指定的 Java 类将提供要调用的方法的实现。



#### <a name="method-registration"></a>方法注册

假定我们更新`ManagedAdder`定义这将覆盖`Adder.Add`方法：

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

回想一下，`Adder.Add`具有`[Register]`自定义属性：

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

`[Register]`自定义特性构造函数接受三个值：

1.  Java 方法中，名称`"add"`在这种情况下。

1.  JNI 类型签名的方法，`"(II)I"`在这种情况下。

1.  *连接器方法*，`GetAddHandler`在这种情况下。
    稍后将作介绍连接器方法。


前两个参数允许 ACW 生成过程，以生成在方法声明重写的方法。 生成 ACW 会包含一些下面的代码：

```csharp
public class ManagedAdder extends mono.android.test.Adder {
    static final String __md_methods;
    static {
        __md_methods = "n_add:(II)I:GetAddHandler\n" +
            "";
        mono.android.Runtime.register (...);
    }
    @Override
    public int add (int p0, int p1) {
        return n_add (p0, p1);
    }
    private native int n_add (int p0, int p1);
    // ...
}
```

请注意，`@Override`声明的方法，该委托给`n_`-前缀相同名称的方法。 这确保 Java 代码调用时`ManagedAdder.add`，`ManagedAdder.n_add`将调用，从而允许重写 C#`ManagedAdder.Add`要执行方法。

因此，最重要的问题： 如何为`ManagedAdder.n_add`挂接到`ManagedAdder.Add`？

Java`native`方法注册 Java （Android 运行时） 运行时通过[JNI RegisterNatives 函数](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734)。
`RegisterNatives` 采用后面的包含要调用的 Java 方法名称、 JNI 类型签名和函数指针的结构数组[JNI 调用约定](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715)。
函数指针必须是采用两个指针自变量跟方法参数的函数。 Java`ManagedAdder.n_add`必须通过具有以下 C 原型的函数的实现方法：

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android 未公开`RegisterNatives`方法。 相反，ACW 和 MCW 共同提供调用所需的信息`RegisterNatives`: ACW 包含方法名称和 JNI 类型签名，唯一欠缺为函数指针挂钩。

这就是*连接器方法*传入。 第三个`[Register]`自定义特性参数是在已注册的类型或可不接受任何参数并返回已注册类型的基类中定义的方法的名称`System.Delegate`。 返回`System.Delegate`又是指具有正确的 JNI 函数签名的方法。 最后，该连接器方法返回的委托*必须*取得 root 权限，以便 GC 不会收集它，如的委托提供与 Java。

```csharp
#pragma warning disable 0169
static Delegate cb_add;
// This method must match the third parameter of the [Register]
// custom attribute, must be static, must return System.Delegate,
// and must accept no parameters.
static Delegate GetAddHandler ()
{
    if (cb_add == null)
        cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
    return cb_add;
}
// This method is registered with JNI.
static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
{
    Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
    return __this.Add (a, b);
}
#pragma warning restore 0169
```

`GetAddHandler`方法创建`Func<IntPtr, IntPtr, int, int,
int>`委托，它是指`n_Add`方法，然后调用[JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/)。
`JNINativeWrapper.CreateDelegate` 将提供的方法包装在 try/catch 块，以便任何未经处理的异常进行处理，并且将导致引发[AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/)事件。 将得到的委托存储在静态`cb_add`变量以便 GC 不会释放委托。

最后，`n_Add`方法负责封送的 JNI 参数为相应的托管类型，然后委托方法调用。

注意： 始终使用`JniHandleOwnership.DoNotTransfer`通过 Java 实例中获取 MCW 时。 将它们视为的本地引用 (并因此调用`JNIEnv.DeleteLocalRef`) 将中断托管-&gt; Java-&gt;管理堆栈转换。



### <a name="complete-adder-binding"></a>完成 Adder 绑定

完整的管理绑定`mono.android.tests.Adder`类型是：

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public class Adder : Java.Lang.Object {

    static IntPtr class_ref = JNIEnv.FindClass ("mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    protected override Type ThresholdType {
        get {return typeof (Adder);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

#region Add
    static IntPtr id_add;

    [Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }

#pragma warning disable 0169
    static Delegate cb_add;
    static Delegate GetAddHandler ()
    {
        if (cb_add == null)
            cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
        return cb_add;
    }

    static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
    {
        Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
        return __this.Add (a, b);
    }
#pragma warning restore 0169
#endregion
}
```



### <a name="restrictions"></a>限制

写入类型时，将匹配下列条件：

1.  子类 `Java.Lang.Object`

1.  具有`[Register]`自定义特性

1.  `RegisterAttribute.DoNotGenerateAcw` 为 `true`


然后针对 GC 交互类型*必须不*具有任何字段可能引用`Java.Lang.Object`或`Java.Lang.Object`在运行时的子类。 例如，类型的字段`System.Object`，不允许任何接口类型。 类型不能引用`Java.Lang.Object`允许使用实例，如`System.String`和`List<int>`。 此限制是为了防止 GC 过早对象集合。

如果该类型必须包含一个实例字段，它可以指`Java.Lang.Object`实例，则字段类型必须是`System.WeakReference`或`GCHandle`。



## <a name="binding-abstract-methods"></a>绑定抽象方法

绑定`abstract`方法在很大程度上等同于绑定虚拟方法。 有只有两个区别：

1.  抽象方法为抽象类。 它仍将保留`[Register]`属性和关联的方法注册，方法绑定到为刚才移动`Invoker`类型。

1.  非`abstract``Invoker`类型创建的子类的抽象类型。 `Invoker`类型必须重写基类中声明的所有抽象方法，且重写的实现的方法绑定实现，但可以忽略的非虚拟调度大小写。


例如，假定上述`mono.android.test.Adder.add`方法`abstract`。 C# 绑定将更改，以便`Adder.Add`已抽象，和一个新`AdderInvoker`类型都将定义该实现`Adder.Add`:

```csharp
partial class Adder {
    [Register ("add", "(II)I", "GetAddHandler")]
    public abstract int Add (int a, int b);

    // The Method Registration machinery is identical to the
    // virtual method case...
}

partial class AdderInvoker : Adder {
    public AdderInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    static IntPtr id_add;
    public override int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
    }
}
```

`Invoker`类型仅时需要获取 JNI 的 Java 创建实例的引用。


## <a name="binding-interfaces"></a>绑定接口

绑定接口是从概念上讲类似于将绑定类包含虚拟方法，但许多细节的区别在于细微 （而不小） 的方法。 请考虑以下[Java 接口声明](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

接口绑定具有两个部分： C# 接口定义和接口的调用程序定义。



### <a name="interface-definition"></a>接口定义

C# 接口定义必须满足以下要求：

-   接口定义必须具有`[Register]`自定义属性。

-   接口定义必须扩展`IJavaObject interface`。
    如果不这样做会阻止 ACWs 从 Java 接口继承。

-   每个接口方法必须包含`[Register]`属性，用于指定相应的 Java 方法名称、 JNI 签名和连接器方法。

-   连接器方法还必须指定连接器方法可以在找到的类型。

在绑定时`abstract`和`virtual`方法，该连接器方法将搜索在要注册的类型的继承层次结构内。 接口可以包含正文，因此这不起作用，因此要求没有方法的指定类型，该值指示连接器方法所在的位置。 在连接器方法字符串中，指定冒号之后的类型`':'`，并且必须包含调用方的类型的程序集限定的类型名称。

接口方法声明后的相应的 Java 方法使用一个翻译*兼容*类型。 对于 Java 内置类型，兼容的类型是相应类型的 C#，例如 Java`int`为 C# `int`。 对于引用类型兼容的类型是可以提供相应的 Java 类型的 JNI 句柄类型。

接口成员将不会直接调用通过 Java&ndash;调用将通过调用程序类型调节&ndash;以便允许一定的灵活性。

Java 进度接口可以是[声明在 C# 中作为](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

请注意，在上述我们映射 Java`int[]`参数[JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/)。
没有必要这样做： 我们无法绑定到 C# `int[]`，或`IList<int>`，还是其他内容完全。 选择任何类型，`Invoker`需要能够将其转换为 Java`int[]`调用的类型。


### <a name="invoker-definition"></a>调用程序定义

`Invoker`必须继承类型定义`Java.Lang.Object`，实现相应的接口，并提供在接口定义中引用的所有连接方法。 没有输入一个不同于类绑定的多个建议：`class_ref`字段和方法的 Id 应为实例成员，非静态成员。

首选实例成员原因所造成与`JNIEnv.GetMethodID`Android 运行时中的行为。 （这可能是也 Java 行为; 未测试它。）`JNIEnv.GetMethodID`查找来自已实现的接口和未声明的接口的方法时，则返回 null。 请考虑[java.util.SortedMap&lt;K，V&gt; ](http://developer.android.com/reference/java/util/SortedMap.html) Java 接口，实现[java.util.Map&lt;K，V&gt; ](http://developer.android.com/reference/java/util/Map.html)接口。 映射提供[清除](http://developer.android.com/reference/java/util/Map.html#clear())方法，因此看似合理`Invoker`SortedMap 定义将能：

```csharp
// Fails at runtime. DO NOT FOLLOW
partial class ISortedMapInvoker : Java.Lang.Object, ISortedMap {
    static IntPtr class_ref = JNIEnv.FindClass ("java/util/SortedMap");
    static IntPtr id_clear;
    public void Clear()
    {
        if (id_clear == IntPtr.Zero)
            id_clear = JNIEnv.GetMethodID(class_ref, "clear", "()V");
        JNIEnv.CallVoidMethod(Handle, id_clear);
    }
     // ...
}
```

上述将失败，因为`JNIEnv.GetMethodID`将返回`null`查找时`Map.clear`方法通过`SortedMap`类实例。

有两个解决方案： 跟踪每个方法来自哪个接口和具有`class_ref`每个接口，或将作为实例成员的所有内容保持和派生程度最高的类类型不是接口类型上执行的方法查找。 后者在中完成**Mono.Android.dll**。

调用程序定义具有六个部分： 构造函数中，`Dispose`方法，`ThresholdType`和`ThresholdClass`成员，`GetObject`方法、 接口方法的实现和连接器方法的实现。



#### <a name="constructor"></a>构造函数

需要查找正在调用的实例的运行时类和实例中存储的运行时类的构造函数`class_ref`字段：

```csharp
partial class IAdderProgressInvoker {
    IntPtr class_ref;
    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref   = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }
}
```

注意：`Handle`属性必须使用在构造函数体内，而不`handle`参数，截止日期 Android v4.0`handle`基构造函数完成执行后参数可能无效。


#### <a name="dispose-method"></a>Dispose 方法

`Dispose`方法需要释放构造函数中分配的全局引用：

```csharp
partial class IAdderProgressInvoker {
    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }
}
```


#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType 和 ThresholdClass

`ThresholdType`和`ThresholdClass`成员都是相同类绑定中找到的内容：

```csharp
partial class IAdderProgressInvoker {
    protected override Type ThresholdType {
        get {
            return typeof (IAdderProgressInvoker);
        }
    }
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```


#### <a name="getobject-method"></a>GetObject 方法

静态`GetObject`支持所需的方法[Extensions.JavaCast&lt;T&gt;（)](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```


#### <a name="interface-methods"></a>接口方法

每个方法的接口需要实现中，调用通过 JNI 相应的 Java 方法：

```csharp
partial class IAdderProgressInvoker {
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd", "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd, new JValue (JNIEnv.ToJniHandle (values)), new JValue (currentIndex), new JValue (currentSum));
    }
}
```



#### <a name="connector-methods"></a>连接器方法

连接器方法和支持基础结构负责封送处理为适当的 C# 类型的 JNI 参数。 Java`int[]`参数将作为 JNI 传递`jintArray`，即`IntPtr`中 C#。 `IntPtr`必须封送到`JavaArray<int>`为了支持调用的 C# 接口：

```csharp
partial class IAdderProgressInvoker {
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
}
```

如果`int[]`将优于`JavaList<int>`，然后[JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type))无法改为使用：

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

但是，请注意，`JNIEnv.GetArray`将复制整个数组 Vm，以便为大型数组这可能导致大量添加 GC 压力之间。


### <a name="complete-invoker-definition"></a>完成调用程序定义

[完成 IAdderProgressInvoker 定义](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

```csharp
class IAdderProgressInvoker : Java.Lang.Object, IAdderProgress {

    IntPtr class_ref;

    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }

    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }

    protected override Type ThresholdType {
        get {return typeof (IAdderProgressInvoker);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }

#region OnAdd
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd",
                    "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd,
                new JValue (JNIEnv.ToJniHandle (values)),
                new JValue (currentIndex),
new JValue (currentSum));
    }

#pragma warning disable 0169
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
#pragma warning restore 0169
#endregion
}
```



## <a name="jni-object-references"></a>JNI 对象引用

很多 JNIEnv 方法返回*JNI* *对象引用*，这是类似于`GCHandle`s。 JNI 提供三种不同类型的对象引用： 本地引用、 全局引用和弱全局引用。 所有三个表示为`System.IntPtr`，*但*（根据 JNI 函数类型部分中） 不是所有`IntPtr`从返回的 s`JNIEnv`方法是引用。 例如， [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/)返回`IntPtr`，但它不会返回一个对象引用，它将返回`jmethodID`。 请查阅[JNI 的函数文档](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)有关详细信息。

本地引用由*大多数*引用创建方法。
Android 仅允许有限的数量的本地引用，以便在任何给定时间，通常 512 存在。 可以通过删除本地引用[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/)。
与不同的 JNI，并非所有引用 JNIEnv 方法的返回的对象引用返回本地 references;[JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/)返回*全局*引用。 强烈建议您快速可以可能通过构造删除本地引用[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)针对对象并指定`JniHandleOwnership.TransferLocalRef`到[Java.Lang.Object (IntPtr处理，JniHandleOwnership 传输）](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)构造函数。

全局引用由[JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/)和[JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/)。
可通过对它们进行销毁[JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/)。
仿真程序具有的限制为 2000 未处理的全局引用，而硬件设备具有大约 52,000 全局引用的限制。

Android v2.2 (Froyo) 及更高版本，弱全局引用才可用。 可以使用删除弱全局引用[JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr))。


### <a name="dealing-with-jni-local-references"></a>处理 JNI 本地引用

[JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/)， [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/)， [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/)， [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)和[JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/)方法返回`IntPtr`其中包含一个 Java JNI 本地引用或`IntPtr.Zero`如果 Java 返回`null`。 由于可以在未完成后 （512 个条目），最好是确保引用的本地引用的有限数目会及时删除。 有三种本地引用可得到处理的方法： 显式删除它们，创建`Java.Lang.Object`实例，用于保存，并使用`Java.Lang.Object.GetObject<T>()`创建围绕它们托管可调用包装器。



### <a name="explicitly-deleting-local-references"></a>显式删除本地引用

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/)用于删除本地引用。 在本地引用已被删除，无法使用它，因此必须格外小心以确保`JNIEnv.DeleteLocalRef`是完成与本地引用的最后一步。

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```



### <a name="wrapping-with-javalangobject"></a>与 Java.Lang.Object 的包装

`Java.Lang.Object` 提供[Java.Lang.Object （IntPtr 句柄，JniHandleOwnership 传输）](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)构造函数用于包装正在退出的 JNI 引用。 [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/)参数确定如何`IntPtr`参数应被视为：

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash;创建`Java.Lang.Object`实例将创建新的全局引用从`handle`参数，和`handle`保持不变。
    调用方负责到释放`handle`，如果有必要。

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash;创建`Java.Lang.Object`实例将创建新的全局引用从`handle`参数，和`handle`随删除[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . 调用方必须释放`handle`，并且不能将`handle`构造函数完成执行后。

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash;创建`Java.Lang.Object`实例将接管所有权的`handle`参数。 调用方必须释放`handle`。


因为 JNI 方法调用方法返回本地 refs`JniHandleOwnership.TransferLocalRef`通常使用：

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

创建的全局引用不会被释放之前`Java.Lang.Object`实例进行垃圾回收。 如果你是可以的则释放的实例将释放的全局引用，加快垃圾回收：

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```



### <a name="using-javalangobjectgetobjectlttgt"></a>Using Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object` 提供[Java.Lang.Object.GetObject&lt;T&gt;（IntPtr 句柄，JniHandleOwnership 传输）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)方法，可以用来创建指定类型的托管可调用包装器。

类型`T`必须满足以下要求：

1.  `T` 必须是引用类型。

1.  `T` 必须实现`IJavaObject`接口。

1.  如果`T`不是一个抽象类或接口，然后`T`必须提供一个构造函数的参数类型与`(IntPtr,
    JniHandleOwnership)`。

1.  如果`T`是一个抽象类或接口，存在*必须*是*调用程序*可用于`T`。 调用程序是继承的非抽象类型`T`或实现`T`，并且具有相同的名称`T`与调用程序后缀。 例如，如果 T 是接口`Java.Lang.IRunnable`，然后类型`Java.Lang.IRunnableInvoker`必须存在并且必须包含所需`(IntPtr,
    JniHandleOwnership)`构造函数。


因为 JNI 方法调用方法返回本地 refs`JniHandleOwnership.TransferLocalRef`通常使用：

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>查找 Java 类型

若要查找的字段或方法中 JNI，字段或方法的声明类型必须查找第一个。 [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String))方法用于查找 Java 类型。 字符串参数是*简化类型引用*或*完整类型引用*Java 类型。 请参阅[JNI 类型引用部分](#_JNI_Type_References)有关简化和完整的类型引用的详细信息。

注意： 与每个`JNIEnv`方法该方法返回对象实例`FindClass`返回全局引用，而不是本地的引用。

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>实例字段

通过操作字段时*字段 Id*。 字段 Id 获取通过[JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/)，这需要类字段定义的字段，名称中和[JNI 类型签名](#JNI_Type_Signatures)的字段。

字段 Id 不需要被释放，并且有效，只要在相应的 Java 类型加载。 （android 不目前不支持类卸载。）

有两个集的操作实例字段的方法： 一台用于读取实例字段，另一个用于编写实例字段。 所有集的方法都需要读取或写入的字段值的字段 ID。


### <a name="reading-instance-field-values"></a>读取实例字段值

读取实例字段值的方法的一套遵循命名模式：

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
其中`*`是字段的类型：

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash;读取的非内置类型，如任何实例字段的值`java.lang.Object`，数组和接口类型。 返回的值为 JNI 本地引用。

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash;读取的值`bool`实例字段。

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash;读取的值`sbyte`实例字段。

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash;读取的值`char`实例字段。

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash;读取的值`short`实例字段。

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash;读取的值`int`实例字段。

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash;读取的值`long`实例字段。

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash;读取的值`float`实例字段。

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash;读取的值`double`实例字段。





### <a name="writing-instance-field-values"></a>正在写入实例字段值

用于写入实例字段值的方法的一套遵循命名模式：

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

其中*类型*是字段的类型：

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash;编写非内置类型，如任何字段的值`java.lang.Object`，数组和接口类型。 `IntPtr`值可能 JNI 本地引用、 JNI 全局引用、 JNI 弱全局引用，或`IntPtr.Zero`(有关`null`)。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash;写入的值`bool`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash;写入的值`sbyte`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Char)) &ndash;写入的值`char`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash;写入的值`short`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash;写入的值`int`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash;写入的值`long`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Single)) &ndash;写入的值`float`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Double)) &ndash;写入的值`double`实例字段。


<a name="_Static_Fields" />

## <a name="static-fields"></a>静态字段

通过操作的静态字段*字段 Id*。 字段 Id 获取通过[JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/)，这需要类字段定义的字段，名称中和[JNI 类型签名](#JNI_Type_Signatures)的字段。

字段 Id 不需要被释放，并且有效，只要在相应的 Java 类型加载。 （android 不目前不支持类卸载。）

有两个集的操作的静态字段的方法： 一台用于读取实例字段，另一个用于编写实例字段。 所有集的方法都需要读取或写入的字段值的字段 ID。


### <a name="reading-static-field-values"></a>读取静态字段值

读取静态字段值的方法的一套遵循命名模式：

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

其中`*`是字段的类型：

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash;读取的任何非内置类型，如的静态字段的值`java.lang.Object`，数组和接口类型。 返回的值为 JNI 本地引用。

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash;读取的值`bool`静态字段。

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash;读取的值`sbyte`静态字段。

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash;读取的值`char`静态字段。

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash;读取的值`short`静态字段。

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash;读取的值`long`静态字段。

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash;读取的值`float`静态字段。

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash;读取的值`double`静态字段。



### <a name="writing-static-field-values"></a>正在写入静态字段值

用于写入静态字段值的方法的一套遵循命名模式：

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

其中*类型*是字段的类型：

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash;编写非内置类型，如任何静态字段的值`java.lang.Object`，数组和接口类型。 `IntPtr`值可能 JNI 本地引用、 JNI 全局引用、 JNI 弱全局引用，或`IntPtr.Zero`(有关`null`)。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash;写入的值`bool`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash;写入的值`sbyte`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash;写入的值`char`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash;写入的值`short`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash;写入的值`int`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash;写入的值`long`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash;写入的值`float`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash;写入的值`double`静态字段。


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>实例方法

通过调用实例方法*方法 Id*。 方法 Id 获取通过[JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/)，这要求的类型，该方法定义中的方法的名称和[JNI 类型签名](#JNI_Type_Signatures)的方法。

方法 Id 不需要被释放，并且有效，只要在相应的 Java 类型加载。 （android 不目前不支持类卸载。）

下面是用于调用方法的方法的两组： 一个用于几乎，调用方法，一个用于非虚拟调用的方法。 这两组方法需要的方法 ID 来调用的方法和非虚拟调用还要求您指定的类实现应被调用。

接口方法可以仅查找内声明的类型;无法查找来自扩展或继承接口的方法。 请参阅更高版本的绑定接口 / 调用程序实现部分以了解更多详细信息。

在类中声明的任何方法或可以查找任何基类或实现的接口。


### <a name="virtual-method-invocation"></a>虚拟方法调用

一套用于调用方法的方法几乎遵循命名模式：

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

其中`*`是方法的返回类型。

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash;调用的方法，它返回非内置类型，如`java.lang.Object`、 数组和接口。 返回的值为 JNI 本地引用。

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash;调用返回的方法`bool`值。

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash;调用返回的方法`sbyte`值。

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash;调用返回的方法`char`值。

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash;调用返回的方法`short`值。

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash;调用返回的方法`long`值。

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash;调用返回的方法`float`值。

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash;调用返回的方法`double`值。



### <a name="non-virtual-method-invocation"></a>非虚拟方法调用

用于调用方法的方法集非几乎遵循命名模式：

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

其中`*`是方法的返回类型。 非虚拟方法调用通常用于调用虚方法的基本方法。

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash;非几乎调用的方法，它返回非内置类型，如`java.lang.Object`、 数组和接口。 返回的值为 JNI 本地引用。

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash;非几乎调用返回的方法`bool`值。

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash;非几乎调用返回的方法`sbyte`值。

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash;非几乎调用返回的方法`char`值。

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash;非几乎调用返回的方法`short`值。

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash;非几乎调用返回的方法`long`值。

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash;非几乎调用返回的方法`float`值。

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash;非几乎调用返回的方法`double`值。


<a name="_Static_Methods" />

## <a name="static-methods"></a>静态方法

通过调用静态方法*方法 Id*。 方法 Id 获取通过[JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/)，这要求的类型，该方法定义中的方法的名称和[JNI 类型签名](#JNI_Type_Signatures)的方法。

方法 Id 不需要被释放，并且有效，只要在相应的 Java 类型加载。 （android 不目前不支持类卸载。）



### <a name="static-method-invocation"></a>静态方法调用

一套用于调用方法的方法几乎遵循命名模式：

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

其中`*`是方法的返回类型。

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash;调用的静态方法，它返回非内置类型，如`java.lang.Object`、 数组和接口。 返回的值为 JNI 本地引用。

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash;调用的静态方法，它返回`bool`值。

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash;调用的静态方法，它返回`sbyte`值。

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash;调用的静态方法，它返回`char`值。

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash;调用的静态方法，它返回`short`值。

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash;调用的静态方法，它返回`long`值。

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash;调用的静态方法，它返回`float`值。

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash;调用的静态方法，它返回`double`值。


<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>JNI 类型签名

[JNI 类型签名](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432)是[JNI 类型引用](#_JNI_Type_References)（但不简化的类型引用），但方法除外。 使用方法时，JNI 类型签名是左括号`'('`后, 跟所有类型连接在一起 （具有不分隔开来将使用逗号或任何其他内容） 后, 跟右括号的参数的类型引用`')'`，跟 JNI 类型引用的方法的返回类型。

例如，给定 Java 方法：

```java
long f(int n, String s, int[] array);
```

将 JNI 类型签名：

```csharp
(ILjava/lang/String;[I)J
```

通常情况下，是*强*建议使用`javap`命令，以确定 JNI 签名。 例如，JNI 类型签名的[java.lang.Thread.State.valueOf(String)](http://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String))方法是"（lang/Ljava/字符串;） Ljava/lang/线程$ 状态;"，而 JNI 键入签名[java.lang.Thread.State.values](http://developer.android.com/reference/java/lang/Thread.State.html#values)方法是"（) [Ljava/lang/线程$ 状态;"。 留意尾随的分号;这些*是*JNI 类型签名的一部分。

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>JNI 类型引用

不同于 Java 类型引用具有 JNI 类型引用。 不能使用完全限定的 Java 类型名称，如`java.lang.String`JNI，您必须改为使用 JNI 变体`"java/lang/String"`或`"Ljava/lang/String;"`，具体取决于上下文; 请参见下面的详细信息。
有四种类型的 JNI 类型引用：

-  **built-in**
-  **simplified**
-  **type**
-  **array**


### <a name="built-in-type-references"></a>内置类型引用

内置类型引用是单个字符，用于引用内置值类型。 映射将如下：

-  `"B"` 有关`sbyte`。
-  `"S"` 有关`short`。
-  `"I"` 有关`int`。
-  `"J"` 有关`long`。
-  `"F"` 有关`float`。
-  `"D"` 有关`double`。
-  `"C"` 有关`char`。
-  `"Z"` 有关`bool`。
-  `"V"` 有关`void`方法返回类型。


<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>简化的类型引用

简化的类型引用仅可在[JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String))。
有两种方法，以派生简化的类型引用：

1.  从的完全限定的 Java 名称，将每个`'.'`在包名称和类型名与之前`'/'`，每个`'.'`内类型名与`'$'`。

1.  读取输出的`'unzip -l android.jar | grep JavaName'`。


这两个任一将导致 Java 类型[java.lang.Thread.State](http://developer.android.com/reference/java/lang/Thread.State.html)映射到简化的类型引用`java/lang/Thread$State`。


### <a name="type-references"></a>类型引用

类型引用为内置类型引用或使用的简化的类型引用`'L'`前缀和`';'`后缀。 Java 类型[java.lang.String](http://developer.android.com/reference/java/lang/String.html)，简化的类型引用的`"java/lang/String"`，而类型引用为`"Ljava/lang/String;"`。

与数组类型引用和 JNI 签名使用类型引用。

若要获取的类型引用的其他方法是通过读取的输出`'javap -s -classpath android.jar fully.qualified.Java.Name'`。
具体取决于类型涉及，你可以使用构造函数声明或方法返回类型来确定 JNI 名称。 例如:

```shell
$ javap -classpath android.jar -s java.lang.Thread.State
Compiled from "Thread.java"
```

```java
public final class java.lang.Thread$State extends java.lang.Enum{
public static final java.lang.Thread$State NEW;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State RUNNABLE;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State BLOCKED;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TIMED_WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TERMINATED;
  Signature: Ljava/lang/Thread$State;
public static java.lang.Thread$State[] values();
  Signature: ()[Ljava/lang/Thread$State;
public static java.lang.Thread$State valueOf(java.lang.String);
  Signature: (Ljava/lang/String;)Ljava/lang/Thread$State;
static {};
  Signature: ()V
}
```

`Thread.State` 是 Java 枚举类型，以便我们可以使用的签名`valueOf`方法来确定类型引用是 Ljava/lang/线程$ 状态;。



### <a name="array-type-references"></a>数组类型引用

数组类型引用`'['`JNI 类型引用的前缀。
指定数组时，不能使用简化的类型引用。

例如，`int[]`是`"[I"`，`int[][]`是`"[[I"`，和`java.lang.Object[]`是`"[Ljava/lang/Object;"`。



## <a name="java-generics-and-type-erasure"></a>Java 泛型和类型擦除

*大多数*情况下，通过 JNI，Java 泛型所示*不存在*。
有一些"褶皱，"，但这些褶皱正在 Java 与泛型，不与 JNI 如何查找并调用泛型成员交互的方式。

通过 JNI 交互时，没有任何泛型类型或成员和非泛型类型或成员之间的区别。 例如，泛型类型[java.lang.Class&lt;T&gt; ](http://developer.android.com/reference/java/lang/Class.html)也是"原始"的泛型类型`java.lang.Class`，这两个具有相同的简化的类型引用， `"java/lang/Class"`。


## <a name="java-native-interface-support"></a>Java 本机接口支持

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)是 Jave 本机接口 (JNI) 有关的托管的包装。 JNI 函数中声明[Java 本机接口规格](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)，不过方法已更改，以删除显式`JNIEnv*`参数和`IntPtr`而不是使用`jobject`， `jclass`， `jmethodID`，等等。例如，考虑[JNI NewObject 函数](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

此名称公开为[JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/)方法：

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

两个调用之间进行转换还是相当简单。 在 C 中，您将有：

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

将 C# 等效项：

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

容纳 IntPtr 的 Java 对象实例之后，可能需要使用它执行某些操作。 你可以使用 JNIEnv 方法，如[ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/)进行管理，但如果已存在的模拟的 C# 包装然后你将需要通过 JNI 引用构造包装。 你可以执行因此通过[Extensions.JavaCast <t>（)</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/)扩展方法：

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

你还可以使用[Java.Lang.Object.GetObject <t>（)</t> ](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)方法：

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

此外，所有 JNI 函数已被修改的删除`JNIEnv*`存在于每个 JNI 函数的参数。


## <a name="summary"></a>摘要

直接处理 JNI 是应当不惜任何代价避免可怕体验。 遗憾的是，它并不总是不如;适用于 Android 命中具有 Mono 未绑定的 Java 用例时，本指南提供的一些帮助的希望。


## <a name="related-links"></a>相关链接

- [SanityTests （示例）](https://developer.xamarin.com/samples/SanityTests/)
- [Java 本机接口规范](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Java 本机接口函数](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
