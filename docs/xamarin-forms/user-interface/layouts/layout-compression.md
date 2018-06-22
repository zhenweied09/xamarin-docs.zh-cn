---
title: 布局压缩
description: 布局压缩的可视化树中中删除指定的布局，以尝试提升页呈现性能。 此文章介绍了如何启用布局压缩和可以带来的好处。
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 9c698d539ab671ee2a033ae5943a46e0cc870f76
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30791121"
---
# <a name="layout-compression"></a>布局压缩

_布局压缩的可视化树中中删除指定的布局，以尝试提升页呈现性能。此文章介绍了如何启用布局压缩和可以带来的好处。_

## <a name="overview"></a>概述

Xamarin.Forms 执行使用递归方法调用的两个序列的布局：

- 布局开始与页面的可视化树的顶部，将经历的可视化树中包含每个 visual 元素在页面上的所有分支。 其他元素的父元素负责大小和位置相对于其自身及其子级。
- 失效是依据页上的元素中的更改将触发新的布局周期的过程。 当用户不再需要的正确大小或位置时，元素将被视为无效。 具有子级的可视化树中每个元素是自动插入到项目的一个子更改大小。 因此中的可视化树中的某个元素的大小的更改可以导致 ripple 向上树的更改。

有关如何 Xamarin.Forms 执行布局的详细信息，请参阅[创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)。

在布局流程的结果是层次结构的本机控件。 但是，此层次结构包括平台呈现器，进一步 inflating 视图层次结构嵌套其他容器呈现器和包装器。 嵌套级别越高，Xamarin.Forms 必须执行要显示的页面的工作的值就越大。 对于复杂的布局，查看层次结构可以深度和广泛的具有多个级别的嵌套。

例如，考虑到 Facebook 的日志记录的示例应用程序中的以下按钮：

![](layout-compression-images/facebook-button.png "Facebook Button")

此按钮指定为具有以下 XAML 视图层次结构的自定义控件：

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

生成的嵌套的视图层次结构可以检查与[Xamarin 检查器](~/tools/inspector/index.md)。 在 Android 上，嵌套的视图层次结构包含 17 视图：

![](layout-compression-images/no-compression.png "为 Facebook 按钮查看层次结构")

布局压缩，这是适用于 iOS 和 Android 平台上的 Xamarin.Forms 应用程序，旨在平展嵌套的可视化树，这可以提高页面呈现性能中删除指定的布局视图。 传送的性能优势而异的一个页、 正在使用的操作系统的版本和在其运行应用程序的设备的复杂性。 不过，在旧设备上实现的性能提升最大。

> [!NOTE]
> 虽然本文着重于在 Android 上应用布局压缩的结果，它是同样适用于 iOS。

## <a name="layout-compression"></a>布局压缩

在 XAML 中，可以通过设置启用布局压缩`CompressedLayout.IsHeadless`附加到属性`true`布局类上：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

或者，它可以在中启用 C# 通过将布局实例指定的第一个参数为`CompressedLayout.SetIsHeadless`方法：

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> 由于布局压缩的可视化树中删除一种布局，因此不适合布局具有可视外观，或获取触摸屏输入。 因此，它设置的布局[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)属性 (如[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)， [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/)， [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)， [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)， [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)) 或，接受手势，但不适合布局压缩。 但是，启用布局压缩布局设置视觉外观属性，或接受手势，不会导致生成或运行时错误。 相反，将应用布局压缩，可视外观属性和笔势识别，将以静默方式失败。

Facebook 按钮，可以在三个布局类上启用布局压缩：

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

在 Android 上，这会导致 14 视图的嵌套的视图层次结构：

![](layout-compression-images/layout-compression.png "使用布局压缩的 Facebook 按钮查看层次结构")

这与原始的嵌套的视图层次结构的 17 视图相比，表示的 17%的视图数目的减少。 虽然这种数量缩减可能出现无意义，对整个页面的视图减少可更重要。

### <a name="fast-renderers"></a>快速呈现器

快速的呈现器减少的通货膨胀和在 Android 上的 Xamarin.Forms 控件呈现成本平展生成的本机视图层次结构。 这将通过创建更少的对象，这反过来会导致不太复杂的可视化树中和内存使用小于进一步改进性能。 有关快速呈现器的详细信息，请参阅[快速呈现器](~/xamarin-forms/internals/fast-renderers.md)。

对于在示例应用程序 Facebook 按钮，组合布局压缩和快速的呈现器生成 8 视图的嵌套的视图层次的结构：

![](layout-compression-images/layout-compression-with-fast-renderers.png "与布局压缩和快速的呈现器的 Facebook 按钮查看层次结构")

与原始的嵌套的视图层次结构的 17 视图相比，这表示 52%的减少。

示例应用程序包含页从实际的应用程序中提取。 如果没有布局压缩和快速的呈现器，页生成 130 视图在 Android 上的嵌套的视图层次结构。 启用快速呈现器和适当的布局类上的布局压缩可以嵌套的视图层次结构减少到 70 视图，46%的减少。

## <a name="summary"></a>总结

布局压缩的可视化树中中删除指定的布局，以尝试提升页呈现性能。 这带来的性能优势因页面复杂性、要使用的操作系统版本以及运行应用的设备而异。 不过，在旧设备上实现的性能提升最大。


## <a name="related-links"></a>相关链接

- [创建自定义布局](~/xamarin-forms/user-interface/layouts/custom.md)
- [快速呈现器](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)
