---
title: 字体
description: Xamarin.Forms 中的设置字体
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 52c86c63c328729211c4fbd22bd10b5eb1e56615
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2018
---
# <a name="fonts"></a>字体

本指南介绍了如何 Xamarin.Forms 能够让你指定的字体特性 （包括权重和大小） 上显示文本的控件。 字体信息可以是[在代码中指定](#Setting_Font_in_Code)或[在 Xaml 中指定](#Setting_Font_in_Xaml)。
还有可能要使用[自定义字体](#Using_a_Custom_Font)。

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>在代码中设置字体

使用所有控件的显示文本的三个与字体相关的属性：

- **FontFamily** &ndash; `string`字体名称。
- **FontSize** &ndash;的字体大小`double`。
- **FontAttributes** &ndash;一个字符串，指定样式信息，如*斜体*和**加粗**(使用`FontAttributes`C# 中的枚举)。

此代码演示如何创建一个标签和指定的字体大小和权重，则显示：

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>字号

`FontSize`属性可以设置为双精度值，例如：

```csharp
label.FontSize = 24;
```

你还可以使用`NamedSize`枚举，它有四个内置的选项;Xamarin.Forms 选择每个平台的最佳大小。

-  **Micro**
-  **小**
-  **中等**
-  **大型**


`NamedSize`枚举可以是任何位置使用`FontSize`可以使用指定`Device.GetNamedSize`方法以将值转换为`double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

<a name="FontAttributes" />

### <a name="font-attributes"></a>字体特性

字体样式如**粗体**和*斜体*可以对设置`FontAttributes`属性。 当前支持以下值：

-  **无**
-  **加粗**
-  **斜体**

`FontAttribute`枚举可以使用方法如下 (你可以指定单个属性或`OR`在一起):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="formattedstring"></a>FormattedString

某些 Xamarin.Forms 控件 (如`Label`) 还支持在中字符串使用不同的字体特性`FormattedString`类。 A`FormattedString`包含一个或多个`Span`s，其中每个可以有自己的格式设置特性。

`Span`类具有以下属性：

* **文本**&ndash;要显示的值
* **FontFamily** &ndash;的字体名称
* **FontSize** &ndash;字体大小
* **FontAttributes** &ndash;想样式信息*斜体*和**粗体**
* **ForegroundColor** &ndash;文本颜色
* **BackgroundColor** &ndash;背景色

创建和显示的示例`FormattedString`如下所示&ndash;请注意，它将分配给标签的`FormattedText`属性而不`Text`属性。

```csharp
var labelFormatted = new Label ();
var fs = new FormattedString ();
fs.Spans.Add (new Span { Text="Red, ", ForegroundColor = Color.Red, FontSize = 20, FontAttributes = FontAttributes.Italic });
fs.Spans.Add (new Span { Text=" blue, ", ForegroundColor = Color.Blue, FontSize = 32 });
fs.Spans.Add (new Span { Text=" and green!", ForegroundColor = Color.Green, FontSize = 12 });
labelFormatted.FormattedText = fs;
```


### <a name="setting-font-info-per-platform"></a>每个平台的设置字体信息

或者，`Device.RuntimePlatform`属性可用来设置不同的字体名称在每个平台上，在此代码中所示：

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

适用于 iOS 的字体信息的一个很好来源是[iosfonts.com](http://iosfonts.com)。

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>在 Xaml 中设置字体

Xamarin.Forms 控制所有具有该显示文本`Font`可以在 Xaml 中设置的属性。 在 Xaml 中设置字体的最简单方法是使用命名的大小的枚举值，如本示例中所示：

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

没有内置转换器`Font`允许所有字体设置表示为 Xaml 中的字符串值的属性。 下面的示例演示如何可以在 Xaml 中指定的字体特性和大小：

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

若要指定多个`Font`设置，结合到单个字体属性字符串所需的设置。 字体属性字符串的格式应为`"[font-face],[attributes],[size]"`。 参数的顺序很重要，所有参数都是可选的以及多个`attributes`可指定，例如：

```xaml
<Label Text="Small bold text" FontAttributes="Bold" FontSize="Micro" />
<Label Text="Really big italic text" FontAttributes="Italic" FontSize="72" />
```

`FormattedString`类还可在 XAML 中，如下所示：

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <FormattedString.Spans>
                <Span Text="Red, " ForegroundColor="Red" FontAttributes="Italic" FontSize="20" />
                <Span Text=" blue, " ForegroundColor="Blue" FontSize="32" />
                <Span Text=" and green! " ForegroundColor="Green" FontSize="12"/>
            </FormattedString.Spans>
        </FormattedString>
    </Label.FormattedText>
</Label>
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) 此外可在 XAML 中呈现在每个平台上不同的字体。 下面的示例在 iOS 上使用自定义字体 (<span style="font-family:MarkerFelt-Thin">MarkerFelt 精简</span>)，并在其他平台上指定仅大小/属性：

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

指定自定义字体时, 它始终是一个好办法使用`OnPlatform`，如很难查找在所有平台上可用的字体。

<a name="Using_a_Custom_Font" />

## <a name="using-a-custom-font"></a>使用自定义字体

使用以外的内置字样字体需要一些特定于平台的编码。 此屏幕截图中显示的自定义字体**Lobster**从[Google 的开放源代码字体](https://www.google.com/fonts)呈现使用 Xamarin.Forms。

 [![在 iOS 和 Android 的自定义字体](fonts-images/custom-sml.png "自定义字体示例")](fonts-images/custom.png#lightbox "自定义字体示例")

每个平台所需的步骤如下所述。 包含与应用程序的自定义字体文件时，请务必验证字体的许可证允许进行分发。

### <a name="ios"></a>iOS

可以通过先确保将加载它，然后使用 Xamarin.Forms 来按名称引用它来显示自定义字体`Font`方法。
按照中的说明[这篇博客文章](http://blog.xamarin.com/custom-fonts-in-ios/):

1. 添加字体文件**生成操作： BundleResource**，和
2. 更新**Info.plist**文件 (**字体由应用程序提供**，或`UIAppFonts`、 密钥)，然后
3. 它根据名称引用只要 Xamarin.Forms 中定义一种字体 ！

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

适用于 Android 的 Xamarin.Forms 可以引用按照特定的命名标准添加到项目的自定义字体。 第一次添加字体文件**资产**文件夹中的应用程序项目和组*生成操作： AndroidAsset*。 然后，使用的完整路径和*字体名称*隔开哈希 （#） 作为 Xamarin.Forms 中的字体名称，如以下代码段所示：

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

对于 Windows 平台的 Xamarin.Forms 可以引用按照特定的命名标准添加到项目的自定义字体。 第一次添加字体文件 **/Assets/字体/** 文件夹中的应用程序项目和组<span class="UIItem">生成操作： 内容</span>。 然后，使用的完整路径和字体文件名，跟哈希 （#） 和<span class="UIItem">字体名称</span>，如以下代码段所示：

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> 请注意，但使用的字体文件名称和字体名称可能不同。 若要发现 Windows 上的字体名称，右键单击.ttf 文件，然后选择**预览**。 然后可从预览窗口中确定的字体名称。

此时完成了应用程序的常用代码。 此时，可将特定于平台的电话拨号程序实现为 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

### <a name="xaml"></a>XAML

你还可以使用[ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values) XAML 呈现自定义字体中：

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

<a name="Summary" />

## <a name="summary"></a>总结

Xamarin.Forms 提供了简单的默认设置，以便你可以调整大小可轻松地为所有支持的平台的文本。 它还允许你指定字体和大小&ndash;甚至以不同方式为每个平台&ndash;当需要进行更细化的控制时。 `FormattedString`类可以用于构造字符串包含使用不同的字体规格`Span`类。

此外可以在 Xaml 中使用格式正确的字体特性指定字体信息或`FormattedString`具有元素`Span`子级。


## <a name="related-links"></a>相关链接

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
