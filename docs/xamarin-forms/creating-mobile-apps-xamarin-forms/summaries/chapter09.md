---
title: 第 9 章的摘要。 特定于平台的 API 调用
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a988c207185fa2305631be67bdd628de089d247e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>第 9 章的摘要。 特定于平台的 API 调用

另外，有时需要运行的平台的可更改某些代码。 此章节介绍技术。

## <a name="preprocessing-in-the-shared-asset-project"></a>预处理共享的资产项目中

Xamarin.Forms 共享资产项目可以执行不同的代码，为每个平台使用 C# 预处理器指令`#if`， `#elif`，和`endif`。 此进行了演示[ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![变量的三个屏幕截图格式化段落](images/ch09fg01-small.png "设备型号和操作系统")](images/ch09fg01-large.png#lightbox "设备型号和操作系统")

但是，得到的代码可以是繁琐且难以读取。

## <a name="parallel-classes-in-the-shared-asset-project"></a>共享资产项目中的并行类

在 SAP 中执行特定于平台的代码更结构化的方法进行了演示[ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2)示例。 每个平台项目包含一个具有相同名称的类和方法，但该特定平台的实现。 SAP 然后只需实例化类，并调用的方法。

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService 和可移植类库

库通常无法访问应用程序项目中的类。 此限制似乎阻止中所示的技术**PlatInfoSap2**从正在使用在 PCL 中。 但是，Xamarin.Forms 包含一个名为类[ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) ，使用.NET 反射来从 PCL 访问应用程序项目中的公共类。

必须定义 PCL`interface`与它需要使用每个平台中的成员。 然后，每个平台包含该接口的实现。 实现接口的类必须使用标识[DependencyAttribute](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyAttribute/)程序集级别上。

PCL 然后使用泛型[ `Get` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DependencyService.Get{T}/p/Xamarin.Forms.DependencyFetchTarget/)方法`DependencyService`获取平台类实现该接口的实例。

此进行了演示[ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo)示例。

## <a name="platform-specific-sound-generation"></a>特定于平台的声音生成

[ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound)示例添加到鸣音**MonkeyTap**通过访问每个平台中的声音生成功能的程序。



## <a name="related-links"></a>相关链接

- [第 9 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [第 9 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [依赖项服务](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
