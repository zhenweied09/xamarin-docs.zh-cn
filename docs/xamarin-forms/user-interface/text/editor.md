---
title: Xamarin.Forms 编辑器
description: 本文介绍如何使用 Xamarin.Forms 编辑器控件接受的应用程序中的多行文本输入。
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 4879ff88d5bbdab5aa92024bee7f50239a141e3b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995859"
---
# <a name="xamarinforms-editor"></a>Xamarin.Forms 编辑器

_多行文本输入_

`Editor`控件用来接受多行输入。 本文将介绍：

- **[自定义](#customization)** &ndash;键盘和颜色选项。
- **[交互性](#interactivity)** &ndash;可以将侦听的事件，以提供交互性。

## <a name="customization"></a>自定义

### <a name="setting-and-reading-text"></a>设置和读取文本

`Editor`，如其他文本呈现的视图，公开`Text`属性。 此属性可以用于设置和读取通过显示的文本`Editor`。 下面的示例演示了如何设置`Text`在 XAML 中的属性：

```xaml
<Editor Text="I am an Editor" />
```

在 C# 中：

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

若要读取的文本，访问`Text`C# 中的属性：

```csharp
var text = MyEditor.Text;
```

### <a name="limiting-input-length"></a>输入的长度限制

[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性可用于限制所允许的范围的输入的长度[ `Editor` ](xref:Xamarin.Forms.Editor)。 此属性应设置为一个正整数：

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

一个[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性值为 0 指示将允许任何输入，并将值`int.MaxValue`，它是默认值， [ `Editor` ](xref:Xamarin.Forms.Editor)，指示是否有任何可能输入的字符数的有效限制。

### <a name="keyboards"></a>键盘

当与用户交互时显示键盘`Editor`可以通过编程方式设置[ ``Keyboard`` ](xref:Xamarin.Forms.Keyboard)属性。

键盘类型的选项是：

- **默认值**&ndash;默认键盘
- **聊天**&ndash;用于短和位置表情符号会很有用
- **电子邮件**&ndash;输入电子邮件地址时使用
- **数值**&ndash;输入数字时使用
- **电话**&ndash;输入电话号码时，使用
- **Url** &ndash;用于输入文件路径 （& a） 的 web 地址

没有[示例中的每个键盘](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/)我们方案的部分中。

### <a name="enabling-and-disabling-spell-checking"></a>启用和禁用拼写检查

[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性控制是否拼写检查已启用。 默认情况下，该属性设置为`true`。 当用户输入文本，指示拼写错误。

但是，对于某些文本项方案中，输入用户名，如拼写检查功能提供了负的体验，因此应禁用通过设置[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为`false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 当[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为`false`，并自定义键盘未被使用，将禁用本机拼写检查器。 但是，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)具有已设置，以禁用拼写检查，如[ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat)，则`IsSpellCheckEnabled`属性将被忽略。 因此，该属性不能用于启用拼写检查`Keyboard`的显式禁用它。

### <a name="colors"></a>颜色

`Editor` 可以设置为使用自定义背景色通过`BackgroundColor`属性。 特别注意有必要确保将每个平台上可用的颜色。 因为每个平台都有不同的文本颜色的默认值，可能需要设置每个平台的自定义背景颜色。 请参阅[使用平台调整](~/xamarin-forms/platform/device.md)有关优化每个平台的 UI 的详细信息。

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

在 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "使用 BackgroundColor 示例编辑器")

请确保你选择的背景和文本颜色为每个平台上可用，且变得模糊的任何占位符文本。

## <a name="interactivity"></a>交互性

`Editor` 公开两个事件：

- [TextChanged](xref:Xamarin.Forms.Editor.TextChanged) &ndash;文本在编辑器中更改时引发。 更改之前和之后提供的文本。
- [已完成](xref:Xamarin.Forms.Editor.Completed)&ndash;用户已结束输入通过键盘上按 return 键时引发。

### <a name="completed"></a>已完成

`Completed`事件用来与的交互完成做出反应`Editor`。 `Completed` 当用户通过键盘上输入 return 键结束与某个字段的输入时引发。 该事件的处理程序是一个泛型事件处理程序，使发件人和`EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

可以在代码和 XAML 中订阅的已完成的事件：

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

在 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

`TextChanged`事件用于对字段的内容中更改做出反应。

`TextChanged` 时引发`Text`的`Editor`更改。 为事件处理程序将执行的实例`TextChangedEventArgs`。 `TextChangedEventArgs` 提供对旧的和新值的访问`Editor``Text`通过`OldTextValue`和`NewTextValue`属性：

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

可以在代码和 XAML 中订阅的已完成的事件：

在代码中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

在 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```


## <a name="related-links"></a>相关链接

- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [编辑器 API](xref:Xamarin.Forms.Editor)
