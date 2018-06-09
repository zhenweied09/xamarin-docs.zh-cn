---
title: 第 1 章的摘要。 Xamarin.Forms 如何组合？
description: 使用 Xamarin.Forms 创建移动应用： Chapter 1 的摘要。 Xamarin.Forms 如何组合？
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 2897229b0749b1a6ead805d6ad063603a77f8f0d
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240455"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>第 1 章的摘要。 Xamarin.Forms 如何组合？

在编程中的最令人不快作业之一移植的从一个平台到另一个，基本代码，尤其是当该平台涉及不同的编程语言。 没有倾向做法移植，重构代码时，但如果并行必须保留这两个平台，然后两个代码库之间的差异将使将来维护更为困难。

## <a name="cross-platform-mobile-development"></a>跨平台移动开发

此问题是常见的在针对移动平台。 当前，存在两个主要的移动平台、 Iphone 和 Ipad 运行 iOS 操作系统，并在各种手机和平板电脑运行 Android 操作系统的 Apple 系列。 另一个重要的平台是 Microsoft 的通用 Windows 平台 (UWP)，它允许单个程序以面向 Windows 10 和 Windows 10 移动版。

想要面向这些三个平台的软件供应商必须处理不同的用户界面范例、 三个不同的开发环境、 三个不同的编程接口，和&mdash;可能是最困难&mdash;这三种不同的编程语言： OBJECTIVE-C 的 iPhone 和 iPad，对于 Android，Java 和用于 Windows 的 C#。

## <a name="the-c-and-net-solution"></a>C# 和.NET 解决方案

尽管 OBJECTIVE-C、 Java 和 C# 将所有派生自 C 编程语言，但它们已演变通过非常不同的路径。 C# 是最新的一种语言和已成熟非常有用的方式。 此外，C# 是紧密关联与调用.NET，数学、 调试、 反射、 集合、 全球化、 文件 I/O、 网络、 安全、 线程、 web 服务、 数据处理和 XML 提供支持整个编程基础结构和JSON 读取和写入。

Xamarin 当前提供工具以面向本机 Mac、 iOS 和 Android Api 使用 C# 和.NET。 这些工具称为 Xamarin.Mac、 Xamarin.iOS 和 Xamarin.Android，统称为 Xamarin 平台。 这些是库和 express.NET 惯例这些平台的本机 Api 的绑定。

开发人员可以使用 Xamarin 平台在 C# 中编写应用程序，该目标 Mac、 iOS 或 Android。 但是，如果目标多个平台，就可以很有意义共享一些在目标平台的代码。 这涉及到将程序分离到平台相关代码 （通常涉及用户界面） 和独立于平台的代码，这通常要求只是基本的.NET framework。 此独立于平台的代码也可以驻留在可移植类库 (PCL) 或共享的项目，通常称为共享资产项目或 SAP。

## <a name="introducing-xamarinforms"></a>Xamarin.Forms 简介

如果目标多个移动平台，Xamarin.Forms 允许更多代码共享。 为 Xamarin.Forms 编写的单个程序可以面向五个不同的平台：

- iOS 的 iPhone、 iPad 和 iPod touch 运行的程序
- 在 Android 手机和平板电脑上运行的程序的 android
- 通用 Windows 平台到目标 Windows 10 和 Windows 10 移动版
- Windows 8.1 的 Windows 运行时 API
- Windows Phone 8.1 的 Windows 运行时 API

当前的 Xamarin.Forms 解决方案模板不包括适用于 Windows 8.1 和 Windows Phone 8.1 平台的项目模板。

PCL 或 SAP 中存在的 Xamarin.Forms 程序大容量。 每个平台包含调入 PCL 一个小应用程序，存根。 Xamarin.Forms Api 将映射到每个平台上的本机控件，以便每个平台维护其特征的外观和感觉：

[![平台共享的视觉对象的三个屏幕截图](images/ch01fg03-small.png "每个平台上的 Xamarin.Forms 控件")](images/ch01fg03-large.png#lightbox "Xamarin.Forms 每个平台上的控件")

从左到右的屏幕截图显示了 iPhone、 Android 手机和 Windows 10 移动电话。 每个屏幕上的页面包含 Xamarin.Forms [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)用于显示文本， [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)适用于启动操作， [ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)为选择一个开/关的值，和一个[ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)用于指定连续范围内的值。 这些视图的所有四个是子级[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)上[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)。

此外附加到该网页是几个包含一个 Xamarin.Forms 工具栏[ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)对象。 这些是显示为图标的 iOS 和 Android 屏幕顶部和底部的 Windows 10 移动版屏幕。

Xamarin.Forms 还支持 XAML，Microsoft 在开发用于多个应用程序平台的可扩展应用程序标记语言。 上面所示的程序的所有视觉对象在 XAML 中定义，如中所示[ **PlatformVisuals** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)示例。

Xamarin.Forms 程序可以确定哪些平台运行，并相应地执行不同的代码。 更强大，开发人员可以编写用于各种平台自定义代码，并独立于平台的方式从 Xamarin.Forms 程序运行该代码。 开发人员还可以通过编写为每个平台的呈现器中创建其他控件。

虽然 Xamarin.Forms 是一个不错的解决方案，对于业务线应用程序，或原型制作，或者使快速概念证明演示，是不太适合用于需要矢量图形或复杂触摸交互的应用程序。

## <a name="your-development-environment"></a>你的开发环境

什么想要面向的平台和什么机你想要使用取决于你的开发环境。

如果你要面向 iOS，你将需要安装了 Xcode 和 Xamarin 平台安装的 Mac。 也支持 Android 需要安装 Java 和所需的 Sdk。 您可以指定 iOS 和 Android 的 Visual Studio 用于 mac。

安装 Visual Studio 可以在电脑上面向 iOS、 Android 和所有 Windows 平台。 但是，面向 iOS 从 Visual Studio 仍需要安装了 Xcode 和 Xamarin 平台安装的 Mac。

你可以测试任一实际设备上连接了 USB 到计算机，或在模拟器上的程序。

## <a name="installation"></a>安装

创建在和之前生成 Xamarin.Forms 应用程序，你应尝试创建单独生成 iOS 应用程序、 Android 应用程序，和 UWP 应用程序，具体取决于你想目标并开发环境的平台。

在 Xamarin 和 Microsoft 网站包含有关如何执行此操作：

- [IOS 入门](~/ios/get-started/index.md)
- [Android 入门](~/android/get-started/index.md)
- [Windows 开发人员中心](http://dev.windows.com)

一次可以创建和运行于这些单个平台的项目，你应具有创建和运行 Xamarin.Forms 应用程序没有问题。



## <a name="related-links"></a>相关链接

- [第 1 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [第 1 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
