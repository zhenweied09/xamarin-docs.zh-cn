---
title: "使用行操作"
description: "本指南演示如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction 创建的表行的自定义轻扫操作"
ms.topic: article
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2017
ms.openlocfilehash: 23a8fcd0633757bfffdb1761c3fc811268341b96
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-row-actions"></a>使用行操作

_本指南演示如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction 创建的表行的自定义轻扫操作_

![演示在行上的轻扫操作](row-action-images/action02.png)

iOS 提供两种方法在表上执行操作：`UISwipeActionsConfiguration`和`UITableViewRowAction`。

`UISwipeActionsConfiguration` 在 iOS 11 中引入了和用于定义一组应采取的操作时发生用户刷_在任一方向_表视图中某一行上。 此行为是类似于本机将 Mail.app 

`UITableViewRowAction`类用于定义当用户刷留水平表视图中的某行时将发生的操作。
例如，当编辑一个表，轻扫行上的左侧显示**删除**默认情况下的按钮。 通过将附加的多个实例`UITableViewRowAction`类到`UITableView`，可以定义多个自定义操作，每个都有其自己的文本、 格式设置和行为。


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

有三个步骤实现使用轻扫操作所需`UISwipeActionsConfiguration`:

1. 重写`GetLeadingSwipeActionsConfiguration`和/或`GetTrailingSwipeActionsConfiguration`方法。 这些方法返回`UISwipeActionsConfiguration`。 
2. 实例化`UISwipeActionsConfiguration`要返回。 此类采用的数组`UIContextualAction`。
3. 创建 `UIContextualAction`。

下列各节中更详细地对它们进行了解释。

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1.实现 SwipeActionsConfigurations 方法

`UITableViewController` (以及`UITableViewSource`和`UITableViewDelegate`) 包含两个方法：`GetLeadingSwipeActionsConfiguration`和`GetTrailingSwipeActionsConfiguration`，用于在表视图行实现一组轻扫操作。 前导轻扫操作是指到轻扫，从左到右语言中的屏幕的左下方以及从右到左语言中的屏幕的右端。 

下面的示例 (从[TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)示例) 演示如何实现前导轻扫配置。 两个操作的上下文的操作，解释了从创建[下面](#create-uicontextualaction)。 这些操作然后传递给新初始化[ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations)，这用作返回值。


```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });
    
    leadingSwipe.PerformsFirstActionWithFullSwipe = false;
    
    return leadingSwipe;
}  
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2.实例化 `UISwipeActionsConfiguration`

实例化`UISwipeActionsConfiguration`使用`FromActions`方法将添加一个新数组的`UIContextualAction`s，如下面的代码段中所示：

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

请务必注意，在其中显示你的操作的顺序是依赖于如何传递到你的阵列。 例如，前导刷上面的代码，因此显示的操作：

![前导轻扫操作显示在一个表行](row-action-images/action03.png)

为尾随刷，操作将会显示，如下图中所示：

![尾随轻扫操作显示在一个表行](row-action-images/action04.png)

此代码段也使用新`PerformsFirstActionWithFullSwipe`属性。 默认情况下，此属性设置为 true，这意味着用户完全刷行上时，将发生数组中的第一个操作。 如果你有不是破坏性操作 (例如"删除"，这可能不是理想的行为，并且因此应将其设置为`false`。

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>创建 `UIContextualAction`

上下文的操作是实际创建用户刷表行时，将显示的操作。

若要初始化的操作必须提供`UIContextualActionStyle`、 一个标题，以及一`UIContextualActionHandler`。 `UIContextualActionHandler`采用三个参数： 操作、 操作中，显示的视图和完成处理程序：

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null)); 
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);
                            
                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

可以编辑各种的可视属性，如的背景色或图像的操作。 上面的代码段演示如何将图像添加到操作并设置它的背景色为蓝色。

一旦创建上下文的操作，它们可用于初始化`UISwipeActionsConfiguration`中`GetLeadingSwipeActionsConfiguration`方法。

## <a name="uitableviewrowaction"></a>UITableViewRowAction

可以定义一个或多个自定义行操作`UITableView`，你将需要创建的实例`UITableViewDelegate`类并重写`EditActionsForRow`方法。 例如:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

静态`UITableViewRowAction.Create`方法用于创建一个新`UITableViewRowAction`将显示**Hi**按钮用户刷水平保留在表中的行上时。 更高版本的新实例`TableDelegate`创建并附加到`UITableView`。 例如:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

当在运行上面的代码并在一个表行上, 剩余用户刷**Hi**按钮才会显示而不是**删除**默认情况下显示的按钮：

[![](row-action-images/action01.png "正在显示而不是删除按钮 Hi 按钮")](row-action-images/action01.png#lightbox)

如果用户点击**Hi**按钮，`Hello World!`将写出到 Visual Studio 中的控制台用于 Mac 或 Visual Studio 中调试模式下运行应用程序时。



## <a name="related-links"></a>相关链接

- [TableSwipeActions （示例）](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
