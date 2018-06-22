---
title: 编写响应的应用程序
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: b8c113b67b3fbfa57ca86c72e11ddeb0e4e1a9ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763496"
---
# <a name="writing-responsive-applications"></a>编写响应的应用程序

维护响应 GUI 的密钥之一是执行后台线程上的长时间运行任务，因此 GUI 不会被阻塞。 让我们假设我们想要计算一个值，以显示给用户，但该值需要 5 秒钟时间来计算：

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        SlowMethod ();
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

这将起作用，但应用程序将"挂起"为 5 秒时计算的值。 在此期间，应用程序将不响应任何用户交互。 若要获取解决此，我们想要执行后台线程上的我们计算：

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

现在我们计算后台线程上的值，因此我们 GUI 计算期间保持响应状态。 但是，完成计算后，我们的应用程序崩溃时，请将此保留在日志：

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

这是因为你必须更新从 GUI 线程 GUI。 我们的代码将更新从线程池线程，导致应用崩溃的 GUI。 我们需要计算上的后台线程中，我们值但然后执行我们在与处理的 GUI 线程上的更新[Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        RunOnUiThread (() => textview.Text = "Method Complete");
    }
}
```

此代码按预期运行。 此 GUI 保持响应状态，并计算复杂后获取正确更新。

请注意这种技术不只是用于计算成本高昂的值。 它可以用于可以在后台，如 web 服务调用或下载 internet 数据的任何长时间运行任务。
