---
title: "使用警报"
description: "本文介绍如何使用 UIAlertController 向 Xamarin.tvOS 中的用户显示一条警告消息。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 6dabba30c5242d6e7e9ef42a4025f87826a5b89e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-alerts"></a>使用警报

_本文介绍如何使用 UIAlertController 向 Xamarin.tvOS 中的用户显示一条警告消息。_


如果你需要获取 tvOS 用户注意或询问执行破坏性操作 （如删除的文件） 的权限，你可以提供警报消息使用`UIAlertViewController`:

[![](alerts-images/alert01.png "示例 UIAlertViewController")](alerts-images/alert01.png#lightbox)

如果添加到显示一条消息后的，你可以添加按钮和文本字段对警报以允许用户响应操作并提供反馈。

<a name="About-Alerts" />

## <a name="about-alerts"></a>有关警报

如上面所述，使用警报以获取用户的关注并通知他们你的应用程序或请求反馈的状态。 警报必须存在一个标题，它们可根据需要一条消息和一个或多个按钮或文本字段。

[![](alerts-images/alert04.png "示例警报")](alerts-images/alert04.png#lightbox)

Apple 具有使用警报的以下建议：

- **警报尽可能少地使用**-警报中断与应用程序的用户的流和中断的用户体验，并在这种情况下，应仅用于重要的情况下，例如错误通知、 应用内购买和破坏性操作。 
- **提供有用的选择**-如果警报选项显示给用户，你应确保每个选项提供关键信息，并提供有用的用户来执行的操作。

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>警报的标题和消息

Apple 具有出示警报的标题以及可选的消息的以下建议：

- **使用 Multiword 标题**-警报的标题应获取跨清楚地同时仍保持简单的情况的点。 单个单词标题极少数情况下提供足够的信息。
- **使用不需要一条消息的描述性标题**-只要有可能，请考虑使警报的标题描述性足够可选的消息文本是不需要。 
- **使消息较短的完整句子**-如果可选的消息是需要获取跨警报的点，使其保持尽可能简单，并将其与正确的大写和标点符号的一个完整的句子。

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>警报的按钮

Apple 提供有关将按钮添加到警报以下建议：

- **限制到两个按钮**-只要有可能，限制为两个按钮的最大值的警报。 单个按钮警报提供信息但没有操作。 两个按钮警报提供一个简单的操作是/否选项。
- **使用 Succinct，逻辑按钮标题**-简单清晰地描述按钮的操作的一到两个 word 按钮标题的工作效率最佳。 有关详细信息，请参阅我们[使用按钮](~/ios/tvos/user-interface/buttons.md)文档。
- **清楚地标记破坏性按钮**-对于执行破坏性操作 （如删除的文件） 的按钮清楚地将其与标记`UIAlertActionStyle.Destructive`样式。

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>显示警报

若要显示警报，你创建的实例`UIAlertViewController`并将其配置通过添加操作 （按钮） 并选择警报的样式。 例如，下面的代码显示确定 / 取消警报：

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...
        
var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

让我们看看此代码中详细信息。 首先，我们将使用给定的标题和消息创建新的警报：

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

接下来，我们想要在警报中显示每个按钮我们创建了定义按钮，其样式和我们想要在按下了按钮采取的操作的标题操作：

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ => 
    // Do something when the button is pressed
    ...
);
```

`UIAlertActionStyle`枚举可用于将按钮的样式设置为以下项之一：

- **默认**-按钮将处于默认按钮处于选中状态时显示警报。
- **取消**-按钮是警报的取消按钮。
- **破坏性**-突出显示该按钮，作为破坏性操作，如删除的文件。 目前，tvOS 呈现包含一个红色背景破坏性的按钮。

`AddAction`方法将添加到给定的操作`UIAlertViewController`和最后`PresentViewController (alertController, true, null)`方法为用户显示给定的警报。

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>添加文本字段

除了将操作 （按钮） 添加到警报，你可以将文本字段添加到警报后，若要允许用户填写信息，例如用户 Id 和密码：

[![](alerts-images/alert02.png "警报中的文本字段")](alerts-images/alert02.png#lightbox)

如果用户选择文本字段，将显示标准 tvOS 键盘，从而使它们可以输入字段的值：

[![](alerts-images/alert03.png "输入文本")](alerts-images/alert03.png#lightbox)

下面的代码显示确定 / 取消警报与单个文本字段用于输入一个值：

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

`AddTextField`方法将新的文本字段添加到的警报，然后，你可以配置通过设置属性，例如占位符文本 （空字段时，将出现的文本）、 默认文本值和键盘的类型。 例如:

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

以便我们可以处理的更高版本的文本字段的值，我们也节省了一份使用下面的代码：

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

用户的文本字段中输入一个值后，我们可以使用`field`变量来访问该值。

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>警报视图控制器帮助器类

因为显示简单、 公共类型的警报使用`UIAlertViewController`可能会导致大量的重复代码，可以使用一个帮助器类来减少重复的代码量。 例如:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

使用此类、 显示和响应与简单警报可以进行如下所示：

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```


<a name="Summary" />

## <a name="summary"></a>摘要

本文介绍了使用`UIAlertController`向 Xamarin.tvOS 中的用户显示一条警告消息。 首先，它还介绍了如何显示简单的警告，同时添加按钮。 接下来，它还介绍了如何将文本字段添加到警报。 最后，它说明了如何使用一个帮助器类以减少所需以显示警报的重复代码。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [应用程序对 tvOS 的编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
