---
title: 图像和在 Xamarin.iOS 的图标
description: 本部分包括控件和为自定义文档类型提供图标中的各种，其中涵盖使用 Xamarin.iOS 应用程序，例如，使用它们以图标形式中的映像，启动屏幕的文章或包括它们。
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 444e3cbd88dfd3ff50153d858367ecd0310d240a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784047"
---
# <a name="images-and-icons-in-xamarinios"></a>图像和在 Xamarin.iOS 的图标

_本部分包括控件和为自定义文档类型提供图标中的各种，其中涵盖使用 Xamarin.iOS 应用程序，例如，使用它们以图标形式中的映像，启动屏幕的文章或包括它们。_

有多种资产在 iOS 应用程序内使用该映像。 从只需显示如将其分配到 UI 控件的应用程序的 UI，一部分图像`UIButton`或`UIImageView`，为您提供图标和启动屏幕，Xamarin.iOS 可以轻松地将极佳图片添加到 iOS 应用程序，通过以下方式： 

- **分辨率独立图像**– 用于 iOS 的内置支持跨不同设备的解决方法和类型 （iPhone、 iPad 等） 处理映像。
- **资产目录映像集**-使用**资产目录映像集**进行管理和分组的所需的应用程序的给定的图像资产的所有版本。
- **在 iOS 设计器中的映像**-使用 iOS 设计器为控件设置图像。
- **在代码中的映像**– 使用`UIImage`加载和处理图像资产并将它们分配给在 C# 代码的 UI 控件的类的方法。
- **应用程序图标**-定义所需的每个 iOS 应用的应用程序图标。 用户将点击此图标在 iOS 主页屏幕中以启动应用程序。 此外，此图标使用 Game Center 有可能的话。
- **聚光灯图标**-定义应用程序的 Spotlight 图标。 每当用户在 Spotlight 搜索输入应用的名称，将显示此图标。
- **设置图标**-定义应用程序的**设置**图标。 如果用户输入**设置**应用在其 iOS 设备，此图标上的将显示在应用程序的设置列表的末尾。 
- **启动屏幕**-定义应用程序的启动屏幕。 用户点击应用图标后之前的第一个视图显示,，将显示空白的屏幕。 幸运的是，iOS 包括支持使用情节提要显示图像替代空白的屏幕。 
- **iTunes 图标**-提供 iTune 图标。 如果使用提供的应用程序 （不管是为企业用户或在真实设备上的测试） 的即席方法，开发人员还需要包括 512 x 512 和将用于表示在 iTunes 应用的 1024 x 1024 映像。
- **文档图标**-映像用作任何特定的文档类型 Xamarin.iOS 应用程序支持，或者创建一个图标。

有几个创建 iOS 应用程序，以及这些资产将使用其中的多个位置的图像资产时应考虑的注意事项。 其中每个产生影响不仅多少图像资产将必需的但如何创建这些资产。 以下主题介绍将需要的图像资产、 如何将这些资产包括在应用程序的捆绑和如何使用的图像资产来提供所需的功能的类型：


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

本文介绍如何包括 Xamarin.iOS 应用程序并显示该映像，通过使用 C# 代码或通过将它分配给 iOS 设计器中的控件中的图像资产。

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[应用程序图标](~/ios/app-fundamentals/images-icons/app-icons.md)

此文章介绍如何包括和管理一个 Xamarin.iOS 应用程序中的图像资产，要用作应用程序图标。

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[备用的应用图标](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple iOS 10.3，允许管理图标将其应用到添加了几项增强功能：

 - `ApplicationIconBadgeNumber` -获取或设置应用程序图标的徽章 Springboard 中。
 - `SupportsAlternateIcons` -如果`true`应用程序具有一组备用的图标。
 - `AlternateIconName` -返回当前选定的备用图标的名称或`null`如果使用的主图标。
 - `SetAlternameIconName` -使用此方法以切换到给定的备用图标的应用程序的图标。


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)

本文介绍如何使用一种特殊类型的情节提要为每个 iOS 设备大小和分辨率提供通用的启动屏幕。

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[自定义文档类型](~/ios/app-fundamentals/images-icons/custom-document-types.md)

此文章介绍如何包括和管理一个 Xamarin.iOS 应用程序中的图像资产，用作自定义文档类型的图标。



## <a name="related-links"></a>相关链接

- [使用图像 （示例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和映像创建指南](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
