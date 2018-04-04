---
title: 图像
description: 本文介绍如何使用图像和 Xamarin.Mac 应用程序中的图标。 它介绍创建和维护需要创建你的应用程序图标并使用 C# 代码和 Xcode 的接口生成器中的映像的映像。
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: dc33dc78c09c0b5b7cb7533afdd2f95b8ebd9c4e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="images"></a>图像

_本文介绍如何使用图像和 Xamarin.Mac 应用程序中的图标。它介绍创建和维护需要创建你的应用程序图标并使用 C# 代码和 Xcode 的接口生成器中的映像的映像。_


## <a name="overview"></a>概述

在使用 C# 和.NET Xamarin.Mac 应用程序中，你有权访问相同的图像和图标工具的开发人员*Objective C*和*Xcode*未。

有多种资产在 macOS （以前称为 Mac OS X） 应用程序内使用该映像。 从只需显示为您提供图标，将其分配给工具栏或源列表项，如 UI 控件，你的应用程序 UI 的一部分图像 Xamarin.Mac 可以轻松地将极佳图片添加到 macOS 应用程序，通过以下方式: 

- **UI 元素**-作为背景或图像视图中的应用程序的一部分，可以显示映像 (`NSImageView`)。
- **按钮**-映像可以显示在按钮 (`NSButton`)。
- **图像单元格**-与基于的表控件的一部分 (`NSTableView`或`NSOutlineView`)，可在图像单元映像 (`NSImageCell`)。
- **工具栏项**-可以将图像添加到工具栏 (`NSToolbar`) 作为图像工具栏项 (`NSToolbarItem`)。
- **源列表图标**-与源列表的一部分 (特殊格式`NSOutlineView`)。
- **应用程序图标**的一系列图像可以组合到一起`.icns`设置并使用为应用程序的图标。 请参阅我们[应用程序图标](~/mac/deploy-test/app-icon.md)文档以了解更多信息。

此外，macOS 提供一组可以在你的应用程序使用的预定义映像。

[![示例运行的应用程序](image-images/intro01.png "示例运行的应用程序")](image-images/intro01-large.png#lightbox)

在本文中，我们将介绍在 Xamarin.Mac 应用程序中使用图像和图标的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。


## <a name="adding-images-to-a-xamarinmac-project"></a>向 Xamarin.Mac 项目中添加图像

当在 Xamarin.Mac 应用程序中添加用于图像，有几个位置和开发人员可以包括到项目的源的图像文件的方式：

- **[已弃用] 的主要项目树**-映像可以直接添加到项目树。 当调用映像存储在代码中的主项目树中，将指定没有文件夹位置。 例如：`NSImage image = NSImage.ImageNamed("tags.png");`。 
- **[已弃用] 的资源文件夹**-这两个特殊**资源**映像 （或任何其他图像或文件开发人员将成为应用程序的一部分的任何文件的捆绑如图标、 启动屏幕或常规文件夹是想要添加）。 调用存储在映像时**资源**代码中的文件夹，就像映像存储在主项目树中，指定没有文件夹位置。 例如：`NSImage.ImageNamed("tags.png")`。
- **自定义文件夹或子文件夹 [不推荐使用]** -开发人员可以将自定义文件夹添加到项目源树和存储存在的映像。 若要进一步的帮助组织项目的子文件夹中，可以嵌套其中添加文件的位置。 例如，如果开发人员添加`Card`与项目的子文件夹的文件夹`Hearts`到该文件夹，然后将映像存储**Jack.png**中`Hearts`文件夹，`NSImage.ImageNamed("Card/Hearts/Jack.png")`将加载在映像运行时。
- **资产目录映像集 [首选]** -在 OS X El Capitan 中，添加**资产目录映像集**包含所有版本或图像的表示形式，支持各种设备并缩放因素需要你应用程序。 而不是依靠图像资产文件名 (**@1x**， **@2x**)。

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>将映像添加到资产目录映像设置

如前所述，**资产目录映像集**包含所有版本或图像的所需支持各种设备和缩放你的应用程序的因素的表示形式。 而不是依靠图像资产文件名 （请参阅分辨率独立图像和图像命名法上面）**映像集**资产编辑器用于指定哪个映像属于哪些设备和/或解决方法。

1. 在**解决方案 Pad**，双击**Assets.xcassets**文件以打开进行编辑： 

    ![选择 Assets.xcassets](image-images/imageset01.png "选择 Assets.xcassets")
2. 右键单击**资产列表**和选择**新映像设置**: 

    [![添加新的映像集](image-images/imageset02.png "添加一新映像组")](image-images/imageset02-large.png#lightbox)
3. 选择新的映像集，并将显示的编辑器： 

    [![选择新的映像集](image-images/imageset03.png "选择新的映像集")](image-images/imageset03-large.png#lightbox)
4. 此处我们可为每个不同设备和所需的分辨率拖动图像中。 
5. 双击新映像集**名称**中**资产列表**对其进行编辑： 

    [![编辑图像设置名称](image-images/imageset04.png "编辑图像设置名称")](image-images/imageset04-large.png#lightbox)
    
一种特殊**向量**类，如已添加到**映像集**这使我们能够包括_PDF_格式化中改为包括在各个位图文件 casset 矢量图像不同的解决方法。 使用此方法，你提供的单个向量文件**@1x** （格式为向量 PDF 文件） 的解析和**@2x**和**@3x**将在编译时生成文件的版本，并将其包含在应用程序的捆绑包。

[![映像设置编辑器界面](image-images/imageset05.png "映像设置编辑器接口")](image-images/imageset05-large.png#lightbox)

例如，如果你包括`MonkeyIcon.pdf`文件作为资产目录分辨率高达 150px x 150px，以下资产将在编译时包含在最终应用程序捆绑的位图的矢量：

1. **MonkeyIcon@1x.png** -150px x 150px 解析。
2. **MonkeyIcon@2x.png** -300px x 300px 解析。
3. **MonkeyIcon@3x.png** -450px x 450px 解析。

以下应考虑到在资产目录中使用 PDF 矢量图像时：

- 这不是完整向量支持，因为在编译时和在最终应用程序中提供的位图 PDF 将光栅位图。
- 设置资产目录中后，你无法调整图像的大小。 如果你尝试调整图像 （不管是在代码或通过使用自动布局和大小类） 的大小的图像将会扭曲就像任何其他位图一样。

使用时**映像设置**在 Xcode 的接口生成器中，可以只需选择集的名称中的下拉列表从**属性检查器**: * *

![在 Xcode 的接口生成器中选择映像设置](image-images/imageset06.png "在 Xcode 的接口生成器中选择映像设置")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>添加新的资产集合

当处理资产目录中的映像可能是的时候你想要创建新的集合，而不是添加到你的映像的所有**Assets.xcassets**集合。 例如，在设计上按需资源时。

若要将新的资产目录添加到你的项目：

1. 右键单击该项目中**解决方案 Pad**和选择**添加** > **新文件...**
2. 选择**Mac** > **资产目录**，输入**名称**集合和单击**新建**按钮： 

    ![添加新的资产目录](image-images/asset01.png "添加新的资产目录")

从此处你可以使用集合默认值的方式相同**Assets.xcassets**自动包括在项目中的集合。


### <a name="adding-images-to-resources"></a>将映像添加到资源

> [!IMPORTANT]
> 使用在 macOS 应用程序中的映像的此方法已被否决通过 Apple。 应使用[资产目录映像集](#asset-catalogs)manager 到您的应用程序改为映像。

它可以 Xamarin.Mac 应用程序 （不管是在 C# 代码或从接口生成器） 中使用图像文件之前需要包含在项目的**资源**文件夹作为**捆绑资源**。 若要添加到项目文件，请执行以下操作：

1. 右键单击**资源**在项目中的文件夹**解决方案 Pad**和选择**添加** > **添加文件...**: 

    ![添加文件](image-images/add01.png "添加文件")
2. 从**添加文件**对话框中，选择映像，将文件添加到项目中，选择`BundleResource`为**替代生成操作**单击**打开**按钮：

    [![选择要添加的文件](image-images/add02.png "选择要添加的文件")](image-images/add02-large.png#lightbox)
3. 如果文件尚不在**资源**文件夹中，将要求你如果你想要**复制**，**移动**或**链接**文件。 选取其每个套件你的需求，通常，将**复制**:

    ![选择添加操作](image-images/add04.png "选择添加操作")
4. 将包括在项目中的新文件，并将其用于读取： 

    ![新图像文件添加到解决方案 Pad](image-images/add03.png "新图像文件添加到解决方案填充")
5. 为所需的任何图像文件重复此过程。

你可以使用任何 png、 jpg、 或 pdf 文件作为源映像 Xamarin.Mac 应用程序中。 在下一步的部分中，我们将考察添加我们的图像的高分辨率版本和图标，以支持 Retina 基于 Mac。

> [!IMPORTANT]
> 如果要添加到映像**资源**文件夹中，你可以将**替代生成操作**设置为**默认**。 默认值生成的此文件夹的操作是`BundleResource`。

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>提供所有应用程序图形资源的高分辨率的版本

任何添加到 Xamarin.Mac 应用程序 （图标、 自定义控件、 自定义光标、 自定义图片等） 的图形资产需要具有除了其标准高分辨率版本的高分辨率版本。 这是必需的以便你的应用程序时的外观达到最佳效果 Retina 显示屏上的运行配备 Mac 计算机。


### <a name="adopt-the-2x-naming-convention"></a>采用@2x命名约定

> [!IMPORTANT]
> 使用在 macOS 应用程序中的映像的此方法已被否决通过 Apple。 应使用[资产目录映像集](#asset-catalogs)manager 到您的应用程序改为映像。

在创建映像的标准和高分辨率版本时，它们包含在 Xamarin.Mac 项目中时请遵循此映像对此命名约定：

- **标准解析**  - **ImageName.filename 扩展**(示例： **tags.png**)
- **高分辨率**  -  **ImageName@2x.filename-extension** (示例： **tags@2x.png**)

在添加到项目，它们将显示，如下所示：

![在解决方案本图像文件](image-images/add03.png "解决方案板中的图像文件")

当映像分配到接口生成器中的 UI 元素时你只需将选取中的文件_ImageName_**。**_文件扩展名_格式 (示例： **tags.png**)。 相同的 C# 代码中使用的映像，将选取中的文件_ImageName_**。**_文件扩展名_格式。

你 Xamarin.Mac 应用程序在 Mac 上，运行时_ImageName_**。**_文件扩展名_格式的映像将使用标准的分辨率显示**ImageName@2x.filename-extension**映像将自动选取 Retina 显示基本 Mac。


## <a name="using-images-in-interface-builder"></a>在接口生成器中使用图像

你已添加到任何图像资源**资源**文件夹中你 Xamarin.Mac 项目，然后将生成操作设置为**BundleResource**自动将显示在接口生成器中，并且可以是（如果它处理图像），请选择为 UI 元素的一部分。

在接口生成器中挑选一个映像，请执行以下操作：

1. 将映像添加到**资源**文件夹**生成操作**的`BundleResource`: 

     ![在解决方案本图像资源](image-images/ib00.png "解决方案板中的图像资源")
2. 双击**Main.storyboard**文件以打开它以在接口生成器中编辑： 

     [![编辑主情节提要](image-images/ib01.png "编辑主情节提要")](image-images/ib01-large.png#lightbox)
3. 拖动 UI 元素采用到设计图面上的映像 (例如，**图像工具栏项**): 

     ![编辑工具栏项](image-images/ib02.png "编辑工具栏项")
4. 选择的映像添加到**资源**文件夹中的**映像名称**下拉列表中： 

     [![选择是工具栏项的图像](image-images/ib03.png "选择是工具栏项的图像")](image-images/ib03-large.png#lightbox)
5. 在设计图面中，将显示所选的映像： 

     ![在工具栏编辑器中所显示的图像](image-images/ib04.png "正在工具栏编辑器中显示的图像")
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

上面的步骤适用于任何允许其映像属性中设置的 UI 元素**属性检查器**。 同样，如果你已包含**@2x**的图像文件，它将自动使用版本上 Retina 显示屏基于 Mac。

> [!IMPORTANT]
> 如果图像不可用于**映像名称**下拉列表中，关闭.storyboard 项目在 Xcode 中的，并重新打开它从 Visual Studio for mac。 如果映像仍不可用，确保其**生成操作**是`BundleResource`和图像添加到**资源**文件夹。

## <a name="using-images-in-c-code"></a>在 C# 代码中使用图像

映像时加载到内存 Xamarin.Mac 应用程序中使用 C# 代码的映像，请将存储在`NSImage`对象。 如果图像文件已包括在 Xamarin.Mac 应用程序捆绑包 （包含在资源），使用下面的代码加载图像：

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

上述代码使用静态`ImageNamed("...")`方法`NSImage`类来加载到内存中从给定的图像**资源**文件夹中，如果找不到映像，`null`将返回。 如果有包含喜欢在接口生成器中，指定图像**@2x**的图像文件，它将自动使用版本上 Retina 显示屏基于 Mac。

若要加载在应用程序的捆绑 （从 Mac 文件系统） 之外的图像，请使用下面的代码：

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>处理模板映像

根据您 macOS 的应用程序的设计，有时可能何时需要自定义图标或在用户界面，以匹配 （例如，根据用户首选项） 的配色方案中的更改的图像。

若要实现此效果，切换_呈现模式_的图像资产到**模板映像**:

[![设置它的模板图像](image-images/templateimage01.png "设置它的模板图像")](image-images/templateimage01-large.png#lightbox)

从 Xcode 的接口生成器中，将分配到 UI 控件的图像资产：

![在 Xcode 的接口生成器中选择映像](image-images/templateimage02.png "在 Xcode 的接口生成器中选择映像")

或 （可选） 在代码中设置图像源：

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

将下面的公共函数添加到你的视图控制器：

```csharp
public NSImage ImageTintedWithColor (NSImage image, NSColor tint)
{
    var tintedImage = image.Copy () as NSImage;
    var frame = new CGRect (0, 0, image.Size.Width, image.Size.Height);

    // Apply tint
    tintedImage.LockFocus ();
    tint.Set ();
    NSGraphics.RectFill (frame, NSCompositingOperation.SourceAtop);
    tintedImage.UnlockFocus ();
    tintedImage.Template = false;

    // Return tinted image
    return tintedImage;
}
```

最后，若要色调淡模板映像，请调用此函数针对要为着色的图像：

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>表视图中使用图像

若要包含图像，作为一部分中的单元格`NSTableView`，你将需要更改表视图返回数据的方式`NSTableViewDelegate's``GetViewForItem`方法来使用`NSTableCellView`而不是典型`NSTextField`。 例如：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

有几行的兴趣要点。 首先，我们想要包含图像的列，我们创建一个新`NSImageView`的所需的大小和位置，我们还创建一个新`NSTextField`并将其默认位置，基于我们是否使用映像：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我们需要将新的图像视图和文本字段包含在父`NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最后，我们需要告诉文本字段，它可以收缩并随表视图单元格增长：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

示例输出：

[![在应用程序显示图像的示例](image-images/tables01.png "举例说明在应用程序显示图像")](image-images/tables01-large.png#lightbox)

有关使用表视图的详细信息，请参阅我们[表视图](~/mac/user-interface/table-view.md)文档。

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>大纲视图中使用图像

若要包含图像，作为一部分中的单元格`NSOutlineView`，你将需要更改该大纲视图返回数据的方式`NSTableViewDelegate's``GetView`方法来使用`NSTableCellView`而不是典型`NSTextField`。 例如：

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

有几行的兴趣要点。 首先，我们想要包含图像的列，我们创建一个新`NSImageView`的所需的大小和位置，我们还创建一个新`NSTextField`并将其默认位置，基于我们是否使用映像：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我们需要将新的图像视图和文本字段包含在父`NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最后，我们需要告诉文本字段，它可以收缩并随表视图单元格增长：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

示例输出：

[![大纲视图中所显示的图像的示例](image-images/outline01.png "的大纲视图中所显示的图像示例")](image-images/outline01-large.png#lightbox)

有关使用大纲视图的详细信息，请参阅我们[大纲视图](~/mac/user-interface/outline-view.md)文档。


## <a name="summary"></a>总结

本文已详细的介绍了的 Xamarin.Mac 应用程序中使用图像和图标。 我们看到不同的类型，并使用的图像、 如何在 Xcode 的接口生成器中使用的图像和图标和如何在 C# 代码中使用的图像和图标。



## <a name="related-links"></a>相关链接

- [MacImages（示例）](https://developer.xamarin.com/samples/mac/MacImages/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [表视图](~/mac/user-interface/table-view.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [macOS X 人机接口指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [有关适用于 OS X 的高分辨率](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
