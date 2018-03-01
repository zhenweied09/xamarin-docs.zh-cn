---
title: "显示警报"
ms.topic: article
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 09f4178d5d6e12388771fa63875fbe1f489c959a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="displaying-alerts"></a>显示警报

从开始 iOS 8，UIAlertController 具有已完成的被替换的 UIActionSheet 和 UIAlertView 两种现已弃用。

不同于类替换，是 UIView 的子类，UIAlertController 才 UIViewController 的子类。

使用`UIAlertControllerStyle`以指示要显示的警报类型。 这些警报类型包括：

- **UIAlertControllerStyleActionSheet**
    * 预 iOS 8 这已经是 UIActionSheet
- **UIAlertControllerStyleAlert**
    * 预 iOS 8 这已经是 UIAlertView 

有三个必需的步骤才能创建警报控制器：

- 创建和使用答： 配置警报
    * 标题
    * 消息
    * preferredStyle
    
- （可选）添加文本字段
- 添加所需的操作
- 提供视图控制器

最简单的警报包含一个按钮，此屏幕截图中所示：

 ![警报一个按钮](alerts-images/alert1.png)

要显示一个简单的警报的代码如下所示：

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

显示与多个选项的警报以类似的方式完成，但将添加两个操作。 例如，下面的屏幕截图显示了两个按钮的警报：

 ![ 两个按钮警报](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

警报还可以显示一个操作表，类似于下面的屏幕截图：

 ![操作表警报](alerts-images/alert3.png)

按钮会添加到与警报`AddAction`方法：

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Three pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
- [警报控制器](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)
