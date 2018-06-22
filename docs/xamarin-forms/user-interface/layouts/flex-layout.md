---
title: Xamarin.Forms FlexLayout
description: 使用 FlexLayout 堆叠或包装的子视图的集合。
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: 7585138cd6c33c2a5dc537ba28101a84e1c4b7ae
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921831"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin.Forms FlexLayout

_使用 FlexLayout 堆叠或包装的子视图的集合。_

Xamarin.Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) Xamarin.Forms 版本 3.0 中。 它基于 CSS[灵活框布局模块](http://www.w3.org/TR/css-flexbox-1/)，通常称为_弹性布局_或_弹性框_，因此调用因为它包含许多的灵活选项可用于排列子级在布局。

`FlexLayout` 类似于 Xamarin.Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md)在于它可以排列其子水平和垂直堆栈。 但是，`FlexLayout`仍可包装及其子级，如果有太多的单个行或列中, 容纳不下，还提供许多选项方向、 对齐和适应各种屏幕尺寸。

`FlexLayout` 派生自[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)和继承[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/)类型的属性`IList<View>`。

`FlexLayout` 定义六个公共可绑定属性和影响大小、 方向和子元素的对齐方式的五个附加的可绑定属性。 (如果你不熟悉附加可绑定属性，请参阅文章**[附加属性](~/xamarin-forms/xaml/attached-properties.md)**。)这些属性在以下各节中的详细信息中所述上**[详细信息中的可绑定属性](#bindable-properties)** 和**[详细附加的可绑定属性](#attached-properties)**. 但是，本文开头上某些部分**[常见使用方案](#common-scenarios)** 的`FlexLayout`非正式名称描述其中的许多属性。 将本文末尾看到如何组合使用`FlexLayout`与[CSS 样式表](~/xamarin-forms/user-interface/styles/css/index.md)。

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>常见使用方案

**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 示例程序包含多个页面的一些常见用途该 demonstate `FlexLayout` ，并允许您尝试使用其属性。

### <a name="using-flexlayout-for-a-simple-stack"></a>使用适用于简单堆栈 FlexLayout

**简单堆栈**页面显示了如何`FlexLayout`可替换为`StackLayout`但更简单的标记。 XAML 页中定义了此示例中的所有内容。 `FlexLayout`包含四个子项：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.SimpleStackPage"
             Title="Simple Stack">

    <FlexLayout Direction="Column"
                AlignItems="Center"
                JustifyContent="SpaceEvenly">

        <Label Text="FlexLayout in Action"
               FontSize="Large" />

        <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />

        <Button Text="Do-Nothing Button" />

        <Label Text="Another Label" />
    </FlexLayout>
</ContentPage>
```

下面是在 iOS、 Android 和通用 Windows 平台上运行该页面：

[![简单堆栈页](flex-layout-images/SimpleStack.png "简单堆栈页")](flex-layout-images/SimpleStack-Large.png#lightbox)

三个属性`FlexLayout`中所示**SimpleStackPage.xaml**文件：

- [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction)属性设置为值为[ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection)枚举。 默认值为 `Row`。 将属性设置为`Column`导致的子级`FlexLayout`项的单个列中排列。

    当中的项`FlexLayout`排列在一列，`FlexLayout`称具有垂直_主轴_和水平_跨轴_。

- [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems)属性属于类型[ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems)和指定项在横轴上的对齐方式。 `Center`选项将导致每个项可水平居中。

    如果你使用`StackLayout`而不是`FlexLayout`对于此任务，您将通过分配居中的所有项`HorizontalOptions`每个项的属性`Center`。 `HorizontalOptions`属性不起作用的子级`FlexLayout`，但单`AlignItems`属性来实现相同的目标。 如果需要你可以使用`AlignSelf`附加可绑定属性重写`AlignItems`各项的属性：

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    进行该更改后，此`Label`将位于的左边缘`FlexLayout`时的阅读顺序是从左到右。

- [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent)属性属于类型[ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)，并指定项主要轴上的排列方式。 `SpaceEvenly`选项分配各个所有项之间均匀地和上方的第一项，和下方的最后一项的所有剩余垂直空间。

    如果你使用`StackLayout`，你将需要分配`VerticalOptions`每个项的属性`CenterAndExpand`来实现类似的效果。 但`CenterAndExpand`选项将分配比每个项的第一项之前和之后的最后一项之间的两倍于空间。 可模拟`CenterAndExpand`选项`VerticalOptions`通过设置`JustifyContent`属性`FlexLayout`到`SpaceAround`。

这些`FlexLayout`部分中的更详细地讨论属性**[详细信息中的可绑定属性](#bindable-properties)** 下面。

### <a name="using-flexlayout-for-wrapping-items"></a>用于 FlexLayout 包装项

**照片包装**页**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 示例演示如何`FlexLayout`可以包装到其他行或列及其子级。 XAML 文件实例化`FlexLayout`并分配它的两个属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.PhotoWrappingPage"
             Title="Photo Wrapping">
    <Grid>
        <ScrollView>
            <FlexLayout x:Name="flexLayout"
                        Wrap="Wrap"
                        JustifyContent="SpaceAround" />
        </ScrollView>

        <ActivityIndicator x:Name="activityIndicator"
                           IsRunning="True"
                           VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

`Direction`此属性`FlexLayout`未设置，因此它具有的默认设置`Row`，这意味着，子级排列为行，并且主要轴为水平方向。

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap)属性是枚举类型[ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap)。 如果有太多的项，以适应行上，则此属性设置可能导致要换行到下一行中的项。

请注意，`FlexLayout`是的子级`ScrollView`。 如果有太多的行，以适合在页上，则`ScrollView`具有一个默认`Orientation`属性`Vertical`，并允许垂直滚动。

`JustifyContent`属性分配主轴 （水平轴） 上的剩余空间，以便每个项围绕的空白区域相同的量。

代码隐藏文件访问示例照片集合，并将它们添加到`Children`集合`FlexLayout`:

```csharp
public partial class PhotoWrappingPage : ContentPage
{
    // Class for deserializing JSON list of sample bitmaps
    [DataContract]
    class ImageList
    {
        [DataMember(Name = "photos")]
        public List<string> Photos = null;
    }

    public PhotoWrappingPage ()
    {
        InitializeComponent ();

        LoadBitmapCollection();
    }

    async void LoadBitmapCollection()
    {
        int imageDimension = Device.RuntimePlatform == Device.iOS ||
                             Device.RuntimePlatform == Device.Android ? 240 : 120;

        string urlSuffix = String.Format("?width={0}&height={0}&mode=max", imageDimension);

        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("http://docs.xamarin.com/demo/stock.json");
                byte[] data = await webClient.DownloadDataTaskAsync(uri);

                // Convert to a Stream object
                using (Stream stream = new MemoryStream(data))
                {
                    // Deserialize the JSON into an ImageList object
                    var jsonSerializer = new DataContractJsonSerializer(typeof(ImageList));
                    ImageList imageList = (ImageList)jsonSerializer.ReadObject(stream);

                    // Create an Image object for each bitmap
                    foreach (string filepath in imageList.Photos)
                    {
                        Image image = new Image
                        {
                            Source = ImageSource.FromUri(new Uri(filepath + urlSuffix))
                        };
                        flexLayout.Children.Add(image);
                    }
                }
            }
            catch
            {
                flexLayout.Children.Add(new Label
                {
                    Text = "Cannot access list of bitmap files"
                });
            }
        }

        activityIndicator.IsRunning = false;
        activityIndicator.IsVisible = false;
    }
}
```

此处是渐进式滚动从顶部到底部的三个平台上运行的程序：

[![照片包装页](flex-layout-images/PhotoWrapping.png "照片包装页")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>与 FlexLayout 页面布局

存在正在调用的 web 设计的标准布局[_圣杯_](https://en.wikipedia.org/wiki/Holy_grail_(web_design))因为它是非常可取的但通常难以实现与完美的布局格式。 布局包括在页面顶部的页眉和页脚在底部，这两个将扩展到页面的整个宽度。 占用页的中心是主要内容，但通常与纵栏表的内容和补充信息左侧菜单 (有时称为_留出_区域) 的右侧。 [CSS 灵活框布局规范的第 5.4.1 节](http://www.w3.org/TR/css-flexbox-1/#order-accessibility)描述如何在弹性框实现圣杯布局。

**圣杯布局**页**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 示例演示一个使用此布局的简单实现`FlexLayout`嵌套在另一个。 因为此页专为手机上纵向模式中，左侧和右侧的内容区域的区域仅包括 50 像素宽：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.HolyGrailLayoutPage"
             Title="Holy Grail Layout">

    <FlexLayout Direction="Column">

        <!-- Header -->
        <Label Text="HEADER"
               FontSize="Large"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center" />

        <!-- Body -->
        <FlexLayout FlexLayout.Grow="1">

            <!-- Content -->
            <Label Text="CONTENT"
                   FontSize="Large"
                   BackgroundColor="Gray"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center"
                   FlexLayout.Grow="1" />

            <!-- Navigation items-->
            <BoxView FlexLayout.Basis="50"
                     FlexLayout.Order="-1"
                     Color="Blue" />

            <!-- Aside items -->
            <BoxView FlexLayout.Basis="50"
                     Color="Green" />

        </FlexLayout>

        <!-- Footer -->
        <Label Text="FOOTER"
               FontSize="Large"
               BackgroundColor="Pink"
               HorizontalTextAlignment="Center" />
    </FlexLayout>
</ContentPage>
```

此处三个平台上运行它：

[![圣杯布局页](flex-layout-images/HolyGrailLayout.png "圣杯布局页")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

导航和 aside 区域呈现使用`BoxView`左侧和右侧。

第一个`FlexLayout`在 XAML 文件具有垂直主轴，且包含排列在一列的三个子级。 它们分别标头、 页上和页脚的主体。 嵌套`FlexLayout`水平主轴有排列在行中的三个子级。

在此程序中演示了三个附加的可绑定属性：

- `Order`设置附加的可绑定属性的第一天`BoxView`。 此属性是一个整数，它默认值为 0。 此属性可用于更改布局顺序。 通常开发人员喜欢页面才会出现在之前的导航项标记的内容，并且保留项。 设置`Order`在第一个属性`BoxView`为一个值小于其他同级使它显示为一行中的第一个项。 同样，你可以确保项通过设置显示最后一个`Order`属性大于其同级的值。

- `Basis`对两个设置附加的可绑定属性`BoxView`项来为它们指定为 50 像素宽度。 此属性是类型`FlexBasis`，定义类型的静态属性的结构`FlexBasis`名为`Auto`，这是默认设置。 你可以使用`Basis`指定像素大小或一个百分比，它指示多少空间项占用主要轴上。 它称为_基础_因为它指定项大小，是所有后续的布局的基础。

- `Grow`属性设置上嵌套`Layout`并在`Label`子表示内容。 此属性是类型`float`和默认值为 0。 设置为正值时，主要轴的所有剩余空间分配到该项目和具有正值的同级`Grow`。 值，有些类似中的星型规范按比例分配空间`Grid`。

    第一个`Grow`设置附加的属性上嵌套`FlexLayout`，以指示此`FlexLayout`是占用内外部的所有未使用垂直空间`FlexLayout`。 第二个`Grow`上设置附加的属性`Label`表示的内容，，该值指示此内容以占据内部内的所有未使用水平空间`FlexLayout`。

    此外还有类似`Shrink`附加子级的大小超过的大小时可以使用的可绑定属性`FlexLayout`但不是需要包装。

### <a name="catalog-items-with-flexlayout"></a>与 FlexLayout 的目录项

**目录项**页面**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 示例是类似于[示例 1 中的 CSS 弹性布局框规范部分1.1](http://www.w3.org/TR/css-flexbox-1/#overview)，只不过它将显示一系列可水平滚动的图片和三个黑客的说明：

[![目录项页](flex-layout-images/CatalogItems.png "目录项页")](flex-layout-images/CatalogItems-Large.png#lightbox)

每三个黑客是`FlexLayout`中包含`Frame`给定显式高度和宽度，并且也是更大的子`FlexLayout`。 此 XAML 文件的属性中的大多数中`FlexLayout`中样式，除一种是隐式样式指定子级：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CatalogItemsPage"
             Title="Catalog Items">
    <ContentPage.Resources>
        <Style TargetType="Frame">
            <Setter Property="BackgroundColor" Value="LightYellow" />
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="Margin" Value="10" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="Margin" Value="0, 4" />
        </Style>

        <Style x:Key="headerLabel" TargetType="Label">
            <Setter Property="Margin" Value="0, 8" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="Blue" />
        </Style>

        <Style TargetType="Image">
            <Setter Property="FlexLayout.Order" Value="-1" />
            <Setter Property="FlexLayout.AlignSelf" Value="Center" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="White" />
            <Setter Property="BackgroundColor" Value="Green" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}"
                           WidthRequest="240"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

隐式样式`Image`包括两个附加的可绑定属性的设置`Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

`Order`设置&ndash;1 会导致`Image`首先显示在每个嵌套元素`FlexLayout`而不考虑子集合内其位置的视图。 `AlignSelf`属性`Center`导致`Image`以内居中`FlexLayout`。 此设置的设置将替代`AlignItems`属性，用于具有默认值的`Stretch`，这意味着，`Label`和`Button`子级将拉伸到的整个宽度`FlexLayout`。

在这三个`FlexLayout`视图，空白`Label`之前`Button`，但它具有`Grow`设置为 1。 这意味着，将所有额外的垂直空间分配到此值为空`Label`，这有效地推送`Button`到底部。

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>详细信息中的可绑定属性

现在，你已了解的一些常见应用程序`FlexLayout`的属性`FlexLayout`可以更详细地解决。 
`FlexLayout` 定义设置的六个可绑定属性`FlexLayout`本身，放在代码或 XAML 中的，为控件 orientatin 和对齐方式。 (其中一个属性， [ `Position` ](xref:Xamarin.Forms.FlexLayout.Position)，未涵盖这篇文章中。)

你可以试验具有五个剩余可绑定属性使用**试验**页**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 示例。 此页面允许您添加或删除从子级`FlexLayout`并设置五种可绑定属性的组合。 所有子级`FlexLayout`是`Label`的各种颜色和大小，视图与`Text`属性设置为对应的数字到它的位置中`Children`集合。

当程序启动时，五个`Picker`视图以显示五个的默认值`FlexLayout`属性。 `FlexLayout`屏幕的底部包含三个子级：

[![试验页中： 默认值](flex-layout-images/ExperimentDefault.png "试验页中的默认值")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

每个`Label`视图具有灰色背景显示与分配的空间`Label`内`FlexLayout`。 背景的`FlexLayout`本身是 Alice 蓝色。 在左侧和右侧的小边距除外，它占用页的整个底部的区域。

<a name="direction" />

### <a name="the-direction-property"></a>方向属性

[ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction)属性属于类型[ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection)，一个包含四个成员的枚举：

- `Column`
- `ColumnReverse` （或者"列的反向"在 XAML 中）
- `Row`默认值
- `RowReverse` （或者"行的反向"在 XAML 中）

在 XAML 中，你可以指定使用的枚举成员名称以小写字母，大写，此属性的值或混合大小写，也可以使用显示在 CSS 指示器相同的括号中的两个其他字符串。 (在中定义的"列反向"和"行反向"字符串[ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) XAML 分析器使用的类。)

下面是**试验**显示 （从左到右），页`Row`方向，`Column`方向，和`ColumnReverse`方向：

[![试验页中： 方向](flex-layout-images/ExperimentDirection.png "试验页-方向")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

请注意，对于`Reverse`项启动选项，在右侧或底部。

<a name="wrap" />

### <a name="the-wrap-property"></a>换行属性

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap)属性属于类型[ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap)，一个包含三个成员的枚举：

- `NoWrap`默认值
- `Wrap`
- `Reverse` （或者"自动换行的反向"在 XAML 中）

从左到右，这些屏幕显示`NoWrap`，`Wrap`和`Reverse`12 子级的选项：

[![试验页中： 包装](flex-layout-images/ExperimentWrap.png "试验页中的包装")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

当`Wrap`属性设置为`NoWrap`和主轴被约束 （如所示此程序中），并且主轴宽或高度不够，无法以适应所有子级，而不是`FlexLayout`尝试使项更小，作为 iOS 屏幕快照演示。 你可以控制的项的 shrinkness [ `Shrink` ](#shrink)附加可绑定属性。

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>JustifyContent 属性

[ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent)属性属于类型[ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)，一个包含六个成员的枚举：

- `Start` （或"弹性启动"在 XAML 中），默认值
- `Center`
- `End` （或者"弹性端"在 XAML 中）
- `SpaceBetween` （或者"空间-之间"在 XAML 中）
- `SpaceAround` （或空间-围绕"在 XAML 中）
- `SpaceEvenly`

此属性指定如何将项目分布在主轴，在此示例中为水平轴上：

[![试验页中： 说明内容](flex-layout-images/ExperimentJustifyContent.png "试验页中-调整内容")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

在所有三个屏幕截图，`Wrap`属性设置为`Wrap`。 `Start`默认以前的 Android 屏幕截图所示。 此处的 iOS 屏幕截图显示`Center`选项： 所有项都将都移到中心。 在三个其他选项开头单词`Space`分配项未占用的额外空间。 `SpaceBetween` 分配的空间各个项，则之间均匀地`SpaceAround`将等于每个项周围的空间时`SpaceEvenly`将等于每个项之间的第一项前后的最后一项在该行上的空间。

<a name="align-items" />

### <a name="the-alignitems-property"></a>AlignItems 属性

[ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems)属性属于类型[ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems)，一个包含四个成员的枚举：

- `Stretch`默认值
- `Center`
- `Start` （或者"弹性启动"在 XAML 中）
- `End` （或者"弹性端"在 XAML 中）

这是两个属性之一 (另一个执行[ `AlignContent` ](#align-content))，该值指示子级交叉轴上的对齐方式。 在每个行，子级拉伸 （如上面的屏幕快照中所示），或者对齐开始、 居中或末尾每个项，如以下三个屏幕快照中所示：

[![试验页中： 对齐项](flex-layout-images/ExperimentAlignItems.png "试验页中的对齐项")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

在 iOS 屏幕截图中，所有的子级的顶部对齐。 在 Android 的屏幕截图，项垂直居中基于最高子。 在 UWP 屏幕截图中，所有项的底部对齐。

对于任何单个项，`AlignItems`可用重写设置[ `AlignSelf` ](#align-self)附加可绑定属性。

<a name="align-content" />

### <a name="the-aligncontent-property"></a>AlignContent 属性

[ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent)属性属于类型[ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent)，一个包含七个成员的枚举：

- `Stretch`默认值
- `Center`
- `Start` （或者"弹性启动"在 XAML 中）
- `End` （或者"弹性端"在 XAML 中）
- `SpaceBetween` （或者"空间-之间"在 XAML 中）
- `SpaceAround` （或空间-围绕"在 XAML 中）
- `SpaceEvenly`

如`AlignItems`、`AlignContent`属性还对齐子级上交叉轴，但会影响整个行或列：

[![试验页中： 对齐内容](flex-layout-images/ExperimentAlignContent.png "试验页中的对齐内容")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

在 iOS screnshot，这两个行位于顶部;Android 的屏幕截图中它们是在中心;并且 UWP 屏幕截图中它们是在底部。 此外可以通过多种方式分隔行：

[![试验页中： 对齐内容 2](flex-layout-images/ExperimentAlignContent2.png "试验页中的对齐内容 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

`AlignContent`时没有只有一个行或列没有任何作用。

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>详细信息中的附加可绑定属性

`FlexLayout` 定义五个附加的可绑定属性。 在的子级上设置这些属性`FlexLayout`和等仅适用于该特定的子级。

<a name="align-self" />

### <a name="the-alignself-property"></a>AlignSelf 属性

[ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty)附加可绑定属性属于类型[ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent)，一个包含五个成员的枚举：

- `Auto`默认值
- `Stretch`
- `Center`
- `Start` （或者"弹性启动"在 XAML 中）
- `End` （或者"弹性端"在 XAML 中）

任何单个子`FlexLayout`，此属性设置会替代[ `AlignItems` ](#align-items)属性设置的`FlexLayout`本身。 默认设置`Auto`意味着若要使用`AlignItems`设置。

有关`Label`元素名为`label`（或示例），你可以设置`AlignSelf`类似下面的代码中的属性：

```csharp
FlexAlign.SetAlignSelf(label, FlexAlignSelf.Center);
```

请注意，没有未引用`FlexLayout`的父级`Label`。 在 XAML 中，你可以设置此类属性：

```xaml
<Label ... FlexAlign.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>顺序属性

[ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty)属性属于类型`int`。 默认值为 0。

`Order`属性允许您更改的顺序的子级`FlexLayout`排列。 通常情况下的子级`FlexLayout`排列它们在中出现的顺序相同`Children`集合。 可以通过设置重写此顺序`Order`附加到一个或多个子级上一个非零整数值的可绑定属性。 `FlexLayout`然后排列其子基于的设置`Order`上每个子级，但具有相同的子级的属性`Order`设置排列的顺序，它们会显示在`Children`集合。

### <a name="the-basis-property"></a>基础属性

[ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty)附加可绑定属性指示分配给的子级的空间量`FlexLayout`主要轴上。 通过大小指定`Basis`属性是沿父级主轴大小`FlexLayout`。 因此，`Basis`时子级排列在行或高度当列中排列子级时，该值指示的子级的宽度。

`Basis`属性属于类型[ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis)，一种结构。 可以用独立于设备的单位或大小的百分比指定大小`FlexLayout`。 默认值`Basis`属性是静态属性`FlexBasis.Auto`，这意味着子的请求使用宽度或高度。

在代码中，你可以设置`Basis`属性`Label`名为`label`到 40 设备无关的单位，如下：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

第二个参数`FlexBasis`构造函数被命名为`isRelative`并指示是否是相对大小 (`true`) 或绝对 (`false`)。 自变量具有默认值为`false`，因此你还可以使用下面的代码：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

隐式转换`float`到`FlexBasis`定义的因此你可以进一步简化：

```csharp
FlexLayout.SetBasis(label, 40);
```

你可以将大小设置为 25%的`FlexLayout`父如下：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

此小数部分的值必须在 0 到 1 的范围内。

在 XAML 中，可以以独立于设备的单位大小使用大量：

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

或者，你可以在 0%到 100%范围内指定百分比：

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

**基础试验**页**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 示例允许你能够试验`Basis`属性。 该页面显示五个包装的列`Label`使用替代的背景和前景颜色的元素。 两个`Slider`元素，你可以指定`Basis`的第二个和第四个值`Label`:

[![基础试验页](flex-layout-images/BasisExperiment.png "基础试验页")](flex-layout-images/BasisExperiment-Large.png#lightbox)

在左侧的 iOS 屏幕快照显示了两个`Label`被授予高度设备无关的单位中的元素。 Android 屏幕显示它们所提供的总高度的小数部分的高度`FlexLayout`。 如果`Basis`设置达到 100%，则的高度，该子级则`FlexLayout`，并将换到下一列和占该列中，整个高度，如 UWP 屏幕截图所示： 看起来就像在一行中排列的五个子级但其实际排列五个列中。

### <a name="the-grow-property"></a>增长属性

[ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty)附加可绑定属性属于类型`int`。 默认值为 0，并且值必须大于或等于 0。

`Grow`属性所扮演的角色时`Wrap`属性设置为`NoWrap`和子级的行的总宽度小于的宽度`FlexLayout`，或子级的列具有比短高度`FlexLayout`。 `Grow`属性指示如何分配子级之间的剩余空间。

在**增长试验**页上，五个`Label`的交替颜色元素的形式显示列和第二个`Slider`元素，可以调整`Grow`属性的第二个和第四个`Label`。 在最左侧的 iOS 屏幕快照显示的默认`Grow`0 的属性：

[![增加试验页](flex-layout-images/GrowExperiment.png "增加试验页")](flex-layout-images/GrowExperiment-Large.png#lightbox)

如果任何一个子有一个值为正`Grow`值，则该子级占用的所有剩余的空间，如 Android 的屏幕截图所示。 此外可以在两个或多个子级之间分配此空间。 在 UWP 屏幕截图中，`Grow`第二个属性`Label`设置为 0.5，而`Grow`属性的第四个`Label`1.5，这将使第四个`Label`三倍的剩余空间作为第二个`Label`.

子视图使用该空间的方式取决于特定的子类型。 有关`Label`，可将文本放置的总空间内`Label`使用属性`HorizontalTextAlignment`和`VerticalTextAlignment`。

<a name="shrink" />

### <a name="the-shrink-property"></a>收缩属性

[ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty)附加可绑定属性属于类型`int`。 默认值为 1，并且值必须大于或等于 0。

`Shrink`属性中扮演着角色时`Wrap`属性设置为`NoWrap`和子级的行的聚合宽度大于的宽度`FlexLayout`，或包含一列的子级的聚合高度大于高度`FlexLayout`。 通常`FlexLayout`时将显示这些子级又会限制运行其大小。 `Shrink`属性可以指示哪些子级获得的优先级中显示在其完整大小。

**收缩试验**页创建`FlexLayout`的五个是单个行`Label`需要更多的空间比的子级`FlexLayout`宽度。 在左侧的 iOS 屏幕快照显示了所有`Label`具有默认值为 1 的元素：

[![收缩试验页](flex-layout-images/ShrinkExperiment.png "收缩试验页")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

在 Android 屏幕截图中，`Shrink`第二个值`Label`设为 0，并且`Label`显示在其完整的宽度。 此外，第四个`Label`给定`Shrink`值大于 1，并已收缩。 UWP 屏幕快照显示了同时`Label`元素被授予`Shrink`值为 0 以允许它们显示在其完整大小，如果该一点的。

也可以同时设置`Grow`和`Shrink`值以容纳其中聚合子大小可能有时是小于或小于大小的有时甚至更好的情况下`FlexLayout`。

## <a name="css-styling-with-flexlayout"></a>与 FlexLayout 的 CSS 样式

你可以使用[CSS 样式](~/xamarin-forms/user-interface/styles/css/index.md)功能门户中的 Xamarin.Forms 3.0 中引入了`FlexLayout`。 **CSS 目录项**页**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** 示例重复项的布局**目录项**页上，但使用 CSS对于许多样式的样式表：

[![CSS 目录项页](flex-layout-images/CssCatalogItems.png "CSS 目录项页")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

原始**CatalogItemsPage.xaml**文件具有五个`Style`中的定义其`Resources`15 节`Setter`对象。 在**CssCatalogItemsPage.xaml**文件，它具有向这两个已减少`Style`定义具有只需四个`Setter`对象。 这些样式补充 Xamarin.Forms CSS 样式功能目前不支持的属性的 CSS 样式表：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CssCatalogItemsPage"
             Title="CSS Catalog Items">
    <ContentPage.Resources>
        <StyleSheet Source="CatalogItemsStyles.css" />

        <Style TargetType="Frame">
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey" StyleClass="header" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey" StyleClass="header" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey" StyleClass="header" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

CSS 样式表引用中的第一行`Resources`部分：

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

在每个的三个项的两个元素，包括另请注意`StyleClass`设置：

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

这些是指中的选择器**CatalogItemsStyles.css**样式表：

```css
frame {
    width: 300;
    height: 480;
    background-color: lightyellow;
    margin: 10;
}

label {
    margin: 4 0;
}

label.header {
    margin: 8 0;
    font-size: large;
    color: blue;
}

label.empty {
    flex-grow: 1;
}

image {
    height: 180;
    order: -1;
    align-self: center;
}

button {
    font-size: large;
    color: white;
    background-color: green;
}
```

多个`FlexLayout`此处引用的附加的可绑定属性。 在`label.empty`选择器，你将看到`flex-grow`属性，样式空`Label`提供上述一些空白区域`Button`。 `image`选择器包含`order`属性和`align-self`属性，这两种对应于`FlexLayout`附加可绑定属性。

你已了解你可以直接在上设置属性`FlexLayout`和您可以设置附加的可绑定属性的子级`FlexLayout`。 或者，你可以设置这些属性间接使用传统的基于 XAML 的样式或 CSS 样式。 重要的一点是了解以及了解这些属性。 这些属性是使`FlexLayout`真正灵活。 

## <a name="flexlayout-with-xamarinuniversity"></a>与 Xamarin.University FlexLayout

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin.Forms 3.0 弹性布局中，通过[Xamarin 大学](https://university.xamarin.com/)**

## <a name="related-links"></a>相关的链接

- [FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)
