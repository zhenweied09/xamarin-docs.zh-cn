---
title: 在 Xamarin.Mac 工具栏
description: 本指南介绍了使用 Xamarin.Mac 应用程序中的工具栏。 它涵盖创建和维护工具栏中 Xcode 和接口生成器中，将其公开到代码中，并以编程方式使用它们。
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 11c01a2b2378ddc519185996deb78ba5bcbd2d1a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792883"
---
# <a name="toolbars-in-xamarinmac"></a>在 Xamarin.Mac 工具栏

_本指南介绍了使用 Xamarin.Mac 应用程序中的工具栏。它涵盖创建和维护工具栏中 Xcode 和接口生成器中，将其公开到代码中，并以编程方式使用它们。_

Xamarin.Mac 使用适用于 Mac 的 Visual Studio 的开发人员有权访问可供 macOS 开发人员使用 Xcode，包括工具栏控件相同的 UI 控件。 因为 Xamarin.Mac 与 Xcode 直接集成，则可以使用 Xcode 的接口生成器来创建和维护工具栏项。 此外可以在 C# 中创建这些工具栏项。

在 macOS 工具栏添加到一个窗口顶部的部分，并提供方便您访问其功能相关的命令。 工具栏可以隐藏、 显示，或通过应用程序的用户自定义和出示以多种方式工具栏项。

本文介绍如何使用工具栏和工具栏项 Xamarin.Mac 应用程序中的基础知识。 

继续之前，请通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章-专门[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分-因为它介绍主要概念和将本指南中使用的技术。

此外看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档。 它还说明了`Register`和`Export`用于连接到 OBJECTIVE-C 的类的 C# 类的属性。

## <a name="introduction-to-toolbars"></a>工具栏简介

在 macOS 应用程序中的任何窗口可以包含一个工具栏：

![带有一个工具栏的示例窗口](toolbar-images/info01.png "带有工具栏的示例窗口")

工具栏可以方便地快速访问重要的应用程序的用户或常用的功能。 例如，文档编辑应用程序可能会提供用于设置文本颜色、 字体，更改或打印当前文档的工具栏项。

工具栏可以通过三种方式显示项：

1. **图标和文本** 

     ![图标和文本与工具栏](toolbar-images/info02.png "图标和文本与工具栏")

2. **仅图标** 

     ![仅图标工具栏](toolbar-images/info03.png "仅图标工具栏")

3. **仅文本** 

     ![纯文本工具栏](toolbar-images/info04.png "纯文本的工具栏")

通过右键单击工具栏上，从上下文菜单中选择的显示模式这些模式之间切换：

![工具栏的上下文菜单](toolbar-images/info05.png "工具栏的上下文菜单")

使用相同的菜单显示工具栏在较小的大小：

![一个带有小图标的工具栏](toolbar-images/info06.png "一个带有小图标的工具栏")

菜单还允许自定义工具栏：

![用于自定义工具栏对话框](toolbar-images/info07.png "用于自定义工具栏对话框")

当设置 Xcode 的接口生成器中的工具栏，开发人员可以提供不是其默认配置的一部分的额外工具栏项。 应用程序的用户然后可以自定义工具栏上，添加和删除根据需要这些预定义的项。 当然，工具栏可以重置为其默认配置。

工具栏自动连接到**视图**菜单上，可用于隐藏它，显示它，并且其进行自定义：

![在视图菜单中的工具栏相关项](toolbar-images/info08.png "视图菜单中的工具栏相关项")

请参阅[内置菜单功能](~/mac/user-interface/menu.md)更多详细信息的文档。

此外，如果接口生成器中正确配置工具栏，则应用程序将自动在保持原样工具栏自定义项的应用程序的多个启动。

本指南的下一步各节介绍如何创建和维护使用 Xcode 的接口生成器的工具栏以及如何在代码中使用它们。

## <a name="setting-a-custom-main-window-controller"></a>设置自定义的主窗口控制器

若要公开对通过插座和操作、 Xamarin.Mac 应用的 C# 代码的 UI 元素必须使用自定义窗口控制器：

1. 在 Xcode 的接口生成器中打开应用程序的情节提要。
2. 选择设计图面上的窗口控制器。
3. 切换到**标识检查器**并输入"WindowController"作为**类名**: 

    [![设置窗口控制器的自定义类名称](toolbar-images/windowcontroller01.png "设置窗口控制器的自定义类名称")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. 保存所做的更改并返回到 Visual Studio for Mac 同步。
5. 一个 **WindowController.cs** 文件将被添加到Visual Studio for Mac中 **解决方案板** 中的项目中： 

    ![在解决方案本选择 WindowController.cs](toolbar-images/windowcontroller02.png "解决方案板中选择 WindowController.cs")

6. 重新打开在 Xcode 的接口生成器情节提要。
7. **WindowController.h**文件将可供使用： 

    [![WindowController.h 文件](toolbar-images/windowcontroller03.png "WindowController.h 文件")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>创建和维护在 Xcode 中的工具栏

创建和使用 Xcode 的接口生成器维护工具栏。 若要将工具栏添加到应用程序，编辑应用程序的主情节提要 (在这种情况下**Main.storyboard**) 通过在中双击**解决方案 Pad**:

![在解决方案板中打开 Main.storyboard](toolbar-images/edit01.png "解决方案板中打开 Main.storyboard")

在**库检查器**，进入"工具"**搜索框**以使其更轻松地查看所有可用的工具栏项：

![库检查器筛选，以显示工具栏项](toolbar-images/edit02.png "库检查器中，筛选，以显示工具栏项")

将一个工具栏拖到中的窗口**界面编辑器**。 通过选择工具栏，通过设置属性中配置其行为**属性检查器**:

![属性检查器工具栏](toolbar-images/edit04.png "属性检查器工具栏")

以下属性可用：

1. **显示**-控制工具栏是否显示图标和 / 或文本，
2. **在启动可见**-如果选择，该工具栏是默认情况下可见。
3. **可自定义**-如果选中，用户可以编辑和自定义工具栏。
4. **分隔符**-如果选择，一条细横线分离开来工具栏窗口的内容。
5. **大小**-设置工具栏的大小
6. **自动保存**-如果选择，应用程序将在保持原样用户的工具栏配置更改应用程序将启动。

选择**自动保存**选项，然后将所有其他属性保留为其默认设置。 

打开中的工具栏后**接口层次结构**，通过选择工具栏上的项目打开的自定义对话框：

![自定义工具栏](toolbar-images/edit05.png "自定义工具栏")

设置工具栏上，设计应用程序，默认的工具栏中包含的项的属性，以便用户能够选择自定义工具栏时的额外工具栏项，请使用此对话框。 若要添加到工具栏项，将它们从拖动**库检查器**:

![库检查器](toolbar-images/edit06.png "库检查器")

可以添加以下工具栏项：

- **图像工具栏项**-使用自定义映像以图标形式是工具栏项。
- **灵活的空间工具栏项**-用于对齐后续工具栏项的灵活空间。 例如，一个或多个工具栏项灵活空间工具栏项后, 跟另一个工具栏项将固定的右侧的工具栏上的最后一项。
- **空间工具栏项**-固定的工具栏上的项之间的空间
- **分隔符工具栏项**的两个或多个工具栏项，为分组之间的可见分隔符
- **自定义工具栏项**-允许用户自定义工具栏
- **打印工具栏项**-允许用户打印打开的文档
- **显示颜色工具栏项**-显示标准系统颜色选取器： 

     ![系统颜色选取器](toolbar-images/edit07.png "系统颜色选取器")

- **显示字体工具栏项**-显示标准系统字体对话框： 

     ![字体选择器](toolbar-images/edit08.png "字体选择器")

> [!IMPORTANT]
> 将看到更高版本，如搜索字段、 分段的控件和水平滑块的很多标准 Cocoa UI 控件还添加到工具栏。

### <a name="adding-an-item-to-a-toolbar"></a>将项添加到工具栏

若要将项添加到工具栏，选择中的工具栏**接口层次结构**，单击一个项，从而导致显示自定义对话框。 接下来，将从的新项的拖动**库检查器**到**允许工具栏项**区域：

![工具栏自定义对话框中的允许工具栏项](toolbar-images/add01.png "工具栏自定义对话框中的允许工具栏项")

若要确保新项是默认的工具栏的一部分，将其拖到**默认工具栏项**区域： 

![通过拖动的重新排序是工具栏项](toolbar-images/add02.png "通过拖动的重新排序是工具栏项")

要重新排序默认工具栏项，将左或向右拖动它们。

接下来，使用**属性检查器**设置项的默认属性：

![自定义使用属性检查器是工具栏项](toolbar-images/add03.png "自定义使用属性检查器是工具栏项")

以下属性可用：

- **映像名称**-要用作项作为图标图像
- **标签**-要显示的工具栏中项文本
- **调色板标签**-若要显示有关中的项的文本**允许工具栏项**区域
- **标记**-帮助识别代码中的项的可选、 唯一标识符。
- **标识符**-定义了工具栏项类型。 自定义的值可以用于在代码中选择是工具栏项。
- **可选择**-如果选中，项将充当开/关按钮。

> [!IMPORTANT]
> 将项添加到**允许工具栏项**区域，但不是提供用户的自定义选项默认工具栏。 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>将其他 UI 控件添加到工具栏

多个 Cocoa UI 元素，如搜索字段和分段的控件也可以添加到工具栏。

若要这样做时，打开中的工具栏**接口层次结构**和选择要打开自定义对话框的工具栏项。 拖动**搜索字段**从**库检查器**到**允许工具栏项**区域：

![使用工具栏自定义对话框](toolbar-images/add05.png "使用工具栏自定义对话框")

从此处，使用接口生成器配置搜索字段中并将其公开给代码中通过一个操作或 outlet。

## <a name="built-in-toolbar-item-support"></a>内置工具栏项支持

默认情况下，几个 Cocoa UI 元素与标准工具栏项交互。 例如，拖动**文本视图**到应用程序的窗口，并将其以填充内容的区域：

[![将文本视图添加到应用程序](toolbar-images/edit09.png "将文本视图添加到应用程序")](toolbar-images/edit09-large.png#lightbox)

保存文档时，返回到 Visual Studio for Mac 可与 Xcode 同步，运行该应用程序、 输入某些文本，选中它，然后单击**颜色**工具栏项。 请注意，文本视图自动配合颜色选取器：

![内置工具栏功能，带有文本视图和颜色选取器](toolbar-images/edit10.png "内置工具栏功能，带有文本视图和颜色选取器")

## <a name="using-images-with-toolbar-items"></a>使用图像的工具栏项

使用**图像工具栏项**，任何位图图像添加到**资源**文件夹 (和给定的生成操作**捆绑资源**) 可以作为图标在工具栏上显示：

1. Visual Studio 中的 Mac 上，用于在**解决方案 Pad**，右键单击**资源**文件夹，然后选择**添加** > **添加文件**.
2. 从**添加文件**对话框框中，导航到所需图像、 选择它们并单击**打开**按钮： 

    [![选择要添加的映像](toolbar-images/edit11.png "选择要添加的映像")](toolbar-images/edit11-large.png#lightbox)

3. 选择**复制**，检查**的所有选定文件使用相同的操作**，然后单击**确定**:

    ![选择添加图像的复制操作](toolbar-images/edit12.png "选择添加图像的复制操作")

4. 在**解决方案 Pad**，双击**MainWindow.xib**在 Xcode 中打开它。

5. 选择中的工具栏**接口层次结构**，单击一个其项目以打开自定义对话框。

6. 拖动**图像工具栏项**从**库检查器**到工具栏的**允许工具栏项**区域： 

    ![图像工具栏项添加到允许工具栏项区域](toolbar-images/edit14.png "图像工具栏项添加到允许工具栏项区域")

7. 在**属性检查器**，选择适用于 Mac 刚添加 Visual Studio 中的映像： 

    ![设置是工具栏项的自定义图像](toolbar-images/edit15.png "设置是工具栏项的自定义映像")

8. 设置**标签**到"回收站"和**调色板标签**"擦除文档": 

    ![设置工具栏项标签和调色板标签](toolbar-images/edit16.png "标签和调色板标签设置工具栏项")

9. 拖动**分隔符工具栏项**从**库检查器**到工具栏的**允许工具栏项**区域： 

    [![分隔符工具栏项添加到允许工具栏项区域](toolbar-images/edit17.png "分隔符工具栏项添加到允许工具栏项区域")](toolbar-images/edit17-large.png#lightbox)

10. 拖动分隔符项和到"回收站"项**默认工具栏项**区域和工具栏的顺序中的项的组从左到右，如下所示 （颜色、 字体、 分隔符、 垃圾桶、 灵活的空间、 打印）： 

    ![默认工具栏项](toolbar-images/edit18.png "默认工具栏项")

11. 保存更改并返回到 Visual Studio for Mac 与 Xcode 同步。

运行应用程序来验证新的工具栏显示默认情况下：

![一种带有自定义的默认项工具栏](toolbar-images/edit19.png "与自定义的默认项工具栏")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>公开与插座和操作的工具栏项

若要访问工具栏或在代码中的工具栏项，必须将它附加到电源插座或某项操作：

1. 在**解决方案 Pad**，双击**Main.storyboard**在 Xcode 中打开它。
2. 确保自定义类"WindowController"已分配到中的主窗口控制器**标识检查器**:

    [![使用标识检查器窗口控制器设置自定义类](toolbar-images/edit20a.png "使用标识检查器窗口控制器设置自定义类")](toolbar-images/edit20a-large.png#lightbox)

3. 接下来，工具栏中选择项**接口层次结构**: 

    ![选择接口层次结构中的工具栏项](toolbar-images/edit20.png "接口层次结构中选择工具栏项")  

4. 打开**助手视图**，选择**WindowController.h**文件，并从工具栏项到控件拖动**WindowController.h**文件。
5. 设置**连接**键入到**操作**，输入"trashDocument"**名称**，然后单击**连接**按钮： 

    [![配置是工具栏项的一项操作](toolbar-images/edit23.png "配置是工具栏项的一项操作")](toolbar-images/edit23-large.png#lightbox)

6. 公开**文本视图**作为中调用"documentEditor"上的电源插座**ViewController.h**文件： 

    [![配置为文本视图上的电源插座](toolbar-images/edit24.png "配置为文本视图上的电源插座")](toolbar-images/edit24-large.png#lightbox)

7. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

在适用于 Mac 的 Visual Studio 中编辑**ViewController.cs**文件并添加以下代码：

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

接下来，编辑**WindowController.cs**文件并将下面的代码添加到底部`WindowController`类：

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

当运行该应用程序，**垃圾桶**工具栏项将处于活动状态：

![一种带有 active 垃圾桶项工具栏](toolbar-images/edit25.png "与活动垃圾桶项工具栏")

请注意，**垃圾桶**工具栏项现可用于删除文本。

## <a name="disabling-toolbar-items"></a>禁用工具栏项

若要禁用工具栏上的项，创建自定义`NSToolbarItem`类并重写`Validate`方法。 然后，在接口生成器中，为你想要启用/禁用的项中指定的自定义的类型。

若要创建自定义`NSToolbarItem`类，请右键单击该项目并选择**添加** > **新文件...**.选择**常规** > **空类**，输入"ActivatableItem"**名称**，然后单击**新建**按钮： 

![在 Visual Studio 中添加空类，适用于 Mac](toolbar-images/custom01.png "适用于 Mac 在 Visual Studio 中添加空类")

接下来，编辑**ActivatableItem.cs**文件以使内容如下：

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

双击**Main.storyboard**在 Xcode 中打开它。 选择**垃圾桶**工具栏项上述步骤中创建并将其类更改为"ActivatableItem"，在**标识检查器**:

![设置是工具栏项的自定义类](toolbar-images/custom02.png "设置是工具栏项的自定义类")

创建调用 outlet`trashItem`为**垃圾桶**工具栏项。 保存更改并返回到 Visual Studio for Mac 与 Xcode 同步。 最后，打开**了构成**和更新`AwakeFromNib`方法来读取，如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

运行应用程序，并请注意，**垃圾桶**项现在禁用工具栏中：

![与非活动垃圾桶项工具栏](toolbar-images/custom03.png "与处于非活动状态的垃圾桶项工具栏")

## <a name="summary"></a>总结

本文已了解使用工具栏和 Xamarin.Mac 应用程序中的工具栏项的详细的信息。 它介绍如何创建和维护在 Xcode 的接口生成器中的工具栏、 如何某些 UI 控件自动与工具栏项工作、 如何在 C# 代码中，工具栏使用和如何启用和禁用工具栏项。


## <a name="related-links"></a>相关链接

- [MacToolbar （示例）](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [工具栏的人机接口指南](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [工具栏简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
