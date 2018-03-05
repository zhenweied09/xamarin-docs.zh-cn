---
title: "可访问性"
description: "生成可访问应用程序可确保应用程序可由的方法与范围的需求和经验的用户界面的人员。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 54ca72669926822e84cdb96b5195e7cffbe39b52
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="accessibility"></a>可访问性

_生成可访问应用程序可确保应用程序可由的方法与范围的需求和经验的用户界面的人员。_

使 Xamarin.Forms 应用程序具备辅助功能意味着考虑的布局和设计的许多用户界面元素。 有关要考虑的问题的指南，请参阅[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。 已可以通过 Xamarin.Forms Api 寻址如大字体，以及合适的颜色和对比度设置的许多可访问性问题。

[Android 可访问性](~/android/app-fundamentals/accessibility.md)和[iOS 可访问性](~/ios/app-fundamentals/accessibility.md)指南包含由 Xamarin，公开的本机 Api 的详细信息和[MSDN 上的 UWP 可访问性指南](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information)说明在该平台上的本机方法。 这些 Api 用于完全实现每个平台上的可访问应用程序。

当前不具有 Xamarin.Forms*内置*支持所有 Api 可用各个基础平台上的可访问性。 但是，它支持设置可访问性值在用户界面元素以支持屏幕读取器和导航协助工具，这是一个生成可访问应用程序的最重要的部分。 有关详细信息，请参阅[用户界面元素上的设置可访问性值](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md)。

其他可访问性 Api (如[在 iOS 上的 PostNotification](~/ios/app-fundamentals/accessibility.md)) 可能会更适合于[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)或[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)实现。 本指南中未涉及这些。

## <a name="testing-accessibility"></a>测试辅助功能

Xamarin.Forms 应用程序通常针对多个平台，这意味着测试根据平台的辅助功能。 单击下面的链接了解如何将测试每个平台上的辅助功能：

- [**iOS 测试**](~/ios/app-fundamentals/accessibility.md)
- [**Android 测试**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)


## <a name="related-links"></a>相关链接

- [跨平台可访问性](~/cross-platform/app-fundamentals/accessibility.md)
- [在用户界面元素上设置可访问性值](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md)
