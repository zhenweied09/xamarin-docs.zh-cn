---
title: Xamarin.Forms 手势
description: 本指南说明如何使用 Xamarin.Forms 的手势识别器来检测 Xamarin.Forms 应用程序中的用户与视图交互。
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106177"
---
# <a name="xamarinforms-gestures"></a>Xamarin.Forms 手势

_笔势识别器可以用于检测在 Xamarin.Forms 应用程序中的用户与视图交互。_

Xamarin.Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer)类上支持点击、 挤压、 平移和轻扫手势[ `View` ](xref:Xamarin.Forms.View)实例。

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[添加的点击手势识别程序](tap.md)

点击手势用于点击检测和识别的[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)类。

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[添加捏合手势识别器](pinch.md)

收缩手势用于执行交互式缩放和的可识别[ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer)类。

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[添加平移手势识别器](pan.md)

平移手势用于检测的手指在屏幕上四处移动，并将该移动应用到的内容，并识别与[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)类。

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[添加轻扫手势识别器](swipe.md)

轻扫手势手指在水平或垂直方向，在屏幕上移动，并且通常用于初始化通过内容导航时发生。 轻扫手势识别具有[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)类。
