---
title: "提供 Haptic 反馈"
description: "本文介绍如何在 iOS 10 和如何在 Xamarin.iOS 中实现它们中可用的 haptic 反馈的新类型。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 9f4eb989fe0c91471c9473c512c4befd36e4ace2
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="providing-haptic-feedback"></a>提供 Haptic 反馈

_本文介绍如何在 iOS 10 和如何在 Xamarin.iOS 中实现它们中可用的 haptic 反馈的新类型。_

<a name="Overview" />

## <a name="overview"></a>概述

在 iPhone 7 和 iPhone 7 加、 Apple 具有包含提供其他方式来以物理方式面向用户的新 haptic 响应。 Haptic 反馈 （通常简称为 Haptics） 在用户界面设计使用触摸 （通过强制执行、 振动或移动） 的理解。 使用这些新的边用反馈选项以获取用户的注意并强调其操作。

将详细介绍以下主题：

- [有关 Haptic 反馈](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>有关 Haptic 反馈

几个内置的 UI 元素已提供 haptic 反馈如选取器、 交换机和滑块。 iOS 10 现在添加了以编程方式触发 haptics 使用的具体子类的功能`UIFeedbackGenerator`类。

开发人员可以使用以下项之一`UIFeedbackGenerator`子类以编程方式触发器 haptic 反馈：

- `UIImpactFeedbackGenerator` -使用此反馈生成器可补充操作或任务如提供"thud"视图将滑入到位时或如果两个屏幕上的对象发生冲突。
- `UINotificationFeedbackGenerator` -使用此反馈生成器例如操作完成、 失败或任何其他类型的警告通知。
- `UISelectionFeedbackGenerator` -使用此反馈生成器主动如选取列表中的一个项更改选择的。

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

使用此反馈生成器来补充操作或任务如提供"thud"视图将滑入到位时或如果两个屏幕上的对象发生冲突。

使用触发器影响反馈下面的代码：

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

当开发人员创建的新实例`UIImpactFeedbackGenerator`它们提供的类`UIImpactFeedbackStyle`指定为反馈的强度：

- `Heavy`
- `Medium`
- `Light`

`Prepare`方法`UIImpactFeedbackGenerator`调用以通知系统 haptic 反馈即将发生，以便它可以尽量减少延迟。

`ImpactOccurred`方法然后触发 haptic 反馈。

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

使用此反馈生成器，例如操作完成、 失败或任何其他类型的警告通知。

使用触发器通知反馈下面的代码：

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

新实例`UINotificationFeedbackGenerator`创建类并将其`Prepare`方法被调用以通知系统 haptic 反馈即将发生，以便它可以尽量减少延迟。

`NotificationOccurred`调用以触发 haptic 反馈给定`UINotificationFeedbackType`的：

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

使用此反馈生成器主动如选取列表中的一个项更改选择的。

使用触发器选择反馈下面的代码：

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

新实例`UISelectionFeedbackGenerator`创建类并将其`Prepare`方法被调用以通知系统 haptic 反馈即将发生，以便它可以尽量减少延迟。

`SelectionChanged`方法然后触发 haptic 反馈。

## <a name="summary"></a>摘要

本文已覆盖 haptic 反馈位于 iOS 10 和如何在 Xamarin.iOS 中实现它们的新类型。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
