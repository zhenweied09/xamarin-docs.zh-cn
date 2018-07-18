---
title: Xamarin.Forms 效果
description: 效果允许进行自定义，而不必求助于自定义呈现器实现每个平台上的本机控件。
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 9d859377c40c6fca07e140c50da46d8f30aaae04
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994453"
---
# <a name="xamarinforms-effects"></a>Xamarin.Forms 效果

_使用目标平台，允许保留每个平台相应的外观和感觉的 Xamarin.Forms 应用程序的本机控件呈现 Xamarin.Forms 的用户界面。效果允许进行自定义，而不必求助于自定义呈现器实现每个平台上的本机控件。_

## <a name="introduction-to-effectsintroductionmd"></a>[效果简介](introduction.md)

效果允许进行自定义，每个平台上的本机控件，通常用于较小的样式更改。 本文介绍了影响，概述了影响与自定义呈现器之间的边界，并介绍了`PlatformEffect`类。

## <a name="creating-an-effectcreatingmd"></a>[创建一种效果](creating.md)

效果简化控件的自定义项。 本文演示如何创建更改的背景色的效果[ `Entry` ](xref:Xamarin.Forms.Entry)控制当控件获得焦点。

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[将参数传递给效果](passing-parameters/index.md)

创建通过参数配置的效果使要重复使用的效果。 这些文章演示了如何使用属性将参数传递给某个效果，请和更改在运行时参数。

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[调用影响来自事件](touch-tracking.md)

效果可以调用的事件。 本文介绍如何创建实现低级别多点触控手指跟踪并向发出信号的触摸屏输入按下、 移动和版本的应用程序的事件。
