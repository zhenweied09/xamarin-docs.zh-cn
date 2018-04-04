---
title: 演练： 使用 JSON 元素创建的用户界面
description: MonoTouch.Dialog （保持联系D） 包括对 JSON 数据通过动态 UI 生成的支持。 在本教程中，我们将演练如何使用 JSONElement 从 JSON 包含与应用程序，或从远程 Url 加载创建用户界面。
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 322857295383d17da03507bdd5ac78753f8c0619
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough-using-a-json-element-to-create-a-user-interface"></a>演练： 使用 JSON 元素创建的用户界面

_MonoTouch.Dialog （保持联系D） 包括对 JSON 数据通过动态 UI 生成的支持。在本教程中，我们将演练如何使用 JSONElement 从 JSON 包含与应用程序，或从远程 Url 加载创建用户界面。_


保持联系D 支持在 JSON 中声明的创建用户界面。 当使用 JSON，保持联系声明元素D 将关联的元素为你自动创建。 JSON 可以加载从本地文件，分析`JsonObject`实例或甚至远程 Url。

保持联系D 支持使用 JSON 时元素 API 中可用的功能的完整范围。 例如，下面的屏幕截图中的应用程序是完全声明使用 JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "例如，此屏幕截图中的应用程序完全声明使用 JSON") ](json-element-walkthrough-images/01-load-from-file.png#lightbox) [ ![ ](json-element-walkthrough-images/01-load-from-file.png "例如，此屏幕截图中的应用程序完全使用声明JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

让我们重新访问中的示例[元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)教程中，显示如何添加使用 JSON 任务详细信息屏幕。

## <a name="json-walkthrough"></a>JSON 演练

本演练的示例允许要创建的任务。 在第一个屏幕上选择一项任务时，详细信息屏幕显示所示：

 [![](json-element-walkthrough-images/03-task-list.png "如所示的第一个屏幕上选择一项任务时，显示详细信息屏幕")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>创建 JSON

对于此示例，我们将从名为的项目中的文件加载 JSON `task.json`。 保持联系D 需要 JSON 以符合镜像元素 API 的语法。 就像使用元素 API 代码中，我们在使用 JSON 时，声明部分和在这些部分中，我们添加元素。 若要声明部分和 JSON 中的元素，我们使用字符串"部分"和"元素"分别作为键。 对于每个元素，使用设置关联的元素类型`type`密钥。 作为键的情况下，每个其他元素属性设置的属性名称。

例如，以下 JSON 描述的部分和任务详细信息的元素：

```csharp
{
    "title": "Task",
    "sections": [
        {
          "elements" : [
            {
                "id" : "task-description",
                "type": "entry",
                "placeholder": "Enter task description"
            },
            {
                "id" : "task-duedate",
                "type": "date",
                "caption": "Due Date",
                "value": "00:00"
            }
         ]
        }
    ]
  }
```

请注意上述 JSON 包括每个元素的 id。 任何元素可以包含一个 id，以在运行时引用它。 我们将了解如何在稍后当我们介绍如何加载的 JSON 代码中使用此。

 <a name="Loading_the_JSON_in_Code" />


## <a name="loading-the-json-in-code"></a>加载代码中的 JSON

一旦定义了 JSON，我们需要将其装载到保持联系D 使用`JsonElement`类。 假设使用我们在上面创建的 JSON 文件已添加到与名称 sample.json 项目并给定生成操作的内容，加载`JsonElement`非常简单，调用以下代码行：

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

由于我们要添加此按需每次创建一个任务，我们可以修改，如下所示从前面的元素 API 示例中所单击的按钮：

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

 <a name="Accessing_Elements_at_Runtime" />


## <a name="accessing-elements-at-runtime"></a>在运行时访问元素

回想一下我们在我们将它们声明在 JSON 文件时，id 添加到这两个元素。 我们可以使用的 id 属性来访问在运行时修改其属性在代码中的每个元素。 例如，下面的代码引用的项和日期元素从任务对象中设置的值：

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        taskElement.Caption = task.Name;

        var description = taskElement ["task-description"] as EntryElement;

        if (description != null) {
                description.Caption = task.Name;
                description.Value = task.Description;       
        }

        var duedate = taskElement ["task-duedate"] as DateElement;

        if (duedate != null) {                
                duedate.DateValue = task.DueDate;
        }
        _rootElement [0].Add (taskElement);
};
```

 <a name="Loading_JSON_from_a_Url" />


## <a name="loading-json-from-a-url"></a>从 Url 加载 JSON

保持联系D 还支持从外部 Url 动态加载 JSON，只需将 Url 传递给构造函数的`JsonElement`。 保持联系D 将展开层次结构在屏幕之间导航，在 JSON 中按需声明。 例如，考虑如下面的 JSON 文件位于根目录下的本地 web 服务器：

```csharp
{
    "type": "root",
    "title": "home",
    "sections": [
       {
         "header": "Nested view!",
         "elements": [
           {
             "type": "boolean",
             "caption": "Just a boolean",
             "id": "first-boolean",
             "value": false
           },
           {
             "type": "string",
             "caption": "Welcome to the nested controller"
           }
         ]
       }
     ]
}
```

我们可以加载这使用`JsonElement`如以下代码所示：

```csharp
_rootElement = new RootElement ("Json Example"){
        new Section (""){ new JsonElement ("Load from url",
                "http://localhost/sample.json")
        }
};
```

在运行时，检索和分析保持联系的文件当用户导航到第二个视图中，如下面的屏幕截图中所示的 D:

 [![](json-element-walkthrough-images/04-json-web-example.png "将检索文件，然后将其保持联系的分析D 当用户导航到第二个视图")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>总结

本文介绍了如何创建使用接口保持联系从 JSON D。 它介绍了如何加载包含在文件与应用程序以及从远程 Url 中的 JSON。 它还介绍了如何访问在运行时在 JSON 中所述的元素。


## <a name="related-links"></a>相关链接

- [MTDJsonDemo (sample)](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [段视频-Miguel de Icaza 使用 MonoTouch.Dialog 创建 iOS 登录屏幕](http://youtu.be/3butqB1EG0c)
- [段视频-使用方便地创建 iOS 用户界面 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [MonoTouch.Dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [反射 API 演练](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [在 Github 上的 MonoTouch 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
