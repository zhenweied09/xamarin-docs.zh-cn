---
title: 效果
description: 使用目标平台，允许 Xamarin.Forms 应用程序保留了适当的外观和感觉，为每个平台的本机控件呈现 Xamarin.Forms 用户界面。 效果，而无需采用自定义呈现器实现自定义每个平台上的本机控件。
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 63d7750294321a28dbde833f72fe6b7277ada397
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="effects"></a>效果

_使用目标平台，允许 Xamarin.Forms 应用程序保留了适当的外观和感觉，为每个平台的本机控件呈现 Xamarin.Forms 用户界面。效果，而无需采用自定义呈现器实现自定义每个平台上的本机控件。_

## <a name="introduction-to-effectsintroductionmd"></a>[效果简介](introduction.md)

效果允许进行自定义，每个平台上的本机控件，通常用于小样式更改。 本文提供效果的简介，概述了效果和自定义呈现器之间的边界，并描述`PlatformEffect`类。

## <a name="creating-an-effectcreatingmd"></a>[创建效果](creating.md)

效果简化控件的自定义项。 本文演示如何创建更改的背景色的效果[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控件时控件获得焦点。

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[将参数传递给效果](passing-parameters/index.md)

创建配置，可通过参数的效果使要重用的效果。 这些文章演示如何使用属性来将参数传递给效果，并更改在运行时参数。

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[调用从效果的事件](touch-tracking.md)

效果可以调用的事件。 这篇文章演示如何创建实现低级别的多点触控手指跟踪并向用于触摸按可实现、 移动和版本的应用程序的信号的事件。