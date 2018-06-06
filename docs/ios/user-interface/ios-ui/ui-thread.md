---
title: 使用 Xamarin.iOS 中的 UI 线程
description: 本文档介绍如何使用在 Xamarin.iOS 在 UI 线程。 它讨论 UI 线程执行、 提供后台线程示例中，并检查异步/等待。
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 4328b84625aff4c92d6e97029ced7dde747d4fc4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790404"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>使用 Xamarin.iOS 中的 UI 线程

应用程序用户界面始终是单线程的即使在多线程设备 – 没有只能有一个表示屏幕形式和对显示的内容的任何更改需要进行协调通过单个访问点。 这可以防止多个线程尝试 （例如） 在同一时间更新相同的像素。

你的代码仅应更改用户界面控件从主要 （或 UI） 线程。 在不同线程 （如回调或后台线程） 发生的任何 UI 更新不可能获取其呈现到屏幕，或甚至可能导致崩溃。

## <a name="ui-thread-execution"></a>UI 线程执行

当要在视图中，创建控件，或处理用户启动的事件，如触摸屏输入时，已在 UI 线程的上下文中执行代码。

如果代码在中的任务或回调的后台线程上执行则很可能不在主 UI 线程上执行。 在这种情况下应将代码包装的调用中`InvokeOnMainThread`或`BeginInvokeOnMainThread`如下所示：

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

`InvokeOnMainThread`方法定义上`NSObject`以便它可以从内部调用任何 UIKit 对象 （如视图或视图控制器） 上定义的方法。

调试时 Xamarin.iOS 应用程序，如果你的代码尝试从错误的线程访问 UI 控件，则将引发错误。 这可帮助你跟踪和解决这些问题 InvokeOnMainThread 方法。 这仅在调试时发生，且不会在发布版本中引发错误。 错误消息将显示如下：

 ![](ui-thread-images/image10.png "UI 线程执行")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>后台线程示例

下面是一个尝试访问用户界面控件的示例 ( `UILabel`) 从后台线程使用的是简单的线程：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

代码将引发`UIKitThreadAccessException`进行调试时。 若要解决该问题 （并确保用户界面控件仅可从主 UI 线程），将包装引用 UI 控件内的任何代码`InvokeOnMainThread`如下的表达式：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

你不需要的使用该 for 中本文档中，但它的示例的其余部分是在你的应用程序进行网络请求时要记住的一个重要概念使用通知中心或需要完成的处理程序将运行在另一台其他方法线程。

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>异步/等待示例

使用 C# 5 异步/等待关键字时`InvokeOnMainThread`不需要因为等待的任务完成时该方法将继续在调用线程上。

此示例代码 （其等待在延迟方法调用上，只是出于演示目的） 显示在 UI 线程 （它位于 TouchUpInside 处理程序） 调用异步方法。 因为在 UI 线程上调用包含的方法时，UI 之类的操作上设置的文本`UILabel`或显示`UIAlertView`后，可以安全地调用异步操作已完成在后台线程。

```csharp
async partial void button2_TouchUpInside (UIButton sender)
{
    textfield1.ResignFirstResponder ();
    textfield2.ResignFirstResponder ();
    textview1.ResignFirstResponder ();
    label1.Text = "async method started";
    await Task.Delay(1000); // example purpose only
    label1.Text = "1 second passed";
    await Task.Delay(2000);
    label1.Text = "2 more seconds passed";
    await Task.Delay(1000);
    new UIAlertView("Async method complete", "This method", 
               null, "Cancel", null)
        .Show();
    label1.Text = "async method completed";
}
```

如果从后台线程 （不主 UI 线程） 调用异步方法然后`InvokeOnMainThread`仍然需要。


## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
- [线程处理](~/ios/app-fundamentals/threading.md)
