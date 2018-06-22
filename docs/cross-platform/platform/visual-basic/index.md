---
title: 可移植 Visual Basic.NET
description: 本指南将说明如何使用 Visual Basic 在面向 Xamarin.iOS 和 Xamarin.Android 的解决方案中编写可以使用的可移植类库 (PCL) 项目。
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 71ee153135df97d3b4fa149d3d788d3b940fe944
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
ms.locfileid: "33917962"
---
# <a name="portable-visual-basicnet"></a>可移植 Visual Basic.NET

Xamarin iOS 和 Android 项目本质上不支持 Visual Basic;但是开发人员可以使用可移植类库，将现有 Visual Basic 代码迁移到 iOS 和 Android，或在 Visual Basic 中编写的应用程序对其逻辑的重要部分。 完全在 Visual Basic 中 （不包括自定义呈现器、 依赖关系服务和 XAML 代码隐藏），可以创建 Xamarin.Forms 应用程序。

## <a name="requirements"></a>要求

在 Xamarin.Android 4.10.1，Xamarin.iOS 7.0.4 和 Xamarin Studio 4.2，这意味着使用这些工具创建的任何 Xamarin 项目可以集成 Visual Basic PCL 程序集添加可移植类库支持。

若要创建和编译 Visual Basic 可移植类库必须在 Windows (Visual Studio 2012 或更高版本) 上使用 Visual Studio。

> [!NOTE]
> Visual Basic 库只能创建和编译使用 Visual Studio。 Xamarin.iOS 和 Xamarin.Android 不支持 Visual Basic 语言。
>
> 如果你在 Visual Studio 中的单独工作可以从 Xamarin.iOS 和 Xamarin.Android 项目中引用 Visual Basic 项目。
>
> 如果你的 iOS 和 Android 的项目必须同时加载在 Visual Studio 中为 Mac 应从 Visual Basic PCL 中引用的输出程序集。


## <a name="creating-a-visual-basicnet-pcl"></a>创建 Visual Basic.NET PCL

此部分将指导完成如何创建 Visual Basic 可移植类库使用 Visual Studio。
然后可以在其他项目中，包括 Xamarin.iOS 和 Xamarin.Android，Xamarin.Forms 应用引用库。

### <a name="creating-a-pcl"></a>创建 PCL

在 Visual Studio 中添加 Visual Basic PCL 时必须选择描述哪些平台应符合你的库的配置文件。 配置文件简介 PCL 文档所述。

若要创建 PCL 并选择其配置文件的步骤如下：

1.  在**新项目**屏幕上，选择**Visual Basic > 类库 （可移植）** 选项：

    [![](images/image1-sml.png "创建新的 Visual Basic 可移植库")](images/image1.png#lightbox)

1.  Visual Studio 将立即替换为以下提示**添加可移植类库**对话框，以便可以配置该配置文件。 刻度的平台，你需要支持，并按**确定**。

    [![](images/image2-sml.png "通过选择平台选择 PCL 配置文件")](images/image2.png#lightbox)

1.  Visual Basic PCL 项目中将显示中所示**解决方案资源管理器**如下所示：

    [![](images/image3-sml.png "空的 Visual Studio PCL 项目")](images/image3.png#lightbox)


PCL 现已为要添加的 Visual Basic 代码。 PCL 项目可以引用由其他项目 （应用程序项目，库项目和甚至其他 PCL 项目）。

### <a name="editing-the-pcl-profile"></a>编辑 PCL 配置文件

PCL 配置文件 （即控件 PCL 适用于哪些平台） 都可以查看和更改通过右键单击项目并选择**属性 > 库 > 更改...**.出现的对话框中此屏幕截图所示：

 [![](images/image4-sml.png "在项目属性中编辑 PCL 配置文件")](images/image4.png#lightbox)

如果代码已添加到 PCL 后更改配置文件，，则可能库将无法再编译，如果该代码引用不是新选择的配置文件的一部分的功能。


## <a name="summary"></a>总结

本文演示了如何使用 Visual Basic 代码中使用 Visual Studio 和可移植类库的 Xamarin 应用程序。 即使 Xamarin 不直接支持 Visual Basic，将 Visual Basic 编译成 PCL 允许使用要包括在 iOS 和 Android 应用的 Visual Basic 编写的代码。

下面的页介绍了如何在本机或 Xamarin.Forms 应用中使用 Visual Basic.NET Pcl:

- [构建本机的 Xamarin.iOS 和 Xamarin.Android 应用程序使用 VB](native-apps.md)
- [使用 VB 构建 Xamarin.Forms 应用程序](xamarin-forms.md)


## <a name="related-links"></a>相关链接

- [TaskyPortableVB （示例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB （示例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [使用.NET Framework (Microsoft) 的跨平台开发](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
