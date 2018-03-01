---
title: "垃圾回收"
ms.topic: article
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: d2298cf3edcadcc8a4d781e3e121852886fbf1d2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="garbage-collection"></a>垃圾回收

Xamarin.Android 使用 Mono 的[简单代的垃圾回收器](http://www.mono-project.com/Compacting_GC)。 这是与这两代标记扫描垃圾回收器和*大型对象空间*，使用两种类型的集合： 

-   次要集合 （收集 Gen0 堆） 
-   （收集 Gen1 和大型对象空间堆） 的主要集合。 

> [!NOTE]
> **注意：**通过显式集合没有[GC。Collect()](https://developer.xamarin.com/api/member/System.GC.Collect/)集合是*按需*、 根据堆分配。 *这不是引用计数系统*; 对象*只要没有任何未完成的引用将不会收集*，或当作用域已退出。 GC 将次要堆新分配的内存不足时运行。 如果不存在分配，则将无法运行。


次要集合比较便宜而频繁，并且用于收集最近分配和死对象。 在分配的对象的每个几 MB 后执行次要的集合。 次要集合可能手动执行通过调用[GC。收集 (0)](https://developer.xamarin.com/api/member/System.GC.Collect/p/System.Int32/) 

主要的集合成本高昂且不太频繁，并且用于回收所有死对象。 主要的集合执行后 （在调整大小的堆） 之前的当前的堆大小耗尽内存。 主要的集合可能手动执行通过调用[GC。收集 （）](https://developer.xamarin.com/api/member/System.GC.Collect/)或通过调用[GC。收集 (int)](https://developer.xamarin.com/api/member/System.GC.Collect/p/System.Int32)具有自变量[GC。MaxGeneration](https://developer.xamarin.com/api/property/System.GC.MaxGeneration/)。 


<a name="Cross-VM_Object_Collections" />

## <a name="cross-vm-object-collections"></a>跨 VM 对象集合

有三个类别的对象类型。

-   **托管对象**： 类型执行此操作*不*继承[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) ，例如[System.String](https://developer.xamarin.com/api/type/System.String/)。 
    这些通常收集的 GC。 

-   **Java 对象**: Java 类型进行了 Android 运行时 VM 中存在但不是能用于 Mono VM。 这些无聊，并且不会进一步讨论。 这些通常收集的 Android 运行时 VM。 

-   **对等对象**： 类型可实现[IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) ，例如所有[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)和[Java.Lang.Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/)子类。 这些类型的实例具有两个"halfs"*托管的对等*和*本机的对等*。 托管的对等方是 C# 类的实例。 是一个实例的 VM，Android 的运行时中的 Java 类和 C#，本机的对等[IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/)属性包含到本机的对等方的 JNI 全局引用。 


有两种类型的本机对等节点：

-   **框架对等节点**： 知道 nothing Xamarin.Android，例如"Normal"Java 类型[android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)。

-   **用户对等节点**: [Android 可调用包装器](~/android/platform/java-integration/working-with-jni.md)在每个应用程序中存在的 Java.Lang.Object 子类的生成时生成的。


因为有两个 Vm 在 Xamarin.Android 进程内的，有两种类型的垃圾回收：

-   Android 运行时集合 
-   单声道集合 

Android 运行时集合运行正常，但需要注意： JNI 全局引用被作为垃圾回收根节点。 因此，如果存在 JNI 全局引用 VM 对象，该对象保存到 Android 运行时*无法*被收集，即使这样，则不符合回收的条件。

单声道集合是 fun 会发生情况。 通常情况下收集托管的对象。 对等对象将收集通过执行以下过程：

1.  符合单声道回收的条件的所有对等对象都有替换 JNI 弱全局引用其 JNI 全局引用。 

2.  调用 VM GC Android 运行时。 可能会收集任何本机的对等实例。 

3.  检查在 (1) 中创建的 JNI 弱全局引用。 如果已收集的弱引用，然后收集对等对象。 弱引用是否*不*已收集，然后弱引用替换为 JNI 全局引用并不收集到的对等对象。 注意： 在 API 14+，这意味着，从返回的值`IJavaObject.Handle`垃圾回收后仍可能会更改。 

所有这是一个对等对象的实例将实时，只要它通过以下任一方式引用的最终结果托管代码 (例如存储在`static`变量) 或所引用的 Java 代码。 此外，本机对等方的生存期将扩展超出否则它们将实时，直至本机的对等和托管等变为可回收本机的对等不会可回收。

<a name="Object_Cycles" />

## <a name="object-cycles"></a>对象循环

对等对象是中的 Android 运行时和 Mono VM 的逻辑上存在。 例如， [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)托管的对等实例将具有相应[android.app.Activity](http://developer.android.com/reference/android/app/Activity.html)框架对等 Java 实例。 继承的所有对象[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)需要具有这两个 Vm 中的表示形式。 

具有这两个 Vm 中表示形式的所有对象都将都有进行了扩展，相比有仅在一个单独的 VM 内的对象的生存期 (如[ `System.Collections.Generic.List<int>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.List%601/))。 调用[GC。收集](https://developer.xamarin.com/api/member/System.GC.Collect/)一定不会收集这些对象，如 Xamarin.Android GC 需要确保它在收集前不引用由任一 VM 的对象。 

若要缩短对象生存期[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)应被调用。 这将会手动"断开"上的对象通过释放的全局引用，从而允许要更快地收集的对象的两个 Vm 之间的连接。 

<a name="Automatic_Collections" />

## <a name="automatic-collections"></a>自动集合

开头[版本 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0)，Xamarin.Android gref 阈值时自动执行完整的 GC。 此阈值为 90%的平台已知的最大 grefs: 1800 grefs 在仿真程序 (2000 max) 和 46800 grefs 硬件 (最大 52000) 上的。 *注意：* Xamarin.Android 只对计数由 grefs [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)，并且将无法知道有关过程中创建的任何其他 grefs。 这是启发式方法*仅*。 

当执行自动的集合时，将向调试日志输出类似于以下的消息：

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

此匹配项是不确定的并可能发生这种情况不适当的时候 （例如中间图形呈现）。 如果你看到此消息，你可能想要执行的显式集合在其他位置，或你可能想要尝试[缩短对等对象的生存期](#Helping_the_GC)。 

## <a name="gc-bridge-options"></a>GC 桥选项

Xamarin.Android 提供与 Android 和 Android 运行时的透明内存管理。 它作为单声道垃圾回收器调用的扩展实现*GC 桥*。 

在单声道垃圾回收和图形对等方出对象需要使用 Android 的运行时堆的验证其"活动"过程的工作原理 GC 桥。 GC 桥做出该决定通过执行以下步骤 （按顺序）：

1.  引入到它们表示的 Java 对象无法访问对等对象的单声道引用关系图。 

2.  执行 Java GC。

3.  验证为真的死对象的对象。 

此复杂的过程是一种使的子类`Java.Lang.Object`自由地引用的任何对象; 它可以消除的 Java 对象可绑定到 C# 任何限制。 由于此复杂性，桥过程可能会耗费大量资源，并且可能导致应用程序中的明显暂停。 如果应用程序遇到大量暂停，值得调查以下三个 GC 桥实现之一： 

-   **Tarjan** -GC 桥的全新设计基于[Robert Tarjan 算法和向后引用传播](http://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm)。
    它具有在我们模拟工作负荷下的最佳性能，但它还具有更大的实验性代码共享。 

-   **新**-原始代码，修复二次行为的两个实例，但保留的核心算法的主要检查 (根据[Kosaraju 的算法](http://en.wikipedia.org/wiki/Kosaraju's_algorithm)强查找连接组件)。 

-   **旧**-（视为最稳定的三个） 的原始实现。 这是应用程序应使用如果桥`GC_BRIDGE`暂停时间会可接受。 


最好地找出哪些 GC 桥工作原理的唯一方法是通过应用程序中进行试验和分析输出。 有两种方法来为基准测试收集数据： 

-   **启用日志记录**-启用日志记录 (如描述[配置](~/android/internals/garbage-collection.md)部分) 为每个 GC 桥选项，然后捕获并比较每个设置的日志输出。 检查`GC`消息对于每个选项，尤其是，`GC_BRIDGE`消息。 为非交互式应用程序可承受，但上面非常交互式应用程序 （如游戏） 60 毫秒的暂停时间会有问题，请暂停达 150ms年。 

-   **启用桥记帐**-桥记帐将显示指向桥过程中涉及的每个对象的对象的平均成本。 按大小排序此信息将提供有关什么持有的其他对象最大数量的提示。 


指定哪些`GC_BRIDGE`应使用选项应用程序，请将传递`bridge-implementation=old`，`bridge-implementation=new`或`bridge-implementation=tarjan`到`MONO_GC_PARAMS`环境变量，例如： 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

默认设置是**Tarjan**。 如果你发现回归测试，你可能发现需要将此选项设置为**旧**。 此外，你可以选择使用更加稳定**旧**选项如果**Tarjan**不会生成中的性能改进。 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>帮助 GC

有多种方法来帮助以减少内存使用和收集时间的 GC。


<a name="Disposing_of_Peer_instances" />

### <a name="disposing-of-peer-instances"></a>释放的对等实例

GC 具有不完整的视图的过程和可能不会运行时内存较低的因为 GC 不知道该内存较低。 

例如，实例[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)类型或派生的类型是至少 20 字节的大小 (有更改恕不另行通知等，等等。)。 
[托管可调用包装器](~/android/internals/architecture.md)不要添加其他实例成员，因此如果你具有[Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/)实例引用的内存的 10 MB blob Xamarin.Android 的 GC 不会知道&ndash;GC将看到 20 字节对象，将无法确定它链接到 Android 保持 10 MB 的内存处于活动状态的运行时分配的对象。 

它是经常需要帮助 GC。 遗憾的是， *GC。AddMemoryPressure()*和*GC。RemoveMemoryPressure()*不支持，因此，如果你*知道*你只需释放大型 Java 分配的对象关系图可能需要手动调用[GC。Collect()](https://developer.xamarin.com/api/member/System.GC.Collect/)到提示符 GC 释放 Java 端内存，也可以显式释放*Java.Lang.Object*子类，重大托管可调用包装器和 Java 实例之间的映射。 有关示例，请参阅[Bug 1084](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6)。 


> [!NOTE]
> **注意：**你必须是*极*释放时请小心`Java.Lang.Object`子类实例。

为了尽量减少内存损坏的可能性，遵循以下指导原则调用时`Dispose()`。


#### <a name="sharing-between-multiple-threads"></a>多个线程间共享

如果*Java 或托管*可能多个线程间共享实例*不应`Dispose()`d*，**曾经**。 例如， [ `Typeface.Create()` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle))可能会返回*缓存的实例*。 如果多个线程提供的相同自变量，它们将获得*相同*实例。 因此，`Dispose()`运算结果`Typeface`实例从一个线程可能会使其他线程，这可能会导致`ArgumentException`从 s `JNIEnv.CallVoidMethod()` （及其他） 因为实例已从另一个线程处理。 


#### <a name="disposing-bound-java-types"></a>释放绑定的 Java 类型

如果实例是绑定的 Java 类型，可以释放实例*，只要*实例不会重复使用从托管代码*和*Java 实例不能在线程 （请参阅以前之间共享`Typeface.Create()`讨论)。 （此决定可能很难做出。）Java 实例进入下一次托管代码，*新*将为其创建包装器。 

这会经常用到，当涉及到 Drawables 和其他资源密集型实例：

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

以上是安全因为对等方， [Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/)框架对等方，将引用返回*不*用户对等。 `Dispose()`的末尾调用`using`块将中断之间的托管关系[Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/)和 framework [Drawable](http://developer.android.com/reference/android/graphics/drawable/Drawable.html)情况下，允许 Java 实例设置为收集只要 Android 运行时需要。 这将*不*为安全起见，如果对等实例引用了用户对等; 我们将在此处使用"external"须知*知道*，`Drawable`用户对等方，不能引用，因此`Dispose()`调用是安全的。 


#### <a name="disposing-other-types"></a>释放其他类型 

如果实例是指不 Java 类型的绑定类型 (如自定义`Activity`)，**根本不**调用`Dispose()`除非你*知道*没有 Java 代码将在程序中调用重写的方法实例。 如果不这样做会导致[ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls)。 

例如，如果你有一个自定义单击侦听器：

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

你*不应*释放此实例，如 Java 将尝试在将来调用它的方法：

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>使用显式检查以避免异常

如果已实现[Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/)重载方法，避免接触涉及 JNI 的对象。 这样可以创建*双释放*它可以让你的代码 （严重） 的情况下尝试访问已被垃圾回收的基础的 Java 对象。 这样将产生类似于以下异常： 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

此情况通常在窗体的对象的第一个 dispose 导致成员变为空，并在此 null 成员上的后续访问尝试，则可能会引发异常导致时发生。 具体而言，该对象的`Handle`（该链接的托管的实例到其基础的 Java 实例） 上第一个窗体的 dispose，失效，但仍将托管的代码尝试访问此基础的 Java 实例，即使它不再可用 （请参阅[管理可调用包装器](~/android/internals/architecture.md#Managed_Callable_Wrappers)有关 Java 实例和托管的实例之间的映射详细信息)。 

若要避免此异常一种好方法是若要显式验证在你`Dispose`的托管的实例和基础 Java 实例之间的映射是否仍然有效; 即方法中，检查以查看对象的`Handle`为 null (`IntPtr.Zero`)然后才能访问其成员。 例如，以下`Dispose`方法访问`childViews`对象： 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);
        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

如果初始释放传递原因`childViews`具有无效`Handle`、`for`循环访问将引发`ArgumentException`。 通过添加显式`Handle`null 检查，然后再第一个`childViews`访问，以下`Dispose`方法可防止发生异常： 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);

        // Check for a null handle:
        if (this.childViews.Handle == IntPtr.Zero)
            return;

        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

<a name="Reduce_Referenced_Instances" />

### <a name="reduce-referenced-instances"></a>减少引用的实例

每当的实例`Java.Lang.Object`类型或子类 GC，整个过程中扫描*对象图*，该实例是指必须还能扫描。 对象图是一组的"根实例"引用的对象实例*加上*引用根实例的所有内容引用，以递归方式。 

请考虑以下类：

```csharp
class BadActivity : Activity {

    private List<string> strings;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

当`BadActivity`是构造，对象图将包含 10004 实例 (1 x `BadActivity`，1 x `strings`，1 x`string[]`持有`strings`，x 字符串实例的 10000)，*所有*的这将需要为每当扫描`BadActivity`扫描实例。 

这可能会产生不利影响上您集合的时间，从而提高 GC 暂停时间。 

你可以帮助通过 GC*减少*这由用户对等实例取得 root 权限的对象图的大小。 在上面的示例中，这可以通过移动`BadActivity.strings`到一个单独的类，该类从 Java.Lang.Object 不继承： 

```csharp
class HiddenReference<T> {

    static Dictionary<int, T> table = new Dictionary<int, T> ();
    static int idgen = 0;

    int id;

    public HiddenReference ()
    {
        lock (table) {
            id = idgen ++;
        }
    }

    ~HiddenReference ()
    {
        lock (table) {
            table.Remove (id);
        }
    }

    public T Value {
        get { lock (table) { return table [id]; } }
        set { lock (table) { table [id] = value; } }
    }
}

class BetterActivity : Activity {

    HiddenReference<List<string>> strings = new HiddenReference<List<string>>();

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

<a name="Minor_Collections" />

## <a name="minor-collections"></a>次要集合

次要集合可能手动执行通过调用[GC。Collect(0)](https://developer.xamarin.com/api/member/System.GC.Collect/p/System.Int32)。 次要集合是 （相比于主要的集合） 比较便宜，但不要具有明显固定成本，因此你不想要触发过于频繁，并且应具有几毫秒的暂停时间。 

如果你的应用程序具有即相同的操作需要进行反复的"任务周期"，帐户可以是手动继续执行次要集合，建议你，一旦任务周期已结束。 示例工作周期包括： 

-  单个游戏框架在呈现周期。
-  与给定的应用对话框 （打开，填充，关闭） 的整个交互 
-  若要刷新/同步应用程序数据的网络请求的组。


<a name="Major_Collections" />

## <a name="major-collections"></a>主要的集合

主要的集合可能手动执行通过调用[GC。Collect()](https://developer.xamarin.com/api/member/System.GC.Collect/)或`GC.Collect(GC.MaxGeneration)`。 

应进行很少，并且它们可以有第二个的暂停时间在 Android 样式设备上收集 512 MB 堆时。 

主要的集合才应手动调用，即使有： 

-   时间较长的税末尾周期和 long 类型的值的暂停时不会向用户显示问题。 

-   中被重写[Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/)方法。 


<a name="Diagnostics" />

## <a name="diagnostics"></a>诊断

若要跟踪创建和销毁全局引用时，你可以设置[debug.mono.log](~/android/troubleshooting/index.md)系统属性以包含[ *gref* ](~/android/troubleshooting/index.md)和/或[ *gc*](~/android/troubleshooting/index.md)。 


<a name="Configuration" />

## <a name="configuration"></a>配置

可以通过设置配置 Xamarin.Android 垃圾回收器`MONO_GC_PARAMS`环境变量。 可能具有的生成操作设置环境变量[AndroidEnvironment](~/android/deploy-test/environment.md)。

`MONO_GC_PARAMS`环境变量是以下参数的以逗号分隔列表： 

-   `nursery-size` = *大小*： 设置 nursery 的大小。 大小以字节为单位指定，并且必须是 2 的幂。 后缀`k`，`m`和`g`可用来分别指定千、 兆和千兆字节。 Nursery 是第一代 （两个）。 更大 nursery 通常会提高程序速度，但很明显使用更多内存。 默认 nursery 大小 512 kb。 

-   `soft-heap-limit` = *大小*： 目标最大托管应用程序的内存消耗。 当内存使用情况低于指定的值时，GC 非常适合执行时间 （较少集合）。 
    此限制，上面 GC 进行了优化内存使用情况 （多个集合）。 

-   `evacuation-threshold` = *阈值*： 设置撤出所需的阈值百分比。 值必须在 0 到 100 范围内的整数。 默认值为 66。 如果集合的扫描阶段找到特定的堆块类型的占用小于此百分比，它将执行在下一步的主要集合中，该块类型，从而恢复到占用接近 100%的复制集合。 值为 0 将关闭撤出所需的。 

-   `bridge-implementation` = *桥接实现*： 此操作将设置 GC 桥选项，可帮助解决 GC 性能问题。 有三个可能值：*旧*，*新*， *tarjan*。

-   `bridge-require-precise-merge`： 桥包含一种优化这可能，在少数情况下，会使对象可 Tarjan 收集一个 GC 后第一次将变为垃圾回收。 包括此选项会禁用该优化，使 Gc 更可能较慢但可预测。

例如，若要配置为具有堆大小限制为 128 MB 到 GC，添加一个新的文件到你的项目与**生成操作**的`AndroidEnvironment`的内容： 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
