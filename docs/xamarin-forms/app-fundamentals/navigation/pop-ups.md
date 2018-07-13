---
title: 显示弹出窗口
description: Xamarin.Forms 提供了两个弹出注册类似于用户界面元素 – 警报和操作工作表。 本文演示如何使用警报和操作工作表 Api 要求简单的问题的用户和来指导用户完成任务。
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 156c2f9dca47a7755d4f810d7921a05662388ded
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996709"
---
# <a name="displaying-pop-ups"></a>显示弹出窗口

_Xamarin.Forms 提供了两个弹出注册类似于用户界面元素 – 警报和操作工作表。本文演示如何使用警报和操作工作表 Api 要求简单的问题的用户和来指导用户完成任务。_

显示警报或要求用户选择一个选项是一项常见 UI 任务。 Xamarin.Forms 具有两种方法[ `Page` ](xref:Xamarin.Forms.Page)与一个弹出窗口，通过对用户进行交互的类： [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)并[ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*)。 它们将与每个平台上的相应本机控件呈现。

## <a name="displaying-an-alert"></a>显示警报

所有 Xamarin.Forms 支持的平台都具有模式弹出框，提醒用户，或者提出这些简单的问题。 若要显示这些警报在 Xamarin.Forms 中，使用[ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)方法对任何[ `Page` ](xref:Xamarin.Forms.Page)。 以下代码行向用户显示一个简单的消息：

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "警报对话框，其中一个按钮")

此示例不会从用户收集信息。 警报以模式方式显示和关闭用户之后会继续与该应用程序进行交互。

[ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)方法还可用于捕获用户的响应，通过提供两个按钮，并返回`boolean`。 若要获得警报的响应，提供这两个按钮的文本和`await`方法。 在用户选择一个选项后，答案将返回到你的代码。 请注意`async`和`await`下面的示例代码中的关键字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "警报对话框，其中包含两个按钮")](pop-ups-images/alert2.png#lightbox "警报对话框，其中包含两个按钮")

## <a name="guiding-users-through-tasks"></a>指导用户完成任务

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html)是 iOS 中的常见 UI 元素。 Xamarin.Forms [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*)方法可用于在跨平台应用中，呈现在 Android 和 UWP 中的本机替代项包括此控件。

若要显示操作工作表， `await` [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*)任何[ `Page` ](xref:Xamarin.Forms.Page)、 将消息传递和按钮标签设置为字符串。 该方法返回的字符串为标签的用户单击的按钮。 一个简单的示例如下所示：

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet 对话框")

`destroy`按钮呈现不同的方式，并且可以在`null`或指定为第三个字符串参数。 下面的示例使用`destroy`按钮：

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Destroy 按钮操作表对话框")](pop-ups-images/action2.png#lightbox "Destroy 按钮操作表对话框")

## <a name="summary"></a>总结

本文演示了使用警报和操作工作表 Api 要求简单的问题的用户和来指导用户完成任务。 Xamarin.Forms 有两种方法[ `Page` ](xref:Xamarin.Forms.Page)与一个弹出窗口，通过对用户进行交互的类： [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)并[ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*)，并且它们同时使用每个平台上的相应本机控件呈现。



## <a name="related-links"></a>相关链接

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
