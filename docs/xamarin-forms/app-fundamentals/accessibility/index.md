---
title: Xamarin.Forms 可访问性
description: 构建可访问的应用程序可确保应用程序是否可由方法通过一系列需求和体验的用户界面的人员。
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: ac0ffbdce6b0c55e8ad9d774d80e3d9b8bf84089
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116441"
---
# <a name="xamarinforms-accessibility"></a>Xamarin.Forms 可访问性

_构建可访问的应用程序可确保应用程序是否可由方法通过一系列需求和体验的用户界面的人员。_

使 Xamarin.Forms 应用程序可访问意味着考虑的布局和设计的许多用户界面元素。 要考虑的问题的指导原则，请参阅[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。 Xamarin.Forms Api 已可解决很多可访问性问题，如大字体和合适的颜色和对比度设置。

[Android 可访问性](~/android/app-fundamentals/accessibility.md)并[iOS 可访问性](~/ios/app-fundamentals/accessibility.md)指南包含的 Xamarin，公开的本机 Api 的详细信息并[UWP MSDN 上的可访问性指南](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information)说明在该平台上的本机方法。 这些 Api 用于完全实现每个平台上的可访问应用程序。

当前不具有 Xamarin.Forms*内置*支持所有可访问性 Api 适用于每个基础平台。 但是，它支持设置自动化属性在用户界面元素以支持屏幕读取器和导航帮助工具，这是构建可访问应用程序的最重要的部分之一。 有关详细信息，请参阅[自动化属性](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)。

Xamarin.Forms 应用程序还可以指定的控件的 tab 键顺序。 有关详细信息，请参阅[的键盘导航](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)。

其他辅助功能 Api (如[在 iOS 上的 PostNotification](~/ios/app-fundamentals/accessibility.md)) 可能会更好地适合于[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)或[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)实现。 这些不是在本指南中介绍。

## <a name="testing-accessibility"></a>测试辅助功能

Xamarin.Forms 应用程序通常针对多个平台，这意味着测试根据平台的可访问性功能。 单击这些链接可了解如何测试每个平台上的辅助功能：

- [**iOS 测试**](~/ios/app-fundamentals/accessibility.md)
- [**Android 测试**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>相关链接

- [跨平台可访问性](~/cross-platform/app-fundamentals/accessibility.md)
- [自动化属性](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [键盘辅助功能](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)
