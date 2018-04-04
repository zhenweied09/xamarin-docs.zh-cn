---
title: API 设计
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: a9c0b02457f006f75dc5b6f0a52e68865d620f67
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="api-design"></a>API 设计


## <a name="overview"></a>概述

除了属于 Mono 的基类库的核心，Xamarin.Android 附带的各种 Android Api 允许开发人员使用 Mono 创建本机 Android 应用程序的绑定。

存在 Xamarin.Android 的核心是互操作的引擎与 Java 领域这样桥 C# 的环境并允许开发人员访问对 Java Api 通过 C# 或其他.NET 语言。


## <a name="design-principles"></a>设计原则

以下是一些 Xamarin.Android 绑定我们设计原则

-  符合[.NET Framework 设计准则](http://msdn.microsoft.com/en-us/library/ms229042.aspx)。

-  允许开发人员子类 Java 类。

-  子类应适用于 C# 标准的结构。

-  从现有类派生。

-  链接到调用基构造函数。

-  重写方法应使用 C# 重写系统完成。

-  使常见的 Java 任务变得简单和硬 Java 任务成为可能。

-  将 JavaBean 属性公开为 C# 属性。

-  公开一个强类型的 API:

    - 提高类型安全性。

    - 最大程度减少运行时错误。

    - 获取返回类型上的 IDE intellisense。

    - 允许 IDE 弹出项文档。

-  鼓励 IDE 中浏览的 api:

    - 利用最小化 Java Classlib 公开的 Framework 替代方法。

    - 当相应且适用，而不是单方法接口公开 C# 委托 (lambda，匿名方法和 System.Delegate)。

    - 提供一种机制来调用任意 Java 库 ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/))。



## <a name="assemblies"></a>程序集

Xamarin.Android 包含组成程序集的大量*MonoMobile 配置文件*。 [程序集](~/cross-platform/internals/available-assemblies.md)页提供了更多信息。

中包含到 Android 平台绑定`Mono.Android.dll`程序集。 此程序集包含使用 Android Api 的整个绑定以及与 Android 运行时 VM 通信。


## <a name="binding-design"></a>绑定设计


### <a name="collections"></a>集合

Android Api 利用广泛要提供列表、 集和地图的 java.util 集合。 我们将使用这些元素公开[System.Collections.Generic](https://developer.xamarin.com/api/namespace/System.Collections.Generic/)我们的绑定中的接口。 基本的映射是：

-   [java.util.Set<E> ](http://developer.android.com/reference/java/util/Set.html)映射到系统类型[ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx)，帮助器类[Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/)。

-   [java.util.List<E> ](http://developer.android.com/reference/java/util/List.html)映射到系统类型[IList<T>](http://msdn.microsoft.com/en-us/library/5y536ey6.aspx)，帮助器类[Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/)。

-   [java.util.Map < K，V >](http://developer.android.com/reference/java/util/Map.html)映射到系统类型[IDictionary < TKey，>](http://msdn.microsoft.com/en-us/library/s4ys34ea.aspx)，帮助器类[Android.Runtime.JavaDictionary < K，V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/)。

-   [java.util.Collection<E> ](http://developer.android.com/reference/java/util/Collection.html)映射到系统类型[ICollection<T>](http://msdn.microsoft.com/en-us/library/92t2ye13.aspx)，帮助器类[Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/)。

我们提供了帮助程序类，来帮助更快地 copyless 这些类型的封送处理。 如果可能，我们建议使用这些提供而不是提供框架实现的集合，如[ `List<T>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.List%601/)或[ `Dictionary<TKey, TValue>` ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%602/)。 [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/)实现内部利用本机 Java 集合，因此不需要复制到和从本机集合，将传递给 Android API 成员时。

你可以将任何接口的实现传递给 Android 方法接受该接口，例如传递`List<int>`到[ArrayAdapter&lt;int&gt;(上下文，int、 IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)构造函数。 *但是*，为所有实现*除*对于 Android.Runtime 实现中，这涉及到*复制*从 Mono VM 到 Android 运行时 VM 的列表。 如果列表为更高版本更改 Android 运行时中 (例如通过调用[ArrayAdapter&lt;T&gt;。Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/)方法)，这些更改*不将*可在托管代码中看到。 如果`JavaList<int>`已使用，这些更改将可见。

重新解释，集合接口实现的*不*上述任一列出**帮助器类**es 仅将封送处理 []:

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```


### <a name="properties"></a>属性

Java 方法被转换为属性，在适当的时候：

-  Java 方法对`T getFoo()`和`void setFoo(T)`转换为`Foo`属性。 示例： [Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/)。

-  Java 方法`getFoo()`转换为只读 Foo 属性。 示例： [Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/)。

-  仅属性不会生成。

-  属性是*不*生成如果属性类型将是一个数组。



### <a name="events-and-listeners"></a>事件和侦听器

Android Api 的构建基于 Java 和其组件遵循挂接事件侦听器的 Java 模式。 此模式往往会很麻烦，因为它需要用户创建一个匿名类并声明重写的方法，例如，这是将如何在使用 Java 的 Android 中执行操作：

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

将 C# 使用事件中的等效代码：

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

请注意，上面的机制都适用于 Xamarin.Android。 您可以实施侦听器接口并将其附加与 View.SetOnClickListener，或者你可以将附加创建通过任何的常用 C# 范例到 Click 事件的委托。

当侦听器回叫方法具有 void 返回时，我们创建基于的 API 元素[EventHandler&lt;TEventArgs&gt; ](https://developer.xamarin.com/api/type/System.EventHandler%601/)委托。 我们生成事件如上面的示例中为这些侦听器类型。 但是，如果侦听器回调返回一个非 void 的和非-**布尔**不使用值、 事件和事件处理。 相反，我们将生成特定回调的签名委托并将添加而不是事件的属性。 原因是处理委托调用排序并返回处理。 此方法会镜像使用 Xamarin.iOS API 执行哪些操作。

如果仅自动生成 C# 事件或属性 Android 事件注册方法：

1. 具有`set`前缀，例如[*设置*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/)。

1. 具有`void`返回类型。

1. 只接受一个参数，参数类型是接口，该接口具有只有一个方法，和中结束的接口名称`Listener`，例如[View.OnClick*侦听器*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/)。


此外，如果侦听器接口方法具有返回类型的**布尔**而不是**void**，然后生成*EventArgs*子类将包含*Handled*属性。 值*Handled*属性用作的返回值*侦听器*方法，并默认为`true`。

例如，Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/)方法接受[View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener)接口，与[View.OnKeyListener.onKey （视图、 int、 KeyEvent）](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/)方法具有返回类型为 boolean 类型。 Xamarin.Android 生成相应[View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/)事件，即[EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/)。
*KeyEventArgs*类反过来具有[View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/)属性的返回值中用作*View.OnKeyListener.onKey()*方法。

我们想要添加的其他方法和构造来公开基于委托的连接的重载。 此外，具有多个回调的侦听器需要某些其他检查，以确定是否实现单个回调是合理的因此我们要将这些转换随着对它们进行标识。 如果没有对应的事件，侦听器必须使用 C# 中，但请使任何你认为可能具有委托使用情况与我们关注。 我们还执行某些转换而不使用"侦听器"后缀的接口时很显然，它们可以将受益于委托的替代项。

所有的侦听器接口实现[ `Android.Runtime.IJavaObject` ](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)接口，因为绑定，因此侦听器类必须实现此接口的实现详细信息。 这可以通过上的一个子类中实现侦听器接口完成[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)或任何其他包装 Java 对象，如对 Android 活动。


### <a name="runnables"></a>可运行对象

Java 利用[java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/)接口，以提供一种委派机制。 [Java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/)类是值得注意的使用者的此接口。 Android 已经利用了 API 以及中的接口。
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/)和[View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable)是值得注意的示例。

`Runnable`接口包含单个 void 方法[run （)](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/)。 它因此本身适于在 C# 中作为绑定[System.Action](http://msdn.microsoft.com/en-us/library/system.action.aspx)委托。 我们提供了在绑定中的重载接受`Action`参数使用的所有 API 成员`Runnable`在本机 API 中，例如[Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action))和[View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

我们留[IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/)作为可运行对象直接传递中而不是因为几种类型实现的接口，并因此可以替换它们的位置的重载。


### <a name="inner-classes"></a>内部类

Java 具有两个不同类型的[嵌套类](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html)： 静态嵌套类和非静态类。

Java 静态嵌套的类均与 C# 嵌套类型相同。

非静态嵌套类，也称为*内部类*，大不相同。 它们包含对其封闭类型的实例的隐式引用，并且不能包含静态成员 （在本概述的范围之外的其他差异）。

当涉及到绑定和 C# 使用时，静态的嵌套的类被视为正常的嵌套类型。 内部类，同时，具有两个重要的差异：

1. 作为构造函数参数，必须显式提供对包含类型的隐式引用。

1. 从内部类，内部类继承时*必须*嵌套在类型从包含类型的基的内部类，该类继承，派生的类型必须提供与 C# 属于同一类型的构造函数包含类型。


例如，考虑[Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/)内部类。 因为它是一个内部的类、 [WallpaperService.Engine() 构造函数](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/)采用对引用[WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/)实例 (比较和对比到 Java [WallpaperService.Engine (） 构造函数，](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/)其中不带任何参数)。

内部类示例派生是 CubeWallpaper.CubeEngine:

```csharp
class CubeWallpaper : WallpaperService {
        public override WallpaperService.Engine OnCreateEngine ()
        {
                return new CubeEngine (this);
        }

        class CubeEngine : WallpaperService.Engine {
                public CubeEngine (CubeWallpaper s)
                        : base (s)
                {
                }
        }
}
```

请注意如何`CubeWallpaper.CubeEngine`嵌套在`CubeWallpaper`，`CubeWallpaper`继承自包含的类`WallpaperService.Engine`，和`CubeWallpaper.CubeEngine`具有构造函数采用的声明的类型-`CubeWallpaper`为所有在此情况下-上面指定。


### <a name="interfaces"></a>接口

Java 接口可以包含以下三组成员，其中两个会导致问题，从 C#:

1. 方法

1. 类型

1. 字段


Java 接口将转换为两种类型：

1. （可选） 接口包含方法声明。 此接口具有相同的名称与 Java 接口，*除*它还具有*我*前缀。

1. 在 Java 接口中声明 （可选） 的静态类，包含所有字段。

嵌套的类型是"重新定位"是封闭接口而不是嵌套类型，作为前缀的封闭接口名称的同级。

例如，考虑[android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/)接口。
*Parcelable*接口包含方法、 嵌套的类型和常量。 *Parcelable*接口方法放入[Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/)接口。
*Parcelable*接口常量放入[Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/)类型。 嵌套[android.os.Parcelable.ClassLoaderCreator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html)和[android.os.Parcelable.Creator <t> </t> ](http://developer.android.com/reference/android/os/Parcelable.Creator.html)类型目前不由于我们泛型的支持; 中的限制绑定如果它们受支持，它们将显示为*Android.OS.IParcelableClassLoaderCreator*和*Android.OS.IParcelableCreator*接口。 例如，嵌套[android.os.IBinder.DeathRecpient](http://developer.android.com/reference/android/os/IBinder.DeathRecipient.html)接口绑定为[Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/)接口。


> [!NOTE]
> 从开始 Xamarin.Android 1.9，Java 接口常量是<em>重复</em>为了简化移植 Java 代码。 这有助于改善依赖于 Java 代码移植[android 提供程序](http://developer.android.com/reference/android/provider/package-summary.html)接口常量。

除了上述所有类型，有四个进一步的更改：

1. 具有名称与 Java 接口相同的类型生成以包含常量。

1. 此外包含接口常量的类型包含来自实现的 Java 接口的所有常量。

1. 实现 Java 接口包含常量的所有类都获取新的嵌套的 InterfaceConsts 类型其中包含来自所有实现的接口的常量。

1. *Consts*类型现在已废弃不用。


有关*android.os.Parcelable*接口，这意味着现在会出现[ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/)以包含常量的类型。 例如， [Parcelable.CONTENTS_FILE_DESCRIPTOR](http://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR)常量将可绑定为[ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/)常量，而不是用作*ParcelableConsts.ContentsFileDescriptor*常量。

对于包含实现包含其他接口的常量但多个常量的接口，现在将生成的所有常量联合。 例如， [android.provider.MediaStore.Video.VideoColumns](http://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html)接口实现[android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/)接口。 但是之前 1.9， [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/)类型具有访问上声明的常量的任何方式[Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/)。
因此，Java 表达式*MediaStore.Video.VideoColumns.TITLE*需要绑定到 C# 表达式*MediaStore.Video.MediaColumnsConsts.Title*即硬发现而无需读取Java 文档的很多。 在 1.9，等效的 C# 表达式将[ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/)。

此外，考虑[android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/)类型，后者实现 Java *Parcelable*接口。 由于它实现该接口，例如在该接口上的所有常量都是"通过"绑定类型，可访问*Bundle.CONTENTS_FILE_DESCRIPTOR*是完全合法的 Java 表达式。
以前，若要移植对 C# 此表达式将需要查看所有接口都实现，若要查看哪种类型从*CONTENTS_FILE_DESCRIPTOR*来源。 从开始 Xamarin.Android 1.9，实现 Java 接口包含常量的类将具有嵌套*InterfaceConsts*类型，将包含所有继承的接口常量。 这将允许转换*Bundle.CONTENTS_FILE_DESCRIPTOR*到[ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/)。

最后，类型带有*Consts*如后缀*Android.OS.ParcelableConsts*现在已过时，而非新引入 InterfaceConsts 嵌套类型。 它们将在 Xamarin.Android 3.0 中删除。


## <a name="resources"></a>资源

可以为应用程序中包括图像、 布局说明、 二进制 blob 和字符串字典[资源文件](http://developer.android.com/guide/topics/resources/providing-resources.html)。
各种 Android Api 设计为[操作上的资源 Id](http://developer.android.com/guide/topics/resources/accessing-resources.html)而不是图像处理，字符串或二进制 blob 直接。

例如，示例 Android 应用程序包含用户界面布局 ( `main.axml`)，国际化表字符串 ( `strings.xml`) 和一些图标 ( `drawable-*/icon.png`) 的"资源"目录中的应用程序还可以保留其资源：

    Resources/
        drawable-hdpi/
            icon.png

        drawable-ldpi/
            icon.png

        drawable-mdpi/
            icon.png

        layout/
            main.axml

        values/
            strings.xml

本机 Android Api 不直接使用文件名，但改为运行上的资源 Id。 生成系统编译的 Android 应用程序使用的资源时，将包分发的资源并生成一个名为类`Resource`包含这些资源包括每个令牌。 例如，对于上面的资源布局，这是 R 类将公开：

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

然后，你将使用`Resource.Drawable.icon`引用`drawable/icon.png`文件，或`Resource.Layout.main`引用`layout/main.xml`文件，或`Resource.String.first_string`引用字典文件中的第一个字符串`values/strings.xml`。


## <a name="constants-and-enumerations"></a>常量和枚举

本机 Android Api 具有许多使用或不返回一个 int，必须映射到常量字段来确定 int 的含义的方法。 要使用这些方法，用户将需要查看文档，了解哪些常量是适当的值，即小于理想。

例如，考虑[Activity.requestWindowFeature (int featureID)](http://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int))。

在这些情况下，我们尽力相关的常量组合到一个.NET 枚举，并将重新映射要改为需要在枚举的方法。
通过执行此操作，我们就能够提供 IntelliSense 所选内容的潜在值。

上面的示例将成为： [Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/))。

请注意，这是一个非常手动过程来找出哪些常量属于一起使用，任意的 Api 使用这些常量。 请在 API，它将是更好地表示为枚举文件常量用于任何 bug。
