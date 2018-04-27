---
title: 显示弹出窗口
description: Xamarin.Forms 提供了两个高类似 pop 的用户界面元素 – 警报和操作表。 本文演示如何使用 Api 中的警报和操作表，来询问简单的问题的用户并指导用户完成任务。
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 97f0917e4e8670ab379aae1b2707ae08cb29bb70
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="displaying-pop-ups"></a>显示弹出窗口

_Xamarin.Forms 提供了两个高类似 pop 的用户界面元素 – 警报和操作表。本文演示如何使用 Api 中的警报和操作表，来询问简单的问题的用户并指导用户完成任务。_

显示警报，或要求用户进行选择是一项常见 UI 任务。 Xamarin.Forms 有两种方法[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)与通过一个弹出窗口的用户进行交互的类： [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/)和[ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/)。 它们呈现在每个平台上的相应本机控件。

## <a name="displaying-an-alert"></a>显示警报

所有 Xamarin.Forms 支持平台都具有一个模式的弹出窗口，若要向用户发出警报或提出这些简单的问题。 若要显示这些警报 Xamarin.Forms 中，使用[ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/)方法对任何[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)。 以下代码行向用户显示一个简单消息：

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "具有一个按钮的警报对话框")

此示例不会从用户收集信息。 警报以模式方式显示和解除用户之后会继续与应用程序进行交互。

[ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/)方法还可以用于通过提供两个按钮，并返回捕获用户响应`boolean`。 若要从警报中获取响应，提供这两个按钮的文本和`await`方法。 用户选择选项之一后答案将返回到代码中。 请注意`async`和`await`下面的示例代码中的关键字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "警报具有两个按钮的对话框")](pop-ups-images/alert2.png#lightbox "警报具有两个按钮的对话框")

## <a name="guiding-users-through-tasks"></a>指导用户完成任务

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html)是 iOS 中的常见 UI 元素。 Xamarin.Forms [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/)方法，您可以在跨平台应用中，呈现 Android 和 UWP 的本机备选项包含此控件。

若要显示一个操作表中， `await` [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/)中任何[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)、 将消息传递和按钮以字符串形式的标签。 该方法返回的字符串为标签已被用户单击的按钮。 一个简单的示例所示：

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet 对话框")

`destroy`按钮不同于其他呈现，并且可以保留`null`或指定为第三个字符串参数。 下面的示例使用`destroy`按钮：

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "与破坏按钮的操作表对话框")](pop-ups-images/action2.png#lightbox "与破坏按钮的操作表对话框")

## <a name="summary"></a>总结

这篇文章演示了使用 Api 中的警报和操作表，来询问简单的问题的用户并指导用户完成任务。 Xamarin.Forms 有两种方法[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)与通过一个弹出窗口的用户进行交互的类： [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/)和[ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/)，并且它们是同时呈现的每个平台上的相应本机控件。



## <a name="related-links"></a>相关链接

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
