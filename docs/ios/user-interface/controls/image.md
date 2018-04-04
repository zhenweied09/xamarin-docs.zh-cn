---
title: 显示图像
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 406cfe813cbb58111769203f3b6c3fb0c2edad3c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="displaying-images"></a>显示图像

将映像添加到你的应用程序需要两个步骤： 首先，将图像添加到你的项目;然后，添加控件和代码以在屏幕上显示它们。 请参阅[处理映像](~/ios/app-fundamentals/images-icons/index.md)文章更详细的映像在 Xamarin.iOS 中处理的覆盖率。

## <a name="adding-images-to-your-app"></a>将映像添加到你的应用程序

可以将图像添加到你 Mac 解决方案的 Visual Studio 中的任何文件夹如果**生成操作**设置为**内容**则文件将包含在你的应用程序，并可以显示。

适用于 Mac 的 visual Studio 还支持特殊名还可以包含图像文件的资源的目录。 资源文件夹中的文件应该具有**生成操作**设置为**BundleResource**。

此屏幕快照显示**生成操作**文件时出现的选项，则右键单击：

 [![](image-images/image30a.png "生成操作菜单")](image-images/image30a.png#lightbox)

适用于 Mac 的 visual Studio 通常情况下将选择正确**生成操作**自动，但你应注意的这些设置，尤其是在你的项目中移动文件。

### <a name="adding-an-image-file"></a>添加图像文件

若要将图像文件添加到你的项目中，首先右键单击该项目并选择**添加文件...**

 [![](image-images/image31a.png "添加文件...菜单")](image-images/image31a.png#lightbox)

选择 （或多个图像） 想要包括在标准文件对话框。 默认值生成操作将在映像**BundleResource** – 不重写此值，除非你有特定原因。

 [![](image-images/image32a.png "添加文件对话框")](image-images/image32a.png#lightbox)

将将映像添加到你的项目，并可用于加载并显示在代码中。 此屏幕截图中显示的图像添加到 iOS 应用程序项目：

 [![](image-images/image33a.png "在项目中的映像")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>什么是资源目录？

文件放置在资源目录中从常规文件处理方式 – 资源文件夹的内容复制到应用程序的根目录，并可以从代码中引用。 这对于可能十分有用原因有很多：

-  存储在应用程序的属性，例如默认启动映像和应用程序图标中配置的映像。
-  存储其他图像和代码中的单独的文件，因此它们更易于管理 （子目录将保留在资源目录内容将复制时）。


Resources 目录是在库项目中，尤其有用，因为代码可以假定这些映像将被复制到的使用方应用程序，使其更轻松地编写需要图像、 声音、 视频、 XML 或其他的共享的代码库的根文件。



必须因此命名资源目录，并且所有文件应该都具有设置为生成操作**BundleResource**

## <a name="displaying-the-image"></a>显示图像

若要显示使用设计器的图像，图像视图应用作容器，并可以显示单个映像或图像的动画。 **图像视图**图标从工具箱如下所示：

 [![](image-images/image35a.png "工具箱中的 ImageView")](image-images/image35.png#lightbox)

拖动**图像视图**从**Toobox**到视图控制器上。 然后在 * * 图像视图 > 映像 * * 下拉列表中的将提供你的项目中的所有可用的图像文件的列表。 选择任一这些以将其添加到图像视图。

 [![](image-images/image36a.png "工具箱中的 ImageView")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>以编程方式显示的图像

由于 blocks.jpg 位于资源目录的根目录中它将在应用程序捆绑包的根目录中的运行时可用。 若要在 ImageView 显示此图像控件，请使用下面的代码：

```csharp
imageview1.Image = UIImage.FromBundle ("SF Monkey.png");
```

如果我们必须放置在映像`/Resources/Pics/blocks.jpg`则代码将在路径中包含的图片文件夹：

```csharp
imageview1.Image = UIImage.FromBundle ("Pics/SF Monkey.png");
```

资源文件永远不会引用需包括`Resources`文件夹。


## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
