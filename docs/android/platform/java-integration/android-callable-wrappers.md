---
title: "Android 可调用包装器"
ms.topic: article
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 5b74b1486d72176207d3ccd669c85e249d0706b6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="android-callable-wrappers"></a>Android 可调用包装器

每当 Android 运行时将调用托管的代码时，android 可调用包装器 (ACWs) 是必需的。 需要使用这些包装，因为没有方法与画 （Android 运行时） 在运行时注册类。 (具体而言， [JNI DefineClass() 函数](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986)Android 运行时不支持。} Android 可调用包装器从而弥补缺少的运行时类型注册支持。 

*每次*Android 代码要执行所需`virtual`或接口方法`overridden`或托管代码中实现，Xamarin.Android 必须提供 Java 代理，以便此方法会调度到相应的托管类型。 这些 Java 代理类型都是具有与托管类型实现的相同的构造函数，并声明任何重写的基类和接口方法"相同"的基类和 Java 接口列表的 Java 代码。 

Android 可调用包装器生成的**monodroid.exe**程序期间[生成过程](~/android/deploy-test/building-apps/build-process.md): （直接或间接） 继承的所有类型生成[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)。 



## <a name="android-callable-wrapper-naming"></a>Android 可调用包装器命名

在正在导出的类型的程序集限定名称的 MD5SUM 基于 Android 的可调用包装器的包名称。 此命名的方法使相同的完全限定的类型名称，将变得可通过不同的程序集而不会引入打包错误。 

由于此 MD5SUM 命名方案，不能按名称直接访问你的类型。 例如，以下`adb`命令将无法运行，因为类型名称`my.ActivityType`默认情况下不生成： 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

此外，你可能会看到如下所示的错误，如果你尝试按名称引用类型：

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

如果你*执行*需要访问为按名称的类型，可以声明该类型到属性声明中的名称。 例如，以下是声明的完全限定名称的活动的代码`My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

`ActivityAttribute.Name`属性可以设置显式声明此活动的名称： 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

添加此属性设置后，`my.ActivityType`可以按名称从外部的代码以及从访问`adb`脚本。 `Name`属性可以设置为许多不同的类型包括`Activity`， `Application`， `Service`， `BroadcastReceiver`，和`ContentProvider`: 

-   [ActivityAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)
-   [ApplicationAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ApplicationAttribute.Name/)
-   [ServiceAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ServiceAttribute.Name/)
-   [BroadcastReceiverAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.BroadcastReceiverAttribute.Name/)
-   [ContentProviderAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.ContentProviderAttribute.Name/)

在 Xamarin.Android 5.0 引入了基于 MD5SUM ACW 命名。 有关特性命名的详细信息，请参阅[RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)。 



## <a name="implementing-interfaces"></a>实现接口

有时你可能需要实现 Android 接口，如[Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)。 由于所有 Android 类和接口扩展[Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)接口，将出现问题： 我们如何实现`IJavaObject`？ 

上面回答该问题的： 原因需要实现所有 Android 类型`IJavaObject`是以便 Xamarin.Android 具有向 Android，即为给定类型的 Java 代理提供 Android 可调用包装器。 由于**monodroid.exe**仅查找`Java.Lang.Object`子类，和`Java.Lang.Object`实现`IJavaObject,`问题的回答是明显： 子类`Java.Lang.Object`: 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {

    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
    {
        // implementation goes here...
    } 

    public void OnLowMemory ()
    {
        // implementation goes here...
    }
}
```


## <a name="implementation-details"></a>实现详细信息

*此页面的其余部分提供了实现详细信息，如有更改，恕不另行通知*（和开发人员会想要知道发生了什么情况，所以此处提供）。 

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

public class HelloAndroid
    extends android.app.Activity
{
    static final String __md_methods;
    static {
        __md_methods = "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" + "";
        mono.android.Runtime.register (
            "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
            Culture=neutral, PublicKeyToken=null", HelloAndroid.class, __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
                Culture=neutral, PublicKeyToken=null", "", this, new java.lang.Object[] {  });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

请注意保留了的基类，和`native`托管代码中重写每个方法均提供方法声明。 
