---
title: Xamarin.Forms 快速呈现器
description: 本文介绍快速呈现器，可将减少通货膨胀和 Xamarin.Forms 控件在 Android 上的呈现成本平展生成的本机控件层次结构。
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 40cc095da41aaae5cb474987d8b03f7cf4a17322
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243056"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.Forms 快速呈现器

_本文介绍快速呈现器，可将减少通货膨胀和 Xamarin.Forms 控件在 Android 上的呈现成本平展生成的本机控件层次结构。_

传统上，在 Android 上原始控件呈现器的大多数组成两个视图：

- 一个本机控件，如`Button`或`TextView`。
- 容器`ViewGroup`，处理一些布局工作、 笔势处理以及其他任务。

但是，此方法具有性能影响两个视图创建为每个逻辑的控件，这会导致更复杂的可视化树需要更多内存，以及更多处理，以在屏幕上呈现的。

快速的呈现器减少的通货膨胀和 Xamarin.Forms 控件呈现成本成单个视图。 因此，而不被创建两个视图，并将它们添加到视图树，创建只有一个。 这提高了性能，通过创建更少的对象，这反过来意味着不太复杂的视图树，并减少内存使用情况 （这也会导致更少的垃圾回收暂停时间）。

快速的呈现器是可用于在 Android 上 Xamarin.Forms 2.4 中的以下控件：

- [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)
- [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)
- [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)

就功能而言，这些快速的呈现器并没有什么区别到原始的呈现器。 但是，它们是当前实验，可以仅能通过添加以下代码的行你`MainActivity`之前调用的类`Forms.Init`:

```csharp
Forms.SetFlags("FastRenderers_Experimental");
```

> [!NOTE]
> 因此将忽略此设置之前应用 compat 活动，都仅适用于应用程序兼容 Android 后端，快速呈现器。

每个应用程序，取决于布局的复杂性将不同的性能改进。 例如，在当滚动时可能是性能改进的 x2 [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)包含数千行的数据，其中每个行中的单元格进行的使用快速呈现器的控件，这会导致明显生成更平滑滚动。


## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
