---
title: Xamarin.Forms 视觉对象
description: 本文介绍了 Xamarin.Forms 视觉对象，在 iOS 和 Android 相同，或很大程度上相同，将视图呈现。
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2018
ms.openlocfilehash: df2351e35817a6468fed236752eea8e5ad11024f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061881"
---
# <a name="xamarinforms-visual"></a>Xamarin.Forms 视觉对象

![预览](~/media/shared/preview.png)

_本文介绍了 Xamarin.Forms 视觉对象，在 iOS 和 Android 相同，或很大程度上相同，将视图呈现。_

许多开发人员想要创建 iOS 和 Android 上 Xamarin.Forms 应用程序看起来完全相同，或大致相同。 Xamarin.Forms 4.0 pre1 包括包括实现可视外观，从而选择启用通过外观的应用程序的其他呈现器的机制`Visual`属性：

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>    
```

实现的可视外观的呈现器然后习惯于呈现器视图，而不是默认呈现器。 在呈现器所选内容时，`Visual`检查并呈现器选择过程中包含在视图的属性。 此外，如果`Visual`属性更改时在运行时，指定的呈现器时都会重新创建任何子级。

> [!IMPORTANT]
> `Visual`属性中定义`VisualElement`类，与视图继承`Visual`从其父级的属性值。 因此，设置`Visual`属性上的`ContentPage`可确保任何支持的视图页中将使用该可视外观。 此外，`Visual`属性可以重写上一个视图。

Xamarin.Forms 4.0 pre1 包括基于材料设计，并以名为材料呈现器呈现器的实验性的可视外观。 材料呈现器有当前包括 iOS 和 Android 上的以下视图：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)

就功能而言，材料呈现器是不不同的默认呈现器。 但是，当前处在试验阶段，仅可以通过添加以下代码行来使用你`AppDelegate`类在 iOS 上，或为你`MainActivity`类在 Android 上，然后才能调用`Forms.Init`:

```csharp
Forms.SetFlags("Visual_Experimental");
```

此外，在 iOS 上，你平台的项目必须具有[Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents/)安装 NuGet 包。 在 Android 上，视觉对象仅适用于 API 29，平台项目必须使用 v28 支持库，并设置其要继承材料组件主题或继续继承 AppCompat 主题，同时将一些新的主题属性添加到主题的主题。 有关详细信息，请参阅[适用于 Android 入门材料组件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

以下屏幕截图显示的用户界面中包括哪些材料呈现器已存在，但使用的默认呈现器呈现的四个视图：

[![默认呈现器](visual-images/default-renderers.png "视图使用默认呈现器")](visual-images/default-renderers-large.png#lightbox)

以下屏幕截图显示使用材料呈现器呈现相同的用户界面：

[![材料呈现器](visual-images/material-renderers.png "视图使用材料呈现器")](visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> 使用材料呈现器呈现的控件仍将本机控件，因此存在仍将用户界面区域，例如字体、 阴影、 颜色和提升的平台之间的差异。

## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
