---
title: 创建自定义布局
description: Xamarin.Forms 定义四个布局类 – StackLayout、 AbsoluteLayout、 RelativeLayout，和网格中，而每个不同的方式排列其子。 但是，有时很必要组织使用不是由 Xamarin.Forms 的布局的页面内容。 本文介绍如何编写一个自定义布局的类，并演示了跨页上，水平排列其子，然后将包装对其他行的后续子级的显示方向区分 WrapLayout 类。
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: f0728ac110fcf86f44a5ccb5ddd80b00af1b8d62
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-custom-layout"></a>创建自定义布局

_Xamarin.Forms 定义四个布局类 – StackLayout、 AbsoluteLayout、 RelativeLayout，和网格中，而每个不同的方式排列其子。但是，有时很必要组织使用不是由 Xamarin.Forms 的布局的页面内容。本文介绍如何编写一个自定义布局的类，并演示了跨页上，水平排列其子，然后将包装对其他行的后续子级的显示方向区分 WrapLayout 类。_

## <a name="overview"></a>概述

Xamarin.Forms 中, 布局的所有类都派生自[ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)类和约束的泛型类型[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)及其派生类型。 反过来，`Layout<T>`类派生自[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)类，该类提供的机制，用于定位和大小调整子元素。

每个 visual 元素负责确定自己首选的大小，这被称为*请求*大小。 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)，和[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)派生的类型负责确定的位置和其子域或子级，相对于其自身的大小。 因此，布局涉及父-子关系，其中父确定应该是什么及其所有子级的大小，但将尝试容纳的子请求的大小。

创建自定义布局需要 Xamarin.Forms 布局和失效周期的全面了解。 现在将讨论这些周期。

## <a name="layout"></a>布局

布局开始与页面的可视化树的顶部，将经历的可视化树中包含每个 visual 元素在页面上的所有分支。 其他元素的父元素负责大小和位置相对于其自身及其子级。

[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)类定义[ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)方法，用于测量布局操作的元素和[ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)方法，它指定将在呈现元素的矩形区域。 当一个应用程序启动并显示的第一页时，*布局周期*包含第一个`Measure`调用，然后`Layout`调用，启动上[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)对象：

1. 在布局周期中，每个父元素是负责调用`Measure`在其子级上的方法。
1. 测量子级后，每个父元素负责调用`Layout`在其子级上的方法。

此周期可确保在页面上的每个 visual 元素收到对调用`Measure`和`Layout`方法。 过程下图所示：

![](custom-images/layout-cycle.png "Xamarin.Forms 布局周期")

> [!NOTE]
> 请注意，布局周期是否也可能发生的可视化树的子集发生更改会影响布局。 这包括正在添加或从例如，在集合中移除的项[ `StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)中的更改[ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/)的元素或中元素的大小的更改的属性。

具有每个 Xamarin.Forms 类`Content`或`Children`属性具有可重写[ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/)方法。 从其中派生的自定义布局类[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)必须重写此方法并确保[ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)和[ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)方法在此元素的所有子级，以提供所需的自定义布局上调用。

此外，每个类派生自[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)或[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)必须重写[ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)方法，即其中布局类确定的大小，它必须是通过调用[ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)及其所有子级的方法。

> [!NOTE]
> 元素可确定基于其大小*约束*，这指示空间量是可用元素的父项中的元素。 约束传递给[ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)和[ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)方法可以介于 0 到`Double.PositiveInfinity`。 元素是*约束*，或*完全约束*在接收到的调用时，其[ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)方法使用有限期参数的约束元素为特定的大小。 元素是*不受约束*，或*部分约束*在接收到的调用时，其`Measure`方法具有至少一个自变量等于`Double.PositiveInfinity`– 可以是无限的约束想象成，该值指示自动调整大小。

## <a name="invalidation"></a>失效

失效是依据页上的元素中的更改将触发新的布局周期的过程。 当用户不再需要的正确大小或位置时，元素将被视为无效。 例如，如果[ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontSize/)属性[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)更改，`Button`被认为是无效的因为它将不再具有正确的大小。 调整大小`Button`就可能更改 ripple 影响通过网页的其余内容的布局中。

元素使自身通过调用[ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/)方法，通常元素的属性更改时，可能会导致新的元素的大小。 此方法将引发[ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/)事件，该元素的父级处理以触发新的布局周期事件。

[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)类设置的处理程序[ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/)事件添加到每个子其`Content`属性或`Children`集合，并分离该处理程序时删除子。 因此，具有子级的可视化树中每个元素是自动插入到项目的一个子更改大小。 下图说明了如何在可视化树中元素的大小的更改可以导致 ripple 向上树的更改：

![](custom-images/invalidation.png "可视化树中的失效")

但是，`Layout`类尝试限制上页的布局的孩子的大小更改的影响。 如果布局满足约束的大小，然后子大小更改不会影响任何大于可视化树中父布局。 但是，通常中布局的大小的更改会影响布局如何排列其子。 因此，布局的大小中的任何更改将开始布局周期的布局和布局将接收对调用其[ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)和[ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/)方法。

[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)类还定义[ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/)方法都有类似用途到[ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/)方法。 `InvalidateLayout`应该调用方法，只要进行的更改会影响布局位置和大小及其子级的方式。 例如，`Layout`类时，将调用`InvalidateLayout`方法添加到或从布局中删除子时。

[ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/)可以重写以实现最大限度的重复调用地缓存[ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)的布局的子级的方法。 重写`InvalidateLayout`方法将提供的子级添加到或从布局中删除时通知。 同样， [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/)可以重写方法，以在布局的子级之一大小更改时提供通知。 有关这两种方法重写中，自定义布局应通过清除缓存响应。 有关详细信息，请参阅[计算和缓存数据](#caching)。

## <a name="creating-a-custom-layout"></a>创建自定义布局

用于创建自定义布局的过程如下所示：

1. 创建一个从 `Layout<View>` 类派生的类。 有关详细信息，请参阅[创建 WrapLayout](#creating)。
1. [*可选*] 添加属性，可绑定属性，则对于应布局类上设置的任何参数作为后盾。 有关详细信息，请参阅[添加由可绑定属性的属性支持](#adding_properties)。
1. 重写[ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)方法来调用[ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)为布局上的布局的所有子级，并返回请求的大小的方法。 有关详细信息，请参阅[重写 OnMeasure 方法](#onmeasure)。
1. 重写[ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/)方法来调用[ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)布局的所有子级上的方法。 未能调用[ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)在布局中的每个子方法将导致子永远不会接收的正确大小或位置，并因此子不会成为在页上可见。 有关详细信息，请参阅[重写 LayoutChildren 方法](#layoutchildren)。

  > [!NOTE]
>  枚举中的子级时[ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)和[ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/)重写，跳过任何子其[ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/)属性设置为`false`. 这将确保自定义布局不会为不可见的儿童留下空间。

1. [*可选*] 重写[ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/)子级添加到或从布局中删除时要通知的方法。 有关详细信息，请参阅[重写 InvalidateLayout 方法](#invalidatelayout)。
1. [*可选*] 重写[ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/)布局的子级之一更改大小时要通知的方法。 有关详细信息，请参阅[重写 OnChildMeasureInvalidated 方法](#onchildmeasureinvalidated)。

> [!NOTE]
> 请注意， [ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)不会调用重写，如果由其父级，而不是其子控制布局的大小。 如果一个或两个约束无限的或者如果布局类具有非默认但是，将调用重写[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)或[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性值。 为此， [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/)重写不能依赖于子大小期间获取[ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)方法调用。 相反，`LayoutChildren`必须调用[ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)布局的子级，然后再调用上方法[ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)方法。 子级的大小或者，在中获得`OnMeasure`可以缓存替代以免以后出现`Measure`中的调用`LayoutChildren`重写时，但布局类将需要知道何时大小需要重新获取。 有关详细信息，请参阅[计算和缓存布局数据](#caching)。

可以通过将其添加到使用布局类[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，通过将子级添加到布局。 有关详细信息，请参阅[使用 WrapLayout](#consuming)。

<a name="creating" />

### <a name="creating-a-wraplayout"></a>创建 WrapLayout

示例应用程序演示方向区分`WrapLayout`跨页上，水平排列其子，然后将包装对其他行的后续子级的显示的类。

`WrapLayout`类为每个子级，称为分配的空间量相同*单元格大小*，具体取决子级的最大大小。 子级小于可以与单元中定位单元格大小基于其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性值。

`WrapLayout`类定义下面的代码示例中所示：

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculating-and-caching-layout-data"></a>计算和缓存布局的数据

`LayoutData`结构在多个属性中存储有关的子对象集合的数据：

- `VisibleChildCount` – 的布局中可见的子级的个数。
- `CellSize` – 所有子级，调整到的布局的大小的最大大小。
- `Rows` – 的行数。
- `Columns` – 的列数。

`layoutDataCache`字段用于存储多个`LayoutData`值。 当应用程序启动时，两个`LayoutData`对象将缓存到`layoutDataCache`当前方向 – 一个用于的约束自变量的字典`OnMeasure`重写时，和一个用于`width`和`height`自变量到`LayoutChildren`重写。 到横向方向旋转设备时`OnMeasure`重写和`LayoutChildren`替代将再次调用，这将导致另一个的两个`LayoutData`被缓存到字典的对象。 但是，当将设备返回到纵向，没有进一步的计算是必需的因为`layoutDataCache`已经具有所需的数据。

下面的代码示例演示`GetLayoutData`方法，该计算的属性方法`LayoutData`结构化基于特定大小：

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

`GetLayoutData`方法执行以下操作：

- 它确定是否计算`LayoutData`值已在缓存中，并将其返回是否可用。
- 否则，它枚举所有子级，调用通过`Measure`上具有无限的宽度和高度，每个子级的方法，并确定最大子大小。
- 前提是没有至少一个可见子，它计算的行和所需，列数，然后将单元格大小计算的维度所基于的子级`WrapLayout`。 请注意单元格大小通常是略高于最大子大小，但是，这也可能是较小如果`WrapLayout`不够宽最宽子或足够高的最高子。
- 它将存储新`LayoutData`缓存中的值。

<a name="adding_properties" />

#### <a name="adding-properties-backed-by-bindable-properties"></a>添加支持的可绑定属性的属性

`WrapLayout`类定义`ColumnSpacing`和`RowSpacing`属性，其值用于分隔的行和列在布局中，而这由可绑定属性。 可绑定属性下面的代码示例所示：

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

每个可绑定属性的属性更改处理程序时，将调用`InvalidateLayout`传递上的方法重写以触发新的布局`WrapLayout`。 有关详细信息，请参阅[重写 InvalidateLayout 方法](#invalidatelayout)和[重写 OnChildMeasureInvalidated 方法](#onchildmeasureinvalidated)。

<a name="onmeasure" />

#### <a name="overriding-the-onmeasure-method"></a>重写 OnMeasure 方法

`OnMeasure`重写下面的代码示例所示：

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

重写时，将调用`GetLayoutData`方法和构造`SizeRequest`对象从返回的数据，同时还考虑`RowSpacing`和`ColumnSpacing`属性值。 有关详细信息`GetLayoutData`方法，请参阅[计算和缓存数据](#caching)。

> [!IMPORTANT]
> [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)和[ `OnMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)方法应该永远不会通过返回请求的无限维度[ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/)属性设置为值`Double.PositiveInfinity`. 但是，至少一个约束参数`OnMeasure`可以是`Double.PositiveInfinity`。

<a name="layoutchildren" />

#### <a name="overriding-the-layoutchildren-method"></a>重写 LayoutChildren 方法

`LayoutChildren`重写下面的代码示例所示：

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

通过调用开始重写`GetLayoutData`方法，然后枚举所有为调整大小并将它们放置在每个孩子的单元格中的子级。 这通过调用来实现[ `LayoutChildIntoBoundingRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/)方法，用于将基于的矩形范围内子其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)属性值。 这相当于子级的调用[ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/)方法。

> [!NOTE]
> 请注意，在该矩形传递给`LayoutChildIntoBoundingRegion`方法包括子可以驻留在其中的整个区域。

有关详细信息`GetLayoutData`方法，请参阅[计算和缓存数据](#caching)。

<a name="invalidatelayout" />

#### <a name="overriding-the-invalidatelayout-method"></a>重写 InvalidateLayout 方法

[ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/)重写调用时添加或删除从布局中，或当一个子级的`WrapLayout`属性更改值，如下面的代码示例中所示：

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

重写会使布局失效，并放弃所有的缓存的布局信息。

> [!NOTE]
> 若要停止[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)类调用[ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/)方法添加到或从一种布局，删除子级时重写[ `ShouldInvalidateOnChildAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded/p/Xamarin.Forms.View/)和[ `ShouldInvalidateOnChildRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved/p/Xamarin.Forms.View/)方法，并返回`false`。 添加或删除子级时，布局类然后可以实现自定义过程。

<a name="onchildmeasureinvalidated" />

#### <a name="overriding-the-onchildmeasureinvalidated-method"></a>重写 OnChildMeasureInvalidated 方法

[ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/)重写时进行调用布局的子级之一更改大小，并在下面的代码示例所示：

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

重写使无效子布局，并放弃所有的缓存的布局信息。

<a name="consuming" />

### <a name="consuming-the-wraplayout"></a>使用 WrapLayout

`WrapLayout`类可供将它放置在[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)派生类型，如下面的 XAML 代码示例中所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

等效的 C# 代码所示：

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

然后可以添加到的子级`WrapLayout`所需的方式。 下面的代码示例演示[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)元素添加到`WrapLayout`:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  var images = await GetImageListAsync();
  foreach (var photo in images.Photos)
  {
    var image = new Image
    {
      Source = ImageSource.FromUri(new Uri(photo + string.Format("?width={0}&height={0}&mode=max", Device.RuntimePlatform == Device.UWP ? 120 : 240)))
    };
    wrapLayout.Children.Add(image);
  }
}

async Task<ImageList> GetImageListAsync()
{
  var requestUri = "https://docs.xamarin.com/demo/stock.json";
  using (var client = new HttpClient())
  {
    var result = await client.GetStringAsync(requestUri);
    return JsonConvert.DeserializeObject<ImageList>(result);
  }
}
```

当页包含`WrapLayout`显示，以异步方式访问远程 JSON 文件包含的照片列表示例应用程序，而创建显示[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)每个元素照片，并将其添加到`WrapLayout`. 这将导致以下屏幕截图中所示的外观：

![](custom-images/portait-screenshots.png "示例应用程序纵向屏幕快照")

下面的屏幕截图演示`WrapLayout`它已转动为横向后：

![](custom-images/landscape-ios.png "示例 iOS 应用程序横向屏幕截图")
![](custom-images/landscape-android.png "示例 Android 应用程序横向屏幕截图")
 ![ ] (custom-images/landscape-uwp.png "示例 UWP 应用程序横向屏幕快照")

每个行中的列数取决于照片大小、 屏幕宽度和每个独立于设备的单位的像素数。 [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)元素以异步方式加载的照片，并因此`WrapLayout`类将接收到的频繁调用其[ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/)方法以及每个`Image`在元素收到新的大小基于加载的照片。

## <a name="summary"></a>总结

本文介绍了如何编写一个自定义布局的类，并演示方向区分`WrapLayout`跨页上，水平排列其子，然后将包装对其他行的后续子级的显示的类。


## <a name="related-links"></a>相关链接

- [WrapLayout （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/CustomLayout/WrapLayout/)
- [自定义布局](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [创建 Xamarin.Forms 中的自定义布局 （视频）](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [布局<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)
- [布局](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [VisualElement](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)
