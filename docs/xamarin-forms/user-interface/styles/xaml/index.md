---
title: 使用 XAML 样式的样式设置 Xamarin.Forms 应用
description: 本指南介绍如何通过使用 XAML 样式自定义 Xamarin.Forms 应用程序的外观。
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 5145572b30302e58c36250fff40e8b637fcd221f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995070"
---
# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>使用 XAML 样式的样式设置 Xamarin.Forms 应用

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

Xamarin.Forms 应用程序通常包含多个具有相同的外观的控件。 设置每个控件的外观会重复且容易出错。 相反，样式可以创建自定义控件外观的分组和可用的控件类型的设置属性。

## <a name="explicit-stylesexplicitmd"></a>[显式样式](explicit.md)

*显式*样式是指通过设置有选择地应用于控件及其[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)属性。

## <a name="implicit-stylesimplicitmd"></a>[隐式样式](implicit.md)

*隐式*样式是指由相同的所有控件[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)，而无需每个控件以引用样式。

## <a name="global-stylesapplicationmd"></a>[全局样式](application.md)

样式可提供全局添加到应用程序的[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 这有助于避免跨页或控件的样式的重复项。

## <a name="style-inheritanceinheritancemd"></a>[样式继承](inheritance.md)

样式可以继承其他样式以减少重复和使重复使用。

## <a name="dynamic-stylesdynamicmd"></a>[动态样式](dynamic.md)

样式，不要响应属性更改和应用程序的持续时间内保持不变。 但是，应用程序可以响应在运行时动态样式更改通过使用动态资源。

## <a name="device-stylesdevicemd"></a>[设备样式](device.md)

Xamarin.Forms 具有六*动态*样式，称为*设备*样式，在[ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles)类。 所有六个样式可应用于[ `Label` ](xref:Xamarin.Forms.Label)仅限实例。
