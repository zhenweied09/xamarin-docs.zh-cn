---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF 与。Xamarin.Forms 应用生命周期
description: 了解应用程序启动过程中，并处理背景状态
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: b4f9aebbbcab48290d37c5732c69267897238272
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF 与。Xamarin.Forms 应用生命周期

Xamarin.Forms 会占用大量的特征之前，尤其是 WPF 的基于 XAML 的框架从设计指南。 但是，其他方式它偏离显著可尝试通过将迁移的人员的粘性点。 本文档尝试标识的一些这些问题，并为 Xamarin.Forms 尽可能到桥 WPF 知识提供指导。

## <a name="app-lifecycle"></a>应用生命周期

WPF 和 Xamarin.Forms 之间的应用程序生命周期是类似的。 同时在外部 （平台） 代码中启动，并启动通过方法调用用户界面。 不同之处在于，Xamarin.Forms 总是以在特定于平台的程序集中将然后初始化并创建应用程序的 UI。

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> `Main`方法，默认情况下是自动生成并在代码中不可见。

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>应用程序类

WPF 和 Xamarin.Forms 具有`Application`类该类创建为单一实例。 在大多数情况下，应用将从此类派生以提供自定义的应用程序，虽然这严格不要求在 WPF 中。 两者都公开`Application.Current`属性可以查找创建的单一实例。

### <a name="global-properties--persistence"></a>全局属性 + 持久性

WPF 和 Xamarin.Forms 具有`Application.Properties`字典可用可以在其中存储应用程序中的任何位置均可访问的全局应用级别对象。 主要区别在于该 Xamarin.Forms 将_保留_时应用程序挂起，并且它重新启动时重新加载这些集合中存储任何基元类型。 WPF 自动不支持该行为-相反，大多数开发人员依赖于独立存储，或使用内置`Settings`支持。

## <a name="defining-pages-and-the-visual-tree"></a>定义页面和可视化树

WPF 使用`Window`作为任何顶级的可视元素的根元素。 这在 Windows 世界中显示信息定义 HWND。 你可以创建和显示同时根据需要在 WPF 中的任意多个窗口。

Xamarin.Forms，这在顶级 visual 始终定义平台-如在 iOS 上，它是`UIWindow`。 它是内容划分为使用这些本机平台表示形式的 Xamarin.Forms 呈现`Page`类。 每个`Page`Xamarin.Forms 中表示唯一"中的页"应用程序，其中仅有一次可见。

这两个 WPFs`Window`与 Xamarin.Forms`Page`包括`Title`属性来影响显示的标题，并且具有`Icon`属性来显示特定图标页 (**注意**，标题和图标并不总是可见 Xamarin.Forms 中）。 此外，你可以更改如的背景色或图像的两侧的常见 visual 属性。

从技术上讲可以呈现到两个单独的平台视图 (例如定义两个`UIWindow`对象，并具有到外部显示器或 AirPlay 第二个呈现器)，它需要特定于平台的代码，若要这样做，并且不直接受支持的功能Xamarin.Forms 本身。

### <a name="views"></a>视图

对于这两个框架 visual 层次结构是类似的。 WPF 是由于对所见即所得的文档的更深入地有点。

**WPF**

```
DependencyObject - base class for all bindable things
   Visual - rendering mechanics
      UIElement - common events + interactions
         FrameworkElement - adds layout
            Shape - 2D graphics
            Control - interactive controls
```

**Xamarin.Forms**

```
BindableObject - base class for all bindable things
   Element - basic parent/child support + resources + effects
      VisualElement - adds visual rendering properties (color, fonts, transforms, etc.)
         View - layout + gesture support
```

### <a name="view-lifcycle"></a>视图 Lifcycle

Xamarin.Forms 主要围绕移动的方案。 在这种情况下，应用程序是_激活_，_挂起_，和_重新激活_当用户与之交互。 这是类似于单击远离`Window`在 WPF 应用程序并且有一组方法和对应的事件，你可以重写或挂钩到要监视此行为。

| 目标 | WPF 方法 | Xamarin.Forms 方法 |
|--- |--- |--- |
|首次激活|ctor + Window.OnLoaded|ctor + Page.OnStart|
|显示|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disapearing|
|挂起/丢失焦点|Window.OnDeactivated|Page.OnSleep|
|激活/获得焦点|Window.OnActivated|Page.OnResume|
|Closed|Window.OnClosing + Window.OnClosed|n/a|


这两个支持显示/隐藏以及子控件，在 WPF 中它是三种状态属性`IsVisible`（可见、 隐藏和折叠）。 Xamarin.Forms，这在很只是可见还是隐藏通过`IsVisible`属性。

### <a name="layout"></a>布局

页面布局中的相同 2-阶段 （度量值/排列） 发生在 WPF 中时发生。 你可以通过重写 Xamarin.Forms 中的以下方法挂钩到页面布局`Page`类：

| 方法 | 目标 |
|--- |--- |
|OnChildMeasureInvalidated|已更改的子级的首选的大小。|
|OnSizeAllocated|分配页的宽度/高度。|
|LayoutChanged 事件|页的布局/大小已更改。|

那里没有全局布局事件，这称为今天，也没有全局`CompositionTarget.Rendering`之类的事件在 WPF 中找到。

#### <a name="common-layout-properties"></a>通用布局属性

WPF 和 Xamarin.Forms 都支持`Margin`到控件的元素，围绕间距和`Padding`到控件间距_内_元素。 此外，大多数 Xamarin.Forms 布局视图具有属性来控制间距 （例如行或列）。

此外，大多数元素具有属性，以改变如何在父容器中放置：

| WPF | Xamarin.Forms | 目标 |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|左/Center/右/Stretch 选项|
|VerticalAlignment|VerticalOptions|顶部/Center/底部/Stretch 选项|

> [!NOTE]
> 这些属性的实际解释依赖于父容器。

#### <a name="layout-views"></a>布局视图

WPF 和 Xamarin.Forms 都使用布局控件来定位子元素。 在大多数情况下，它们是非常接近相互从功能角度讲。

| WPF | Xamarin.Forms | 布局样式 |
|--- |--- |--- |
|StackPanel|StackLayout|从左到右还是从上到下无限堆叠|
|Grid|Grid|表格格式 （行和列）|
|DockPanel|n/a|停靠窗口的边缘|
|Canvas|AbsoluteLayout|像素/坐标定位|
|WrapPanel|n/a|包装堆栈|
|n/a|RelativeLayout|相对基于规则的定位|

> [!NOTE]
> Xamarin.Forms 不支持`GridSplitter`。

这两个平台使用_附加属性_微调子级。

### <a name="rendering"></a>“呈现”

WPF 和 Xamarin.Forms 呈现机制是完全不同。 在 WPF 中，直接创建的控件呈现到屏幕上的像素的内容。 WPF 维护两个对象关系图 (_树_) 来表示此-_逻辑树_表示控件中的代码或 XAML 中，定义和_可视化树_表示这是在屏幕发生的实际呈现执行直接通过可视元素 （通过虚拟绘图方法），或通过 XAML 定义`ControlTemplate`它们可以替换或自定义。 通常情况下，会更复杂，因为它包含此控件，隐式内容，等等的标签周围的边框如对可视化树。WPF 还包括一组 Api (`LogicalTreeHelper`和`VisualTreeHelper`) 若要检查这些两个对象关系图。

在 Xamarin.Forms，控件可定义在`Page`是实际上只是简单的数据对象。 它们类似于逻辑树表示形式，但永远不会呈现在其自己的内容。 相反，它们是_数据模型_其影响的元素的呈现。 完成实际呈现[单独一套_visual 呈现器_该列映射到每个控件类型](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。 这些呈现器均由特定于平台的 Xamarin.Forms 程序集注册每个特定于平台的项目中。 你可以看到列表[此处](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。 除了替换或扩展呈现器，Xamarin.Forms 还具有对支持[效果](~/xamarin-forms/app-fundamentals/effects/index.md)可用于影响基于每个 plaform 本机呈现。

#### <a name="the-logicalvisual-tree"></a>逻辑/Visual 树

没有公开的 API Xamarin.Forms-中遍历的逻辑树，但你可以使用反射来获取相同的信息。 例如，[下面是可以枚举逻辑子级的方法](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108)使用反射。

## <a name="graphics"></a>图形

Xamarin.Forms 不包括对基元超出一个简单的矩形的图形系统 (`BoxView`)。 你可以包括第三方库，例如[SkiaSharp](~/graphics-games/skiasharp/index.md)若要获取跨平台 2D 绘图或[UrhoSharp](~/graphics-games/urhosharp/index.md) 3d。

## <a name="resources"></a>资源

WPF 和 Xamarin.Forms 都具有资源和资源字典的概念。 你可以将放置到任何对象类型`ResourceDictionary`键然后查找它与`{StaticResource}`不会更改的内容或`{DynamicResource}`可以更改在运行时字典中的内容。 使用情况和机制是相同，但有一处不同： Xamarin.Forms 要求您定义`ResourceDictionary`要分配给`Resources`属性而 WPF 预先创建一个并将其分配为你。

有关示例，请参阅以下定义：

**WPF**

```xaml
<Window.Resources>
   <Color x:Key="redColor">#ff0000</Color>
   ...
</Window.Resources>
```

**Xamarin.Forms**

```xaml
<ContentPage.Resources>
   <ResourceDictionary>
      <Color x:Key="redColor">#ff0000</Color>
      ...
   </ResourceDictionary>
</ContentPage.Resources>
```

如果未定义`ResourceDictionary`，则会生成运行时错误。

## <a name="styles"></a>样式

样式中 Xamarin.Forms 还完全支持，并且可以用于对主题构成 UI 的 Xamarin.Forms 元素。 它们支持通过触发器 （属性、 事件和数据），继承`BasedOn`，和值的资源查找。 样式应用于元素通过任一明确`Style`属性或通过不提供的资源键-一样 WPF 隐含。

### <a name="device-styles"></a>设备样式

WPF 具有一组预定义的属性 (如存储为一组静态类上的静态值`SystemColors`) 其规定系统颜色、 字体和度量值和资源键的窗体中的值。 Xamarin.Forms 会非常相似，但定义一组[设备样式](~/xamarin-forms/user-interface/styles/device.md)来表示的相同操作。 这些样式是按 frameowrk 提供，并且设置为根据运行时环境 （例如辅助功能） 的值。

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
