---
title: 异步概述
description: C# 语言 – 版本 5 – 最新版本引入了两个新的关键字表示异步操作： async 和 await。 这些关键字，可以编写在另一个线程中使用任务并行库执行长时间运行操作 （如网络访问权限） 的简单代码并轻松地访问在完成的结果。 Xamarin.iOS 和 Xamarin.Android 的最新版本支持 async 和 await-本文档提供了说明和 xamarin 中使用的新语法的示例。
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 638e16a47d30e1d4d61fe9e7d19a0245261e5d7f
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2018
---
# <a name="async-support-overview"></a>异步支持概述

_C# 语言 – 版本 5 – 最新版本引入了两个新的关键字表示异步操作： async 和 await。这些关键字，可以编写在另一个线程中使用任务并行库执行长时间运行操作 （如网络访问权限） 的简单代码并轻松地访问在完成的结果。Xamarin.iOS 和 Xamarin.Android 的最新版本支持 async 和 await-本文档提供了说明和 xamarin 中使用的新语法的示例。_

Xamarin 的异步支持 Mono 3.0 基础上构建，并从正在 Silverlight 以适合移动应用的.NET 4.5 版本的移动友好版本升级 API 配置文件。

## <a name="overview"></a>概述

本文档介绍新的异步和 await 关键字，然后将演练一些简单的示例在 Xamarin.iOS 和 Xamarin.Android 中实现异步方法。

有关 C# 5 （包括示例和不同的使用方案的很多） 的新异步功能的更完整讨论，请参阅 MSDN 文档[使用 Async 和 Await 进行异步编程](http://msdn.microsoft.com/library/vstudio/hh191443.aspx)。

示例应用程序发出简单异步 web 请求 （而不阻塞主线程），然后用已下载的 html 和字符计数更新 UI。

 [![](async-images/AsyncAwait_427x368.png "该示例应用程序而不会阻止主线程发出简单异步 web 请求，然后用下载的 html 和字符计数更新 UI")](async-images/AsyncAwait.png#lightbox)

Xamarin 的异步支持 Mono 3.0 基础上构建，并从正在 Silverlight 以适合移动应用的.NET 4.5 版本的移动友好版本升级 API 配置文件。

## <a name="requirements"></a>要求

C# 5 功能需要包含在 Xamarin.iOS 6.4 和 Xamarin.Android 4.8 的 Mono 3.0。 系统将提示您将 Mono、 Xamarin.iOS、 Xamarin.Android 和升级 Xamarin.Mac 若要利用它。

## <a name="using-async-amp-await"></a>使用 async &amp; await

 `async` 和`await`是新 C# 语言功能一起使用任务并行库，以便可以方便地编写线程的代码，而不会阻止你的应用程序的主线程中执行长时间运行的任务。

## <a name="async"></a>async

### <a name="declaration"></a>声明

`async`关键字位于方法声明中 （或 lambda 或匿名方法） 以指示它包含以异步方式，可以运行的代码 ie。 不会阻止调用方的线程。

方法标记为`async`应包含至少一个 await 表达式或语句。 如果没有`await`s 都位于该方法，则它将同步运行 (相同为好像有没有`async`修饰符)。 此外，这还将导致编译器警告 （但不是错误）。

### <a name="return-types"></a>返回类型

异步方法应返回`Task`，`Task<TResult>`或`void`。

指定`Task`返回类型，如果方法不返回任何其他值。

指定`Task<TResult>`如果该方法需要返回值，其中`TResult`是返回的类型 (如`int`，例如)。

`void`返回类型主要用于需要它的事件处理程序。 调用返回 void 的异步方法的代码不能`await`得出的结果。

### <a name="parameters"></a>参数

异步方法不能声明`ref`或`out`参数。

## <a name="await"></a>await

Await 运算符可以应用于在标记为异步方法内的任务。 它会导致要停止在该点执行并等待，直到任务完成的方法。

使用 await 不会阻止调用方的线程 – 而是将控制权返回给调用方。 这意味着，不阻止调用线程，因此，例如用户界面线程不会阻止时等待的任务。

在任务完成后，该方法恢复在代码中的同一点执行。 这包括返回到 try – catch – finally 块的 try 作用域 （如果有的话）。 await 不能在 catch 或 finally 块。

阅读更多有关[await MSDN 上](http://msdn.microsoft.com/library/vstudio/hh156528.aspx)。

## <a name="exception-handling"></a>异常处理

在异步方法中发生的异常将存储在任务和任务时引发`await`ed。 这些异常可以捕捉和处理 try catch 块内。

## <a name="cancellation"></a>取消

需要很长时间才能完成的异步方法应支持取消。 通常情况下，取消调用，如下所示：

- A`CancellationTokenSource`创建对象。
- `CancellationTokenSource.Token`实例传递给可取消的异步方法。
- 通过调用请求取消`CancellationTokenSource.Cancel`方法。

然后，该任务取消了自身，并且确认取消。

有关取消的详细信息，请参阅[如何取消异步任务](http://msdn.microsoft.com/library/vstudio/jj155761.aspx)MSDN 上。

## <a name="example"></a>示例

下载[示例的 Xamarin 解决方案](https://developer.xamarin.com/samples/mobile/AsyncAwait/)（适用于 iOS 和 Android） 若要查看的工作示例`async`和`await`移动应用中。 此代码示例将讨论此部分中的更多详细信息。

### <a name="writing-an-async-method"></a>编写异步方法

以下方法演示如何代码`async`方法替换`await`ed 任务：

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("http://xamarin.com"); // async method!

    // await! control returns to the caller and the task continues to run on another thread
    string contents = await contentsTask;

    ResultEditText.Text += "DownloadHomepage method continues after async call. . . . .\n";

    // After contentTask completes, you can calculate the length of the string.
    int exampleInt = contents.Length;

    ResultEditText.Text += "Downloaded the html and found out the length.\n\n\n";

    ResultEditText.Text += contents; // just dump the entire HTML

    return exampleInt; // Task<TResult> returns an object of type TResult, in this case int
}
```

请注意以下几点：

-  方法声明中包含`async`关键字。
-  返回类型是`Task<int>`以便调用代码可以访问`int`计算此方法中的值。
-  Return 语句`return exampleInt;`即整数对象 – 该方法返回的事实`Task<int>`属于语言方面的改进。


### <a name="calling-an-async-method-1"></a>调用异步方法 1

此按钮单击事件处理程序可以找到 Android 示例应用程序调用上文所述的方法中：

```csharp
GetButton.Click += async (sender, e) => {

    Task<int> sizeTask = DownloadHomepage();

    ResultTextView.Text = "loading...";
    ResultEditText.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await sizeTask;

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultTextView.Text = "Length: " + intResult ;
    // "returns" void, since it's an event handler
};
```

注意：

-  匿名委托具有异步关键字前缀。
-  异步方法 DownloadHomepage 返回一个任务任务<int>存储在 sizeTask 变量。
-  代码等待 sizeTask 变量上。  *这*是将方法挂起，以及将控制返回到调用代码，直到异步任务完成其自己的线程上的位置。
-  执行未*不*暂停时的方法，尽管那里正在创建的任务的第一行中创建的任务。 Await 关键字表示其中执行将会暂停的位置。
-  异步任务完成时，设置 intResult 并在原始线程，从 await 行上继续执行。


### <a name="calling-an-async-method-2"></a>调用异步方法 2

在 iOS 示例应用程序示例都写入略有不同来演示一种方法。 而是不是使用匿名委托本示例声明`async`如常规事件处理程序分配的事件处理程序：

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

如下所示，然后定义事件处理程序方法：

```csharp
async void HandleTouchUpInside (object sender, EventArgs e)
{
    ResultLabel.Text = "loading...";
    ResultTextView.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await DownloadHomepage();

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultLabel.Text = "Length: " + intResult ;
}
```

一些要点：

-  在方法标记为`async`但返回`void`。 这通常只是为事件处理程序 (否则将返回`Task`或`Task<TResult>`)。
-  代码`await`上`DownloadHomepage`方法直接向变量赋值 ( `intResult` ) 与前面的示例，我们使用中间不同`Task<int>`变量来引用的任务。  *这*是其中控制权返回给调用方直到完成异步方法在另一个线程的位置。
-  在异步方法完成并返回时，继续执行`await`这意味着整数结果都会返回并重然后呈现在 UI 小组件中。


## <a name="summary"></a>总结

使用 async 和 await 极大地简化了生成而不会阻止主线程长时间运行的操作在后台线程上所需的代码。 它们还使其可以轻松地完成任务后访问结果。

本文档已为 Xamarin.iOS 和 Xamarin.Android 提供的新语言关键字和示例的概述。



## <a name="related-links"></a>相关链接

- [AsyncAwait （示例）](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [作为我们代的回调转到语句](http://tirania.org/blog/archive/2013/Aug-15.html)
- [数据 (iOS) （示例）](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) （示例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) （示例）](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [网络研讨会： C# 异步在 iOS 和 Android （视频）](http://xamarin.wistia.com/medias/k27mc627xz)
- [异步编程使用 Async 和 Await (MSDN)](http://msdn.microsoft.com/library/vstudio/hh191443.aspx)
- [微调异步应用程序 (MSDN)](http://msdn.microsoft.com/library/vstudio/jj155761.aspx)
- [等待时，UI 和死锁和 ！哦我 ！(MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx)
- [在完成 (MSDN) 时处理任务](http://blogs.msdn.com/b/pfxteam/archive/2012/08/02/processing-tasks-as-they-complete.aspx)
- [基于任务的异步模式 (TAP)](http://msdn.microsoft.com/library/hh873175.aspx)
- [在 C# 5 （Eric Lippert 博客） – 有关关键字引入的异步](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
