---
title: Text
description: 使用 Xamarin.Forms 来输入或显示文本。
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 37289c4c118c3a84b8c81d3a1c1502ff80564bb9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="text"></a>Text

_使用 Xamarin.Forms 来输入或显示文本。_

Xamarin.Forms 具有三个用于处理文本的主视图：

- **[标签](#Label)** &mdash;呈现单个或多行文本。 可以在同一行中显示多个的格式设置选项的文本。
- **[条目](#Entry)** &mdash;用于输入只有一个行的文本。 条目都具有密码模式。
- **[编辑器](#Editor)** &mdash;用于输入文本，可能也需要多个行。

可以使用内置或自定义更改文本外观[样式](#Styles)和某些控件支持自定义[字体](#Fonts)。

<a name="Label" />

## <a name="labellabelmd"></a>[标签](label.md)

`Label`视图用于显示文本。 它可以显示多行文本或单个文本行。 `Label` 可以向多个内联中使用的格式设置选项显示文本。 标签视图可以包装或截断的文本，当它不能放在一行上。

![](images/label.png "标签示例")

请参阅[标签](label.md)文章更多详细信息。

有关自定义标签中使用的字体的信息，请参阅[字体](fonts.md)。

<a name="Entry" />

## <a name="entryentrymd"></a>[项](entry.md)

`Entry` 用来接受的单行文本输入。 `Entry` 提供控制颜色，但不能具有自定义字体。 `Entry` 具有密码模式，并且可以显示的占位符文本，直到输入文本。

![](images/entry.png "条目示例")

请参阅[条目](entry.md)文章了解详细信息。

请注意，与不同`Label`，`Entry`不能具有自定义字体设置。

<a name="Editor" />

## <a name="editoreditormd"></a>[编辑器](editor.md)

`Editor` 用来接受多行文本输入。 `Editor` 可以具有自定义的背景色，但文本颜色和字体不能进行更改。

![](images/editor.png "编辑器示例")

请参阅[编辑器](editor.md)文章了解详细信息。

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[字体](fonts.md)

`Label`控件支持不同的字体设置每个平台或你的应用程序中包含的自定义字体上使用内置的字体。 请参阅[字体](fonts.md)文章更多详细信息。

<a name="Styles" />

## <a name="stylesstylesmd"></a>[样式](styles.md)

请参阅[使用样式](~/xamarin-forms/user-interface/styles/index.md)若要了解如何设置字体，[颜色](~/xamarin-forms/user-interface/colors.md)，和其他适用于多个控件的显示属性。



## <a name="related-links"></a>相关链接

- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
