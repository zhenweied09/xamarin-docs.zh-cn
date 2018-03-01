---
title: "ListView 性能"
description: "确保基于 ListView 的应用程序具有卓越性能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: 2acaef5fd42b867e88fb9b81d401ea752480124a
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="listview-performance"></a>ListView 性能

如果编写移动应用程序，性能很重要。 用户希望平滑滚动和快速加载时间。 无法满足你的用户预期将你的应用程序存储区中的级别或成本对于业务线应用程序，成本你组织的时间和资金。

尽管[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)是功能强大的视图来显示数据，它具有一些限制。 使用自定义单元格，尤其是当它们包含深度嵌套的视图层次结构或使用需要大量的度量某些布局时，滚动性能会降低。 幸运的是，没有可用于避免性能较差的技术。

在本文中讨论了下列主题：

- **[缓存策略](#cachingstrategy)**
- **[提高 ListView 性能](#improving-performance)**

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>缓存策略

Listview 通常用于显示更多的数据比适合屏幕。 音乐应用程序，例如，请考虑。 歌曲库可能具有数以千计的条目。 最简单的方法，就是创建每首歌曲的行，将使性能变差。 该方法会浪费宝贵的内存，并可能会降低滚动到爬网。 另一种方法是创建和销毁行，如数据滚动到视图。 这需要常量实例化和清理视图对象，可能会很慢。

以节省内存，本机[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)为每个平台的等效项具有重新使用行的内置功能。 仅在屏幕上可见的单元加载到内存中和**内容**将会加载到现有的单元格。 这将阻止应用程序无需实例化数千个对象，从而节省时间和内存。

Xamarin.Forms 允许[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)通过重新使用单元格[ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)枚举，具有以下值：

```csharp
public enum ListViewCachingStrategy
{
  RetainElement,   // the default value
  RecycleElement,
  RecycleElementAndDataTemplate
}
```

> [!NOTE]
> **请注意**: 通用 Windows 平台 (UWP) 将忽略[ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/)缓存策略，因为它始终使用缓存来提高性能。 因此，默认情况下其行为就像[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)应用缓存策略。

### <a name="retainelement"></a>RetainElement

[ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/)缓存策略指定[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)将生成一个单元格用于每个项在列表中，并且是默认`ListView`行为。 它通常应在以下情况下使用：

- 当每个单元格有大量的绑定 (20-30 +)。
- 当单元格模板经常发生变化。
- 当测试中发现的`RecycleElement`缓存策略导致降低的执行速度。

务必要识别所带来的后果[ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/)使用自定义单元格时，缓存策略。 任何单元格初始化代码将需要运行每个单元格创建，这可能会每秒多次。 在此情况下，已在页中，正常的布局技术，如使用多个嵌套[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)情况下，当它们是安装程序将成为性能瓶颈和实时销毁当用户滚动。

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)缓存策略指定[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)将尝试通过回收列表单元格，最小化其内存占用量和执行速度。 此模式下不会始终提供提高性能，并应该进行测试，以确定进行改进。 但是，它通常是首选的解决方案中，并应在以下情况下使用：

- 当每个单元格有中等数量的绑定到一个较小。
- 当每个单元格的[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)定义的所有单元格数据。
- 每个单元格时很大程度上类似，具有单元格模板不变。

在虚拟化过程的单元格将具有更新，其绑定上下文，因此如果应用程序使用此模式下它必须确保绑定上下文更新进行相应处理。 有关单元格的所有数据必须都来自绑定上下文或者一致性错误可能会发生。 这可以通过使用数据绑定来显示单元格数据。 或者，在中设置单元格数据`OnBindingContextChanged`重写，而不是在自定义单元格的构造函数，如下面的代码示例中所示：

```csharp
public class CustomCell : ViewCell
{
  Image image = null;

  public CustomCell ()
  {
    image = new Image();
    View = image;
  }

  protected override void OnBindingContextChanged ()
  {
    base.OnBindingContextChanged ();

    var item = BindingContext as ImageItem;
    if (item != null) {
      image.Source = item.ImageUrl;
    }
  }
}
```

有关详细信息，请参阅[绑定上下文更改](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes)。

在 iOS 和 Android 上，如果单元格使用自定义呈现器，它们必须确保正确实现属性更改通知。 当单元格会重复使用其属性值将发生更改，当绑定上下文更新到的一个可用的单元格中，与`PropertyChanged`引发的事件。 有关详细信息，请参阅[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

#### <a name="recycleelement-with-a-datatemplateselector"></a>与 DataTemplateSelector RecycleElement

当[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)使用[ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)选择[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)、 [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)缓存策略不会缓存`DataTemplate`s。 相反，`DataTemplate`选择列表中的数据的每个数据项。

> [!NOTE]
> **请注意**: [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)缓存策略具有先决条件，Xamarin.Forms 2.4 中引入，当[ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)需要选择[ `DataTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)每个`DataTemplate`必须返回相同[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)类型。 例如，给定[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)与`DataTemplateSelector`，可以返回`MyDataTemplateA`(其中`MyDataTemplateA`返回`ViewCell`类型的`MyViewCellA`)，或`MyDataTemplateB`(其中`MyDataTemplateB`返回`ViewCell`类型的`MyViewCellB`)，当`MyDataTemplateA`返回它必须返回`MyViewCellA`，否则将引发异常。

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

[ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/)缓存策略上生成[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)通过此外确保当缓存策略[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)使用[`DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)选择[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)， `DataTemplate`s 缓存的列表中项的类型。 因此， `DataTemplate`s 每个项类型，而不是每个项实例一次选择一次。

> [!NOTE]
> **请注意**: [ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/)缓存策略具有先决条件， `DataTemplate`s 返回[ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)必须使用[ `DataTemplate`](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/)构造函数采用`Type`。

### <a name="setting-the-caching-strategy"></a>设置缓存策略

[ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)与指定的枚举值[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)构造函数重载，如下面的代码示例中所示：

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

在 XAML 中，设置`CachingStrategy`特性，如下面的代码所示：

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

这具有与在 C# 中; 中的构造函数中设置的缓存策略参数相同的效果请注意，没有任何`CachingStrategy`属性`ListView`。

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>在子类化 ListView 中设置的缓存策略

设置`CachingStrategy`子类化，从 XAML 的属性[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)不会生成所需的行为，因为没有任何`CachingStrategy`属性`ListView`。 此外，如果[XAMLC](~/xamarin-forms/xaml/xamlc.md)是启用，就将生成以下错误消息：**没有属性，可绑定属性或找到 CachingStrategy 的事件**

此问题的解决方案是以指定一个构造函数上子类化[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)接受[ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)参数并将其传递到基类：

```csharp
public class CustomListView : ListView
{
  public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
  {
  }
  ...
}
```

则[ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)枚举值可以通过使用指定从 XAML`x:Arguments`语法：

```xaml
<local:CustomListView>
  <x:Arguments>
    <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
  </x:Arguments>
</local:CustomListView>
```

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>提高 ListView 性能

有许多技术来提高性能的`ListView`:

-  将绑定`ItemsSource`属性`IList<T>`而不是集合`IEnumerable<T>`集合，因为`IEnumerable<T>`集合不支持随机访问。
-  使用内置的单元格 (如`TextCell`  /  `SwitchCell` ) 而不是`ViewCell`每当可以。
-  使用更少元素。 例如，请考虑使用单个`FormattedString`而不是多个标签的标签。
-  替换`ListView`与`TableView`显示非同构数据 – 即不同类型的数据时。
-  限制使用[ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/)方法。 如果过度使用，它将会降低性能。
-  在 Android 上，避免设置`ListView`的行分隔符可见性或颜色后它已实例化，因为它会导致较大的性能损失。
-  避免更改单元格的布局基于[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)。 这将带来很大的布局和初始化代价。
-  避免将深度嵌套的布局层次结构。 使用`AbsoluteLayout`或`Grid`以减少嵌套。
-  避免特定`LayoutOptions`以外`Fill`（填充是 cheapest 计算）。
-  避免将置于`ListView`内`ScrollView`原因如下：
  - `ListView`实现其自己滚动。
  - `ListView`它们将处理的父级将不会收到任何手势`ScrollView`。
  - `ListView`可以提供自定义的页眉和页脚，滚动列表中，可能的产品功能的元素具有`ScrollView`所用的。 有关详细信息请参阅[页眉和页脚](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers)。
-  如果你需要在你的单元格中显示的非常具体，复杂设计，请考虑自定义呈现器。

`AbsoluteLayout` 有可能执行而无需单个度量值调用的布局。 这使得非常强大的性能。 如果`AbsoluteLayout`不能使用，请考虑[ `RelativeLayout` ](http://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)。 如果使用`RelativeLayout`，直接传递约束将为远远快于使用表达式 API。 这是因为表达式 API 使用 JIT，并在 iOS 上树有解释，这是速度较慢。 表达式 API 适合页面布局中只是需要对初始布局和旋转，但在`ListView`滚动，过程中会经常运行在何处，它会影响性能。

生成的自定义呈现器[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)或其单元格是一种方法减少布局计算滚动性能的影响。 有关详细信息，请参阅[自定义 ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)和[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。


## <a name="related-links"></a>相关链接

- [自定义呈现器视图 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [自定义呈现器 ViewCell （示例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
- [ListViewCachingStrategy](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)
