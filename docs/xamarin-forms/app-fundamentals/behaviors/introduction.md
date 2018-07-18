---
title: 行为简介
description: 行为使您可以将功能添加到用户界面控件，而它们不必子类。 相反，该功能是行为类中实现并附加到控件，就像它是控件本身的一部分。 本文介绍的行为。
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 176f41d4b7349af2cf7cc49de8ba0789ad2f8c11
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995809"
---
# <a name="introduction-to-behaviors"></a>行为简介

_行为使您可以将功能添加到用户界面控件，而它们不必子类。相反，该功能是行为类中实现并附加到控件，就像它是控件本身的一部分。本文介绍的行为。_

行为，可实现您通常必须编写为代码隐藏中，因为它直接与 API 中，它可以是用于简明地附加到控件，并且跨多个应用程序打包以供重复使用的方式的控件进行交互的代码。 它们可用于提供一系列完整的功能提供给控件，例如：

- 添加到电子邮件验证程序[ `Entry` ](xref:Xamarin.Forms.Entry)。
- 创建分级控件使用的点击手势识别程序。
- 控制动画。
- 添加到控件的效果。

行为还可实现更高级的方案。 中的上下文*发出命令*，行为是用于连接到命令的控件很有用的方法。 此外，它们可以用于将命令与不设计与命令进行交互的控件相关联。 例如，它们可用于调用以响应事件触发命令。

Xamarin.Forms 支持两种不同样式的行为：

- **Xamarin.Forms 行为**– 派生的类[ `Behavior` ](xref:Xamarin.Forms.Behavior)或[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类，其中`T`是该控件的类型行为应该应用。 有关 Xamarin.Forms 行为的详细信息，请参阅[Xamarin.Forms 行为](~/xamarin-forms/app-fundamentals/behaviors/creating.md)并[可重用行为](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。
- **附加行为**–`static`具有一个或多个附加属性的类。 有关附加行为的详细信息，请参阅[附加行为](~/xamarin-forms/app-fundamentals/behaviors/attached.md)。

本指南重点 Xamarin.Forms 行为，因为它们是行为构造的首选的方法。



## <a name="related-links"></a>相关链接

- [行为](xref:Xamarin.Forms.Behavior)
- [行为&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
