---
title: "复制和粘贴"
description: "本文介绍如何使用粘贴板提供复制和粘贴在 Xamarin.Mac 应用程序。 它演示如何使用可以在多个应用以及如何支持给定应用内的自定义数据之间共享的标准数据类型。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 115f3340c5678c0ead06cf773e193fbdc4ba3d07
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="copy-and-paste"></a>复制和粘贴

_本文介绍如何使用粘贴板提供复制和粘贴在 Xamarin.Mac 应用程序。它演示如何使用可以在多个应用以及如何支持给定应用内的自定义数据之间共享的标准数据类型。_

## <a name="overview"></a>概述

在使用 C# 和.NET Xamarin.Mac 应用程序中，你有权访问的相同粘贴板 （复制和粘贴） 支持开发人员在 Objective C 中工作有。

这篇文章中我们将介绍使用粘贴板 Xamarin.Mac 应用中的两种主要方法：

1. **标准数据类型**-由于粘贴板操作通常会执行两个不相关的应用之间，既不应用已知道的其他支持的数据类型。 若要最大化共享的可能性，请粘贴板可容纳多个表示形式 （使用一组标准的常见数据类型） 的给定项，这允许使用应用程序选取最适合其需求的版本。
2. **自定义数据**-若要支持的复制和粘贴你 Xamarin.Mac 可以定义将由粘贴板处理的自定义数据类型中的复杂数据。 例如，向量绘制应用程序，它允许用户复制和粘贴由多个数据类型和点组成的复杂形状。

[![正在运行的应用的示例](copy-paste-images/intro01.png "正在运行的应用的示例")](copy-paste-images/intro01-large.png)

在本文中，我们将介绍使用粘贴板 Xamarin.Mac 应用程序以支持复制和粘贴操作中的基础知识。 强烈建议你通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和接口生成器简介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)部分中的，因为它介绍主要概念和我们将在本文中使用的技术。

你可能想要看一看[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`属性用于连接你的 C# 类 OBJECTIVE-C 的对象和 UI 元素。

## <a name="getting-started-with-the-pasteboard"></a>入门粘贴板

粘贴板提供标准化的机制用于交换在给定应用程序或应用程序之间的数据。 粘贴板 Xamarin.Mac 应用程序中的典型用途是处理复制和粘贴操作，但也支持许多其他操作的 （如拖放和应用程序服务）。

若要获取你起作用所快速，我们将以开头使用选 Xamarin.Mac 应用中的简单、 实用介绍。 更高版本，我们将提供深入粘贴板的工作原理和使用的方法的说明。

对于此示例中，我们将创建的简单文档基于应用程序管理包含图像视图的窗口。 用户将能够复制和粘贴在应用程序和到或从其他应用或在相同的应用内的多个窗口的文档之间的图像。

### <a name="creating-the-xamarin-project"></a>创建 Xamarin 项目

首先，我们将创建新的文档基于 Xamarin.Mac 应用，我们将会添加复制并粘贴支持。

请执行以下操作：

1. 针对 Mac 和单击启动 Visual Studio**新项目...**链接。
2. 选择**Mac** > **应用** > **Cocoa 应用**，然后单击**下一步**按钮： 

    [![创建一个新 Cocoa 应用程序项目](copy-paste-images/sample01.png "创建一个新 Cocoa 应用程序项目")](copy-paste-images/sample01-large.png)
3. 输入`MacCopyPaste`为**项目名称**并保留为默认值的其他所有内容。 单击下一步: 

    [![设置项目的名称](copy-paste-images/sample01a.png "设置项目的名称")](copy-paste-images/sample01a-large.png)

4. 单击**创建**按钮： 

    [![确认新的项目设置](copy-paste-images/sample02.png "确认新的项目设置")](copy-paste-images/sample02-large.png)

### <a name="add-an-nsdocument"></a>添加 NSDocument

接下来，我们将添加自定义`NSDocument`将充当应用程序的用户界面的后台存储的类。 它将包含单个图像视图，并了解如何将映像复制到默认粘贴板视图从以及如何创建从默认粘贴板映像并将其显示在图像视图中。

右键单击中 Xamarin.Mac 项目**解决方案 Pad**和选择**添加** > **新文件...**:

![向项目添加 NSDocument](copy-paste-images/sample03.png "向项目添加 NSDocument")

对“名称”输入 `ImageDocument`，然后单击“新建”按钮。 编辑**ImageDocument.cs**类并使其如下所示：

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

中详细地看看一些代码。

以下代码提供了要测试是否存在映像上的数据默认剪贴板中，如果可用，映像的属性`true`否则返回`false`:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

下面的代码会将映像复制到默认粘贴板附加的图像视图：

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

和下面的代码粘贴来自默认粘贴板的图像，并且在附加的图像视图中显示它，（如果粘贴板包含有效的映像）：

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

与此文档位置中，我们将创建 Xamarin.Mac 应用的用户界面。

### <a name="building-the-user-interface"></a>构建用户界面

双击**Main.storyboard**文件以在 Xcode 中打开它。 接下来，也添加一个工具栏和映像，并将其配置如下：

[![编辑工具栏](copy-paste-images/sample04.png "编辑工具栏")](copy-paste-images/sample04-large.png)

添加复制和粘贴**图像工具栏项**到工具栏的左边。 我们将使用这三个快捷方式复制和粘贴从编辑菜单。 接下来，添加了四个**映像工具栏项**工具栏的右侧。 我们将使用它们来填充也具有一些默认图像的图像。

有关使用工具栏的详细信息，请参阅我们[工具栏](~/mac/user-interface/toolbar.md)文档。

接下来，让我们也公开以下插座和我们工具栏项和图像的操作：

[![创建 outlet 和操作](copy-paste-images/sample05.png "创建 outlet 和操作")](copy-paste-images/sample05-large.png)

有关使用容器和操作的详细信息，请参阅[插座和操作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)一部分我们[Hello，Mac](~/mac/get-started/hello-mac.md)文档。

### <a name="enabling-the-user-interface"></a>启用用户界面

使用我们在 Xcode 和我们的容器和操作通过公开的 UI 元素中创建的用户界面，我们需要添加代码以启用用户界面。 双击**ImageWindow.cs**文件中**解决方案 Pad**并使其如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

中详细地看一看此代码。

首先，我们公开的实例`ImageDocument`我们在上面创建的类：

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

通过使用`Export`，`WillChangeValue`和`DidChangeValue`，我们让安装程序`Document`属性以便键值对的编码和 Xcode 中的数据绑定。

我们还公开来自也我们将添加到我们的 UI 在 Xcode 中使用下面的属性的映像的映像：

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

当主窗口可以加载并显示时，我们创建的实例我们`ImageDocument`类并将用户界面的映像附加很好地按它替换为以下代码：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

最后，在用户单击复制和粘贴工具栏项的响应，我们调用的实例`ImageDocument`类来执行实际工作：

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>启用文件和编辑菜单

我们需要做的最后一步是启用**新建**从菜单项**文件**菜单 （若要创建我们主窗口的新实例） 并启用**剪切**，**复制**和**粘贴**菜单项从**编辑**菜单。

若要启用**新建**菜单项，编辑**AppDelegate.cs**文件并添加以下代码：

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

有关详细信息，请参阅[使用多个窗口](~/mac/user-interface/window.md)一部分我们[Windows](~/mac/user-interface/window.md)文档。

若要启用**剪切**，**复制**和**粘贴**菜单项，编辑**AppDelegate.cs**文件并添加以下代码：

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

对于每个菜单项，我们将获取当前、 密钥的最顶端窗口并将它转换到我们`ImageWindow`类：

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

从这里，我们调用`ImageDocument`该窗口来处理复制和粘贴操作的类实例。 例如: 

```csharp
window.Document.CopyImage (sender);
```

我们只需要**剪切**，**复制**和**粘贴**菜单项，可以访问如果没有图像默认粘贴板上或很好地的当前活动窗口的映像中的数据。

让我们添加**EditMenuDelegate.cs**到 Xamarin.Mac 项目文件并使其如下所示：

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

同样，我们获取当前的最顶端窗口，并使用其`ImageDocument`类实例，以查看是否存在所需的图像数据。 然后我们使用`MenuWillHighlightItem`方法可启用或禁用每个项基于此状态。

编辑**AppDelegate.cs**文件并进行`DidFinishLaunching`方法外观如下所示：
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

首先，我们禁用自动启用和禁用菜单项在编辑菜单。 接下来，我们将附加的实例`EditMenuDelegate`我们在上面创建的类。

有关详细信息，请参阅我们[菜单](~/mac/user-interface/menu.md)文档。

### <a name="testing-the-app"></a>测试应用程序

位置中的所有内容，我们现在可以测试应用程序。 生成和运行应用并显示在主界面：

![运行应用程序](copy-paste-images/run01.png "运行应用程序")

如果打开编辑菜单，请注意，**剪切**，**复制**和**粘贴**已禁用，因为没有图像映像中良好，甚至在默认粘贴板：

![打开编辑菜单](copy-paste-images/run02.png "打开编辑菜单")

如果你也添加到映像的映像，并重新打开编辑菜单，现在要启用的项目：

![显示编辑菜单项启用](copy-paste-images/run03.png "显示编辑菜单项已启用")

如果你将映像复制并选择**新建**你可以从文件菜单中，粘贴到新的窗口的该映像：

![将图像粘贴到一个新窗口](copy-paste-images/run04.png "粘贴到一个新窗口的图像")

在以下部分中，我们将详细的介绍了在使用 Xamarin.Mac 应用程序中粘贴板。

## <a name="about-the-pasteboard"></a>有关粘贴板

在 macOS （以前称为 OS X） 粘贴板 (`NSPasteboard`) 提供多个服务器处理，例如复制和粘贴、 拖动和删除和应用程序服务的支持。 在以下部分中，我们将花几个关键粘贴板概念进一步查看。

### <a name="what-is-a-pasteboard"></a>什么是粘贴板？

`NSPasteboard`类提供标准化的机制，用于在交换应用程序之间或给定应用内的信息。 粘贴板的主要功能是用于处理复制和粘贴操作：

1. 当用户选择一项以在应用程序，并使用**剪切**或**复制**菜单项的选定项的一个或多个表示形式放置在剪贴板上。
2. 当用户使用**粘贴**菜单项 （在同一应用或一个不同），它可以处理的数据的版本为从剪贴板中复制并添加到应用程序。

不太明显粘贴板用途包括查找、 拖动、 拖动和 drop，和应用程序服务操作：

- 当用户启动拖动操作时，拖动数据复制到剪贴板。 如果在拖动操作结束拖放到另一个应用程序，该应用从粘贴板复制的数据。
- 转换服务要转换的数据的请求的应用复制到剪贴板。 应用程序服务中、 从剪贴板中检索数据，转换过程中，然后粘贴数据重新粘贴板上。

在其最简单的表单中，选用于将数据在给定应用内或应用之间移动，因此在应用程序的过程之外特殊全局内存区中存在。 虽然很容易被选的概念 grasps，有几个更复杂的详细信息，必须考虑。 下面将详细介绍这些文件。

### <a name="named-pasteboards"></a>命名的选

粘贴板可以是公共或私有和可用于多种用途内部应用程序或多个应用程序之间。 macOS 提供了几个标准选，每个都有特定的定义完善的使用情况：

- `NSGeneralPboard` 为-默认粘贴板**剪切**，**复制**和**粘贴**操作。
- `NSRulerPboard` -支持**剪切**，**复制**和**粘贴**对操作**标尺**。
- `NSFontPboard` -支持**剪切**，**复制**和**粘贴**对操作`NSFont`对象。
- `NSFindPboard` -支持特定于应用程序查找可以共享搜索文本的面板。
- `NSDragPboard` -支持**拖放**操作。

对于大多数情况下，你将使用系统定义选之一。 但可能需要您创建你自己选的情况。 在这些情况下，你可以使用`FromName (string name)`方法`NSPasteboard`类，以创建具有给定名称的自定义剪贴板。

（可选） 可以调用`CreateWithUniqueName`方法`NSPasteboard`类，以创建唯一的命名粘贴板。

### <a name="pasteboard-items"></a>粘贴板的项

每个应用程序写入粘贴板的数据片段被视为_粘贴板项_粘贴板同时又可以容纳多个项。 这种方式，应用程序可以写入多个版本的数据被复制到剪贴板 （例如，纯文本和格式化的文本） 中，并且检索应用程序可以读取关闭仅将数据它可以处理 （如仅纯文本）。

### <a name="data-representations-and-uniform-type-identifiers"></a>数据表示形式和统一类型标识符

粘贴板操作通常需要两个 （或更多） 之间的应用程序不知道彼此或数据类型的每个可以处理。 如上面的部分中所述，若要最大限度地共享信息、 的可能性粘贴板可容纳多个表示形式的数据正在复制和粘贴。

每个表示形式标识通过统一类型标识符 （实用程序），这只是一个简单的字符串，唯一标识要呈现的日期的类型 (有关详细信息，请参阅 Apple 的[统一类型标识符概述](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)文档)。 

如果要创建自定义数据类型 （例如，矢量图形应用程序中的绘制对象），则可以创建你自己的实用程序来唯一标识它中复制和粘贴操作。

当应用程序正在准备粘贴从剪贴板中复制的数据时，它必须找到最适合其功能 （如果存在） 的表示。 通常，这将是最丰富的类型可用 （例如格式化文本文字处理应用程序），回退最简单的窗体可用作需要 （纯文本的简单文本编辑器）。

<a name="Promised_Data" />

### <a name="promised-data"></a>承诺的数据

通常情况下，应提供数据被复制，以最大化应用之间共享的任意多个表示的形式。 但是，由于时间和内存的约束，它可能是不切实际的实际写入粘贴板每种数据类型。

在此情况下，可以将第一个数据表示形式放置在粘贴板，并接收应用程序可请求的不同表示形式，可以将生成上的即时之前粘贴操作。

当你将初始项放在剪贴板中时，你将指定一个或多个可用的其他表示由符合对象`NSPasteboardItemDataProvider`接口。 根据接收应用程序的请求，这些对象将点播情况下，提供额外的表示形式。

### <a name="change-count"></a>更改计数

每个粘贴板维护_更改计数_声明该增量每次时，新的所有者。 应用程序可以确定后它会检查它通过检查的值的更改计数中的最后一个时间已被更改剪贴板的内容。

使用`ChangeCount`和`ClearContents`方法`NSPasteboard`类来修改给定粘贴板的更改计数。

## <a name="copying-data-to-a-pasteboard"></a>将数据复制到剪贴板

通过第一个访问粘贴板、 清除任何现有内容和写入多个表示形式之间实现所需的粘贴的数据执行复制操作。

例如:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

通常情况下，你将只会写入常规粘贴板，与我们在上面的示例。 将发送到任何对象`WriteObjects`方法*必须*符合`INSPasteboardWriting`接口。 多个内置类 (如`NSString`， `NSImage`， `NSURL`， `NSColor`， `NSAttributedString`，和`NSPasteboardItem`) 自动符合其此接口。

如果您要粘贴板到编写自定义数据类必须符合`INSPasteboardWriting`接口或实例中包装`NSPasteboardItem`类 (请参阅[自定义数据类型](#Custom_Data_Types)下面一节)。

## <a name="reading-data-from-a-pasteboard"></a>从剪贴板读取数据

如上面所述，若要最大化应用之间的共享数据的可能性所复制数据的多个表示形式之间实现可能将写入到剪贴板。 它负责接收应用程序中，选择其功能可能的丰富版本 （如果存在）。

### <a name="simple-paste-operation"></a>简单粘贴操作

从粘贴板使用读取数据`ReadObjectsForClasses`方法。 它将需要两个参数：

1. 数组`NSObject`基于你想要粘贴板从其中进行读取的类类型。 你应这最所需的数据类型与第一位排序，与减少首选项中的任何剩余类型。
2. 一个包含其他约束 （如因此被限制为特定 URL 的内容类型） 的字典或是否需要任何进一步的约束的空字典。

该方法返回满足我们传递中的条件的项的数组，并因此最多包含相同数量的所请求的数据类型。 还有可能这些请求的类型都不存在，并将返回一个空数组。

例如，下面的代码将检查以确定如果`NSImage`位于常规粘贴板，并且如果是这样，则会显示它以及映像包中：

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>请求多个数据类型

根据正在创建的 Xamarin.Mac 应用程序的类型，它可能能够处理多个表示形式要粘贴的数据。 在此情况下，有从剪贴板中检索数据的两个方案：

1. 单个调用`ReadObjectsForClasses`方法并提供所有所需 （按首选顺序） 的表示的数组。
2. 使多个调用`ReadObjectsForClasses`方法要求的另一个数组类型每次。

请参阅**简单粘贴操作**上面部分以了解更多详细信息从剪贴板中检索数据。

### <a name="checking-for-existing-data-types"></a>检查现有数据类型

有时你可能想要检查是否粘贴板而无需实际从剪贴板读取数据包含给定的数据表示形式 (例如启用**粘贴**菜单项仅在存在有效的数据时)。

调用`CanReadObjectForClasses`粘贴板以查看它是否包含给定的类型的方法。

例如，下面的代码确定是否常规粘贴板包含`NSImage`实例：

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>从剪贴板读取 url

根据给定的 Xamarin.Mac 应用函数，它可能需要从剪贴板读取 Url 但只有它们是否符合给定的一组条件 （如指向文件或 Url 的特定数据类型）。 在此情况下，您可以指定其他搜索条件使用的第二个参数`CanReadObjectForClasses`或`ReadObjectsForClasses`方法。

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>自定义数据类型

有你将需要将您自己的自定义类型保存到剪贴板，从 Xamarin.Mac 应用场合。 例如，矢量图形应用程序，它允许复制和粘贴对象绘制到用户。

在此情况下，你将需要设计您的数据自定义类，以便它继承自`NSObject`且符合的几个接口 (`INSCoding`，`INSPasteboardWriting`和`INSPasteboardReading`)。 或者，你可以使用`NSPasteboardItem`封装要复制或粘贴的数据。

将下面将详细介绍这两个选项。

### <a name="using-a-custom-class"></a>使用自定义类

在本部分中我们将扩展我们在本文档的开始处创建简单的示例应用程序并添加自定义类来窗口之间跟踪我们要复制和粘贴的映像的信息。

向项目添加新类并调用它**ImageInfo.cs**。 编辑文件，并使其看起来如下所示：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

下列部分中我们将详细的介绍了在此类。

#### <a name="inheritance-and-interfaces"></a>继承和接口

可以写入到自定义数据类，或将其从剪贴板读取之前，它必须符合`INSPastebaordWriting`和`INSPasteboardReading`接口。 此外，它必须继承自`NSObject`，并且也符合`INSCoding`接口：

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

类还必须向 Objective C 公开使用`Register`指令和它必须公开任何必需的属性或方法使用`Export`。 例如:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

我们将公开此类将包含数据的图像的名称和其类型 （jpg、 png 等） 的两个字段。 

有关详细信息，请参阅[公开 C# 类 / Objective C 的方法](~/mac/internals/how-it-works.md)部分[Xamarin.Mac 内部](~/mac/internals/how-it-works.md)文档，它说明了`Register`和`Export`属性用于连接你的 C# 类 OBJECTIVE-C 的对象和 UI 元素。

#### <a name="constructors"></a>构造函数

以便它可以从剪贴板读取则在我们的自定义数据类时将需要使用两个构造函数 （正确公开 Objective C）：

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

首先，我们公开_空_构造函数下的默认 Objective C 方法`init`。

接下来，我们公开`NSCoding`符合构造函数将用于从粘贴板上创建对象的新实例，导出名称下方粘贴时`initWithCoder`。

此构造函数将`NSCoder`(因为由创建`NSKeyedArchiver`时写入到粘贴板)，将提取的键/值配对数据和将其保存到数据类的属性字段。

#### <a name="writing-to-the-pasteboard"></a>写入粘贴板

通过符合`INSPasteboardWriting`接口，我们需要将公开两种方法，和 （可选） 的第三个方法，以便类可以写入到剪贴板。

首先，我们需要告诉粘贴板哪种数据类型的表示形式，可以写入自定义类：

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

每个表示形式标识通过统一类型标识符 （实用程序），这只是一个简单的字符串，唯一标识要显示的数据类型 (有关详细信息，请参阅 Apple 的[统一类型标识符概述](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)文档)。

对于我们的自定义格式，我们将创建我们自己的实用程序:"com.xamarin.image-info"（请注意，在一样应用标识符的反向表示法）。 我们类也是支持的标准字符串写入粘贴板 (`public.text`)。 

接下来，我们需要创建对象以请求获取实际写入粘贴板的格式：

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

有关`public.text`类型，我们要返回一个简单、 格式化`NSString`对象。 为自定义`com.xamarin.image-info`类型，我们将使用`NSKeyedArchiver`和`NSCoder`接口进行编码的键/值配对存档的自定义数据类。 我们将需要实现以下方法来实际处理的编码：

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

单个键/值对写入编码器，并将使用我们前面添加的第二个构造函数进行解码。

（可选），我们可以包括以下方法来定义任何选项，将数据写入到粘贴板时：

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

当前仅`WritingPromised`选项提供，并且仅承诺和不实际写入粘贴板给定的类型时，应使用。 有关详细信息，请参阅[承诺数据](#Promised_Data)上面一节。

使用就地这些方法，可以使用下面的代码要粘贴板写入自定义类：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>从剪贴板读取

通过符合`INSPasteboardReading`接口，我们需要进行公开三个方法，以便可以从剪贴板读取自定义数据类。

首先，我们需要告诉粘贴板哪种数据类型的自定义的类可以从剪贴板读取的表示形式：

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

同样，这些定义为简单 UTIs 和中所定义的类型相同**写入粘贴板**上面一节。

接下来，我们需要告诉粘贴板_如何_将使用以下方法读取每个实用程序类型：

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

有关`com.xamarin.image-info`类型，我们将指示粘贴板进行解码使用我们创建的键/值对`NSKeyedArchiver`类时写入粘贴板中通过调用`initWithCoder:`我们添加到类的构造函数。

最后，我们需要添加以下方法从剪贴板读取其他实用程序数据表示形式：

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

使用所有这些方法中的位置，可以从使用下面的代码粘贴板读取自定义数据类：

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>使用 NSPasteboardItem

可能需要自定义项写入粘贴板不值得创建自定义类或你想要在常见的格式，仅在需要提供数据时的时间。 对于这些情况下，你可以使用`NSPasteboardItem`。

A`NSPasteboardItem`提供精细的控制对数据写入粘贴板，专为临时访问-它应释放的后是否已写入粘贴板。

#### <a name="writing-data"></a>将数据写入

你自定义数据写入`NSPasteboardItem`你将需要提供自定义`NSPasteboardItemDataProvider`。 向项目添加新类并调用它**ImageInfoDataProvider.cs**。 编辑文件，并使其看起来如下所示：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

正如我们做的自定义数据类，我们需要使用`Register`和`Export`指令以将其公开给目标。 此类必须继承自`NSPasteboardItemDataProvider`和必须实现`FinishedWithDataProvider`和`ProvideDataForType`方法。

使用`ProvideDataForType`方法以提供的数据，将包装在`NSPasteboardItem`，如下所示：

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

在这种情况下，我们将两条信息存储有关我们映像 （名称和 ImageType） 和写入到一个简单的字符串 (`public.text`)。

类型来将数据写入到剪贴板，并使用以下代码：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>读取数据

若要从剪贴板读取数据，请使用下面的代码：

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>摘要

本文已了解使用 Xamarin.Mac 应用程序以支持复制和粘贴操作中粘贴板的详细的信息。 首先，它引入了一个简单的示例，以帮助你熟悉标准选操作。 接下来，此操作耗时了解粘贴板以及如何读取和写入数据，从它的详细的信息。 最后，它看起来在使用自定义数据类型来支持复制和粘贴在应用内的复杂数据类型。



## <a name="related-links"></a>相关链接

- [MacCopyPaste (sample)](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [粘贴板编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [macOS 人工界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
