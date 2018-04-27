---
title: AbsoluteLayout
description: 使用 AbsoluteLayout 创建像素完美 Ui。
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 110c608558059ba0f207b4cff343b428125e1784
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="absolutelayout"></a>AbsoluteLayout

[`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) 定位并调整其自己的大小和位置或绝对值成比例的子元素的大小。 子视图可以定位和比例的大小使用成比例的值或静态值，并且可以混合使用静态值。

[![](absolute-layout-images/layouts-sml.png "Xamarin.Forms 布局")](absolute-layout-images/layouts.png#lightbox "Xamarin.Forms 布局")

本文将介绍：

- **[目的](#Purpose)** &ndash;的常见用途`AbsoluteLayout`。
- **[使用情况](#Usage)** &ndash;如何使用`AbsoluteLayout`能够实现您所需的设计。
  - **[成比例的布局](#Proportional_Layouts)** &ndash;了解如何成比例的值即可在`AbsoluteLayout`。
  - **[指定值](#Specifying_Values)** &ndash;了解如何指定比例和绝对值。
  - **[按比例值](#Proportional_Values)** &ndash;了解如何按比例值工作。
    - **[绝对值](#Absolute_Values)** &ndash;了解绝对值的工作原理。

<a name="Purpose" />

## <a name="purpose"></a>目标

由于的定位模型`AbsoluteLayout`，布局，使相对比较简单，以便它们与的布局，任何一侧对齐或居中定位元素。 按比例大小和位置、 中的元素`AbsoluteLayout`可以自动缩放到任意视图大小。 对于仅的位置，但不是大小应增加的项，可以混合绝对和比例值。

`AbsoluteLayout` 可以使用任何位置元素需要的视图中定位和对齐到边缘的元素时尤其有用。

<a name="Usage" />

## <a name="usage"></a>用法

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>成比例的布局

`AbsoluteLayout` 具有唯一的定位点模型凭此元素的定位点相对于定位其元素为成比例定位使用时，该元素位于相对于布局。 当使用绝对定位时，锁定标记视图中位于 (0，0)。 这样做有两个重要的后果：

- 无法关闭屏幕使用成比例值定位元素。
- 元素可以可靠地定位沿任何一侧的布局或在中心中，而不考虑布局或设备的大小。

`AbsoluteLayout`如`RelativeLayout`，能够将放置元素，因此它们重叠。

请注意，在以下屏幕截图中，框中的定位标记为一个白色的点。 布局中移动，请注意定位点和框之间的关系：

![](absolute-layout-images/anchor-start.png "定位点在启动")
![](absolute-layout-images/anchor-center.png "定位点在中心")
![](absolute-layout-images/anchor-end.png "末尾的定位点")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>指定值

视图内`AbsoluteLayout`位于使用四个值：

- **X** &ndash;视图的定位点的 x （水平） 位置
- **Y** &ndash;视图的定位点的 y （垂直） 位置
- **宽度**&ndash;视图的宽度
- **高度**&ndash;视图的高度

每个这些值可以设置为[成比例](#Proportional_Values)值或[绝对](#Absolute_Values)值。

值指定为边界和标志的组合。 `LayoutBounds` 是[ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/)包含以下四个值： `x`， `y`， `width`， `height`。

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/) 指定将如何解释值并具有以下预定义的选项：

- **无**&ndash;将所有值都解释为绝对值。 如果未不指定任何布局标志，这是默认值。
- **所有**&ndash;解释为比例的所有值。
- **WidthProportional** &ndash;解释`Width`值作为按比例和所有其他值为绝对值。
- **HeightProportional** &ndash;仅高度会将该值解释为比例绝对所有其他值。
- **XProportional** &ndash;解释`X`时将其他所有值视为绝对值作为成比例。
- **YProportional** &ndash;解释`Y`时将其他所有值视为绝对值作为成比例。
- **PositionProportional** &ndash;解释`X`和`Y`时的大小值都会被解释为绝对值作为成比例。
- **SizeProportional** &ndash;解释`Width`和`Height`绝对位置值时，值作为成比例。

在 XAML 中，边界和标志设置为视图布局中, 定义的一部分使用`AbsoluteLayout.LayoutBounds`属性。 边界设置的值，以逗号分隔列表为`X`， `Y`， `Width`，和`Height`按顺序。 在布局中使用的视图的声明中也指定标志`AbsoluteLayout.LayoutFlags`属性。 请注意，可以在 XAML 中使用逗号分隔的列表组合标志。 请看下面的示例：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.AbsoluteLayoutExploration"
Title="Absolute Layout Exploration">
    <ContentPage.Content>
        <AbsoluteLayout>
            <Label Text="I'm centered on iPhone 4 but no other device"
                AbsoluteLayout.LayoutBounds="115,150,100,100" LineBreakMode="WordWrap"  />
            <Label Text="I'm bottom center on every device."
                AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"
                LineBreakMode="WordWrap"  />
            <BoxView Color="Olive"  AbsoluteLayout.LayoutBounds="1,.5, 25, 100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Red" AbsoluteLayout.LayoutBounds="0,.5,25,100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Blue" AbsoluteLayout.LayoutBounds=".5,0,100,25"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

![](absolute-layout-images/exploration.png "AbsoluteLayout 示例")

请注意以下事项：

- 在中心则标签将定位使用绝对大小和位置的值。 正因如此，它会显示在 iPhone 4S 上居中和较低，但在更大的设备上不为中心。
- 使用成比例的大小和位置值定位底部的布局的文本。 它将始终出现在底部中间的布局，但其大小会随更大的布局大小而增大。
- 三个颜色`BoxView`s 位于使用成比例的位置和绝对大小屏幕的顶部、 左和右边缘。

以下实现 C# 中的布局相同：

```csharp
public class AbsoluteLayoutExplorationCode : ContentPage
{
    public AbsoluteLayoutExplorationCode ()
    {
        Title = "Absolute Layout Exploration - Code";
        var layout = new AbsoluteLayout();

        var centerLabel = new Label {
        Text = "I'm centered on iPhone 4 but no other device.",
        LineBreakMode = LineBreakMode.WordWrap};

        AbsoluteLayout.SetLayoutBounds (centerLabel, new Rectangle (115, 159, 100, 100));
        // No need to set layout flags, absolute positioning is the default

        var bottomLabel = new Label { Text = "I'm bottom center on every device.", LineBreakMode = LineBreakMode.WordWrap };
        AbsoluteLayout.SetLayoutBounds (bottomLabel, new Rectangle (.5, 1, .5, .1));
        AbsoluteLayout.SetLayoutFlags (bottomLabel, AbsoluteLayoutFlags.All);

        var rightBox = new BoxView{ Color = Color.Olive };
        AbsoluteLayout.SetLayoutBounds (rightBox, new Rectangle (1, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (rightBox, AbsoluteLayoutFlags.PositionProportional);

        var leftBox = new BoxView{ Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds (leftBox, new Rectangle (0, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (leftBox, AbsoluteLayoutFlags.PositionProportional);

        var topBox = new BoxView{ Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds (topBox, new Rectangle (.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags (topBox, AbsoluteLayoutFlags.PositionProportional);

        layout.Children.Add (bottomLabel);
        layout.Children.Add (centerLabel);
        layout.Children.Add (rightBox);
        layout.Children.Add (leftBox);
        layout.Children.Add (topBox);

        Content = layout;
    }
}
```
<a name="Proportional_Values" />

### <a name="proportional-values"></a>成比例的值

成比例的值定义布局和视图之间的关系。 此关系定义为父布局的相应值的比例子视图的位置或缩放值。 这些值表示为`double`与介于 0 和 1 之间的值。

为位置和大小视图布局中的，将使用成比例的值。 因此，如果视图的宽度设为比例，生成的宽度值是乘以的比例`AbsoluteLayout`的宽度。 例如，对于`AbsoluteLayout`宽度的`500`和设置，使.5，视图的呈现宽度成比例宽度的视图将 250 (500 x.5).

若要使用成比例的值，设置`LayoutBounds`使用 （x，y） 比例和比例大小，然后设置`LayoutFlags`到`All`。

在 XAML 中：

```xaml
<Label Text="I'm bottom center on every device."
  AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"  />
```

在 C# 中：

```csharp
var label = new Label {Text = "I'm bottom center on every device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(.5,1,.5,.1));
AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.All);
```

<a name="Absolute_Values" />

### <a name="absolute-values"></a>绝对值

绝对值显式定义视图布局中的放置位置。 与成比例值不同绝对值是能够定位并调整大小不适合于布局的边界的视图。

如果不知道的布局的大小，使用绝对值定位可能很危险。 在使用绝对位置时，无法在任何其他大小偏移量在一个大小在屏幕的中心中的某个元素。 请务必跨各种屏幕大小的受支持的设备中测试你的应用程序。

若要使用绝对布局值，设置`LayoutBounds`使用 （x，y） 坐标和显式大小，然后设置`LayoutFlags`到`None`。

在 XAML 中：

```xaml
<Label Text="I'm centered on iPhone 4 but no other device."
  AbsoluteLayout.LayoutBounds="115,150,100,100"  />
```

在 C# 中：

```csharp
var label = new Label {Text = "I'm centered on iPhone 4 but no other device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(115,150,100,100));
```

## <a name="exploring-a-complex-layout"></a>浏览复杂布局

每个布局有优点和缺点为特定布局的创建。 在这一系列的布局文章，整个示例应用程序已创建具有相同的页面布局实现使用三种不同的布局。

请考虑下面的 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.AbsoluteLayoutPage"
Title="AbsoluteLayout">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Save" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <ScrollView>
            <AbsoluteLayout BackgroundColor="Maroon">
                <BoxView BackgroundColor="Gray" AbsoluteLayout.LayoutBounds="0
                    0,1,100" AbsoluteLayout.LayoutFlags="XProportional,YProportional,WidthProportional" />
                <Button BackgroundColor="Maroon"
                    AbsoluteLayout.LayoutBounds=".5,55,70,70" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="35" />
                <Button BackgroundColor="Red" AbsoluteLayout.LayoutBounds=".5
                    60,60,60" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="30" />
                <Label Text="User Name" FontAttributes="Bold" FontSize="26"
                    TextColor="Black" HorizontalTextAlignment="Center" AbsoluteLayout.LayoutBounds=".5,140,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <Entry Text="Bio + Hashtags" TextColor="White"
                    BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds=".5,180,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <AbsoluteLayout BackgroundColor="White"
                        AbsoluteLayout.LayoutBounds="0, 220, 1, 50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional">
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional">
                        <Button BackgroundColor="Black" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Accent Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="1,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional,XProportional">
                        <Button BackgroundColor="Maroon" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Primary Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,270,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Age:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
                        AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,320,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Interests:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin.Forms" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,370,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Ask me about:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
            </AbsoluteLayout>
        </ScrollView>
    </ContentPage.Content>
</ContentPage>
```

上面的代码将导致以下布局：

![](absolute-layout-images/abs.png "复杂 AbsoluteLayout")

请注意， `AbsoluteLayout`s 嵌套的因为在某些情况下嵌套布局可以更方便地比提供相同的布局中的所有元素。

## <a name="related-links"></a>相关链接

- [借助 Xamarin.Forms，第 14 章创建移动应用](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)
- [布局 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 示例 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
