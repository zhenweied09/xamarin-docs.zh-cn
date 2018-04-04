---
title: Windows 平台功能
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: ab6b12738028b4f3439629f334ed5429244f4d5a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="windows-platform-features"></a>Windows 平台功能

开发用于 Windows 平台的 Xamarin.Forms 应用程序需要 Visual Studio。 [要求页](~/xamarin-forms/get-started/installation.md)包含有关无人参与的详细信息。

![](images/allhanselman.png "在 Windows 上运行的 Xamarin.Forms 应用程序")

## <a name="platform-support"></a>平台支持

默认情况下，可在 Visual Studio 中的 Xamarin.Forms 模板包含一个 Windows 项目：

* **通用 Windows 平台应用**-Xamarin.Forms 应用还可以针对 Windows 10 进行优化。 通用 (UWP) 应用程序可以在电话、 平板电脑和桌面版设备上运行。

如果你已在 Visual Studio 安装正确开发选项，还有可能为[添加](installation/index.md)这些项目类型以支持较旧版本的 Windows:

* **Windows 8.1** -你可以部署 Xamarin.Forms 应用以平板和桌面外形规格为 Windows 8.1 应用项目使用 WinRT 控件。
* **Windows Phone 8.1** -Xamarin.Forms 具有对使用 WinRT 的 Windows Phone 8.1 平台的完全支持。 使用 Windows Phone 8.1 支持的应用的外观和感觉可能不同于基于 Silverlight 的早期 Xamarin.Forms Windows Phone 应用。


> [!NOTE]
> Xamarin.Forms 1.x、 2.x 支持_Windows Phone 8 Silverlight_应用程序开发，但是已弃用此项目类型。


## <a name="getting-started"></a>入门

转到**文件 > 新建 > 项目**Visual Studio 中，选择之一**跨平台 > 空白应用 (Xamarin.Forms)**模板吧。

较旧的 Xamarin.Forms 解决方案，或在 macOS 上, 创建的那些不会上面列出的所有 Windows 项目 （但它们需要手动添加）。
如果你想要面向 Windows 平台尚未在解决方案中，访问时重新[安装说明进行操作](installation/index.md)添加所需的 Windows 项目类型/s。


## <a name="samples"></a>示例

[所有这些示例](https://github.com/xamarin/xamarin-forms-book-preview-2)的 Charles Petzold 本书[*具有 Xamarin.Forms 创建移动应用*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)包括 Windows Phone 8.1、 Windows 8.1 和 （适用于 Windows 10) 的通用 Windows 平台项目。

["Scott Hanselman"演示应用](https://github.com/jamesmontemagno/Hanselman.Forms)单独，并且还包括 Apple Watch 和 Android 磨损项目 （分别使用 Xamarin.iOS 和 Xamarin.Android，Xamarin.Forms 不会不运行这些平台上）。


## <a name="related-links"></a>相关链接

- [安装 Windows 项目](~/xamarin-forms/platform/windows/installation/index.md)
