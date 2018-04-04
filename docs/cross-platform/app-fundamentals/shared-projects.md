---
title: 共享的项目
description: 共享的项目中，可以编写大量不同的应用程序项目的引用的常见代码。 该代码编译为每个引用的项目的一部分，并可以包含有助于将特定于平台的功能合并到基本共享的代码的编译器指令。
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: a7d5a25c3c6f2889bde0ff4aeaf85053bcc796fb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="shared-projects"></a>共享的项目

_共享的项目中，可以编写大量不同的应用程序项目的引用的常见代码。该代码编译为每个引用的项目的一部分，并可以包含有助于将特定于平台的功能合并到基本共享的代码的编译器指令。_

（有时也称为共享资产项目） 的共享项目中，可以编写代码，包括 Xamarin 应用程序的多个目标项目之间共享。

它们支持编译器指令，因此，你可以有条件地包括特定于平台的代码编译到的项目的引用共享项目的子集。 此外，还有 IDE 支持，以帮助管理编译器指令和可视化代码中每个应用程序外观。

如果已使用在过去文件链接来共享代码项目之间，共享项目的工作方式相似但更改进了 IDE 支持。



## <a name="what-is-a-shared-project"></a>什么是共享的项目？

与大多数其他项目类型不同共享的项目没有任何输出 （以 DLL 形式），而是会将代码编译到每个项目中引用它。 在下图阐释了这-从概念上讲共享项目的整个内容是"复制到"每个引用的项目，并且编译，就好像它是它们的一部分。

 ![](shared-projects-images/sharedassetproject.png "共享的项目体系结构")

共享项目中的代码可以包含编译器指令将启用或禁用具体取决于哪个应用程序的项目使用的代码，建议通过在关系图的彩色的平台中的代码的部分。

不自行已编译共享项目、 项目存在单纯用作源代码文件可以包含其他项目中的分组。 在引用另一个项目后，代码将有效地编译为*一部分*的该项目。 共享项目不能引用 （包括其他共享项目） 的任何其他项目类型。

请注意，Android 应用程序项目不能引用其他 Android 应用程序项目-例如，Android 单元测试项目不能引用的 Android 应用程序项目。 有关此限制的详细信息，请参阅此[论坛讨论](http://forums.xamarin.com/discussion/comment/98092/)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)



## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 演练


此部分将指导完成如何创建和使用共享项目使用 Visual Studio for mac。 请参阅到[共享项目示例](#Shared_Project_Example)有关完整的示例部分。


## <a name="creating-a-shared-project"></a>创建共享的项目


若要创建一个新的共享项目导航到**文件 > 新解决方案...** ，然后选择一个名称。


![](shared-projects-images/xs-newsolution.png "新建解决方案")


你还可以将新的共享项目通过右键单击解决方案文件，然后选择添加到解决方案**添加 > 添加新项目...**.新的共享项目中看起来如下所示-请注意没有任何引用或组件节点;为共享的项目不支持这些。


![](shared-projects-images/xs-empty.png "空的共享的项目")


共享的项目，以很有用，它需要将引用的至少一个可生成的项目 （如 iOS 或 Android 应用程序或库中或 PCL 项目）。 当有任何引用它，因此语法 （或任何其他） 时，不会不获取编译共享项目错误之前引用的其他内容不会突出显示。



添加对共享项目的引用可引用的正则库项目与相同的方式。 此屏幕截图显示引用共享项目的 Xamarin.iOS 项目。


![](shared-projects-images/xs-reference.png "对共享项目的项目引用")


共享项目引用的其他库或应用程序后，可生成解决方案，在代码中查看任何错误。 当由引用共享项目_两个或多个_其他项目中，将出现一个菜单中的源代码编辑器的顶部左侧显示选择的项目引用此文件。



## <a name="shared-project-options"></a>共享项目选项


当你在共享项目上右击并选择**选项**存在设置更少比其他项目类型。 由于共享项目未编译 （在其自己） 中，无法设置输出或编译器选项、 项目配置、 程序集签名或自定义命令。 共享项目中的代码有效地从任何正在引用其继承这些值。



**选项**屏幕如下所示的项目**名称**和**默认 Namespace**是通常将更改的仅有两个设置。


![](shared-projects-images/xs-sharedprojectoptions.png "共享项目选项")



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)



## <a name="visual-studio-walkthrough"></a>Visual Studio 演练


此部分将指导完成如何创建和使用共享项目使用 Visual Studio。 请参阅到[共享项目示例](#Shared_Project_Example)完整实现的部分。


### <a name="creating-a-shared-project"></a>创建共享的项目


若要创建一个新的共享项目导航到**文件 > 新解决方案...**和选择的项目和解决方案的名称。


![](shared-projects-images/vs-newsolution.png "新建解决方案")


你还可以将新的共享项目通过右键单击解决方案文件，然后选择添加到解决方案**添加 > 新建项目...**.新的共享项目看起来如下所示 （后已添加的类文件）-请注意没有任何引用或组件节点;为共享的项目不支持这些。


![](shared-projects-images/vs-empty.png "空的共享的项目")


共享的项目，以很有用，它需要将引用的至少一个可生成的项目 （如 iOS 或 Android 应用程序或库中或 PCL 项目）。 当有任何引用它，因此语法 （或任何其他） 时，不会不获取编译共享项目错误之前引用的其他内容不会突出显示。



添加对共享项目的引用可引用的正则库项目与相同的方式。 此屏幕截图显示引用共享项目的 Xamarin.iOS 项目。


![](shared-projects-images/vs-reference.png "对共享项目的项目引用")


共享项目引用的其他库或应用程序后，可生成解决方案，在代码中查看任何错误。 当由引用共享项目_两个或多个_其他项目中的源代码编辑器，若要查看的项目引用在当前代码文件的顶部左侧出现一个菜单。


### <a name="shared-project-properties"></a>共享的项目属性


当你选择一个共享项目存在设置更少比其他项目类型的属性面板中。 由于共享项目未编译 （在其自己） 中，无法设置输出或编译器选项、 项目配置、 程序集签名或自定义命令。 共享项目中的代码有效地从任何正在引用其继承这些值。



**属性**面板如下所示的**根 Namespace**是唯一的设置，你可以更改。


![](shared-projects-images/vs-sharedprojectproperties.png "共享的项目属性")



-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>共享的项目示例

[Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)使用共享项目，以便包含常用代码的两个 iOS、 Android 和 Windows Phone 应用程序所使用的示例。 同时`SQLite.cs`和`TaskRepository.cs`源代码文件可以利用编译器指令 （如。 `#if __ANDROID__`) 可为每个引用它们的应用程序生成不同的输出。

完整的解决方案结构如下所示 (适用于 Mac 和 Visual Studio 的 Visual Studio 中分别):

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 ![](shared-projects-images/xs-examplesolution.png "Mac 解决方案的 visual Studio")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 ![](shared-projects-images/vs-examplesolution.png "Visual Studio 解决方案")

-----

Windows Phone 项目可以进行导航从 Visual Studio 中适用于 Mac，即使该项目类型不支持 Visual Studio 中编译 for mac。

下面显示了正在运行的应用程序。

 ![](shared-projects-images/example.png "iOS、 Android、 Windows Phone 示例")



## <a name="summary"></a>总结

本文档描述如何共享项目的工作，可以如何创建和适用于 Mac 的 Visual Studio 和 Visual Studio 中使用它们，并引入的简单示例应用程序演示操作中的共享项目。

## <a name="related-links"></a>相关链接

- [Tasky 示例应用程序](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [可移植类库 （示例）](~/cross-platform/app-fundamentals/pcl.md)
- [共享代码选项 （示例）](~/cross-platform/app-fundamentals/code-sharing.md)
