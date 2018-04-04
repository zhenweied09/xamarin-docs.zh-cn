---
title: 行为简介
description: 行为允许你添加到用户界面控件的功能，而无子类化它们。 相反，功能是行为类中实现并附加到控件，就像它是该控件本身的一部分。 本文介绍行为。
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: b5aa0d3de7092ac87d511ab8d59c329471fa6a28
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-behaviors"></a>行为简介

_行为允许你添加到用户界面控件的功能，而无子类化它们。相反，功能是行为类中实现并附加到控件，就像它是该控件本身的一部分。本文介绍行为。_

行为，可以实现你通常需要编写作为代码隐藏，因为它直接使用 API，它可以确切地附加到控件和跨多个应用程序打包以供重复使用的方式控件进行交互的代码。 它们可以用于提供各种功能到控件，例如：

- 添加到电子邮件验证程序[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)。
- 创建使用点击手势识别器分级控件。
- 动画的控制。
- 向控件添加效果。

行为还启用更高级的方案。 上下文中*命令*，行为是将控件连接到命令提供有用的方法。 此外，它们可以用于将命令与不设计与命令进行交互的控件相关联。 例如，它们可以用于调用中对事件触发做出响应的命令。

Xamarin.Forms 支持两种不同样式的行为：

- **Xamarin.Forms 行为**– 派生自的类[ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)或[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)类，其中`T`是该控件的类型行为应该应用。 有关各种 Xamarin.Forms 行为的详细信息，请参阅[Xamarin.Forms 行为](~/xamarin-forms/app-fundamentals/behaviors/creating.md)和[可重用行为](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。
- **附加行为**–`static`具有一个或多个附加属性的类。 有关附加行为的详细信息，请参阅[附加行为](~/xamarin-forms/app-fundamentals/behaviors/attached.md)。

本指南重点 Xamarin.Forms 行为，因为它们是与行为构造是首选的方法。



## <a name="related-links"></a>相关链接

- [Behavior](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [行为<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
