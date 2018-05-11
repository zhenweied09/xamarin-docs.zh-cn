---
title: 构建跨平台应用程序
description: 摘要以及六个部件，本部分讨论如何使用 Xamarin 开发平台-了解如何 Xamarin 的工作设计移动应用程序，然后测试并将部署到各种应用程序存储从生成应用程序。
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: fba13ab921949cd2361e78535d5ffc96952a1336
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="sharing-code-options"></a>共享代码选项

有用于跨平台移动应用程序间共享代码的两个选项： 共享资产项目和可移植类库。 这些选项[此处讨论](~/cross-platform/app-fundamentals/code-sharing.md); 的详细信息[可移植类库](~/cross-platform/app-fundamentals/pcl.md)和[共享项目](~/cross-platform/app-fundamentals/shared-projects.md)也是可用。

<a name="Sections" />

## <a name="building-cross-platform-mobile-apps"></a>构建跨平台移动应用程序

 [概述](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [第 1 – 了解 Xamarin 移动平台部分](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [第 2 部分 – 体系结构](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [第 3 – 部分设置 Xamarin 跨平台解决方案](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [第 4 – 部分处理多个平台](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [第 5 – 实际的代码共享策略部分](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [第 6 部分 - 测试和应用商店审批](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />


## <a name="case-studies"></a>案例研究

本文档中概述的原则放入示例应用程序中的做法*Tasky*，以及[预生成过应用程序](https://xamarin.com/prebuilt)如[Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm)。

 <a name="Tasky" />


### <a name="tasky"></a>Tasky

Tasky 是用于 iOS、 Android 和 Windows Phone 的简单待办事项列表应用程序。
它演示如何使用 Xamarin 创建跨平台应用程序的基础知识，并使用一个本地的 SQLite 数据库。

 [![tasky 列表](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![tasky 列表](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

读取[Tasky 案例研究](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)。


## <a name="summary"></a>总结

本部分介绍了 Xamarin 的应用程序开发工具，并讨论了如何生成面向多个移动平台的应用程序。

它介绍分层体系结构，请重复使用该结构代码跨多个平台，并描述可在该体系结构内的不同软件模式。

（如文件和网络操作） 的常见应用程序功能和如何它们可以建立方式跨平台的给出了示例。

最后，它简要讨论测试，并提供对案例研究，以将这些原则放入操作的引用。



## <a name="related-links"></a>相关链接

- [共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 示例应用程序 (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Xamarin 移动应用程序开发： 跨平台 C# 和 Xamarin.Forms 基础知识](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/))
- [使用 C# 通过 Greg 进行移动开发桎梏 (O'Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [在 C# 中由 Scott Olson、 John 搜寻、 Ben Horgen，Kenny Goers (Wrox) 专业人员跨平台移动开发](http://www.wiley.com/WileyCDA/WileyTitle/productCd-1118157702.html)
