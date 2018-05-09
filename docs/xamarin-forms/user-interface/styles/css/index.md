---
title: 使用级联样式表样式 Xamarin.Forms 应用
description: Xamarin.Forms 支持使用级联样式表 (CSS) 样式可视元素。
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 811abacff330bf7b6e6240691cb6a15ebbd9d242
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets"></a>使用级联样式表样式 Xamarin.Forms 应用

_Xamarin.Forms 支持使用级联样式表 (CSS) 样式可视元素。_

Xamarin.Forms 3.0 引入了设置的应用使用 CSS 样式的功能。 样式表包含规则，由一个或多个选择器和声明块组成的每个规则的列表。 声明块包含在括号中，每个声明属性、 一个冒号和一个值组成的声明的列表。 当有多个块中的声明时，用分号作为分隔符插入。 下面的代码示例演示一些 Xamarin.Forms 符合 CSS:

```css
^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

Xamarin.Forms，这在 CSS 样式表可以分析和在运行时，而不是编译时计算，样式表是在使用重新分析。

> [!NOTE]
> 目前，可以使用 XAML 样式的样式的所有无法执行带 CSS。 但是，可以使用 XAML 样式来补充 CSS Xamarin.Forms 当前不支持的属性。 有关 XAML 样式的详细信息，请参阅[样式 Xamarin.Forms 应用使用 XAML 样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

[MonkeyAppCSS](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)的示例演示了如何使用 CSS 样式的简单应用，并在下面的屏幕截图所示：

[![使用 CSS 样式 MonkeyApp 主页](css-images/MonkeyAppMainPage.png "与 CSS 样式 MonkeyApp 主页")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp 主页上与 CSS 样式")

[![使用 CSS 样式 MonkeyApp 详细信息页](css-images/MonkeyAppDetailPage.png "MonkeyApp 与 CSS 样式的详细信息页")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp 与 CSS 样式的详细信息页")

> [!NOTE]
> 它目前不可能的样式的背景色[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)使用样式表。 因此，在示例应用程序[ `NavigationPage.BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)在代码中设置属性。

## <a name="consuming-a-style-sheet"></a>使用样式表

将样式表添加到解决方案的过程如下所示：

1. 到.NET 标准库项目中添加一个空的 CSS 文件。
1. 设置到 CSS 文件的生成操作**EmbeddedResource**。

### <a name="loading-a-style-sheet"></a>加载样式表

有多种方法可以用于加载样式表。

### <a name="xaml"></a>XAML

样式表可以加载和分析与[ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet)类，然后添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)页：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    ...
</ContentPage>
```

[ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source)属性为相对于封闭的 XAML 文件，位置或相对的项目根目录位置的 URI 指定的样式表，如果 URI 开头`/`。

> [!WARNING]
> CSS 文件将无法加载若是生成操作未设置为**EmbeddedResource**。

或者，可以加载，则用分列样式表[ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet)类通过内联在`CDATA`部分：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

### <a name="c"></a>C#

在 C# 中，样式表可以加载作为嵌入资源和添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)页：

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        this.Resources.Add(StyleSheet.FromAssemblyResource(
            IntrospectionExtensions.GetTypeInfo(typeof(MyPage)).Assembly,
            "MyProject.Assets.styles.css"));
    }
}
```

第一个参数`StyleSheet.FromAssemblyResource`方法是包含样式表的程序集，同时第二个自变量是`string`表示的资源标识符。 可以从获取的资源标识符**属性**窗口时选择 CSS 文件。

或者，从加载样式表`StringReader`并添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)页：

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

自变量`StyleSheet.FromReader`方法是`TextReader`，它具有读取样式表。

## <a name="selecting-elements-and-applying-properties"></a>选择元素并将其应用属性

CSS 使用选择器来确定哪些元素要目标。 具有匹配的选择器样式的连续，应用定义顺序。 始终最后应用样式定义某个特定项目。 有关受支持的选择器的详细信息，请参阅[选择器引用](#selector-reference)。

CSS 使用属性来设置所选的元素的样式。 每个属性具有一组的可能的值和其他适用于组的元素时，某些属性可能会影响任何类型的元素。 有关支持的属性的详细信息，请参阅[属性引用](#property-reference)。

### <a name="selecting-elements-by-type"></a>选择由类型的元素

可视化树中的元素都可以通过使用区分大小写的类型选取`element`选择器：

```css
stacklayout {
    margin: 20;
}
```

此选择器标识任何[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)使用样式表，并将其边距设置为统一粗细，20 即页上的元素。

> [!NOTE]
> `element`选择器不会确定指定类型的子类。

### <a name="selecting-elements-by-base-class"></a>由基类选择元素

可视化树中的元素都可以通过使用区分大小写的基类选取`^base`选择器：

```css
^contentpage {
    background-color: lightgray;
}
```

此选择器标识任何[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)元素，使用样式表，并将设置其背景颜色到`lightgray`。

> [!NOTE]
> `^base`选择器是特定于 Xamarin.Forms，并且不是 CSS 规范的一部分。

### <a name="selecting-an-element-by-name"></a>按名称选择元素

可以使用区分大小写选择的可视化树中各个元素`#id`选择器：

```css
#listView {
    background-color: lightgray;
}
```

此选择器标识此元素其[ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)属性设置为`listView`。 但是，如果`StyleId`未设置属性，选择器将回退为使用`x:Name`的元素。 因此，在下面的 XAML 示例中，`#listView`选择器将标识[ `ListView` ](xref:Xamarin.Forms.ListView)其`x:Name`属性设置为`listView`，并将将背景色设置为`lightgray`。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>选择具有特定的类特性的元素

可以使用区分大小写选择具有特定的类特性的元素`.class`选择器：

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

CSS 类可以通过设置分配给 XAML 元素[ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass)到 CSS 类名称的元素的属性。 因此，在下面的 XAML 示例中，定义了样式通过`.detailPageTitle`类分配给第一个[ `Label` ](xref:Xamarin.Forms.Label)，而定义的样式`.detailPageSubtitle`类都被分配给第二个`Label`。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>选择子元素

中的可视化树子元素可以选择使用区分大小写`element element`选择器：

```css
listview image {
    height: 60;
    width: 60;
}
```

此选择器标识任何[ `Image` ](xref:Xamarin.Forms.Image)元素的子级的[ `ListView` ](xref:Xamarin.Forms.ListView)元素，并将其高度和宽度设置为 60。 因此，在下面的 XAML 示例中，`listview image`选择器将标识[ `Image` ](xref:Xamarin.Forms.Image)是的子级[ `ListView` ](xref:Xamarin.Forms.ListView)，并将其高度和宽度设置为 60。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> `element element`选择器不需要的子元素要_直接_父-子元素的子级可能具有不同的父级。 提供该祖先是指定的第一个元素，则发生所选内容。

### <a name="selecting-direct-child-elements"></a>选择直接子元素

直接中的可视化树子元素可以选择使用区分大小写`element>element`选择器：

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

此选择器标识任何[ `Image` ](xref:Xamarin.Forms.Image)的元素的直接子级[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)元素，并将其高度和宽度设置为 200。 因此，在下面的 XAML 示例中，`stacklayout>image`选择器将标识[ `Image` ](xref:Xamarin.Forms.Image)是的直接子级[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，并将其高度和宽度设置为 200。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> `element>element`选择器需要，子元素是_直接_的父级子级。

## <a name="selector-reference"></a>选择器引用

Xamarin.Forms 支持以下 CSS 选择器：

|选择器|示例|描述|
|---|---|---|
|`.class`|`.header`|选择具有的所有元素`StyleClass`包含标头的属性。 请注意，此选择器是区分大小写。|
|`#id`|`#email`|选择具有的所有元素`StyleId`设置为`email`。 如果`StyleId`未设置，则回退到`x:Name`。 当使用 XAML，`x:Name`是优于`StyleId`。 请注意，此选择器是区分大小写。|
|`*`|`*`|选择所有元素。|
|`element`|`label`|选择类型的所有元素`Label`，但不是子类。 请注意，此选择器是区分大小写。|
|`^base`|`^contentpage`|选择具有的所有元素`ContentPage`为基类，包括`ContentPage`本身。 请注意此选择器是区分大小写，并且不是 CSS 规范的一部分。|
|`element,element`|`label,button`|选择所有`Button`元素及其所有`Label`元素。 请注意，此选择器是区分大小写。|
|`element element`|`stacklayout label`|选择所有`Label`内的元素`StackLayout`。 请注意，此选择器是区分大小写。|
|`element>element`|`stacklayout>label`|选择所有`Label`元素`StackLayout`作为直接父项。 请注意，此选择器是区分大小写。|
|`element+element`|`label+entry`|选择所有`Entry`之后的元素直接`Label`。 请注意，此选择器是区分大小写。|
|`element~element`|`label~entry`|选择所有`Entry`元素前面是`Label`。 请注意，此选择器是区分大小写。|

具有匹配的选择器样式的连续，应用定义顺序。 始终最后应用样式定义某个特定项目。

> [!TIP]
> 选择器可以组合而没有限制，如`StackLayout>ContentView>label.email`。

以下的选择器是当前不支持：

- `[attribute]`
- `@media` 和 `@supports`
- `:` 和 `::`

> [!NOTE]
> 要求和特殊性替代是不受支持。

## <a name="property-reference"></a>属性引用

Xamarin.Forms 支持以下 CSS 属性 (在**值**列中，类型是_斜体_，当字符串文本在`gray`):

|属性|适用对象|值|示例|
|---|---|---|---|
|`background-color`|`VisualElement`|_颜色_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_字符串_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`|_颜色_ \| `initial`|`border-color: #9acd32;`|
|`border-width`|`Button`|_双精度_ \| `initial` |`border-width: .5;`|
|`color`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`|_颜色_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_字符串_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_double_ \| _namedsize_  \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_双精度_ \| `initial` |`min-height: 250;`|
|`margin`|`View`|_粗细_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_粗细_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_粗细_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_粗细_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_粗细_ \| `initial` |`margin-bottom: 6;`|
|`min-height`|`VisualElement`|_双精度_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_双精度_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_双精度_ \| `initial` |`opacity: .3;`|
|`padding`|`Layout`, `Page`|_粗细_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Layout`, `Page`|_双精度_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Layout`, `Page`| _双精度_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Layout`, `Page`| _双精度_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Layout`, `Page`| _双精度_ \| `initial` |`padding-bottom: 6;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `right` \| `center` \| `start` \| `end` \| `initial`. `left` 和`right`应当避免在从右到左环境中。| `text-align: right;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial `|`visibility: hidden;`|
|`width`|`VisualElement`|_双精度_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` 是一个有效的所有属性值。 它将清除从另一种样式设置的值 （重置为默认值）。

以下属性是当前不支持：

- `all: initial`。
- 布局属性 （框中，或网格）。
- 速记属性，如`font`，和`border`。

此外，没有任何`inherit`值，因此继承不受支持。 因此你不能例如，将设置`font-size`布局上的属性上，并希望所有[ `Label` ](xref:Xamarin.Forms.Label)要继承的值的布局中的实例。 一个例外是`direction`属性，用于具有默认值的`inherit`。

### <a name="color"></a>颜色

以下`color`支持值：

- `X11` [颜色](https://en.wikipedia.org/wiki/X11_color_names/)，CSS 颜色、 UWP 预定义的颜色和 Xamarin.Forms 颜色匹配的。 请注意，这些颜色值区分大小写。
- 十六进制颜色： `#rgb`， `#argb`， `#rrggbb`， `#aarrggbb`
- rgb 颜色： `rgb(255,0,0)`， `rgb(100%,0%,0%)`。 值为范围内，0-255 或 0%至 100%。
- rgba 颜色： `rgba(255, 0, 0, 0.8)`， `rgba(100%, 0%, 0%, 0.8)`。 不透明度值为 0.0 1.0 范围内。
- hsl 颜色： `hsl(120, 100%, 50%)`。 H 值位于范围 0-360，s 和 l 是在范围 0%-100%。
- hsla 颜色： `hsla(120, 100%, 50%, .8)`。 不透明度值为 0.0 1.0 范围内。

### <a name="thickness"></a>Thickness

一个、 两个、 三个或四个`thickness`支持值，每个用空格分隔：

- 单个值指示统一粗细。
- 两个值指示垂直然后水平粗细。
- 三个值指示顶部，然后水平 （左、 右），然后底部粗细。
- 四个值指示顶部，然后右侧，底部，依次左的粗细。

> [!NOTE]
> CSS`thickness`值不同于 XAML [ `Thickness` ](/api/type/Xamarin.Forms.Thickness/)值。 例如，在 XAML 两个值`Thickness`指示水平然后垂直粗细，而四个值`Thickness`指示左侧，然后顶部，然后右，然后下粗细。 此外，XAML`Thickness`的值为逗号分隔。

### <a name="namedsize"></a>NamedSize

以下区分大小写`namedsize`支持值：

- `default`
- `micro`
- `small`
- `medium`
- `large`

每个的确切含义`namedsize`依赖平台的代码和视图相关值。

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>与 Xamarin.University Xamarin.Forms 中的 CSS

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Forms 3.0 CSS，也可由[Xamarin 大学](https://university.xamarin.com/)**

## <a name="related-links"></a>相关链接

- [MonkeyAppCSS （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)
- [使用 XAML 样式的样式 Xamarin.Forms 应用](~/xamarin-forms/user-interface/styles/xaml/index.md)
