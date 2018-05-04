---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: WPF 与。Xamarin.Forms： 相似之处和差异
ms.technology: xamarin-crossplatform
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: e72297963b6be50572e4bb539263065592737c42
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF 与。Xamarin.Forms： 相似之处和差异

## <a name="control-templates"></a>控件模板

WPF 支持的概念*控件模板*提供控件的可视化效果说明 (`Button`，`ListBox`等。)。 Xamarin.Forms 如上所述，使用具体_呈现_与本机平台 （iOS、 Android、 等） 实现控件的可视化效果交互此类。

但是，Xamarin.Forms_未_具有`ControlTemplate`类型-它用于主题`Page`对象。 它提供的定义`Page`提供一致的内容，但允许页的用户更改颜色、 字体、 等和甚至添加元素，以使应用程序唯一。

此常见用法是事项如身份验证对话框，提示，并提供可以在应用内自定义的标准化，但可主题化页外观和感觉。 作为此支持的一部分，使用很多熟悉的 WPF 名为控件：

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

但务必知道，这些是_不_Xamarin.Forms 提供相同的目的。 有关此功能的详细信息，请参阅[文档页](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。

## <a name="xaml"></a>XAML

XAML 中，用作 WPF 和 Xamarin.Forms 的声明性的标记语言。 大多数情况下，语法是相同-的主要区别是 XAML 关系图定义/创建的对象。

- Xamarin.Forms 支持[XAML 2009 规范](/dotnet/framework/xaml-services/xaml-2009-language-features/); 这样会更容易，如定义数据`string`s， `int`s 等也为定义的泛型类型，传递给构造函数的自变量。

- 没有当前方法 dyanmically 加载 XAML 像 WPF 与那样`XamlReader`。 你可以使用相同的基本功能[NuGet 包](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/)尽管。

### <a name="markup-extensions"></a>标记扩展

Xamarin.Forms 支持扩展通过标记扩展，类似于 WPF XAML。 在初始状态下，它具有相同的基本构建基块：

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

此外，它包括`{x:Reference}`XAML 2009 规范中，从和`{TemplateBinding}`标记扩展时使用的专用版本`ControlTemplate`受 Xamarin.Forms。

> [!WARNING]
> `ControlTemplate`支持不是相同的即使它具有相同的名称。

Xamarin.Forms 支持以及-自定义标记扩展而实现是略有不同。 在 WPF 中，你必须派生自`MarkupExtension`-一个抽象基类。 Xamarin.Forms 中,，将被替换为接口`IMarkupExtension`或`IMarkupExtension<T>`即更灵活。

一样 WPF 中，单个所需的方法是`ProvideValue`方法为从标记扩展返回的值。

## <a name="binding-infrastructure"></a>绑定基础结构

在协议上执行的核心概念之一是数据绑定基础结构，以连接到.NET 数据属性的可视属性。 这使体系结构模式如 MVVM。 基本设计等同-具有可绑定基类[BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)，这是的 WPF 中[将](https://msdn.microsoft.com/en-us/library/system.windows.dependencyobject(v=vs.110).aspx)类。 此基类用作作为数据绑定中的目标将参与的所有对象的根上级。 派生的类，则公开[BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)对象充当属性值的后备存储 (它们定义为[DependencyProperty](https://msdn.microsoft.com/library/system.windows.dependencyproperty(v=vs.110).aspx) WPF 中的对象)。

### <a name="defining-bindable-properties"></a>定义可绑定属性

Xamarin.Forms 中的可绑定属性的定义是 WPF 相同：
1. 对象必须派生自`BindableObject`。
2. 必须有类型的公共静态字段`BindableProperty`声明定义属性的后备存储密钥。
3. 应使用的公共实例属性包装`GetValue`和`SetValue`来检索和更改属性值。

有关完整示例，请参阅[Xamarin.Forms 中的可绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。

### <a name="attached-properties"></a>附加的属性

附加的属性可绑定属性的子集以及它们在 WPF 中的相同方式工作。 主要区别是属性包装在这种情况下是省略和替换为一组所属的类上静态 get/set 方法。 请参阅[Xamarin.Forms 中的附加属性](~/xamarin-forms/xaml/attached-properties.md)有关详细信息。

### <a name="using-the-binding-engine"></a>使用绑定引擎

因为它在 WPF 中，使用绑定引擎的过程都是相同的。 它可以在隐藏代码中使用，通过创建`Binding`对象绑定到源对象 （任何.NET 类型） 和一个可选属性值 (如果省略，它会将源对象视为在属性本身-一样 WPF)。 然后，可以使用`SetBinding`上任何`BindableObject`将绑定到`BindableProperty`。

或者，可以在 XAML 中使用定义的绑定关系`BindingExtension`。 它在 WPF 中具有相同的基本扩展值。

更类似于 Silverlight 实现比 WPF 的绑定支持和引擎。 有几个缺少的功能的 Xamarin.Forms 中未实现：

- 没有在绑定中的以下功能的支持：
    - BindingGroupName
    - BindsDirectlyToSource
    - IsAsync
    - 多重绑定
    - NotifyOnSourceUpdated
    - NotifyOnTargetUpdated
    - NotifyOnValidationError
    - UpdateSourceTrigger
    - UpdateSourceExceptionFilter
    - ValidatesOnDataErrors
    - ValidatesOnExceptions
    - Validationrule 集合
    - XPath
    - XmlNamespaceManager
- `Binding.Mode` 不支持`OneTime`，只需使用`OneWay`。

#### <a name="relativesource"></a>RelativeSource

有关不支持`RelativeSource`绑定。 在 WPF 中，这些行为允许您绑定到在 XAML 中定义其他可视元素。 在 Xamarin.Forms，此相同的功能可以实现使用`{x:Reference}`标记扩展。 例如，假设我们有具有名称"otherControl"有一个文本属性的控件，我们可以将绑定到它如下：

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

使用相同的功能可以用于`{RelativeSource Self}`功能。 但是没有用于查找类型的上级支持 (`{RelativeSource FindAncestor}`)。

#### <a name="binding-context"></a>绑定上下文

在 WPF 中，你可以定义`DataContext`属性值的 reprents 对默认的绑定源。 如果未定义的绑定源，则使用此属性的值。 下的可视化树，使其能够在高级别定义，然后使用由子级继承值。

Xamarin.Forms，这在此相同功能是可用，但该属性将`BindingContext`。

#### <a name="value-converters"></a>值转换器

Xamarin.Forms-一样 WPF 中完全支持值转换器。 使用相同的接口形状，但 Xamarin.Forms 具有中定义的界面`Xamarin.Forms`命名空间。

### <a name="model-view-viewmodel"></a>模型-视图-视图模型

WPF 和 Xamarin.Forms 完全支持 MVVM。

WPF 包括一个内置的`RoutedCommand`有时用于;Xamarin.Forms 提供超出没有内置命令支持`ICommand`接口定义。 你可以包含各种 MVVM 多个要添加必需的基本类以实现 MVVM。

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged 和 INotifyCollectionChanged

这两个接口完全支持 Xamarin.Forms 绑定中。 不同于很多基于 XAML 的框架，可以在 Xamarin.Forms （如 WPF) 中的后台线程上引发属性更改通知，并绑定引擎将正确转换为 UI 线程。

此外，这两个环境支持`SynchronziationContext`和`async` / `await`进行合适的线程封送处理。 WPF 还包括`Dispatcher`类 Xamarin.Forms 上所有的可视元素，具有一个静态方法`Device.BeginInvokeOnMainThread`这还可以使用 (虽然`SynchronizationContext`是跨平台编写代码的首选)。

- Xamarin.Forms 包括`ObservableCollection<T>`支持集合更改通知。
- 你可以使用`BindingBase.EnableCollectionSynchronization`以使跨线程更新集合。 API 是与 WPF 变体，略有不同[检查用法的详细信息的文档](https://developer.xamarin.com/api/member/Xamarin.Forms.BindingBase.EnableCollectionSynchronization/)。

## <a name="data-templates"></a>数据模板

若要自定义的呈现的 Xamarin.Forms 中支持数据模板`ListView`行 （单元格）。 与可以利用的 WPF 不同`DataTemplate`s 对于任何面向内容的控件，Xamarin.Forms 当前仅使用其用于`ListView`。 模板定义可以是以内联方式定义 (分配给`ItemTemplate`属性)，或为中的资源`ResourceDictionary`。

此外，它们不是非常灵活性不如及其 WPF 的对应项的。

1. 根元素`DataTemplate`必须_始终_是`ViewCell`对象。
2. 数据触发器完全支持在数据模板中，但必须包括`DataType`，该值指示触发器与之关联的属性类型的属性。
3. `DataTemplateSelector` 也受支持，但派生自`DataTemplate`并因此只需直接分配`ItemTemplate`属性 (与`ItemTemplateSelector`WPF 中)。

## <a name="itemscontrol"></a>ItemsControl

没有到没有内置 equivelent `ItemsControl` Xamarin.Forms; 中但没有[自定义一个用于 Xamarin.Forms 可从以下站点](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs)。

## <a name="user-controls"></a>用户控件

在 WPF 中， `UserControl`s 用于提供具有关联的行为的 UI 的部分。 在 Xamarin.Forms，我们将使用`ContentView`实现同一目的。 都支持在 XAML 中使用绑定和包含。

## <a name="navigation"></a>导航

WPF 包括极少使用`NavigationService`它无法使用提供"类似的浏览器"导航的功能。 大多数应用中未涉及这但是，而不是使用不同`Window`元素或不同节的窗口，以显示数据。

在 phone 设备，不同_屏幕_通常是该解决方案，并且因此 Xamarin.Forms 包括对几种形式的导航的支持：

| 导航样式 | 页类型 |
|--- |--- |
|基于堆栈的 （推送/弹出）|NavigationPage|
|主/详细信息|MasterDetailPage|
|制表符|TabbedPage|
|向左/右轻扫|CarouselView|

`NavigationPage`是最常用的方法，并且每一页具有`Navigation`属性可以用于推送或页打开和关闭导航堆栈中弹出。 这是到最接近的 equivelent`NavigationService`在 WPF 中找到。

### <a name="url-navigation"></a>URL 定位

WPF 是一种面向桌面的技术，并可以接受命令行参数指示启动行为。 可以使用 Xamarin.Forms[深层 URL 链接](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)在启动时跳转到页。
