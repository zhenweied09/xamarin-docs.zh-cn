---
title: "演练： 创建使用反射 API 的应用程序"
description: "除了元素 API，MonoTouch.Dialog （保持联系D） 还包括基于属性的反射 API。 反射 API 使与保持联系创建屏幕D 简单地修饰具有属性的类。 本文提供了演练演示如何创建使用反射 API 的应用程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 44df6fce4ec6d667c096da01cfc339ec2afdb077
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough-creating-an-application-using-the-reflection-api"></a>演练： 创建使用反射 API 的应用程序

_除了元素 API，MonoTouch.Dialog （保持联系D） 还包括基于属性的反射 API。反射 API 使与保持联系创建屏幕D 简单地修饰具有属性的类。本文提供了演练演示如何创建使用反射 API 的应用程序。_


MTD 反射 API 允许类是用属性修饰该保持联系D 使用自动创建的屏幕。 反射 API 提供这些类并在屏幕上显示的内容之间的绑定。 尽管此 API 不提供 API 的元素执行细化的控制，它通过自动构建出基于类修饰元素层次结构来降低复杂性。

 <a name="Getting_Started_with_the_Reflection_API" />


## <a name="getting-started-with-the-reflection-api"></a>反射 API 入门

使用反射 API 就像一样简单：

1.  创建类使用保持联系修饰D 属性。
1.  创建`BindingContext`实例，将其传递上面的类的实例。 
1.  创建`DialogViewController`，将其传递`BindingContext’s` `RootElement` 。 


让我们看一下示例来演示如何使用反射 API。 在此示例中，我们要生成简单的数据输入屏幕如下所示：

 [ ![](reflection-api-walkthrough-images/01-expense-entry.png "在此示例中，我们要生成简单的数据输入屏幕，如下所示")](reflection-api-walkthrough-images/01-expense-entry.png)

 <a name="Creating_a_Class_with_MT.D_Attributes" />


## <a name="creating-a-class-with-mtd-attributes"></a>使用保持联系创建类D 属性

我们需要使用反射 API 的第一件事是用属性修饰的类。 这些特性将用于通过保持联系元素 API 中的 D 内部即可创建对象。 例如，考虑以下类定义：

```csharp
public class Expense
{
        [Section("Expense Entry")]

        [Entry("Enter expense name")]
        public string Name;
        
        [Section("Expense Details")]
  
        [Caption("Description")]
        [Entry]
        public string Details;
        
        [Checkbox]
        public bool IsApproved = true;
}
```

`SectionAttribute`将导致的部分`UITableView`正在创建带用于填充该节的标头字符串参数。 部分声明后，其后的每个字段将包括在该部分中，直到声明另一个部分。
为字段创建的用户界面元素的类型将取决于字段的类型和 MTD 属性修饰它。

例如，`Name`字段是`string`和它用修饰`EntryAttribute`。 这会导致正在添加到具有文本输入字段和指定的标题的表的行。 同样，`IsApproved`字段是`bool`与`CheckboxAttribute`，这会导致在表行中使用一个表单元格右侧的复选框。 保持联系D 使用域名称自动添加一个空格，来创建标题在这种情况下，因为它不指定特性中。

 <a name="Adding_the_BindingContext" />


## <a name="adding-the-bindingcontext"></a>添加 BindingContext

若要使用`Expense`类，我们需要创建`BindingContext`。 A`BindingContext`是将将数据绑定从要创建的元素的层次结构的特性化类的类。 若要创建一个帐户，我们只需其进行实例化并将在特性化类的实例传递给构造函数。

例如，若要添加使用我们声明的 UI 属性中`Expense`类中，包括下面的代码`FinishedLaunching`方法`AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

然后我们所要做，以创建 UI 的就是添加`BindingContext`到`DialogViewController`和将其设置为`RootViewController`的窗口中，如下所示：

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
{
   
        window = new UIWindow (UIScreen.MainScreen.Bounds);
            
        var expense = new Expense ();
        var bctx = new BindingContext (null, expense, "Create a task");
        var dvc = new DialogViewController (bctx.Root);
            
        window.RootViewController = dvc;
        window.MakeKeyAndVisible ();
            
        return true;
}
```

运行应用程序现在将导致显示上面显示的屏幕。

 <a name="Adding_a_UINavigationController" />


### <a name="adding-a-uinavigationcontroller"></a>添加 UINavigationController

但是请注意，标题"创建任务"，我们传递给`BindingContext`不会显示。 这是因为`DialogViewController`不是属于`UINavigatonController`。 让我们更改代码以添加`UINavigationController`窗口的`RootViewController,`并添加`DialogViewController`的 root`UINavigationController`如下所示：

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

现在，当我们运行应用程序，该标题将显示在`UINavigationController’s`为下面所示的屏幕截图的导航栏：

 [ ![](reflection-api-walkthrough-images/02-create-task.png "现在当我们运行应用程序，就会 UINavigationControllers 导航栏中显示标题")](reflection-api-walkthrough-images/02-create-task.png)

通过包括`UINavigationController`，我们现在可以利用保持联系其他功能D 导航所必需的。 例如，我们可以添加到枚举`Expense`类定义的类别的费用，以及保持联系D 将自动创建选择屏幕。 为了演示，修改`Expense`类，以包含`ExpenseCategory`字段，如下所示：

```csharp
public enum Category
{
        Travel,
        Lodging,
        Books
}
        
public class Expense
{
        …

        [Caption("Category")]
        public Category ExpenseCategory;
}
```

运行应用程序现在将导致为类别表中的新行所示：

 [ ![](reflection-api-walkthrough-images/03-set-details.png "运行应用程序现在将会生成为类别表中的新行中所示")](reflection-api-walkthrough-images/03-set-details.png)

选择该行将导致应用程序导航到新的屏幕行对应于枚举，如下所示：

 [ ![](reflection-api-walkthrough-images/04-set-category.png "选择行导致应用程序导航到新的屏幕行对应于枚举")](reflection-api-walkthrough-images/04-set-category.png)

 <a name="Summary" />


## <a name="summary"></a>摘要

本文介绍反射 API 的演练。 我们介绍了如何将属性添加到一个类以控制显示的内容。 我们还讨论了如何使用`BindingContext`将数据从一个类绑定到创建，以及如何使用保持联系元素层次结构使用 D `UINavigationController`。


## <a name="related-links"></a>相关链接

- [MTDReflectionWalkthrough （示例）](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [段视频-Miguel de Icaza 使用 MonoTouch.Dialog 创建 iOS 登录屏幕](http://youtu.be/3butqB1EG0c)
- [段视频-使用方便地创建 iOS 用户界面 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [简介 MonoTouch 对话框](~/ios/user-interface/monotouch.dialog/index.md)
- [元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [JSON 元素演练](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [在 Github 上的 MonoTouch 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
