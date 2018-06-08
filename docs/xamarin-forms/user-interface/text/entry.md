---
title: 条目
description: 单行文本或输入的密码
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: a45a4edb93920cfe1d0289da44ee664e41c25cf1
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847843"
---
# <a name="entry"></a>条目

_单行文本或输入的密码_

Xamarin.Forms`Entry`用于的单行文本输入。 `Entry`、 Like`Editor`视图中，支持多个键盘类型。 此外，`Entry`可以用作密码字段。

## <a name="display-customization"></a>显示自定义项

### <a name="setting-and-reading-text"></a>设置和读取文本

`Entry`，如其他文本呈现的视图，公开`Text`属性。 此属性可以用于设置和读取通过显示的文本`Entry`。 下面的示例演示了如何设置`Text`在 XAML 中的属性：

```xaml
<Entry Text="I am an Entry" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

若要读取文本，访问`Text`C# 中的属性：

```csharp
var text = MyEntry.Text;
```

> [!NOTE]
> 宽度`Entry`可以通过设置定义其`WidthRequest`属性。 不依赖于的宽度`Entry`正在定义的值基于其`Text`属性。

### <a name="limiting-input-length"></a>输入的长度限制

[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性可以用于将输入允许的范围的长度限制[ `Entry` ](xref:Xamarin.Forms.Entry)。 此属性应为正整数设置：

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

A [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性值为 0 指示将允许任何输入，和的值`int.MaxValue`，这是默认值为[ `Entry` ](xref:Xamarin.Forms.Entry)，该值指示是否有任何可能输入的字符数的有效限制。

### <a name="keyboards"></a>键盘

在与用户交互时显示键盘`Entry`可以通过编程方式设置`Keyboard`属性。

键盘类型的选项是：

- **默认**&ndash;默认键盘
- **聊天** &ndash; texting & 位置使用 emoji 可
- **电子邮件**&ndash;输入电子邮件地址时使用
- **数值**&ndash;时输入的数字使用
- **电话**&ndash;输入电话号码时使用
- **Url** &ndash;用于输入文件路径和 web 地址

没有[示例的每个键盘](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/)我们配方部分中。

### <a name="enabling-and-disabling-spell-checking"></a>启用和禁用拼写检查

[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性控制是否拼写正在检查已启用。 默认情况下，该属性设置为`true`。 当用户输入文本，指示拼写错误。

但是，某些文本的条目情况下，输入用户名，如拼写检查提供的负的体验，因此应禁用通过设置[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性`false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 当[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为`false`，和自定义键盘未被使用，本机拼写检查器将被禁用。 但是，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)具有已禁用拼写的集检查，如[ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat)、`IsSpellCheckEnabled`忽略属性。 因此，属性无法用于启用拼写检查`Keyboard`，显式禁用它。

### <a name="placeholders"></a>占位符

`Entry` 可以设置为它不存储用户输入时显示的占位符文本。 在实践中，这是通常在中看到窗体以阐明适合于给定字段的内容。 占位符文本颜色不能自定义，并且都是相同的而不考虑`TextColor`设置。 如果设计调用自定义的占位符颜色，则需要为回退到[自定义呈现器]()。 将创建以下`Entry`与 XAML 中的占位符为"Username":

```xaml
<Entry Placeholder="Username" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "条目占位符示例")

### <a name="password-fields"></a>密码字段

`Entry` 提供`IsPassword`属性。 当`IsPassword`是`true`，将为黑色圆圈显示字段的内容：

在 XAML 中：

```xaml
<Entry IsPassword="true" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "条目 IsPassword 示例")

占位符可能使用的实例，并用`Entry`，配置为密码字段：

在 XAML 中：

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "条目 IsPassword 和占位符示例")


### <a name="colors"></a>颜色

条目可以设置为使用自定义背景和通过以下可绑定属性的文本颜色：

- **TextColor** &ndash;设置的文本颜色。
- **BackgroundColor** &ndash;设置显示文本的背景的颜色。

特别注意，有必要确保颜色将每个平台上可用。 因为每个平台都有不同的默认值为文本和背景色，通常需要同时设置，如果你设置一个。

下面的代码用于设置的项的文本颜色：

在 XAML 中：

```xaml
<Entry TextColor="Green" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![](entry-images/textcolor.png "条目 TextColor 示例")

请注意，将占位符不受指定`TextColor`。

在 XAML 中设置的背景色：

```xaml
<Entry BackgroundColor="#2c3e50" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "条目 BackgroundColor 示例")

请务必确保你选择的背景和文本颜色每个平台上可用，且不遮盖任何占位符文本。

## <a name="events-and-interactivity"></a>事件和交互的能力

项公开两个事件：

- [宽度](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/)&ndash;条目中的文本更改时引发。 更改之前和之后，请提供的文本。
- [完成](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/)&ndash;用户已通过键盘上按 return 键结束输入时引发。

### <a name="completed"></a>已完成

`Completed`使用事件做出反应到完成的交互的条目。 `Completed` 当用户通过键盘上输入的返回密钥结束与字段的输入时引发。 事件的处理程序是一个泛型事件处理程序，采用发件人和`EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

可在 XAML 中订阅已完成的事件：

```xaml
<Entry Completed="Entry_Completed" />
```

和 C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

### <a name="textchanged"></a>宽度

`TextChanged`事件用于响应中的字段的内容的更改。

`TextChanged` 每当引发`Text`的`Entry`更改。 事件的处理程序采用实例`TextChangedEventArgs`。 `TextChangedEventArgs` 提供的旧和新值对的访问`Entry``Text`通过`OldTextValue`和`NewTextValue`属性：

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

`TextChanged`事件可以在 XAML 中订阅：

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
- [条目 API](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)
