---
title: 示例集成
description: 此文档链接到演示 Xamarin 工作簿集成的示例。 链接的示例适用于表示形式呈现和 SkiaSharp。
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 75d88af4c294a35d45f05724eb96ce822cddf126
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793966"
---
# <a name="sample-integrations"></a>示例集成

请参阅[厨房接收器][ KitchenSink]示例有关工作示例的集成。 只需生成`KitchenSink.sln`Visual Studio 中用于 Mac 或 Visual Studio，然后打开`KitchenSink.workbook`。

[![厨房接收器集成屏幕快照](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

厨房接收器示例演示了这两组概念：

* 表示部分演示如何使用`RepresentationManager`若要通过使用内置的表示形式之间实现增强呈现。
* `Person`对象和其关联的 JavaScript 呈现器演示如何使用`ISerializableObject`而无需通过表示提供程序。

另请参阅[SkiaSharp] [ skiasharp]为使用现有集成的一个实际示例[表示形式之间实现](~/tools/workbooks/sdk/representations.md)由 Xamarin 工作簿以呈现其类型。

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
