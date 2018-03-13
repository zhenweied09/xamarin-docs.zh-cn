---
title: "标准控件"
description: "本文介绍如何使用标准的 AppKit 控件，如按钮、 标签、 文本字段、 复选框，并分段 Xamarin.Mac 应用程序中的控件。 它介绍将其添加到接口生成器的接口以及在代码中与它们进行交互。"
ms.topic: article
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: e887026b4f87d2e1bf8c7647a7845765ce8b886c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="standard-controls"></a>标准控件

_本文介绍如何使用标准的 AppKit 控件，如按钮、 标签、 文本字段、 复选框，并分段 Xamarin.Mac 应用程序中的控件。它介绍将其添加到接口生成器的接口以及在代码中与它们进行交互。_

在使用 C# 和.NET Xamarin.Mac 应用程序中，你有权访问相同 AppKit 控件，开发人员使用*Objective C*和*Xcode*未。 因为 Xamarin.Mac 与 Xcode 直接集成，你可以使用 Xcode 的_接口生成器_来创建和维护 Appkit 控件 （或根据需要在 C# 代码中直接创建它们）。

AppKit 控件是用于创建你 Xamarin.Mac 应用程序的用户界面的 UI 元素。 它们如按钮、 标签、 文本字段、 复选框和分段控件的元素组成，并导致出现时用户操作它们的即时操作或可见的结果。

[![](standard-controls-images/intro01.png "示例应用程序主屏幕")](standard-controls-images/intro01.png#lightbox)

在本文中，我们将介绍在 Xamarin.Mac 应用程序中使用 AppKit 控件的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于网络-最多 C# 类 OBJECTIVE-C 的对象和 UI 元素。

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>控件和视图简介

macOS （以前称为 Mac OS X） 提供一组标准的用户界面控件通过 AppKit Framework。 它们如按钮、 标签、 文本字段、 复选框和分段控件的元素组成，并导致出现时用户操作它们的即时操作或可见的结果。

所有 AppKit 控件具有标准的内置的外观将适用于大多数使用，某些指定用于在窗口框架区域中或在备用外观_自然饱和度效果_上下文，如侧栏区域或在通知中心小组件。

在使用 AppKit 控件时，Apple 将建议以下准则：

- 避免混合在同一视图的控件大小。
- 通常情况下，避免调整控件的垂直大小。
- 使用系统字体和控件内的正确的文本大小。
- 使用控件之间的适当间距。

有关详细信息，请参阅[有关控件和视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>在窗口框架中使用控件

有了包括允许它们以包括在窗口的框架区域中的显示样式的 AppKit 控件的子集。 有关示例，请参阅邮件应用程序的工具栏：

[![](standard-controls-images/mailapp.png "Mac 窗口框架")](standard-controls-images/mailapp.png#lightbox)

- **舍入纹理按钮**-`NSButton`使用的样式`NSTexturedRoundedBezelStyle`。
- **纹理舍入分段控件**-`NSSegmentedControl`使用的样式`NSSegmentStyleTexturedRounded`。
- **纹理舍入分段控件**-`NSSegmentedControl`使用的样式`NSSegmentStyleSeparated`。
- **舍入纹理弹出菜单**-`NSPopUpButton`使用的样式`NSTexturedRoundedBezelStyle`。
- **舍入纹理下拉列表菜单**-`NSPopUpButton`使用的样式`NSTexturedRoundedBezelStyle`。
- **搜索栏**- `NSSearchField`。

使用窗口框架中的 AppKit 控件时，Apple 将建议以下准则：

- 不要使用窗口正文中的窗口框架特定控件样式。
- 不使用窗口正文控件或样式的窗口框架中。

有关详细信息，请参阅[有关控件和视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>在接口生成器中创建用户界面

时创建新的 Xamarin.Mac Cocoa 应用程序时，默认情况下将获得标准保留为空，窗口。 在中定义此 windows`.storyboard`自动包括在项目中的文件。 若要编辑您的 windows 设计、 在**解决方案资源管理器**，双击`Main.storyboard`文件：

[![](standard-controls-images/edit01.png "在解决方案资源管理器中选择主情节提要")](standard-controls-images/edit01.png#lightbox)

这将在 Xcode 的接口生成器中打开的窗口设计：

[![](standard-controls-images/edit02.png "编辑在 Xcode 中的情节提要")](standard-controls-images/edit02.png#lightbox)

若要创建你的用户界面，将拖动 UI 元素 （AppKit 控制） 从**库检查器**到**界面编辑器**接口生成器中。 在示例中，**垂直拆分视图**控件已经被从药品**库检查器**并放置在中的窗口上**界面编辑器**:

[![](standard-controls-images/edit03.png "从库中选择的拆分视图")](standard-controls-images/edit03.png#lightbox)

有关在接口生成器中创建用户界面的详细信息，请参阅我们[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)文档。

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>大小调整和定位

一旦控件已包括在用户界面中，使用**约束编辑器**以通过手动输入值设置其位置和大小以及控制如何控件自动定位和大小时的父窗口或视图调整大小时：

[![](standard-controls-images/edit04.png "设置约束")](standard-controls-images/edit04.png#lightbox)

使用**红色我-无线数据交换内容**外周围**Autoresizing**到框中_棍棒_控件与 (x，y) 给定位置。 例如: 

[![](standard-controls-images/edit05.png "编辑约束")](standard-controls-images/edit05.png#lightbox)

指定所选的控件 (在**层次结构视图** & **界面编辑器**) 将粘在窗口或视图的边距和右边位置中，因为它是调整大小或移动。 

如高度和宽度的编辑器控件属性的其他元素：

[![](standard-controls-images/edit06.png "设置高度")](standard-controls-images/edit06.png#lightbox)

你还可以使用约束使用控制元素的对齐方式**对齐编辑器**:

[![](standard-controls-images/edit07.png "对齐编辑器")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> 与 iOS 不同位置 (0，0) 是上限中 macOS (0，0) 的屏幕的左下角是左下角。 这是因为 macOS 将数学坐标系统使用向上和向右值中增加的数字值。 你需要考虑这一点，放置在用户界面上的 AppKit 控件时。

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>设置自定义类

有的时间，你使用 AppKit 控件将需要子类和现有控件并创建你自己的该类的自定义版本。 例如，定义自定义版本的源列表：

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }

        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

其中`[Register("SourceListView")]`指令公开`SourceListView`到 OBJECTIVE-C 使它的类可以在接口生成器中使用。 有关详细信息，请参阅[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`使用命令若要在线-高达你的 C# 类 OBJECTIVE-C 的对象和 UI 元素。

与就地上述代码中，你可以将 AppKit 控件，扩展时，基类型的拖动到设计图面上 (在示例中，**源列表**)，切换到**标识检查器**和设置**自定义类**公开给 Objective C 的名称 (示例`SourceListView`):

[![](standard-controls-images/edit10.png "在 Xcode 中设置的自定义类")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>公开插座和操作

它可以在 C# 代码中访问 AppKit 控件之前，需要为公开**Outlet**或和**操作**。 若要执行此选择给定的控件中任意一种**接口层次结构**或**界面编辑器**并切换到**助手视图**(确保你有`.h`窗口选择要编辑的):

[![](standard-controls-images/edit11.png "选择要编辑的正确文件")](standard-controls-images/edit11.png#lightbox)

控件拖动到让 AppKit 控件从`.h`文件以启动创建**Outlet**或**操作**:

[![](standard-controls-images/edit12.png "拖动以创建 Outlet 或操作的")](standard-controls-images/edit12.png#lightbox)

选择的暴露来创建并授予类型**Outlet**或**操作****名称**: 

[![](standard-controls-images/edit13.png "配置电源插座或操作")](standard-controls-images/edit13.png#lightbox)


有关详细信息使用**Outlet**和**操作**，请参阅[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)一部分我们[Xcode 和接口简介生成器](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)文档。

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>与 Xcode 同步更改

当你切换回 Visual Studio for Mac 从 Xcode 时，则在 Xcode 中所做的任何更改将自动同步与 Xamarin.Mac 项目中。

如果你选择`SplitViewController.designer.cs`中**解决方案资源管理器**你将能够看到如何你**Outlet**和**操作**具有在 C# 代码中我们有线：

[![](standard-controls-images/sync01.png "使用 Xcode 同步更改")](standard-controls-images/sync01.png#lightbox)

请注意如何中的定义`SplitViewController.designer.cs`文件：

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

中的定义与对齐`MainWindow.h`在 Xcode 中的文件：

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

如你所见，适用于 Mac 的 Visual Studio 侦听更改`.h`文件，以及进行自动同步这些更改中各自`.designer.cs`文件以将它们公开给你的应用程序。 你可能注意`SplitViewController.designer.cs`是一个分部类，以便 Visual Studio for Mac 无需修改`SplitViewController.cs `这会覆盖我们具有对类进行了任何更改。

你通常将从不需要以打开`SplitViewController.designer.cs`你自己，它此处提供出于教育目的仅。

> [!IMPORTANT]
> 在大多数情况下，Visual Studio for Mac将自动查看Xcode中所做的任何更改并将其同步到Xamarin.Mac项目。 如果同步不自动进行，请切换回 Xcode，然后再次切换到 Visual Studio for Mac。 这通常会开始同步周期。

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>使用按钮

AppKit 提供几种类型的可在你的用户界面设计的按钮。 有关详细信息，请参阅[按钮](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/buttons01.png "下面举例说明了不同的按钮类型")](standard-controls-images/buttons01.png#lightbox)

如果已经通过公开了一个按钮**Outlet**，下面的代码将响应它已按下：

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

对于通过系统都已公开的按钮**操作**、`public partial`与在 Xcode 中选择的名称与自动为你创建方法。 若要响应**操作**，完成类中的分部方法，**操作**在中定义。 例如:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

对于具有状态的按钮 (如**上**和**关闭**)，可以检查状态，或将其设置与`State`依据属性`NSCellStateValue`枚举。 例如:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

其中`NSCellStateValue`可以是：

- **上**-按钮后，或 （如复选框选中） 选中控件。
- **关闭**-未推送按钮或不选中控件。
- **混合**-的混合**上**和**关闭**状态。

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>标记按钮任意默认并设置密钥的等效项

对于任何已添加到用户界面设计的按钮，你可以将标记为该按钮_默认_当用户按下时，将激活的按钮**返回/Enter**键盘上的密钥。 在 macOS，此按钮将默认情况下收到蓝色背景色。

要设置为默认按钮，它在 Xcode 的接口生成器中进行选择。 接下来，在**属性检查器**，选择**密钥等效**字段，然后按**返回/Enter**密钥：

[![](standard-controls-images/buttons03.png "编辑键等效项")](standard-controls-images/buttons03.png#lightbox)

同样，你可以分配任何可用来激活使用键盘而不鼠标按钮的按键顺序。 例如，通过按在上图中的命令 C 键。

当运行该应用程序和按钮的窗口是密钥和侧重，如果用户按下命令 C，该按钮的操作将被激活 (作为-如果用户已单击的按钮)。

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>使用复选框和单选按钮

AppKit 提供几种类型的复选框和单选按钮组可在你的用户界面设计的。 有关详细信息，请参阅[按钮](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/buttons02.png "下面举例说明可用的复选框类型")](standard-controls-images/buttons02.png#lightbox)


复选框和单选按钮 (通过公开**Outlet**) 具有状态 (如**上**和**关闭**)，可以检查状态，或将其设置与`State`依据属性`NSCellStateValue`枚举。 例如:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

其中`NSCellStateValue`可以是：

- **上**-按钮后，或 （如复选框选中） 选中控件。
- **关闭**-未推送按钮或不选中控件。
- **混合**-的混合**上**和**关闭**状态。

若要在单选按钮组中选择一个按钮，公开单选按钮以选择作为**Outlet**并设置其`State`属性。 例如：

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

若要获取的单选按钮以充当一个组并自动处理所选的状态的集合，创建一个新**操作**并附加到它的组中的每个按钮：

![](standard-controls-images/buttons04.png "创建新的操作")

接下来，为分配唯一`Tag`为在每个单选按钮**属性检查器**:

![](standard-controls-images/buttons05.png "编辑单选按钮标记")

保存所做的更改并返回到 Visual Studio for Mac，添加代码来处理**操作**所有单选按钮附加到：

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

你可以使用`Tag`属性以查看已选择哪个单选按钮。

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>使用菜单控件

AppKit 提供几种类型的可在你的用户界面设计的菜单控件。 有关详细信息，请参阅[菜单控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/menu01.png "示例菜单控件")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>提供菜单控件数据

可以设置供 macOS 菜单控件来填充下拉列表从的内部列表 （可以是预定义接口生成器中，或通过代码填充） 或通过提供你自己的自定义的外部数据源。

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>使用内部数据

除了在接口生成器中，菜单控件定义项 (如`NSComboBox`)，提供一组完整的方法，您可以添加、 编辑或从他们维护的内部列表中删除项：

- `Add` -将新项添加到列表的末尾。
- `GetItem` -返回给定索引处的项。
- `Insert` -将新项插入列表中位于给定位置。
- `IndexOf` -返回给定项的索引。
- `Remove` -从列表中移除给定的项目。
- `RemoveAll` -从列表中移除所有项。
- `RemoveAt` -删除给定索引处的项。
- `Count` -在列表中返回的项数。

> [!IMPORTANT]
> 如果您使用外部数据源 (`UsesDataSource = true`)，调用任何上述方法将引发异常。

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>使用外部数据源

而不是使用内置的内部数据可供你菜单控件的行，或者可以使用外部数据源并提供你自己的后备存储 （如 SQLite 数据库） 的项。

若要使用外部数据源，将创建菜单控件的数据源的实例 (`NSComboBoxDataSource`例如)，并重写了若干方法来提供必要的数据：

- `ItemCount` -在列表中返回的项数。
- `ObjectValueForItem` -返回给定索引的项的值。
- `IndexOfItem` -返回给定项值的索引。
- `CompletedString` -返回部分类型化的项值的第一个匹配项值。 如果已启用自动完成，仅调用此方法 (`Completes = true`)。

请参阅[数据库和组合框](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes)部分[使用数据库](~/mac/app-fundamentals/databases.md)更多详细信息的文档。

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>调整此列表中的外观

下面的方法是可用于调整菜单控件的外观：

- `HasVerticalScroller` -如果`true`，控件将显示垂直滚动条。 
- `VisibleItems` -调整打开该控件时显示的项的数目。 默认值是五 （5)。
- `IntercellSpacing` -通过提供调整给定项周围的空间量`NSSize`其中`Width`指定左边距和右边距和`Height`之前和之后的项指定的空间。
- `ItemHeight` -在列表中指定的每个项的高度。

下拉列表类型的`NSPopupButtons`，第一个菜单项控件提供标题。 例如： 

[![](standard-controls-images/menu02.png "示例菜单控件")](standard-controls-images/menu02.png#lightbox)

若要更改的标题，公开此项作为**Outlet**并使用类似于下面的代码：

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>操作所选的项目

以下方法和属性使您可以操作菜单控件的列表中的选定的项：

- `SelectItem` -选择给定索引处的项。
- `Select` -选择给定的项值。
- `DeselectItem` -取消选择给定索引处的项。
- `SelectedIndex` -返回当前选定项的索引。
- `SelectedValue` -返回当前选定项的值。

使用`ScrollItemAtIndexToTop`显示在列表顶部给定索引处的项和`ScrollItemAtIndexToVisible`滚动到列表中，直到给定索引处的项是否可见。

<a name="Responding to Events" />

### <a name="responding-to-events"></a>对事件作出响应

菜单控件提供要对用户交互作出响应的以下事件：

- `SelectionChanged` -称为时用户已从列表中选择一个值。
- `SelectionIsChanging` -之前调用新的用户选择项将成为活动的选定区域。
- `WillPopup` -之前调用显示项的下拉列表。
- `WillDismiss` -之前调用关闭项的下拉列表。

有关`NSComboBox`控件，它们包括所有作为相同的事件`NSTextField`，如`Changed`每当用户编辑组合框中的文本的值就会调用的事件。

（可选） 你可以作出响应接口生成器通过附加到的项选择中定义的内部的数据菜单项**操作**和类似如下的代码用于响应**操作**正在由用户触发：

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

有关使用菜单和菜单控件的详细信息，请参阅我们[菜单](~/mac/user-interface/menu.md)和[弹出按钮和下拉菜单列出](~/mac/user-interface/menu.md)文档。

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>使用所选内容控件

AppKit 提供几种类型的可在你的用户界面设计的所选内容控件。 有关详细信息，请参阅[选择控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/select01.png "示例选择控件")](standard-controls-images/select01.png#lightbox)

有两种方法来跟踪在所选内容控件通过公开其作为具有用户交互时**操作**。 例如:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

或者通过附加**委托**到`Activated`事件。 例如:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

若要设置或读取的选择控件的值，使用`IntValue`属性。 例如:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

Specialty 控件 （如也颜色和图像很好地） 具有为其值类型的特定属性。 例如：

```csharp
CollorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

`NSDatePicker`具有以下属性用于直接处理日期和时间：

- **DateValue** -形式的当前日期和时间值`NSDate`。
- **本地**-为用户的位置`NSLocal`。
- **TimeInterval** -形式的时间值`Double`。
- **时区**-为用户的时区`NSTimeZone`。

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>使用指示器控件

AppKit 提供几种类型的可在你的用户界面设计的指示器控件。 有关详细信息，请参阅[指示器控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/level01.png "示例指示器控件")](standard-controls-images/level01.png#lightbox)

有两种方法来跟踪在指示器控件具有用户交互，通过将其作为公开时**操作**或**Outlet**和附加**委托**到`Activated`事件。 例如:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

若要读取或设置指示符控件的值，使用`DoubleValue`属性。 例如:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

显示时，不确定和异步进度指示器应进行动画处理。 使用`StartAnimation`方法以在显示时启动动画。 例如:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

调用`StopAnimation`方法，将停止动画。

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>使用文本控件

AppKit 提供几种类型的可在你的用户界面设计的文本控件。 有关详细信息，请参阅[文本控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/text01.png "示例文本控件")](standard-controls-images/text01.png#lightbox)

文本字段 (`NSTextField`)，以下事件可用于跟踪用户交互：

- **更改**-激发的每当用户更改字段的值。 例如，在键入每个字符。
- **EditingBegan** -当用户选择该字段进行编辑时触发。
- **EditingEnded** -当用户按 Enter 键字段中的，或离开字段。

使用`StringValue`属性来读取或设置字段的值。 例如:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

对于显示或编辑数字值的字段，你可以使用`IntValue`属性。 例如:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

`NSTextView`提供完整的特色的文本编辑和显示与内置格式设置的区域。 如`NSTextField`，使用`StringValue`属性来读取或设置区域的值。

有关使用文本视图 Xamarin.Mac 应用中的复杂的示例的示例，请参阅[SourceWriter 示例应用程序](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 指南文档中的相关信息。

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>使用内容视图

AppKit 提供几种类型的内容可在你的用户界面设计的视图。 有关详细信息，请参阅[内容视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

[![](standard-controls-images/content01.png "示例内容视图")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Popover 是暂时性的 UI 元素，它提供与特定控件或屏幕区域直接相关的功能。 Popover 浮动窗口包含的控件或与其相关的区域的上方和其边框包括一个箭头来表示出现了从该点。

若要创建 popover，请执行以下操作：

1. 打开`.storyboard`文件的窗口中你想要通过双击它在添加到 popover**解决方案资源管理器**
2. 拖动**查看控制器**从**库检查器**到**界面编辑器**: 

    [![](standard-controls-images/content02.png "从库中选择的视图控制器")](standard-controls-images/content02.png#lightbox)
4. 定义大小和布局**自定义视图**: 

    [![](standard-controls-images/content04.png "编辑布局")](standard-controls-images/content04.png#lightbox)
5. 单击并将其拖到弹出从源**视图控制器**: 

    [![](standard-controls-images/content05.png "拖动以创建 segue")](standard-controls-images/content05.png#lightbox)
6. 选择**Popover**从弹出菜单： 

    [![](standard-controls-images/content06.png "设置 segue 类型")](standard-controls-images/content06.png#lightbox)
7. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

<a name="Tab_Views" />

### <a name="tab-views"></a>选项卡视图

选项卡视图包含与一组称为的视图结合使用 （如下所示类似于分段控件） 选项卡列表_窗格_。 当用户选择新选项卡时，将显示已向其附加了窗格。 每个窗格包含自己的控件集。

在使用 Xcode 的接口生成器中的选项卡视图，使用**属性检查器**设置选项卡的数目：

[![](standard-controls-images/content08.png "编辑选项卡的数目")](standard-controls-images/content08.png#lightbox)

选择在每个选项卡**接口层次结构**设置其**标题**和 UI 将元素添加到其**窗格**:

[![](standard-controls-images/content09.png "编辑在 Xcode 中的选项卡")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>数据绑定 AppKit 控件

通过使用 Xamarin.Mac 应用程序中的键值对的编码和数据绑定技术，你可以显著缩短你需要编写和维护填充和使用 UI 元素的代码量。 你还可以进一步分离你的备份数据的好处 (_数据模型_) 从你前面结束用户界面 (_模型-视图-控制器_)，从而导致易于维护更灵活的应用程序设计。

键值对的编码 (KVC) 是用于间接访问的对象的属性，请使用键 （特殊格式字符串） 来标识而不是通过实例变量访问这些属性或访问器方法的机制 (`get/set`)。 通过实现 Xamarin.Mac 应用程序中的键值对的编码符合访问器，你可以访问键 / 值观察 (KVO)、 数据绑定、 核心数据、 Cocoa 绑定和 scriptability 等其他 macOS 功能。

有关详细信息，请参阅[的简单数据绑定](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding)一部分我们[数据绑定和键 / 值编码](~/mac/app-fundamentals/databinding.md)文档。


<a name="Summary" />

## <a name="summary"></a>摘要

本文已了解使用如按钮、 标签、 文本字段、 复选框和分段控件 Xamarin.Mac 应用程序中的标准 AppKit 控件的详细的信息。 它介绍将它们添加到 Xcode 的接口生成器中用户界面设计、 将其公开到通过插座和操作的代码以及使用 C# 代码中的 AppKit 控件。

## <a name="related-links"></a>相关链接

- [MacControls （示例）](https://developer.xamarin.com/samples/mac/MacControls/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [有关控件和视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
