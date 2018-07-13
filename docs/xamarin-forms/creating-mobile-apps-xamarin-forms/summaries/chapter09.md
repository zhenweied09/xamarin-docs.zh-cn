---
title: 第 9 章的摘要。 特定于平台的 API 调用
description: 使用 Xamarin.Forms 创建移动应用： 摘要的第 9 章。 特定于平台的 API 调用
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 8a035da3dec468df291a19849ca89964c6707589
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994752"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>第 9 章的摘要。 特定于平台的 API 调用

此外，有时需要按平台运行一些代码，而异。 这一章介绍了一些技巧。

## <a name="preprocessing-in-the-shared-asset-project"></a>预处理共享的资产项目中

Xamarin.Forms 共享资产项目可以执行不同的代码，为每个平台使用 C# 预处理器指令`#if`， `#elif`，和`endif`。 了这一点[ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![变量的三个屏幕快照格式的段落](images/ch09fg01-small.png "设备型号和操作系统")](images/ch09fg01-large.png#lightbox "设备型号和操作系统")

但是，生成的代码可能难以理解且难以阅读。

## <a name="parallel-classes-in-the-shared-asset-project"></a>共享资产项目中的并行类

在 SAP 中执行特定于平台的代码的更多结构化的方法进行了演示[ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2)示例。 每个平台项目包含一个具有相同名称的类和方法，但该特定平台的实现。 将 SAP 然后只需实例化类，并调用方法。

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService 和可移植类库

一种库通常无法访问应用程序项目中的类。 此限制似乎阻止中所示的技术**PlatInfoSap2** PCL 中使用。 但是，Xamarin.Forms 包含名为的类[ `DependencyService` ](xref:Xamarin.Forms.DependencyService) ，使用.NET 反射 PCL 中访问应用程序项目中的公共类。

在 PCL 中必须定义`interface`与需要使用每个平台中的成员。 然后，每个平台包含该接口的实现。 实现接口的类必须带有[DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute)程序集级别上。

然后，在 PCL 中使用泛型[ `Get` ](xref:Xamarin.Forms.DependencyService.Get*)方法的`DependencyService`以获取实现该接口的平台类的实例。

了这一点[ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo)示例。

## <a name="platform-specific-sound-generation"></a>特定于平台的发声功能

[ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound)示例添加到嘟嘟声**MonkeyTap**通过访问每个平台中的发声功能进行编程。



## <a name="related-links"></a>相关链接

- [第 9 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [第 9 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [依赖关系服务](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
