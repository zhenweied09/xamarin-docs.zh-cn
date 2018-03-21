---
title: "体系结构"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 9834da444032622cc3547e7c99ca3de0e41bb603
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2018
---
# <a name="architecture"></a>体系结构

在单声道执行环境中运行 Xamarin.Android 应用程序。
此执行环境运行的并行与 Android 运行时 （艺术作品） 虚拟机。 这两个运行时环境 Linux 内核上运行的服务并公开到用户代码，它允许开发人员能够访问的基础系统的各种 Api。 单声道运行时是用 C 语言编写的。

你可以使用[系统](http://msdn.microsoft.com/en-us/library/system.aspx)， [System.IO](http://msdn.microsoft.com/en-us/library/system.io.aspx)， [System.Net](http://msdn.microsoft.com/en-us/library/system.net.aspx)和.NET 的其余部分类库来访问基础的 Linux 操作系统工具。

在 Android 上，大部分如音频、 图形、 OpenGL 和电话服务的系统功能不可用直接向本机应用程序，它们才会通过驻留在其中一个 Android 运行时 Java Api 公开[Java](https://developer.xamarin.com/api/namespace/Java.Lang/)。 *命名空间或[Android](https://developer.xamarin.com/api/namespace/Android/)。 * 命名空间。 体系结构是大致如下：

[![内核上方和下方.NET/Java + 绑定 Mono 和艺术作品的图示](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Xamarin.Android 开发人员访问操作系统通过他们所熟悉的.NET api 调用 （用于低级别的访问权限） 或使用 Java api 公开的桥梁 Android 命名空间中公开的类中的各种功能Android 运行时。

Android 类如何与 Android 运行时类进行通信的详细信息请参阅[API 设计](~/android/internals/api-design.md)文档。


## <a name="application-packages"></a>应用程序包

Android 应用程序包是包含 ZIP 容器*.apk*文件扩展名。 Xamarin.Android 应用程序包具有相同的结构和布局正常 Android 包，添加了以下内容：

-   （包含 IL） 的应用程序程序集是*存储*内未压缩*程序集*文件夹。 在过程中在版本中的启动生成*.apk*是*mmap()* ed 到过程和程序集已从内存加载。 这就允许更快的应用程序启动，因为程序集不需要在执行之前提取。 - *注意：*之类的程序集位置信息[Assembly.Location](https://developer.xamarin.com/api/property/System.Reflection.Assembly.Location/)和[Assembly.CodeBase](https://developer.xamarin.com/api/property/System.Reflection.Assembly.CodeBase/)
    *不能指望*版本中生成。 为不同的文件系统条目不存在，并且它们没有可用的位置。


-   包含单声道运行时本机库内有*.apk* 。 Xamarin.Android 应用程序必须包含本机库所需目标 Android 体系结构，例如*armeabi* ， *armeabi v7a* ， *x86* 。 Xamarin.Android 应用程序无法运行在平台上，除非它包含相应的运行时库。


Xamarin.Android 应用程序还包含*Android 可调用包装器*以允许 Android 来调入托管代码。



## <a name="android-callable-wrappers"></a>Android 可调用包装器

- **Android 可调用包装器**是[JNI](http://en.wikipedia.org/wiki/Java_Native_Interface)桥用于 Android 的运行时需要调用托管的代码的任何时间。 Android 可调用包装器是如何虚拟方法可以重写，Java 接口可实现。 请参阅[Java 集成概述](~/android/platform/java-integration/index.md)文档中的详细信息。


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>托管的可调用包装器

托管的可调用包装器可用于托管的代码需要调用 Android 代码和重写虚拟方法并实现 Java 接口为提供支持的任何时间 JNI 桥。 整个[Android](https://developer.xamarin.com/api/namespace/Android/)。 * 和相关命名空间是生成通过托管的可调用包装器[.jar 绑定](~/android/platform/binding-java-library/index.md)。
托管的可调用包装器负责进行托管和 Android 类型之间进行转换和调用通过 JNI 基础的 Android 平台方法。

每创建托管的可调用包装器保留的 Java 全局引用，即可以通过访问[Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/)属性。 全局引用用于提供 Java 实例和托管的实例之间的映射。 全局引用是有限的资源： 仿真程序允许仅 2000年的全局引用，而大多数硬件允许超过 52,000 全局引用一次存在存在一次。

若要跟踪创建和销毁全局引用时，你可以设置[debug.mono.log](~/android/troubleshooting/index.md)系统属性以包含[gref](~/android/troubleshooting/index.md)。

可以通过调用显式释放全局引用[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)上托管的可调用包装器。 这将删除 Java 实例和托管的实例之间的映射，并允许要收集的 Java 实例。 如果从托管代码重新访问 Java 实例时，将为其创建新的托管可调用包装器。

当释放的托管可调用包装器，如果实例无意中共享线程，作为释放实例之间将影响任何其他线程的引用，因此必须小心谨慎。 为了最大的安全，仅`Dispose()`的已分配通过实例`new`*或*从方法的您*知道*始终分配新的实例和可能不缓存的实例导致线程间共享的意外实例。



## <a name="managed-callable-wrapper-subclasses"></a>管理可调用包装器子类

托管的可调用包装器子类是所有的"有趣"应用程序特定逻辑可能居住在哪里。 其中包括自定义[Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)子类 (如[Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13)默认项目模板中的类型)。 (具体而言，这些是任何*Java.Lang.Object*子类中执行此操作*不*包含[RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)自定义特性或[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)是*false*，这是默认设置。)

如管理可调用包装器，管理可调用包装器子类还包含全局引用，可通过访问[Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/)属性。 就像与托管的可调用包装器，通过调用全局引用可以显式释放操作[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)。
与托管的可调用包装器，不同*格外注意*应在这种情况下，作为释放之前拍摄*dispose （)*运算结果的实例将中断 Java 实例之间的映射 (的实例Android 可调用包装器） 和托管的实例。


### <a name="java-activation"></a>Java 激活

当[Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)(ACW) 创建从 Java，ACW 构造函数将导致相应 C# 要调用构造函数。 例如，对于 ACW *MainActivity*将包含一个默认构造函数将调用其*MainActivity*的默认构造函数。 (通过完成此操作*TypeManager.Activate()* ACW 构造函数中调用。)

没有一个后果其他构造函数签名： *（IntPtr，JniHandleOwnership）*构造函数。 *（IntPtr，JniHandleOwnership）*每当 Java 对象公开给托管代码和托管的可调用包装器就需要构造管理 JNI 句柄调用构造函数。 这通常是自动完成。

在其中的两种方案*（IntPtr，JniHandleOwnership）*构造函数必须手动管理可调用包装器子类上提供：

1. [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/)进行了子类化。 *应用程序*是特殊; 默认值*应用程序*构造函数将*永远不会*通过调用和[（IntPtr，JniHandleOwnership） 必须改为提供构造函数](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. 虚拟方法从基类构造函数的调用。


请注意，(2) 是存在泄漏的抽象。 在 Java 中，与 C# 中，从构造函数对虚方法的调用会始终调用派生程度最高的方法实现。 例如， [TextView (上下文，AttributeSet，int) 构造函数](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/)调用虚方法[TextView.getDefaultMovementMethod()](http://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod())，该绑定为[TextView.DefaultMovementMethod 属性](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/)。
因此，如果某个类型[LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) (1) 与[子类 TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26)，(2)[重写 TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)，和 (3)[激活的实例类通过 XML，](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29)重写*DefaultMovementMethod* ACW 构造函数有机会执行，并会之前的 C# 构造函数有机会执行之前将调用的属性。

支持这种实例化实例 LogTextBox 通过[LogTextView （IntPtr，JniHandleOwnership）](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28)构造函数时 ACW LogTextBox 实例首次进入托管代码中，然后调用[LogTextBox (上下文，IAttributeSet，int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41)构造函数*同一个实例上*ACW 构造函数的执行时。

事件的发生顺序：

1.  布局 XML 加载到[ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41)。

2.  Android 进行实例化布局对象图中，并实例化的实例*monodroid.apidemo.LogTextBox* ，为 ACW *LogTextBox* 。

3.  *Monodroid.apidemo.LogTextBox*构造函数执行[android.widget.TextView](http://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29)构造函数。

4.  *TextView*构造函数时，将调用*monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* 。

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* invokes *LogTextBox.n_getDefaultMovementMethod()* , which invokes *TextView.n_GetDefaultMovementMethod()* , which invokes [Java.Lang.Object.GetObject&lt;TextView&gt; (handle, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;（)*检查以确定是否已经存在相应的 C# 实例*处理*。 如果没有，则返回它。 在此方案中，不存在，因此*Object.GetObject&lt;T&gt;（)*必须创建一个。

7.  *Object.GetObject&lt;T&gt;（)*查找*LogTextBox （IntPtr，JniHandleOwneship）*构造函数，对其进行调用，之间创建映射*处理*和创建的实例，并返回创建的实例。

8.  *TextView.n_GetDefaultMovementMethod()*时，将调用*LogTextBox.DefaultMovementMethod*属性 getter。

9.  控制权将返回给*android.widget.TextView*构造函数，完成执行。

10. *Monodroid.apidemo.LogTextBox*构造函数执行时，调用*TypeManager.Activate()* 。

11. *LogTextBox (上下文，IAttributeSet，int)*构造函数执行*(7) 中创建的同一个实例*。

12. ...


如果 （IntPtr，JniHandleOwnership） 找不到构造函数，则[System.MissingMethodException](https://developer.xamarin.com/api/type/System.MissingMethodException/)将引发。


<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>过早 dispose （） 调用

没有 JNI 句柄和对应的 C# 实例之间的映射。 Java.Lang.Object.Dispose() 中断此映射。 如果 JNI 句柄映射已中断后进入托管的代码，其外观 Java 激活和*（IntPtr，JniHandleOwnership）*将检查并调用构造函数。 如果构造函数不存在，将引发异常。

例如，给定以下托管可调用 Wraper 子类：

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

如果我们创建实例，dispose （），并且会导致管理可调用包装器重新创建以：

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

将死程序：

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

如果确实包含子类*（IntPtr，JniHandleOwnership）*构造函数，则*新*将创建类型的实例。 因此，该实例将显示"丢失"实例的所有数据，因为它是的新实例。 （请注意的值为 null。）

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

仅*dispose （)*的管理可调用包装器子类，当你知道将不再，使用的 Java 对象或子类不包含实例数据时和*（IntPtr，JniHandleOwnership）*提供了构造函数。



## <a name="application-startup"></a>应用程序启动

当活动、 服务中时，等启动时，Android 将首先检查以查看是否已有运行状态以便托管活动/服务/等的进程。如果没有此进程存在，则将创建一个新进程， [AndroidManifest.xml](http://developer.android.com/guide/topics/manifest/manifest-intro.html)是读取和中指定的类型[ /manifest/application/@android:name ](http://developer.android.com/guide/topics/manifest/application-element.html#nm)加载属性并将其实例化。 接下来，指定的所有类型[ /manifest/application/provider/@android:name ](http://developer.android.com/guide/topics/manifest/provider-element.html#nm)属性值进行实例化，并且具有其[ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/)方法调用。 通过将添加到此 Xamarin.Android 挂钩*单声道。MonoRuntimeProvider* *ContentProvider*到生成过程的 AndroidManifest.xml。 *单声道。MonoRuntimeProvider.attachInfo()*方法负责单声道运行时加载到进程。
在此点之前使用 Mono 任何尝试将失败。 (*注意*： 这是为什么类型的子类[Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/)需要提供[（IntPtr，JniHandleOwnership） 构造函数](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103)，如应用程序实例创建可以初始化 Mono 之前。）

完成后进程初始化，`AndroidManifest.xml`参考查找以启动活动/服务/等的类名称。 例如， [ /manifest/application/activity/@android:name属性](http://developer.android.com/guide/topics/manifest/activity-element.html#nm)用于确定加载活动的名称。 为活动，此类型必须继承[android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)。
指定的类型加载通过[Class.forName()](http://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (这需要的类型是 Java 类型，因此 Android 可调用包装器)，然后实例化。 创建 Android 可调用包装器实例将触发创建相应的 C# 类型的实例。 然后将调用 android [Activity.onCreate(Bundle)](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) ，这将导致相应[Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)要调用，并且你已关闭到争用。
