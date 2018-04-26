---
title: 线程
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 63a213a62021923ac6dae8b080f3f8931621251d
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2018
---
# <a name="threading"></a>线程

Xamarin.iOS 运行时可使开发人员访问.NET 线程处理 Api，同时使用线程时显式 (`System.Threading.Thread, System.Threading.ThreadPool`) 和隐式时使用异步委托模式或 BeginXXX 方法，以及完整范围的 Api 支持任务并行库。



Xamarin 强烈建议你使用[任务并行库](http://msdn.microsoft.com/library/dd460717.aspx)(TPL) 生成应用程序，原因有多种：
-  默认 TPL 计划程序会将委托任务执行的线程池，反过来会动态增长进程发生，同时可避免线程过多 CPU 时间争用结束的其中一个方案所需的线程数。 
-  很容易地考虑 TPL 任务方面的操作。 您可以轻松地对其进行处理、 它们安排、 序列化其执行或启动许多具有一套丰富的 Api 的并行。 
-  它是使用新 C# 异步语言扩展进行编程的基础。 


线程池将慢慢地增长根据可用系统、 系统负载和应用程序的需求的 CPU 内核数为所需的线程数。 可以通过调用方法中的使用此线程池`System.Threading.ThreadPool`或使用默认值`System.Threading.Tasks.TaskScheduler`(属于*并行框架*)。

通常开发人员使用线程时它们需要创建响应的应用程序，他们不希望将阻止运行循环的主 UI。

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>开发响应的应用程序

应限于运行你的应用程序的主循环的相同线程对 UI 元素的访问。 如果你想要从一个线程对主 UI 进行更改，你应通过使用队列代码[NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/type/Foundation.NSObject/)，如下所示：

```csharp
MyThreadedRoutine ()  
{  
    var result = DoComputation ();  

    // we want to update an object that is managed by the main
    // thread; To do so, we need to ensure that we only access
    // this from the main thread:

    InvokeOnMainThread (delegate {  
        label.Text = "The result is: " + result;  
    });
}
```

上述时，将调用内的主线程的上下文中的委托的代码而不会导致任何可使你的应用程序可能崩溃的争用条件。

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>线程处理和垃圾回收

在过程中执行，Objective C 运行时将创建并发布对象。 如果对象被标记为"自动释放"Objective C 运行时将释放这些对象附加到线程的当前`NSAutoReleasePool`。 Xamarin.iOS 将创建一个`NSAutoRelease`从每个线程池`System.Threading.ThreadPool`和主线程的。 对此进行扩展介绍如何创建在 System.Threading.Tasks 中使用默认 taskscheduler 计划的任何线程。

如果创建您自己使用的线程`System.Threading`必须提供您自己的`NSAutoRelease`池，以防止数据泄露。 若要执行此操作，只需将包装你在下面的代码段中的线程：

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

注意： 自 Xamarin.iOS 5.2 你无需提供你自己`NSAutoReleasePool`不再将为你自动提供一个。


## <a name="related-links"></a>相关链接

- [使用 UI 线程](~/ios/user-interface/ios-ui/ui-thread.md)
