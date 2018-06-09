---
title: 章 20 的摘要。 异步和文件 I/O
description: 使用 Xamarin.Forms 创建移动应用： 章 20 的摘要。 异步和文件 I/O
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: fbcf1c9cfb1cd87ea33f47ae61a8ebe233b89736
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241863"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>章 20 的摘要。 异步和文件 I/O

 图形用户界面必须按顺序响应用户输入事件。 这意味着所有处理用户输入事件必须都出现在单个线程，通常称为*主线程*或*UI 线程*。

用户期望图形用户界面能够快地响应。 这意味着程序必须快速处理用户输入事件。 如果这是不可行的然后处理必须要降级到辅助线程的执行。

本书中的几个示例程序已使用[ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/)类。 此类中[ `BeginGetReponse` ](https://developer.xamarin.com/api/member/System.Net.WebRequest.BeginGetResponse/p/System.AsyncCallback/System.Object/)方法会启动工作线程，完成时调用的回调函数。 但是，该回调函数运行在辅助线程中，因此，程序必须调用[ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/)方法来访问的用户界面。

使用更现代的方法进行异步处理是在.NET 和 C# 中可用。 这涉及到[ `Task` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task/)和[ `Task<TResult>` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task%3CTResult%3E/)中, 类和其他类型[ `System.Threading` ](https://developer.xamarin.com/api/namespace/System.Threading/)和[ `System.Threading.Tasks` ](https://developer.xamarin.com/api/namespace/System.Threading.Tasks/)命名空间，以及 C# 5.0`async`和`await`关键字。 这就是本章的重点。

## <a name="from-callbacks-to-await"></a>从回调以等待

`Page`类本身包含三个异步方法，以显示警报框：

- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) 返回`Task`对象
- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/System.String/) 返回`Task<bool>`对象
- [`DisplayActionSheet`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet/p/System.String/System.String/System.String/System.String[]/) 返回`Task<string>`对象

`Task`对象指示这些方法实现基于任务的异步模式，称为 TAP。 这些`Task`快速从方法返回的对象。 `Task<T>`返回值构成的一个"承诺"类型的值`TResult`将在任务完成时可用。 `Task`返回值指示异步操作的将完成，但没有值返回。

在所有这些情况下，`Task`用户解除警报框时完成。  

### <a name="an-alert-with-callbacks"></a>与回调的警报

[ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks)示例演示了如何处理`Task<bool>`返回对象和`Device.BeginInvokeOnMainThread`使用回调方法的调用。

### <a name="an-alert-with-lambdas"></a>与 lambda 警报

[ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas)示例演示如何使用匿名 lambda 函数，用于处理`Task`和`Device.BeginInvokeOnMainThread`调用。  

### <a name="an-alert-with-await"></a>与 await 的警报

更简单的方法涉及`async`和`await`引入 C# 5 中的关键字。 [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait)示例演示其用法。

### <a name="an-alert-with-nothing"></a>与执行任何操作的警报

如果异步方法返回`Task`而非`Task<TResult>`，则该程序不需要使用这些方法之一，如果它不需要知道异步任务完成时。 [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert)示例演示这一操作。

### <a name="saving-program-settings-asynchronously"></a>正在保存程序设置以异步方式

[ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings)示例演示如何使用[ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/)方法`Application`以保存程序设置，因为它们而无需更改重写`OnSleep`方法。

### <a name="a-platform-independent-timer"></a>独立于平台的计时器

可以使用[ `Task.Delay` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Delay/p/System.Int32/)创建独立于平台的计时器。 [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock)示例演示这一操作。

## <a name="file-inputoutput"></a>文件输入/输出

传统上，.NET [ `System.IO` ](https://developer.xamarin.com/api/namespace/System.IO/)命名空间已被文件 I/O 支持的源。 虽然此命名空间中的某些方法支持异步操作，但大多数却没有。 命名空间还支持几个简单的方法调用执行复杂的文件 I/O 功能。

### <a name="good-news-and-bad-news"></a>好消息和错误新闻

通过 Xamarin.Forms 支持应用程序本地存储区支持的所有平台&mdash;是私有的应用程序的存储。

Xamarin.iOS 和 Xamarin.Android 库包括 Xamarin 明确针对这两个平台的.NET 的版本。 其中包括类从`System.IO`可用于在这两个平台中执行与应用程序本地存储的文件 I/O。

但是，如果你搜索这些`System.IO`Xamarin.Forms PCL 中的类，不会找到它们。 问题是 Windows 运行时 API 该 Microsoft 彻底改观文件 I/O。 面向 Windows 8.1、 Windows Phone 8.1 和通用 Windows 平台的程序不使用`System.IO`用于文件 I/O。

这意味着，你将需要使用[ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) (第一次所述[**章 9。特定于平台的 API 调用**](chapter09.md)实现文件 I/O。

### <a name="a-first-shot-at-cross-platform-file-io"></a>第一个快照在跨平台文件 I/O

[ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout)示例定义[ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs)文件 I/O，以及在所有平台中此接口的实现的接口。 但是，Windows 运行时实现不使用此接口中的方法是因为 Windows 运行时文件 I/O 方法是异步的。

### <a name="accommodating-windows-runtime-file-io"></a>容纳 Windows 运行时文件 I/O

在 Windows 运行时下运行的程序使用中的类[ `Windows.Storage` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.aspx)和[ `Windows.Storage.Streams` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.streams.aspx)用于文件 I/O，包括应用程序本地存储的命名空间。 由于 Microsoft 确定需要多个 50 毫秒应为异步模式，以避免阻塞 UI 线程的任何操作，这些文件 I/O 方法主要是异步的。

此代码演示此新方法将在库中，以便它可以由其他应用程序。

## <a name="platform-specific-libraries"></a>特定于平台的库

最好在库中存储的可重用代码。 不同的可重用的代码片段的完全不同的操作系统时，这种显然更困难。

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解决方案演示一种方法。 此解决方案包含七个不同的项目：

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform)，正常 Xamarin.Forms PCL
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS)，iOS 类库
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android)，Android 类库
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP)，通用 Windows 类库
- [**Xamarin.FormsBook.Platform.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Windows)，Windows 8.1 的 PCL。
- [**Xamarin.FormsBook.Platform.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinPhone)，Windows Phone 8.1 的 PCL
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT)，普遍适用于所有 Windows 平台的代码共享的项目

单个平台的所有项目 (除**Xamarin.FormsBook.Platform.WinRT**) 引用了**Xamarin.FormsBook.Platform**。 三个 Windows 项目具有对引用**Xamarin.FormsBook.Platform.WinRT**。

所有项目都包含一个静态`Toolkit.Init`方法，以确保如果 Xamarin.Forms 应用程序解决方案中的项目不直接引用加载库。

**Xamarin.FormsBook.Platform**项目包含新[ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs)接口。 所有方法现在都有名称与`Async`后缀以及返回`Task`对象。

**Xamarin.FormsBook.Platform.WinRT**项目包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) for Windows Runtime 的类。

**Xamarin.FormsBook.Platform.iOS**项目包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs)适用于 iOS 的类。 这些方法现在必须是异步的。 一些方法使用中定义的方法的异步版本`StreamWriter`和`StreamReader`: [ `WriteAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamWriter.WriteAsync/p/System.String/)和[ `ReadToEndAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamReader.ReadToEndAsync()/)。 其他转换到结果`Task`对象使用[ `FromResult` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.FromResult%7BTResult%7D/p/TResult/)方法。

**Xamarin.FormsBook.Platform.Android**项目包含类似[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs)适用于 Android 的类。

**Xamarin.FormsBook.Platform**项目还包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs)类可方便地使用`DependencyService`对象。

若要使用这些库，一个应用程序解决方案必须包含的所有项目**Xamarin.FormsBook.Platform**解决方案，每个应用程序项目必须具有对中的相应库的引用**Xamarin.FormsBook.Platform**。

[ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync)解决方案演示如何使用**Xamarin.FormsBook.Platform**库。 每个项目都调用`Toolkit.Init`。 应用程序将使用异步文件 I/O 函数。

### <a name="keeping-it-in-the-background"></a>将其保存在后台

对多个异步方法进行调用的库中的方法&mdash;如`WriteFileAsync`和`ReadFileASync`Windows 运行时中的方法[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)类&mdash;可以进行某种程度上更高效，因为使用[ `ConfigureAwait` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task%3CTResult%3E.ConfigureAwait/p/System.Boolean/)方法可避免切换回用户界面线程。

### <a name="dont-block-the-ui-thread"></a>避免阻塞 UI 线程 ！

有时，很容易让人想到避免使用`ContinueWith`或`await`使用[ `Result` ](https://developer.xamarin.com/api/property/System.Threading.Tasks.Task%3CTResult%3E.Result/)的方法的属性。 应避免这样做，它可以阻塞 UI 线程或甚至挂起应用程序。

## <a name="your-own-awaitable-methods"></a>你自己的等待方法

你可以通过将其传递到其中一个以异步方式运行某些代码[ `Task.Run` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Run/p/System.Action/)方法。 你可以调用`Task.Run`中的异步方法，处理一些系统开销。

各种`Task.Run`模式将在下文。

### <a name="the-basic-mandelbrot-set"></a>Mandelbrot 组基本

若要绘制设置实时 Mandelbrot [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库具有[ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs)结构类似于中的一个`System.Numerics`命名空间。

[ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet)示例有`CalculateMandeblotAsync`计算基本黑白 Mandelbrot 集，并将其代码隐藏文件中的方法[ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)置于位图。

### <a name="marking-progress"></a>标记进度

报告进度，从异步方法，可以实例化[ `Progress<T>` ](https://developer.xamarin.com/api/type/System.Progress%3CT%3E/)类并定义异步方法具有类型自变量[ `IProgress<T>` ](https://developer.xamarin.com/api/type/System.IProgress%3CT%3E/)。 此进行了演示[ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)示例。

### <a name="cancelling-the-job"></a>正在取消作业

你还可以编写不可取消的异步方法。 开始使用名为的类[ `CancellationTokenSource` ](https://developer.xamarin.com/api/type/System.Threading.CancellationTokenSource/)。 [ `Token` ](https://developer.xamarin.com/api/property/System.Threading.CancellationTokenSource.Token/)属性是类型的值[ `CancellationToken` ](https://developer.xamarin.com/api/type/System.Threading.CancellationToken/)。 这将传递到异步函数。 程序调用[ `Cancel` ](https://developer.xamarin.com/api/member/System.Threading.CancellationTokenSource.Cancel()/)方法`CancellationTokenSource`（通常在用户的操作的响应） 若要取消的异步函数。

异步方法可以定期检查[ `IsCancellationRequested` ](https://developer.xamarin.com/api/property/System.Threading.CancellationToken.IsCancellationRequested/)属性`CancellationToken`并退出如果属性是`true`，或只需调用[ `ThrowIfCancellationRequested` ](https://developer.xamarin.com/api/member/System.Threading.CancellationToken.ThrowIfCancellationRequested()/)方法，请在这种情况下方法结尾[ `OperationCancelledException` ](https://developer.xamarin.com/api/type/System.OperationCanceledException/)。

[ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation)示例演示如何使用可取消的函数。

### <a name="an-mvvm-mandelbrot"></a>MVVM Mandelbrot

[ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF)示例有更广泛的用户界面，并且它主要基于[ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs)和[ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)类：

[![Mandelbrot X F 的三个屏幕截图](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>返回到 web

[ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/)某些示例中使用的类使用调用的异步编程模型或 APM 老式异步协议。 可以将此类转换为使用其中一个的现代 TAP 协议`FromAsync`中的方法[ `TaskFactory` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskFactory%3CTResult%3E/)类。 [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap)示例演示这一操作。



## <a name="related-links"></a>相关链接

- [章 20 的全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [章 20 示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [使用文件](~/xamarin-forms/app-fundamentals/files.md)
