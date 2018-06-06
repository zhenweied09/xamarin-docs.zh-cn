---
title: 创建使用元素 API 的 Xamarin.iOS 应用程序
description: 本文基于 MonoTouch 对话框文章简介中提供的信息。 它提供的演练，演示如何使用 MonoTouch.Dialog (MTD） 元素 API 若要快速开始构建保持联系的应用程序D。
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 64f407288da72dbf51fb3388ddf36caea377f09c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790427"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>创建使用元素 API 的 Xamarin.iOS 应用程序

_本文基于 MonoTouch 对话框文章简介中提供的信息。它提供的演练，演示如何使用 MonoTouch.Dialog (MTD） 元素 API 若要快速开始构建保持联系的应用程序D。_

在本演练中，我们将使用 MTD 元素 API 创建主 / 从样式的应用程序会显示任务列表。 当用户选择<span class="ui"> + </span>按钮在导航栏中，新行将添加到任务的表。 选择该行将导航到，我们便可以更新任务说明和到期日期，如下所示的详细信息屏幕：

 [![](elements-api-walkthrough-images/01-task-list-app.png "选择该行将导航到，我们便可以更新任务说明和到期日期的详细信息屏幕")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 <a name="Elements_API_Walkthrough" />


## <a name="elements-api-walkthrough"></a>元素 API 演练

在[简介 MonoTouch 对话框](~/ios/user-interface/monotouch.dialog/index.md)我们获得深入了解保持联系的不同部分的文章，D。 让我们使用元素 API 在一起放入应用程序。

 <a name="Setting_up_the_Multi-Screen_Application" />


## <a name="setting-up-the-multi-screen-application"></a>设置多屏幕应用程序

若要启动屏幕创建过程，MonoTouch.Dialog 创建`DialogViewController`，然后添加`RootElement`。

若要创建 MonoTouch.Dialog 多屏幕应用程序，我们需要：

1.  创建  `UINavigationController.`
1.  创建  `DialogViewController.`
1.  添加`DialogViewController`的 root  `UINavigationController.` 
1.  添加`RootElement`到  `DialogViewController.`
1.  添加`Sections`和`Elements`到  `RootElement.` 


 <a name="Using_A_UINavigationController" />


### <a name="using-a-uinavigationcontroller"></a>使用 UINavigationController

若要创建导航样式的应用程序，我们需要创建`UINavigationController`，并将其作为`RootViewController`中`FinishedLaunching`方法`AppDelegate`。 若要使`UINavigationController`使用 MonoTouch.Dialog，我们将添加`DialogViewController`到`UINavigationController`如下所示：

```csharp
public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
{
        _window = new UIWindow (UIScreen.MainScreen.Bounds);
            
        _rootElement = new RootElement ("To Do List"){new Section ()};

        // code to create screens with MT.D will go here …

        _rootVC = new DialogViewController (_rootElement);
        _nav = new UINavigationController (_rootVC);
        _window.RootViewController = _nav;
        _window.MakeKeyAndVisible ();
            
        return true;
}
```

上面的代码中创建的实例`RootElement`和将其传递到`DialogViewController`。 `DialogViewController`始终具有`RootElement`在其层次结构的顶部。 在此示例中，`RootElement`创建与字符串"待办事项列表，"它可作为导航控制器的导航栏中的标题。 此时，运行应用程序将显示如下所示的屏幕如下：

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "运行应用程序将显示在此处显示的屏幕")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

让我们了解如何使用 MonoTouch.Dialog 的层次结构的`Sections`和`Elements`添加多个屏幕。

 <a name="Creating_the_Dialog_Screens" />


### <a name="creating-the-dialog-screens"></a>创建对话框屏幕

A`DialogViewController`是`UITableViewController`MonoTouch.Dialog 用于添加屏幕的子类。 MonoTouch.Dialog 加上创建屏幕`RootElement`到`DialogViewController`，如我们前面的说明。 `RootElement`可以`Section`表示的部分的表的实例。
各节的元素组成，其他部分，或者甚至其他`RootElements`。 通过嵌套`RootElements`，MonoTouch.Dialog 自动创建导航样式的应用程序，如我们所见下一步。

 <a name="Using_DialogViewController" />


### <a name="using-dialogviewcontroller"></a>使用 DialogViewController

`DialogViewController`，正在`UITableViewController`子类，拥有`UITableView`作为其视图。 在此示例中，我们想要将项添加到表每次<span class="ui"> + </span>点击按钮。 由于`DialogViewController`已添加到`UINavigationController`，我们可以使用`NavigationItem`的`RightBarButton`属性来添加<span class="ui"> + </span>按钮，如下所示：

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

当我们创建`RootElement`更早版本，我们将其传递一个`Section`实例，以便我们无法添加元素作为<span class="ui"> + </span>用户点击按钮。 我们可以使用下面的代码完成此在事件处理程序按钮：

```csharp
_addButton.Clicked += (sender, e) => {
                
        ++n;
                
        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
        var taskElement = new RootElement (task.Name){
                new Section () {
                        new EntryElement (task.Name, 
                                "Enter task description", task.Description)
                },
                new Section () {
                        new DateElement ("Due Date", task.DueDate)
                }
        };
        _rootElement [0].Add (taskElement);
};
```

此代码创建一个新`Task`对象每次点击按钮。 下面显示的简单实现`Task`类：

```csharp
public class Task
{   
        public Task ()
        {
        }
        
        public string Name { get; set; }
        
        public string Description { get; set; }

        public DateTime DueDate { get; set; }
}
```

 []()

该任务的`Name`属性用于创建`RootElement`的标题以及一个名为的计数器变量`n`，就会递增每个新的任务。 MonoTouch.Dialog 将元素转换为添加到的行`TableView`时每个`taskElement`添加。

 <a name="Presenting_and_Managing_Dialog_Screens" />


## <a name="presenting-and-managing-dialog-screens"></a>提供和管理对话框屏幕

我们使用`RootElement`以便 MonoTouch.Dialog 将自动创建每个任务的详细信息的新屏幕并选择行时导航到它。

任务详细信息屏幕自身组成两个部分;以下各节包含单个元素。 第一个元素从创建`EntryElement`为任务提供一个可编辑的行`Description`属性。 选择元素时，会看到进行文本编辑的键盘，如下所示：

 [![](elements-api-walkthrough-images/03-create-task.png "如所示，选择元素时，显示进行文本编辑的键盘")](elements-api-walkthrough-images/03-create-task.png#lightbox)

第二个部分包含`DateElement`，可让我们管理任务的`DueDate`属性。 选择日期将自动加载日期选取器，如所示：

 [![](elements-api-walkthrough-images/04-date-picker.png "选择日期将自动加载作为日期选取器")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

在这两`EntryElement`和`DateElement`情况下 （或者为 MonoTouch.Dialog 中的任意数据输入元素），将自动保留为值的任何更改。 我们可以通过编辑日期，然后导航根屏幕和各种任务详细信息，其中保留的详细信息屏幕中的值之间的来回对此进行演示。

 <a name="Summary" />


## <a name="summary"></a>总结

本文介绍的演练，介绍了如何使用 MonoTouch.Dialog 元素 API。 它涵盖使用保持联系创建多屏幕应用程序的基本步骤D，包括如何使用`DialogViewController`以及如何添加元素和节创建屏幕。 此外，它说明了如何使用保持联系结合 D `UINavigationController`。


## <a name="related-links"></a>相关链接

- [MTDWalkthrough （示例）](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [段视频-Miguel de Icaza 使用 MonoTouch.Dialog 创建 iOS 登录屏幕](http://youtu.be/3butqB1EG0c)
- [段视频-使用方便地创建 iOS 用户界面 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [MonoTouch.Dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [反射 API 演练](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [JSON 元素演练](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [在 Github 上的 MonoTouch 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
