---
title: Xamarin.Forms 条目
description: 此文章介绍了如何使用 Xamarin.Forms 入口类接受的单行文本或应用程序中的密码输入。
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 95afdfde878759d4a598e200d16fe6fb1fa2005e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998217"
---
# <a name="xamarinforms-entry"></a>Xamarin.Forms 条目

_单行文本或输入的密码_

Xamarin.Forms`Entry`用于单行文本输入。 `Entry`，例如`Editor`视图中，支持多个键盘类型。 此外，`Entry`可以用作密码字段。

## <a name="display-customization"></a>显示自定义

### <a name="setting-and-reading-text"></a>设置和读取文本

`Entry`，如其他文本呈现的视图，公开`Text`属性。 此属性可以用于设置和读取通过显示的文本`Entry`。 下面的示例演示了如何设置`Text`在 XAML 中的属性：

```xaml
<Entry Text="I am an Entry" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

若要读取的文本，访问`Text`C# 中的属性：

```csharp
var text = MyEntry.Text;
```

> [!NOTE]
> 宽度`Entry`可以通过设置定义其`WidthRequest`属性。 不依赖于的宽度`Entry`所定义的值基于其`Text`属性。

### <a name="limiting-input-length"></a>输入的长度限制

[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性可用于限制所允许的范围的输入的长度[ `Entry` ](xref:Xamarin.Forms.Entry)。 此属性应设置为一个正整数：

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

一个[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性值为 0 指示将允许任何输入，并将值`int.MaxValue`，它是默认值， [ `Entry` ](xref:Xamarin.Forms.Entry)，指示是否有任何可能输入的字符数的有效限制。

### <a name="keyboards"></a>键盘

当与用户交互时显示键盘`Entry`可以通过编程方式设置`Keyboard`属性。

键盘类型的选项是：

- **默认值**&ndash;默认键盘
- **聊天**&ndash;用于短和位置表情符号会很有用
- **电子邮件**&ndash;输入电子邮件地址时使用
- **数值**&ndash;输入数字时使用
- **电话**&ndash;输入电话号码时，使用
- **Url** &ndash;用于输入文件路径和 web 地址

没有[示例中的每个键盘](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/)我们方案的部分中。

### <a name="enabling-and-disabling-spell-checking"></a>启用和禁用拼写检查

[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性控制是否拼写检查已启用。 默认情况下，该属性设置为`true`。 当用户输入文本，指示拼写错误。

但是，对于某些文本项方案中，输入用户名，如拼写检查功能提供了负的体验，因此应禁用通过设置[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为`false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 当[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为`false`，并自定义键盘未被使用，将禁用本机拼写检查器。 但是，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)具有已设置，以禁用拼写检查，如[ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat)，则`IsSpellCheckEnabled`属性将被忽略。 因此，该属性不能用于启用拼写检查`Keyboard`的显式禁用它。

### <a name="placeholders"></a>占位符

`Entry` 可以设置为显示占位符文本，它不存储用户输入时。 在实践中，这是通常出现在窗体中以阐明适用于给定字段的内容。 占位符文本颜色不能进行自定义，而不考虑相同`TextColor`设置。 如果设计调用的自定义占位符的颜色，您将需要故障回复到[自定义呈现器]()。 将创建以下`Entry`与 XAML 中的占位符为"Username":

```xaml
<Entry Placeholder="Username" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "条目占位符示例")

### <a name="password-fields"></a>密码字段

`Entry` 提供了`IsPassword`属性。 当`IsPassword`是`true`，字段的内容将显示为黑色圆圈：

在 XAML:

```xaml
<Entry IsPassword="true" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "条目 IsPassword 示例")

占位符可能使用的实例，并用`Entry`的配置为密码字段：

在 XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "条目 IsPassword 和占位符的示例")


### <a name="colors"></a>颜色

若要使用的自定义背景和文本颜色通过以下可绑定属性，可以设置项：

- **TextColor** &ndash;设置文本的颜色。
- **BackgroundColor** &ndash;设置显示文本的背景的颜色。

特别注意有必要确保将每个平台上可用的颜色。 因为每个平台都有不同的默认值的文本和背景颜色，通常需要同时设置，如果你设置一个。

使用以下代码设置的项的文本颜色：

在 XAML:

```xaml
<Entry TextColor="Green" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![](entry-images/textcolor.png "条目 TextColor 示例")

请注意，占位符不是受影响的指定`TextColor`。

若要在 XAML 中设置的背景色：

```xaml
<Entry BackgroundColor="#2c3e50" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "项的 BackgroundColor 示例")

请务必确保您选择的背景和文本颜色每个平台上可用，并且变得模糊的任何占位符文本。

## <a name="events-and-interactivity"></a>事件和交互性

项公开两个事件：

- [TextChanged](xref:Xamarin.Forms.Entry.TextChanged) &ndash;文本项中更改时引发。 更改之前和之后提供的文本。
- [已完成](xref:Xamarin.Forms.Entry.Completed)&ndash;用户已结束输入通过键盘上按 return 键时引发。

### <a name="completed"></a>已完成

`Completed`使用事件做出反应的条目的交互完成。 `Completed` 当用户通过键盘上输入 return 键结束与某个字段的输入时引发。 该事件的处理程序是一个泛型事件处理程序，使发件人和`EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

可以在 XAML 中订阅的已完成的事件：

```xaml
<Entry Completed="Entry_Completed" />
```

和 C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

### <a name="textchanged"></a>TextChanged

`TextChanged`事件用于对字段的内容中更改做出反应。

`TextChanged` 时引发`Text`的`Entry`更改。 为事件处理程序将执行的实例`TextChangedEventArgs`。 `TextChangedEventArgs` 提供对旧的和新值的访问`Entry``Text`通过`OldTextValue`和`NewTextValue`属性：

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

`TextChanged`事件可以在 XAML 中进行订阅：

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

和 C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```


## <a name="related-links"></a>相关链接

- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [入口 API](xref:Xamarin.Forms.Entry)
