---
title: "数据绑定和键 / 值编码"
description: "本文介绍如何使用键值对的编码和观察以便用于数据绑定到 Xcode 的接口生成器中的 UI 元素的键 / 值。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 2c01a36eabb15fbe9b975c91328dfa7cfd651896
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="data-binding-and-key-value-coding"></a>数据绑定和键 / 值编码

_本文介绍如何使用键值对的编码和观察以便用于数据绑定到 Xcode 的接口生成器中的 UI 元素的键 / 值。_

## <a name="overview"></a>概述

在使用 C# 和.NET Xamarin.Mac 应用程序中，你有权访问的相同键值对的编码和数据绑定技术，的开发人员*Objective C*和*Xcode*未。 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的_接口生成器_到数据绑定与而不编写代码的 UI 元素。

通过使用键 / 值编码和数据绑定技术 Xamarin.Mac 应用程序中，你可以显著缩短您需要编写和维护填充和使用 UI 元素的代码量。 你还可以进一步分离你的备份数据的好处 (_数据模型_) 从你前面结束用户界面 (_模型-视图-控制器_)，从而导致易于维护更灵活的应用程序设计。

[![正在运行的应用程序示例](databinding-images/intro01.png "正在运行的应用程序示例")](databinding-images/intro01-large.png)

在本文中，我们将介绍使用键值对的编码和 Xamarin.Mac 应用程序中的数据绑定的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`属性用于连接你的 C# 类 OBJECTIVE-C 的对象和 UI 元素。

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>什么是键值对的编码

键值对的编码 (KVC) 是用于间接访问的对象的属性，请使用键 （特殊格式字符串） 来标识而不是通过实例变量访问这些属性或访问器方法的机制 (`get/set`)。 通过实现 Xamarin.Mac 应用程序中的键值对的编码符合访问器，你可以访问键 / 值观察 (KVO)、 数据绑定、 核心数据、 Cocoa 绑定和 scriptability 等其他 macOS （以前称为 OS X） 功能。

通过使用键 / 值编码和数据绑定技术 Xamarin.Mac 应用程序中，你可以显著缩短您需要编写和维护填充和使用 UI 元素的代码量。 你还可以进一步分离你的备份数据的好处 (_数据模型_) 从你前面结束用户界面 (_模型-视图-控制器_)，从而导致易于维护更灵活的应用程序设计。 

例如，让我们看一下 KVC 符合对象的以下类定义：

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

首先，`[Register("PersonModel")]`属性注册类并将它暴露给目标。 然后，需要从继承类`NSObject`(或继承自的子类`NSObject`)，这将添加多个基类允许为 KVC 符合的方法。 接下来，`[Export("Name")]`属性公开`Name`属性并定义更高版本将用于通过 KVC 和 KVO 技术访问属性的密钥值。 

最后，若要能够将键 / 值观察到更改为该属性的值，访问器必须将包装对在其值更改`WillChangeValue`和`DidChangeValue`方法调用 (指定与相同的密钥`Export`属性)。  例如:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

此步骤是_非常_重要在 Xcode 中的数据绑定的接口生成器 （正如我们将看到这篇文章中更高版本）。

有关详细信息，请参阅 Apple 的[键-值编码编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)。

### <a name="keys-and-key-paths"></a>密钥和密钥路径

A_密钥_是一个字符串，标识对象的特定属性。 通常，密钥对应于键值编码符合对象中的访问器方法的名称。 密钥必须使用 ASCII 编码，通常以小写字母开头且不能包含空格。 因此给定上例中，`Name`将密钥值`Name`属性`PersonModel`类。 密钥和它们公开的属性名称不一定要相同，但是在大多数情况下它们。

A_注册表项路径_点的字符串字符分隔用来指定对象属性层次结构，以遍历的密钥。 序列中的第一个键的属性是相对于接收方，并且每个后续的密钥进行计算相对于以前的属性的值。 相同的方式使用点表示法来遍历对象和 C# 类中的其属性。

例如，如果你展开`PersonModel`类并添加`Child`属性：

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

子女的姓名的密钥路径将是`self.Child.Name`或只需`Child.Name`（基于如何使用密钥值）。

### <a name="getting-values-using-key-value-coding"></a>获取使用键值对的编码的值

`ValueForKey`方法返回为指定键的值 (作为`NSString`)，相对于接收请求 KVC 类的实例。 例如，如果`Person`是的一个实例`PersonModel`上面定义的类：

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

此命令将返回的值`Name`实例的属性`PersonModel`。 

### <a name="setting-values-using-key-value-coding"></a>使用键值对的编码的设置值

同样，`SetValueForKey`将值设置为指定的键 (作为`NSString`)，相对于接收请求 KVC 类的实例。 同样，使用的实例`PersonModel`类，如下所示：

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

将更改的值`Name`属性`Jane Doe`。

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>遵循值的更改

使用键 / 值观察 (KVO)，可以将观察者附加到 KVC 符合类的特定密钥和随时通知 （使用 KVC 技术或直接访问 C# 代码中的给定的属性） 修改该注册表项的值。 例如:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

现在，任何时候`Name`属性`Person`实例`PersonModel`修改类时，新值写出到控制台。 

有关详细信息，请参阅 Apple 的[键-值观察编程指南简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i)。

## <a name="data-binding"></a>数据绑定

以下部分将说明如何使用键值对的编码和键 / 值观察符合类将数据绑定到 UI 元素在 Xcode 的接口生成器中，而不是读取和写入使用 C# 代码的值。 以这种方式中，你将单独你_数据模型_更加灵活和易于维护，则从用来显示它们的视图，使该 Xamarin.Mac 应用程序。 你还将大大减少所必须写入代码的量。

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>定义数据模型

你可以将数据绑定接口生成器中的 UI 元素之前，你必须具有 Xamarin.Mac 应用程序充当中定义的 KVC/KVO 符合类_数据模型_绑定。 数据模型提供所有的数据，将在用户界面中显示并接收对数据的用户运行该应用程序时在 UI 中进行任何修改。

例如，如果你在编写的应用程序管理的员工组，你可以使用下面的类定义数据模型：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

中介绍了此类的功能的大部分[什么是键值对的编码](#What_is_Key-Value_Coding)上面一节。 但是，让我们看一下几个特定的元素和所做以允许此类，以充当数据模型的一些附加条件**阵列控制器**和**树控制器**（其中我们以后将使用到数据将绑定**树视图**，**大纲视图**和**集合视图**)。

第一次，员工可能是一个管理器，因为我们已使用`NSArray`(专门`NSMutableArray`以便可以修改的值) 以允许它们管理要附加到它们的员工：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

此处需要注意的两项操作：

1. 我们使用`NSMutableArray`而不是标准的 C# 数组或集合，因为这是数据绑定到 AppKit 控件要求，如**表视图**，**大纲视图**和**集合**.
2. 我们通过它强制转换为公开的员工数组`NSArray`用于数据绑定目的和更改其 C# 已设置格式的名称， `People`，到一个数据绑定的预期`personModelArray`形式**{class_name} 数组**（请注意第一个字符进行了大小写）。

接下来，我们需要添加一些特殊名称公共方法，以支持**阵列控制器**和**树控制器**:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

这些行为允许请求和修改数据，它们显示的控制器。 如公开`NSArray`更高版本，这些消息具有非常特定的命名约定 （即不同于典型 C# 命名约定）：

- `addObject:` -将对象添加到数组。
- `insertObject:in{class_name}ArrayAtIndex:` -其中`{class_name}`是您的类的名称。 此方法将对象插入到给定索引处的数组。
- `removeObjectFrom{class_name}ArrayAtIndex:` -其中`{class_name}`是您的类的名称。 此方法中删除给定索引处的数组中的对象。
- `set{class_name}Array:` -其中`{class_name}`是您的类的名称。 此方法，可将替换为一个新的现有携带。

在这些方法，我们已包装的数组中的更改`WillChangeValue`和`DidChangeValue`KVO 符合性的消息。

最后，由于`Icon`属性依赖于的值`isManager`属性，更改为`isManager`属性可能不会反映在`Icon`的 （在期间 KVO) 的数据绑定 UI 元素：

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
``` 

若要更正的我们使用以下代码：

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

请注意，除了其自己的密钥，`isManager`访问器同时发送`WillChangeValue`和`DidChangeValue`消息`Icon`密钥以便会看到更改以及。

我们将使用`PersonModel`本文的其余的数据模型。

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>简单数据绑定

与我们数据模型中定义，让我们看一下在 Xcode 的接口生成器数据绑定的一个简单的示例。 例如，让我们将窗体添加到我们 Xamarin.Mac 应用程序可以用于编辑`PersonModel`我们上面定义的。 我们将添加几个文本字段和复选框来显示和编辑的我们的模型的属性。

首先，让我们添加一个新**视图控制器**到我们**Main.storyboard**文件位于接口生成器，并将其类`SimpleViewController`: 

[![添加新视图控制器](databinding-images/simple01.png "添加的新视图控制器")](databinding-images/simple01-large.png)

接下来，将返回到 Visual Studio for Mac，请编辑**SimpleViewController.cs**文件 （即已自动添加到我们的项目） 和公开这样的实例`PersonModel`，我们将数据绑定到窗体。 添加以下代码：

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

下一步时加载此视图，让我们创建的实例我们`PersonModel`并填充此代码：

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

现在我们需要创建窗体，双击**Main.storyboard**文件以打开它以在接口生成器中编辑。 布局窗体看起来类似于以下内容：

[![编辑在 Xcode 中的情节提要](databinding-images/simple02.png "编辑在 Xcode 中的情节提要")](databinding-images/simple02-large.png)

要向数据绑定到窗体`PersonModel`我们通过公开`Person`密钥，执行以下操作：

1. 选择**雇员姓名**文本字段，切换到**绑定检查器**。
2. 检查**将绑定到**框中，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.Name`为**注册表项路径**: 

    [![输入的密钥路径](databinding-images/simple03.png "输入密钥的路径")](databinding-images/simple03-large.png)
3. 选择**职业**文本字段，并检查**将绑定到**框中，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.Occupation`为**注册表项路径**:  

    [![输入的密钥路径](databinding-images/simple04.png "输入密钥的路径")](databinding-images/simple04-large.png)
4. 选择**员工是一个管理器**复选框，并检查**将绑定到**框中，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.isManager`为**注册表项路径**:  

    [![输入的密钥路径](databinding-images/simple05.png "输入密钥的路径")](databinding-images/simple05-large.png)
5. 选择**管理的员工数**文本字段，并检查**将绑定到**框中，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.NumberOfEmployees`为**注册表项路径**:  

    [![输入的密钥路径](databinding-images/simple06.png "输入密钥的路径")](databinding-images/simple06-large.png)
6. 如果员工不是管理器，我们想要隐藏的员工的管理标签数和文本字段。
7. 选择**管理的员工数**标签，展开**Hidden** turndown 并检查**将绑定到**框中，然后选择**简单视图控制器**从下拉列表。 接下来输入`self.Person.isManager`为**注册表项路径**:  

    [![输入的密钥路径](databinding-images/simple07.png "输入密钥的路径")](databinding-images/simple07-large.png)
8. 选择`NSNegateBoolean`从**值转换器**下拉列表中：  

    ![选择 NSNegateBoolean 密钥转换](databinding-images/simple08.png "选择 NSNegateBoolean 密钥转换")
9. 这将告知数据绑定是否，将隐藏标签的值`isManager`属性是`false`。
10. 重复步骤 7 和 8**管理的员工数**文本字段。
11. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

如果你运行应用程序，从值`Person`属性将自动填充我们的表单：

[![显示的自动填充窗体](databinding-images/simple09.png "显示自动填充的窗体")](databinding-images/simple09-large.png)

用户对窗体的任何更改将返回写入到`Person`视图控制器中的属性。 例如，取消选择**员工是一个管理器**更新`Person`实例我们`PersonModel`和**管理的员工数**标签和文本字段隐藏的自动 （通过数据绑定）：

[![隐藏非经理的员工数](databinding-images/simple10.png "隐藏非经理的员工数")](databinding-images/simple10-large.png)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>表视图数据绑定

现在，我们已开绑定数据的基础知识，让我们看一下更复杂的数据绑定任务通过使用_阵列控制器_和数据绑定到表视图。 有关使用表视图的详细信息，请参阅我们[表视图](~/mac/user-interface/table-view.md)文档。

首先，让我们添加一个新**视图控制器**到我们**Main.storyboard**文件位于接口生成器，并将其类`TableViewController`:

[![添加新视图控制器](databinding-images/table01.png "添加的新视图控制器")](databinding-images/table01-large.png)

接下来，让我们编辑**TableViewController.cs**文件 （即已自动添加到我们的项目） 和公开数组 (`NSArray`) 的`PersonModel`，我们将数据绑定到窗体的类。 添加以下代码：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

只需像我们在`PersonModel`类上面[定义数据模型](#Defining_your_Data_Model)部分中，我们已公开四个专门命名的公共方法，以便从我们的集合的数组控制器和读取和写入数据`PersonModels`。

接下来加载视图后，我们需要填充我们数组替换为以下代码：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

现在我们需要创建我们表视图，双击**Main.storyboard**文件以打开它以在接口生成器中编辑。 布局表看起来类似于以下内容：

[![新的表视图布局](databinding-images/table02.png "布局新的表视图")](databinding-images/table02-large.png)

我们需要添加**阵列控制器**若要提供绑定的数据到我们的表，请执行以下操作：

1. 拖动**数组控制器**从**库检查器**到**界面编辑器**:  

    ![从库中选择一个阵列控制器](databinding-images/table03.png "从库中选择一个阵列控制器")
2. 选择**阵列控制器**中**接口层次结构**并切换到**属性检查器**:  

    [![选择属性检查器](databinding-images/table04.png "选择属性检查器")](databinding-images/table04-large.png)
3. 输入`PersonModel`为**类名**，单击**加上**按钮，然后添加三个密钥。 对其命名`Name`，`Occupation`和`isManager`:  

    ![添加所需的密钥路径](databinding-images/table05.png "添加所需的密钥路径")
4. 这将告知阵列控制器什么管理一个数组，并且哪些属性应公开 （通过密钥）。
5. 切换到**绑定检查器**并在列表视图**内容数组**选择**将绑定到**和**表视图控制器**。 输入**模型的注册表项路径**的`self.personModelArray`:  

    ![输入的密钥路径](databinding-images/table06.png "输入注册表项路径")
6. 这会绑定到的数组的数组控制器`PersonModels`我们在我们的视图控制器上公开。

现在我们需要将我们的表视图绑定到数组控制器，请执行以下操作：

1. 选择表视图和**绑定检查器**:  

    [![选择绑定检查器](databinding-images/table07.png "选择绑定检查器")](databinding-images/table07-large.png)
2. 下**表内容**turndown，选择**将绑定到**和**阵列控制器**。 输入`arrangedObjects`为**控制器密钥**字段：  

    ![定义控制器密钥](databinding-images/table08.png "定义控制器密钥")
3. 选择**表视图单元格**下**员工**列。 在**绑定检查器**下**值**turndown，选择**将绑定到**和**表单元格视图**。 输入`objectValue.Name`为**模型的注册表项路径**:  

    [![设置模型项路径](databinding-images/table09.png "设置模型项路径")](databinding-images/table09-large.png)
4. `objectValue` 为当前`PersonModel`数组由数组控制器中。
5. 选择**表视图单元格**下**职业**列。 在**绑定检查器**下**值**turndown，选择**将绑定到**和**表单元格视图**。 输入`objectValue.Occupation`为**模型的注册表项路径**:  

    [![设置模型项路径](databinding-images/table10.png "设置模型项路径")](databinding-images/table10-large.png)
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

如果我们运行应用程序时，将使用我们的数组填充表`PersonModels`:

[![运行应用程序](databinding-images/table11.png "运行应用程序")](databinding-images/table11-large.png)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>大纲视图数据绑定

针对大纲视图的数据绑定是非常类似于针对表视图的绑定。 主要区别是，我们将使用**树控制器**而不是**阵列控制器**到大纲视图中提供的绑定的数据。 有关使用大纲视图的详细信息，请参阅我们[大纲视图](~/mac/user-interface/outline-view.md)文档。

首先，让我们添加一个新**视图控制器**到我们**Main.storyboard**文件位于接口生成器，并将其类`OutlineViewController`: 

[![添加新视图控制器](databinding-images/outline01.png "添加的新视图控制器")](databinding-images/outline01-large.png)

接下来，让我们编辑**OutlineViewController.cs**文件 （即已自动添加到我们的项目） 和公开数组 (`NSArray`) 的`PersonModel`，我们将数据绑定到窗体的类。 添加以下代码：

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

只需像我们在`PersonModel`类上面[定义数据模型](#Defining_your_Data_Model)部分中，我们已公开四个专门命名的公共方法，以便从我们的集合的树控制器和读取和写入数据`PersonModels`。

接下来加载视图后，我们需要填充我们数组替换为以下代码：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

现在我们需要创建我们大纲视图中，双击**Main.storyboard**文件以打开它以在接口生成器中编辑。 布局表看起来类似于以下内容：

[![创建该大纲视图](databinding-images/outline02.png "创建大纲视图")](databinding-images/outline02-large.png)

我们需要添加**树控制器**若要提供给我们大纲绑定的数据，请执行以下操作：

1. 拖动**树控制器**从**库检查器**到**界面编辑器**:  

    ![从库中选择的树控制器](databinding-images/outline03.png "从库中选择的树控制器")
2. 选择**树控制器**中**接口层次结构**并切换到**属性检查器**:  

    [![选择属性检查器](databinding-images/outline04.png "选择属性检查器")](databinding-images/outline04-large.png)
3. 输入`PersonModel`为**类名**，单击**加上**按钮，然后添加三个密钥。 对其命名`Name`，`Occupation`和`isManager`:  

    ![添加所需的密钥路径](databinding-images/outline05.png "添加所需的密钥路径")
4. 这将告知树控制器什么管理一个数组，并且哪些属性应公开 （通过密钥）。
5. 下**树控制器**部分中，输入`personModelArray`为**子级**，输入`NumberOfEmployees`下**计数**并输入`isEmployee`下**叶**:  

    ![设置树控制器密钥路径](databinding-images/outline05.png "设置树控制器密钥路径")
6. 这将告知树控制器在哪里可以找到任何子节点、 多少的子节点和如果当前节点具有子节点。
7. 切换到**绑定检查器**并在列表视图**内容数组**选择**将绑定到**和**文件的所有者**。 输入**模型的注册表项路径**的`self.personModelArray`:  

    ![编辑注册表项路径](databinding-images/outline06.png "编辑注册表项路径")
8. 这会绑定到的数组树控制器`PersonModels`我们在我们的视图控制器上公开。

现在我们需要将我们的大纲视图绑定到树控制器，请执行以下操作：

1. 选择该大纲视图并在**绑定检查器**选择：  

    [![选择绑定检查器](databinding-images/outline07.png "选择绑定检查器")](databinding-images/outline07-large.png)
2. 下**大纲视图内容**turndown，选择**将绑定到**和**树控制器**。 输入`arrangedObjects`为**控制器密钥**字段：  

    ![设置控制器项](databinding-images/outline08.png "设置控制器项")
3. 选择**表视图单元格**下**员工**列。 在**绑定检查器**下**值**turndown，选择**将绑定到**和**表单元格视图**。 输入`objectValue.Name`为**模型的注册表项路径**:  

    [![输入的模型项路径](databinding-images/outline09.png "输入模型注册表项路径")](databinding-images/outline09-large.png)
4. `objectValue` 为当前`PersonModel`数组由树控制器中。
5. 选择**表视图单元格**下**职业**列。 在**绑定检查器**下**值**turndown，选择**将绑定到**和**表单元格视图**。 输入`objectValue.Occupation`为**模型的注册表项路径**:  

    [![输入的模型项路径](databinding-images/outline10.png "输入模型注册表项路径")](databinding-images/outline10-large.png)
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

如果我们运行应用程序时，将使用我们的数组填充大纲`PersonModels`:

[![运行应用程序](databinding-images/outline11.png "运行应用程序")](databinding-images/outline11-large.png)

### <a name="collection-view-data-binding"></a>集合视图数据绑定

数据绑定与集合视图非常类似绑定，其中表视图中，因为阵列控制器用于找不到提供数据。 由于集合视图不包含的预设的显示格式，是需要提供用户交互反馈，并跟踪用户选择更多工作。

> [!IMPORTANT]
> 因为 Xcode 7 和 macOS 10.11 （和更高版本） 中的问题，而集合视图都无法使用情节提要 (.storyboard) 文件内部。 因此，你将需要继续使用.xib 文件以定义 Xamarin.Mac 应用你集合视图。 请参阅我们[集合视图](~/mac/user-interface/collection-view.md)文档以了解更多信息。

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`: 

![](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1.  **Collection View Item** -  That manages a single instance of an item in the collection.
2.  **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>调试本机故障

在你的数据绑定中进行了错误的操作可能会导致_本机崩溃_在非托管代码并导致 Xamarin.Mac 应用程序完全因`SIGABRT`错误：

[![本机崩溃对话框中的示例](databinding-images/debug01.png "本机崩溃对话框中的示例")](databinding-images/debug01-large.png)

在数据绑定时通常存在的本机损坏的四个主要原因：

1. 你的数据模型不是继承自`NSObject`或的子类`NSObject`。
2. 未公开到 Objective C 使用你属性`[Export("key-name")]`属性。
3. 未不换行中的访问器的值更改`WillChangeValue`和`DidChangeValue`方法调用 (指定与相同的密钥`Export`属性)。
4. 有错误或错误输入密钥**绑定检查器**接口生成器中。

### <a name="decoding-a-crash"></a>解码崩溃

让我们在我们的数据绑定中导致本机故障，因此我们可以向展示如何找到并修复此错误。 在接口生成器中，让我们更改我们的绑定中的集合视图示例的第一个标签的`Name`到`Title`:

[![编辑绑定密钥](databinding-images/debug02.png "编辑绑定密钥")](databinding-images/debug02-large.png)

让我们保存更改，请切换回 Visual Studio for Mac 与 Xcode 同步并运行我们的应用程序。 集合视图显示时，应用程序将短暂出现崩溃，`SIGABRT`错误 (如中所示**应用程序输出**Visual Studio 中用于 Mac) 因为`PersonModel`不公开具有键的属性`Title`:

[![绑定错误的示例](databinding-images/debug03.png "绑定错误的示例")](databinding-images/debug03-large.png)

如果我们滚动到中的错误的最顶端**应用程序输出**我们可以看到解决此问题的密钥：

[![在错误日志中查找问题](databinding-images/debug04.png "错误日志中查找问题")](databinding-images/debug04-large.png)

此行将告诉我们，密钥`Title`我们要绑定到的对象上不存在。 如果我们更改绑定回`Name`在接口生成器中，保存，同步，重新生成并运行，应用程序将运行按预期不会出现问题。

## <a name="summary"></a>摘要

本文已了解使用数据绑定和在 Xamarin.Mac 应用程序中键 / 值对编码的详细的信息。 首先，它看起来在通过使用键 / 值编码 (KVC) 和键 / 值观察 (KVO) 公开到 Objective C 的 C# 类。 接下来，它说明了如何使用 KVO 符合类和数据将其绑定到 Xcode 的接口生成器中的 UI 元素。 最后，它显示复杂数据绑定使用**阵列控制器**和**树控制器**。


## <a name="related-links"></a>相关链接

- [MacDatabinding 情节提要 （示例）](https://developer.xamarin.com/samples/mac/MacDatabinding-Storyboard/)
- [MacDatabinding XIBs （示例）](https://developer.xamarin.com/samples/mac/MacDatabinding-XIBs/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [标准控件](~/mac/user-interface/standard-controls.md)
- [表视图](~/mac/user-interface/table-view.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [集合视图](~/mac/user-interface/collection-view.md)
- [键 / 值编码编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [键值对的观察编程指南简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Cocoa 绑定编程主题简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [简介 Cocoa 绑定引用](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [macOS 人工界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
