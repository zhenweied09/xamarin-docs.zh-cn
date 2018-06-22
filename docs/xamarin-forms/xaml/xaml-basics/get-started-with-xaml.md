---
title: 第 1 部分。 Getting Started with XAML
description: 在 Xamarin.Forms 应用中，XAML 主要用于定义 visual 页和一同使用的代码隐藏文件的内容。
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/10/2018
ms.openlocfilehash: 5883564841a4ef0e19518dd3b12ee00fe35ed778
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049722"
---
# <a name="part-1-getting-started-with-xaml"></a>第 1 部分。 Getting Started with XAML

_在 Xamarin.Forms 应用中，XAML 主要用于定义 visual 页和一同使用 C# 隐藏代码文件的内容。_

代码隐藏文件提供的标记的代码的支持。 在一起，这两个文件参与包含子视图和属性初始化的新类定义。 在 XAML 文件中，类和属性引用与 XML 元素和属性，并建立标记和代码之间的链接。

## <a name="creating-the-solution"></a>创建解决方案

若要开始编辑你的第一个 XAML 文件，使用 Visual Studio 或 Visual Studio for Mac，以创建新的 Xamarin.Forms 解决方案。 （选择下面对应于你的环境的选项卡。）

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Windows 中，使用 Visual Studio 选择**文件 > 新建 > 项目**从菜单。 在**新项目**对话框中，选择**Visual C# > 跨平台**左侧，，然后**移动应用 (Xamarin.Forms)** 从中心中的列表。 

![](get-started-with-xaml-images/win/newprojectdialog.w157.png "新建项目对话框")

选择的解决方案的位置，为其提供的名称**XamlSamples** （或你希望的任何内容），然后按**确定**。

在下一个屏幕上，选择**空白应用程序**模板和 **.NET 标准**代码共享策略：

![](get-started-with-xaml-images/win/newcrossplatformapp.png "新建应用程序对话框")

Press **OK**. 

在解决方案中创建四个项目： **XamlSamples** .NET 标准库， **XamlSamples.Android**， **XamlSamples.iOS**，和通用 Windows 平台解决方案， **XamlSamples.UWP**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在适用于 Mac 的 Visual Studio，选择**文件 > 新解决方案**从菜单。 在**新项目**对话框中，选择**多平台 > 应用**左侧，和**空白窗体应用程序**(*不***窗体应用程序**) 从模板列表：

![](get-started-with-xaml-images/mac/newprojectdialog1.png "新建项目对话框 1")

按**下一步**。

在下一步的对话框中，为项目指定名称的**XamlSamples** （或你希望的任何内容）。 请确保**使用.NET 标准**单选按钮处于选中状态：

![](get-started-with-xaml-images/mac/newprojectdialog2.png "新建项目对话框 2")

按**下一步**。 

在以下对话框中，你可以选择项目的位置：

![](get-started-with-xaml-images/mac/newprojectdialog3.png "新建项目对话框 3")

按**创建**

在解决方案中创建三个项目： **XamlSamples** .NET 标准库， **XamlSamples.Android**，和**XamlSamples.iOS**。 

-----

在创建后**XamlSamples**解决方案，你可能想要通过为解决方案启动项目，选择各种平台项目中测试你的开发环境，并且通过创建构建和部署简单的应用程序在 phone 仿真程序或实际设备上的项目模板中。

除非你需要编写特定于平台的代码，共享**XamlSamples** .NET 标准库项目是其中你将会花几乎所有编程时间。 这些文章不将该项目外风险。

### <a name="anatomy-of-a-xaml-file"></a>XAML 文件的剖析

在**XamlSamples** .NET 标准库是一对具有以下名称的文件：

- **App.xaml**，XAML 文件中; 和
- **App.xaml.cs**，C#*代码隐藏*与 XAML 文件相关联的文件。

你将需要旁边单击箭头**App.xaml**若要查看的代码隐藏文件。 

同时**App.xaml**和**App.xaml.cs**贡献名为的类`App`派生自`Application`。 与 XAML 文件的大多数其他类派生自的类参与`ContentPage`; 这些文件使用 XAML 定义整个页面的可视化内容。 这是中的其他两个文件的 true **XamlSamples**项目：

- **MainPage.xaml**，XAML 文件中; 和
- **MainPage.xaml.cs**，C# 代码隐藏文件。

**MainPage.xaml** （尽管可能稍有不同的格式设置），则文件类似如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <StackLayout>
        <!-- Place new controls here -->
        <Label Text="Welcome to Xamarin Forms!" 
               VerticalOptions="Center" 
               HorizontalOptions="Center" />
    </StackLayout>

</ContentPage>
```

两个 XML 命名空间 ( `xmlns`) 声明将引用 Uri，看似 Xamarin 的 web 站点上的第一个和第二个 on Microsoft 的。 你不必费神检查到哪个这些 Uri 点。 无需进行任何存在。 它们是只需通过 Xamarin 和 Microsoft，拥有的 Uri 和它们基本上函数与版本标识符。

第一个 XML 命名空间声明意味着在没有任何前缀的 XAML 文件内定义的标记例如指中 Xamarin.Forms，这类`ContentPage`。 第二个命名空间声明定义的前缀`x`。 这使用多个元素和属性是固有的 XAML 本身的支持和 XAML 的其他实现。 但是，这些元素和特性都是略有不同，具体取决于嵌入在 URI 中的年份。 Xamarin.Forms 支持 2009 XAML 规范中，但它不是所有。

`local`命名空间声明，可从.NET 标准库项目中访问其他类。

在该第一个标记，末尾`x`前缀用于一个名为特性`Class`。 因为这一点的用法`x`前缀是几乎通用的 XAML 命名空间，XAML 特性，如`Class`几乎总是称为`x:Class`。

`x:Class`特性指定完全限定的.NET 类名称：`MainPage`类`XamlSamples`命名空间。 这意味着此 XAML 文件定义一个名为的新类`MainPage`中`XamlSamples`派生自的命名空间`ContentPage`— 在其中标记`x:Class`特性随即显示。

`x:Class`属性只能出现在一个 XAML 文件以定义派生的 C# 类的根元素。 这是唯一的新 XAML 文件中定义的类。 所有其他 XAML 文件中显示的内容格式而是只需从现有类实例化且已初始化。

**MainPage.xaml.cs**文件如下所示 (除了未使用`using`指令):

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

`MainPage`类派生自`ContentPage`，但请注意`partial`类定义。 这暗示了应为另一个分部类定义`MainPage`，但其中是它？ 那是什么和`InitializeComponent`方法？ 

Visual Studio 在生成项目时分析 XAML 文件生成的 C# 代码文件。 如果你查看**XamlSamples\XamlSamples\obj\Debug**目录中，你将找到名为的文件**XamlSamples.MainPage.xaml.g.cs**。 G 代表生成。 这是其他分部类定义`MainPage`，它包含的定义`InitializeComponent`从调用的方法`MainPage`构造函数。 这些两个部分`MainPage`类定义然后可在一起编译。 具体取决于是否或不编译的 XAML，可执行文件中嵌入的 XAML 文件或二进制形式的 XAML 文件。

在运行时，代码中特定平台项目调用`LoadApplication`方法，并向它传递的新实例`App`.NET 标准库中的类。 `App`类构造函数实例化`MainPage`。 该类的构造函数调用`InitializeComponent`，后者随后调用`LoadFromXaml`从.NET 标准库中提取的 XAML 文件 （或其已编译的二进制文件） 的方法。 `LoadFromXaml` 初始化在 XAML 文件中定义的所有对象、 将它们连接在一起父-子关系中，将附加到在 XAML 文件中设置的事件的代码中定义的事件处理程序和作为页面的内容设置对象的结果树。

尽管你通常不需要与生成的代码文件花费多长时间，有时运行时异常会引发代码在生成的文件，因此你应该熟悉它们。

在编译和运行此程序`Label`元素出现在页的中心中，如 XAML 所示。 从左到右的三个平台是 iOS、 Android 和 UWP:

[![](get-started-with-xaml-images/xamlsamples.png "默认 Xamarin.Forms 显示")](get-started-with-xaml-images/xamlsamples-large.png#lightbox "默认 Xamarin.Forms 显示")

对于更有趣的视觉对象，你需要的全部是多个有趣的 XAML。

## <a name="adding-new-xaml-pages"></a>添加新的 XAML 页面

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要添加其他基于 XAML 的`ContentPage`类到你的项目，选择**XamlSamples** .NET 标准库项目，然后调用**项目 > 添加新项**菜单项。 在左侧的**添加新项**对话框中，选择**Visual C#** 和**Xamarin.Forms**。 从列表中选择**内容页**(不**内容页 (C#)**，这将创建一个仅限代码的页中，或**内容视图**，这不是页)。 例如，为页面提供一个名称， **HelloXamlPage.xaml**:

![](get-started-with-xaml-images/win/addnewitemdialog.w157.png "添加新项对话框")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要添加其他基于 XAML 的`ContentPage`类到你的项目，选择**XamlSamples** .NET 标准库项目，然后调用**文件 > 新建文件**菜单项。 在左侧的**新文件**对话框中，选择**窗体**左侧，和**窗体内容页 Xaml** (不**窗体内容页**，后者将创建一个仅限代码的页中，或**内容视图**，这不是页)。 例如，为页面提供一个名称， **HelloXamlPage**:

![](get-started-with-xaml-images/mac/newfiledialog.png "新建文件对话框")

-----

两个文件添加到项目中， **HelloXamlPage.xaml**和代码隐藏文件**HelloXamlPage.xaml.cs**。 

## <a name="setting-page-content"></a>设置页面内容

编辑**HelloXamlPage.xaml**文件，以便仅标记是那些用于`ContentPage`和`ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>
        
    </ContentPage.Content>
</ContentPage>
```

`ContentPage.Content`标记是唯一的 XAML 语法的一部分。 首先，它们看起来有点无效的 XML，但它们是合法的。 期间不在 XML 中的特殊字符。

`ContentPage.Content`称为标记*属性元素*标记。 `Content` 是的一个属性`ContentPage`，并通常将设置为单一视图，还是一种布局有子视图。 属性通常具有属性在 XAML 中，但这样将很难设置`Content`属性设为复杂对象。 为此，该属性表示为 XML 元素，包含的类名称与句点分隔的属性名称。 现在`Content`属性可以设置之间`ContentPage.Content`标记，如下：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

另请注意，`Title`已经在根标记上设置了特性。

在此期间，类、 属性和 XML 之间的关系应该是显而易见： Xamarin.Forms 类 (如`ContentPage`或`Label`) 为一个 XML 元素的 XAML 文件中显示。 该类的属性，包括`Title`上`ContentPage`和七个属性`Label`-通常显示为 XML 属性。

多个快捷方式存在设置这些属性的值。 某些属性是基本数据类型： 例如，`Title`和`Text`属性属于类型`String`，`Rotation`属于类型`Double`，和`IsVisible`(即`true`默认情况下，只能为在此处设置图） 的类型是`Boolean`。

`HorizontalTextAlignment`属性属于类型`TextAlignment`，这是一个枚举。 任何枚举类型的属性，你只需提供为其成员名称。

对于更复杂类型的属性，但是，转换器用于分析 XAML。 这些是派生自的 Xamarin.Forms 中的类`TypeConverter`。 许多都是公共类，但某些不可。 对于此特定的 XAML 文件，多个这些类播放在幕后的角色：

-  `LayoutOptionsConverter` 有关`VerticalOptions`属性
-  `FontSizeConverter` 有关`FontSize`属性
-  `ColorTypeConverter` 有关`TextColor`属性

这些转换器控制允许的属性设置的语法。

`ThicknessTypeConverter`可以处理一个、 两个，或用逗号分隔的四个数字。 如果提供一个数字，则它将适用于所有四个边。 两个数字，第一种是左侧和右侧填充，与第二个是顶部和底部。 四个数字位于顺序左、 顶部、 右侧和底部。

`LayoutOptionsConverter`可转换的公共静态字段的名称`LayoutOptions`结构类型的值`LayoutOptions`。

`FontSizeConverter`可以处理`NamedSize`成员或数值的字体大小。

`ColorTypeConverter`可接受的公共静态字段的名称`Color`结构或十六进制 RGB 值，无论 alpha 通道，前面有数字符号 （#）。 下面是没有 alpha 通道语法：

 `TextColor="#rrggbb"`

每个小字母是十六进制数字。 下面是如何 alpha 通道是包括：

 `TextColor="#aarrggbb">`

Alpha 通道，请记住，FF 是完全不透明，00 是完全透明。

两个其他格式，可以指定仅每个通道的单个十六进制数字：

 `TextColor="#rgb"` `TextColor="#argb"`

在这些情况下，该数字将重复以形成的值。 例如，#CF3 是 RGB 颜色抄送 FF 33。

## <a name="page-navigation"></a>页导航

当你运行**XamlSamples**程序，`MainPage`显示。 若要查看新`HelloXamlPage`可以设置，作为新的启动页中**App.xaml.cs**文件，或导航到新页上，从`MainPage`。

若要实现导航，第一次更改代码中的**App.xaml.cs**构造函数，以便`NavigationPage`创建对象：

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

在**MainPage.xaml.cs**构造函数，你可以创建一个简单`Button`和使用事件处理程序来导航到`HelloXamlPage`:

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

设置`Content`页属性替换的设置`Content`XAML 文件中的属性。 在编译和部署此程序的新版本，在屏幕上会出现一个按钮。 按其导航到`HelloXamlPage`。 下面是在 iPhone、 Android 和 UWP 结果页上：

[![](get-started-with-xaml-images/helloxaml1.png "旋转标签文本")](get-started-with-xaml-images/helloxaml1-large.png#lightbox "旋转标签文本")

你可以向后定位到`MainPage`使用 **< 回**iOS，在页面顶部或底部的电话在 Android 上，使用向左箭头键或在 Windows 10 上的页面的顶部使用向左的箭头上的按钮。

随意尝试不同的方法来呈现 XAML `Label`。 如果你需要在文本中嵌入的任何 Unicode 字符，你可以使用标准的 XML 语法。 例如，若要将问候语放在引号中，使用：

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

下面是如下所示：

[![](get-started-with-xaml-images/helloxaml2.png "轮换使用 Unicode 字符的标签文本")](get-started-with-xaml-images/helloxaml2-large.png#lightbox "旋转使用 Unicode 字符的标签文本")

## <a name="xaml-and-code-interactions"></a>XAML 和代码交互

**HelloXamlPage**示例包含只有一个`Label`在页上，但这是非常不常见。 大多数`ContentPage`衍生产品组`Content`的布局的某些属性进行排序，如`StackLayout`。 `Children`属性`StackLayout`定义的类型为`IList<View>`但它是实际的类型的对象`ElementCollection<View>`，并可以与多个视图或其他布局填充集合。 在 XAML 中，这些父-子关系将建立与普通 XML 层次结构。 下面是一个名为的新页的 XAML 文件**XamlPlusCodePage**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

此 XAML 文件的语法完整，而以下是如下所示：

[![](get-started-with-xaml-images/xamlpluscode1.png "页面上的多个控件")](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox "页面上的多个控件")

但是，很可能要考虑此程序要功能不足之处。 可能是`Slider`应导致`Label`若要显示的当前值和`Button`可能用于执行某些在程序中的操作。

正如在看到[第 4 部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)，显示的作业`Slider`值使用`Label`完全在 XAML 中可以处理与数据绑定。 但十分有用，可以先看到解决方案代码。 即便如此，处理`Button`单击明确需要的代码。 这意味着的代码隐藏文件`XamlPlusCodePage`必须包含的处理程序`ValueChanged`事件`Slider`和`Clicked`事件`Button`。 让我们将它们添加：

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

这些事件处理程序不需要是公共的。

返回在 XAML 文件中，`Slider`和`Button`标记需要包括的属性`ValueChanged`和`Clicked`引用这些处理程序的事件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

请注意，分配到的事件处理程序分配到属性的值与相同的语法。

如果处理程序`ValueChanged`事件`Slider`将使用`Label`若要显示的当前值，该处理程序需要从代码中引用该对象。 `Label`需要名称，使用指定`x:Name`属性。

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

`x`前缀`x:Name`属性指示此属性是固有的 XAML。

你将分配给名称`x:Name`属性具有与 C# 变量名相同的规则。 例如，它必须以字母或下划线开头并且不包含嵌入的空格。

现在`ValueChanged`事件处理程序可以设置`Label`以显示新`Slider`值。 新值是可从事件自变量：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

或处理程序无法获取`Slider`对象生成此事件从`sender`自变量并获取`Value`于属性：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

当你首次运行该程序，`Label`不显示`Slider`值因为`ValueChanged`尚未尚未触发事件。 但的任何操作`Slider`导致要显示的值：

[![](get-started-with-xaml-images/xamlpluscode2.png "显示的滑块值")](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox "显示的滑块值")

现在为`Button`。 让我们模拟响应`Clicked`通过显示与警报的事件`Text`的按钮。 事件处理程序可以安全地强制转换`sender`参数`Button`，然后访问其属性：

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

该方法定义为`async`因为`DisplayAlert`方法是异步的应开头`await`运算符，函数方法完成时返回。 因为此方法获取`Button`触发从事件`sender`自变量，相同的处理程序可用于多个按钮。

在 XAML 中定义的对象可以触发事件是在代码隐藏文件中中, 处理和代码隐藏文件可以访问在使用分配给与它的名称的 XAML 中定义的对象，你已了解`x:Name`属性。 这些是代码和 XAML 交互的两种基本方法。

如何通过检查新生成可以收集 XAML 工作原理的一些额外见解**XamlPlusCode.xaml.g.cs 文件**，它现在包括分配给任何任何名称`x:Name`作为私有字段的属性。 下面是该文件的简化的版本：

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

此字段的声明允许可自由地使用中的任意位置的变量`XamlPlusCodePage`下您所在地区的分部类文件。 在运行时，字段被赋予后分析 XAML。 这意味着，`valueLabel`字段是`null`时`XamlPlusCodePage`构造函数开始后但有效`InitializeComponent`调用。

后`InitializeComponent`返回控制返回到构造函数，就像它们具有已实例化并在代码中初始化已构造的页的视觉对象。 XAML 文件不再在类中起任何作用。 你可以操作在任何所需方式，例如，通过添加到视图的页上的这些对象`StackLayout`，或设置`Content`其他页的属性完全。 你可以"遍历该树"通过检查`Content`属性页和中的项的`Children`布局的集合。 可以在这种方式，访问的视图上设置属性或动态分配给他们的事件处理程序。

可以随意。 它是你的页面，并且 XAML 是仅用于生成其内容的工具。

## <a name="summary"></a>总结

随着此推出，你已了解于类定义，如何提供一个 XAML 文件和代码文件和 XAML 和代码的文件如何交互。 但 XAML 也有其自己唯一的语法功能，使它可以用于以非常灵活的方式。 你可以开始浏览这些中[第 2 部分。基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)。



## <a name="related-links"></a>相关链接

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 2 部分：基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
