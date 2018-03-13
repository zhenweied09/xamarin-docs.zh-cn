---
title: "显示图像"
description: "本文介绍如何包括 Xamarin.iOS 应用程序并显示该映像，通过使用 C# 代码或通过将它分配给 iOS 设计器中的控件中的图像资产。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 1ca537ef47f48886483e99bb33b0d524d16593f8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="displaying-an-image"></a>显示图像

_本文介绍如何包括 Xamarin.iOS 应用程序并显示该映像，通过使用 C# 代码或通过将它分配给 iOS 设计器中的控件中的图像资产。_

本文详细介绍了以下主题：

- [添加和组织中 Xamarin.iOS 应用程序的映像](#adding-assets)-适用于图像资产，它们可以包含，如何组织和管理在 Xamarin.iOS 项目。
- [将映像添加到资产目录](#asset-catalogs)-管理映像与资产目录。
    - [在资产目录中使用矢量图像](#Using-Vector-Images-in-Asset-Catalogs)-所有图像大小提供单个向量。
- [处理模板映像](#Working-with-Template-Images)-通过设置图像资产中，以将模板映像，开发人员可以轻松地着色其通过设置图像的适合于应用的 UI 的主题更改`Tint`属性。
- [使用图像的控件](#controls)-介绍使用 UI 控件，如具有的 Xamarin.iOS 项目中包含的图像资产`UIButton`和`UIImageView`以及如何处理在 C# 中使用的图像`UIImage`对象的[FromBundle](#frombundle)方法。
- [情节提要中显示图像](#Displaying-an-Image-in-a-Storyboards)-提供了一个示例显示使用情节提要图像。
- [在代码中显示图像](#Displaying-an-Image-in-Code)-提供的显示图像使用 C# 代码示例。

<a name="adding-assets" />

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>添加和组织 Xamarin.iOS 应用程序中的映像

当在 Xamarin.iOS 应用程序中添加用于图像，开发人员将使用_资产目录_以支持每个 iOS 设备和所需的应用的分辨率。

在 iOS 7，添加**资产目录映像集**包含所有版本或所需支持各种设备和规模因素已应用程序的图像的表示形式。 而不是依靠图像资产文件名 (请参阅[分辨率独立图像和图像命名法](~/ios/app-fundamentals/images-icons/displaying-an-image.md))，**映像集**使用 Json 文件来指定哪个映像属于哪些设备和/或解决方法. 这是首选的方法，以管理和支持 iOS （从 iOS 9 或更高版本) 中的映像。

<a name="asset-catalogs" />

## <a name="adding-images-to-an-asset-catalog-image-set"></a>添加图像到资产目录映像设置

如前所述，**资产目录映像集**包含所有版本或所需支持各种设备和规模因素已应用程序的图像的表示形式。 而不是依靠图像资产文件名，**映像集**使用 Json 文件来指定哪个映像属于哪些设备和/或解决方法。

若要创建新的映像集，并向其中添加映像，请执行以下操作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**解决方案资源管理器**，双击`Assets.xcassets`文件以打开进行编辑：

    ![](displaying-an-image-images/imageset01.png "在解决方案资源管理器 Assets.xcassets")
2. 右键单击**资产列表**和选择**新映像设置**:

    ![](displaying-an-image-images/imageset02.png "添加新的映像集")
3. 选择新的映像集，并将显示的编辑器：

    ![](displaying-an-image-images/imageset03.png "设置图像编辑器")
4. 从此处，拖动图像中为每个不同设备和所需的解决方法。 (注意： 这些解决方法与在中指定的分辨率[图像大小和文件名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)文档。)
5. 双击新映像集**名称**中**资产列表**对其进行编辑： ![ ](displaying-an-image-images/imageset04.png "编辑新映像集的名称")

使用时**映像设置**在 iOS 设计器中，只需选择集的名称从下拉列表在属性编辑器中：

![](displaying-an-image-images/imageset06.png "从下拉列表中选择的映像集名称")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 打开从资产目录**解决方案资源管理器**，并在左上角，单击**加上**按钮：

    ![](displaying-an-image-images/asset5.png "单击加号按钮")
2. 选择**添加映像集**和映像设置编辑器将显示为新的映像集。 从此处，拖动图像中为每个不同设备和所需的解决方法。 (注意： 这些解决方法与在中指定的分辨率[图像大小和文件名](~/ios/app-fundamentals/images-icons/displaying-an-image.md)文档):

    ![](displaying-an-image-images/asset7.png "映像集编辑器")

### <a name="renaming-an-image-set"></a>重命名图像集

若要重命名一个映像集，请执行以下操作：

1. 在**解决方案资源管理器**，双击**资产目录**文件以打开进行编辑：

    ![](displaying-an-image-images/rename01.png "中的资产目录中的解决方案资源管理器")
2. 选择**映像设置**重命名：

    ![](displaying-an-image-images/rename02.png "选择映像设置重命名")
3. 在**属性资源管理器**，滚动到底部并选择**名称**(下**杂项**部分):

    ![](displaying-an-image-images/rename03.png "选择杂项部分下的名称")
4. 输入一个新**名称**为**映像设置**并保存所做的更改。

-----

使用时**映像设置**在代码中，它按名称引用调用`FromBundle`方法`UIImage`类。 例如：

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> **请注意**： 如果分配给一个映像集的映像未显示正确，请确保与使用正确的 filename`FromBundle`方法 (**映像设置**而不是父级**资产目录**名称)。 对于 PNG 图像，`.png`扩展，则可以省略。 有关其他映像的格式，此扩展是必需 （如。 `PurpleMonkey.jpg`）格式模式中出现的位置生成。

<a name="Using-Vector-Images-in-Asset-Catalogs" />

### <a name="using-vector-images-in-asset-catalogs"></a>在资产目录中使用矢量图像

截至 iOS 8，特殊**向量**类，如已添加到**映像集**，允许开发人员包括**PDF**格式化中改为包括盒式的矢量图像在不同的解决方法的各个位图文件。 使用此方法提供的单个向量文件`@1x`（格式为向量 PDF 文件） 的解析和`@2x`和`@3x`将在编译时生成文件的版本，并将其包含在应用程序的捆绑包。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](displaying-an-image-images/imageset05.png "在资产目录编辑器中的矢量图像")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/asset8.png "在资产目录编辑器中的矢量图像")

-----

例如，如果开发人员包括`MonkeyIcon.pdf`文件作为资产目录分辨率高达 150px x 150px，以下资产将在编译时包含在最终应用程序捆绑的位图的矢量：

- `MonkeyIcon@1x.png` -150px x 150px 解析。
- `MonkeyIcon@2x.png` -300px x 300px 解析。
- `MonkeyIcon@3x.png` -450px x 450px 解析。

以下应考虑到在资产目录中使用 PDF 矢量图像时：

- 这不是完整向量支持，因为在编译时和在最终应用程序中提供的位图 PDF 将光栅位图。
- 设置资产目录中后，不能调整图像的大小。 如果开发人员尝试调整图像 （不管是在代码或通过使用自动布局和大小类） 的大小的图像将会扭曲就像任何其他位图一样。
- 资产目录才符合 iOS 7 和更高版本，如果应用程序需要支持 iOS 6 或更低时，它不能使用资产目录。

<a name="Working-with-Template-Images" />

## <a name="working-with-template-images"></a>处理模板映像

根据 iOS 应用程序的设计，可能有开发人员需要自定义图标或在用户界面，以匹配 （例如，根据用户首选项） 的配色方案中的更改的图像的时间。

若要轻松地实现此效果，切换_呈现模式_的图像资产以进行**模板映像**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage01.png "呈现模式设置为模板映像")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage01vs.png "呈现模式设置为模板")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

从 iOS 的设计器中，可以将图像资产分配到 UI 控件，然后设置**浅色**要着色映像：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage03.png "设置要为图像着色浅色")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage03vs.png "设置要为图像着色浅色")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

（可选） 可以直接在代码中设置的图像资产和浅色：

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

若要使用完全中代码的模板映像，请执行以下操作：

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

因为`RenderMode`属性`UIImage`是只读的使用`ImageWithRenderingMode`方法创建具有所需的呈现模式设置的图像的新实例。

有三个可能设置`UIImage.RenderMode`通过`UIImageRenderingMode`枚举：

* `AlwaysOriginal` -强制要呈现为原始源映像文件而不进行任何更改的图像。
* `AlwaysTemplate` -强制要呈现为一个模板映像，通过着色的像素与指定的图像`Tint`颜色。
* `Automatic` -请呈现图像，因为模板或原始基于在中使用的环境。 例如，如果在使用的映像`UIToolBar`， `UINavigationBar`，`UITabBar`或`UISegmentControl`将被视为一个模板。

<a name="Adding-new-Assets-Collections" />

## <a name="adding-new-assets-collections"></a>添加新的资产集合

当处理资产目录中的映像可能是一个新集合会必需的而不是添加的应用程序的映像分发到所有`Assets.xcassets`集合。 例如，在设计上按需资源时。

若要向项目添加新的资产目录：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 右键单击**项目名称**中**解决方案资源管理器**和选择**添加** > **新文件...**
2. 选择**iOS** > **资产目录**，输入**名称**集合和单击**新建**按钮：

    ![](displaying-an-image-images/asset01.png "创建新的资产目录")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在解决方案资源管理器，右键单击**资产目录**文件夹，并选择**添加 > 新资产目录**。
2. 为其提供一个名称并单击**添加**:

    ![](displaying-an-image-images/asset1.png "创建新的资产目录")

-----


从此处，集合可以得到与默认值的方式相同`Assets.xcassets`自动包括在项目中的集合。

<a name="controls" />

## <a name="using-images-with-controls"></a>使用图像的控件

除了使用映像来支持应用程序，iOS 还使用映像使用与应用程序控件类型，如选项卡条、 工具栏、 导航栏、 表和按钮。 使图像控件上显示一个简单方法是将分配`UIImage`到控件的实例`Image`属性。

<a name="frombundle" />

### <a name="frombundle"></a>FromBundle

`FromBundle`方法调用是同步的 （阻塞的） 调用具有大量的内置的如缓存支持和自动处理各种分辨率的图像文件的图像加载和管理功能。  

下面的示例演示如何设置的图像`UITabBarItem`上`UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

假设`MyImage`是图像资产添加到上述资产目录的名称。 在处理资产目录映像时，只需指定的设置在图像名称`FromBundle`方法**PNG**格式化映像：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

对于任何其他映像格式，包括名称的扩展。 例如:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

有关图标和映像的详细信息，请参阅 Apple 文档上[自定义图标和映像创建指南](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)。

<a name="Displaying-an-Image-in-a-Storyboards" />

## <a name="displaying-an-image-in-a-storyboards"></a>情节提要中显示图像

一旦映像已添加到使用资产目录为 Xamarin.iOS 项目，它可以轻松地显示在情节提要使用`UIImageView`iOS 设计器中。 例如，如果已添加以下图像资产：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](displaying-an-image-images/display01.png "已添加示例图像资产")

执行以下操作来将其显示在情节提要：

1. 双击`Main.storyboard`文件中**解决方案资源管理器**以将其打开以进行编辑 iOS 设计器中。
2. 选择**图像视图**从**工具箱**:

     ![](displaying-an-image-images/display02.png "从工具箱中选择图像视图")
3. 将图像视图拖动到设计图面和位置，并调整其大小根据需要：

    ![](displaying-an-image-images/display03.png "在设计图面上新图像视图")
4. 在**小组件**部分**属性资源管理器**选择所需的**映像**要显示的资产：

    ![](displaying-an-image-images/display04.png "选择所需的图像资产，要显示")
5. 在**视图**部分中，使用**模式**来控制如何图像将调整**图像视图**一起调整大小。
6. 与**图像视图**处于选中状态，单击它以添加**约束**:

    ![](displaying-an-image-images/display05.png "将约束添加")
7. 在每个边缘上拖动"T"调整控点**图像视图**到相应的一侧屏幕"固定"到四条边的图像。 这种方式，**图像视图**将收缩并按调整屏幕的增长。
8. 将所做的更改保存到情节提要。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/display01vs.png "已添加示例图像资产")

执行以下操作来将其显示在情节提要：

1. 双击`Main.storyboard`文件中**解决方案资源管理器**以将其打开以进行编辑 iOS 设计器中。
2. 选择**图像视图**从**工具箱**:

     ![](displaying-an-image-images/display02vs.png "从工具箱中选择图像视图")
3. 将图像视图拖动到设计图面和位置，并调整其大小根据需要：

    ![](displaying-an-image-images/display03vs.png "在设计图面上新图像视图")
4. 在**小组件**部分**属性资源管理器**选择所需的**映像**要显示的资产：

    ![](displaying-an-image-images/display04vs.png "选择所需的图像资产，要显示")
5. 在**视图**部分中，使用**模式**来控制如何图像将调整**图像视图**一起调整大小。
6. 与**图像视图**处于选中状态，单击它以添加**约束**:

    ![](displaying-an-image-images/display05vs.png "将约束添加")
7. 在每个边缘上拖动"T"调整控点**图像视图**到相应的一侧屏幕"固定"到四条边的图像。 这种方式，**图像视图**将收缩并按调整屏幕的增长。
8. 将所做的更改保存到情节提要。

-----


<a name="Displaying-an-Image-in-Code" />

## <a name="displaying-an-image-in-code"></a>在代码中显示图像

就像在情节提要中显示图像，图像添加到使用资产目录中，为 Xamarin.iOS 项目后它可以轻松地显示使用 C# 代码。

请参见以下示例：

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

此代码创建一个新`UIImageView`并为其提供的初始大小和位置。 然后它从映像资产添加到项目加载图像并添加`UIImageView`到父`UIView`以显示它。

## <a name="related-links"></a>相关链接

- [使用图像 （示例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和映像创建指南](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
