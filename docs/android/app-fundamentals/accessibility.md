---
title: 在 Android 上的辅助功能
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: 2a49d15651b8c6ab7417a69d934af5d20bfc13d0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763899"
---
# <a name="accessibility-on-android"></a>在 Android 上的辅助功能

本页介绍如何使用 Android 的可访问性 Api 来生成应用根据[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。
请参阅[iOS 可访问性](~/ios/app-fundamentals/accessibility.md)和[OS X 可访问性](~/mac/app-fundamentals/accessibility.md)其他平台 Api 的页。


## <a name="describing-ui-elements"></a>描述 UI 元素

Android 提供`ContentDescription`读取 Api 的屏幕用于提供控件的用途的可访问描述的属性。

在任一 C# 或 AXML 布局文件中，可以设置的内容的说明。

**C#**

可以在为任何字符串 （或字符串资源） 的代码中设置的说明：

```csharp
saveButton.ContentDescription = "Save data";
```

**AXML 布局**

在 XML 布局使用`android:contentDescription`属性：

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>用于 TextView 提示

有关`EditText`和`TextView`控制数据输入，使用`Hint`属性来提供的应有哪些输入说明 (而不是`ContentDescription`)。
当已输入一些文本时，文本本身将"读取"而不是提示。

**C#**

设置`Hint`在代码中的属性：

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**AXML 布局**

在 XML 布局文件使用`android:hint`属性：

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor 链接输入带有标签的字段

若要将数据输入控件与关联标签，使用`LabelFor`属性

**C#**

在 C# 中，设置`LabelFor`这此内容控件的资源 id 的属性描述 （通常此属性设置上一个标签和引用其他输入的控件）：

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**AXML 布局**

在布局中 XML 使用`android:labelFor`属性来引用另一个控件的标识符：

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>宣布可访问性

使用`AnnounceForAccessibility`方法对任何查看控件通信向用户的事件或状态更改时启用可访问性。 此方法不是针对大多数操作，内置旁白提供足够的反馈，但应附加信息将很有帮助的用户应使用必需的。

下面的代码显示简单的示例调用`AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>更改焦点设置

可访问导航依赖于控件具有焦点，以帮助用户了解均提供哪些操作。 Android 提供`Focusable`可以标记为专门能够在导航期间接收焦点的控件的属性。

**C#**

若要防止控件获得焦点使用 C#，将设置`Focusable`属性`false`:

```csharp
label.Focusable = false;
```

**AXML 布局**

在布局中 XML 文件集`android:focusable`属性：

```xml
<android:focusable="false" />
```

你还可以控制焦点顺序`nextFocusDown`， `nextFocusLeft`， `nextFocusRight`，`nextFocusUp`布局 AXML 中通常设置的特性。 使用这些属性来确保用户可以轻松地浏览屏幕上的控件。


## <a name="accessibility-and-localization"></a>可访问性和本地化

是的提示和内容说明上面的示例中直接设置为显示值。 最好使用中的值是**Strings.xml**文件，这样：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

使用文本字符串文件中所示在 C# 和 AXML 布局文件中：

**C#**

而不是在代码中使用字符串文字，查找已翻译值从字符串文件`Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

在布局 XML 辅助功能属性，如`hint`和`contentDescription`可以将设置为字符串标识符：

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

将文本存储在单独的文件的好处是可以在应用程序中提供的文件的多个语言翻译。 请参阅[Android 本地化指南](~/android/app-fundamentals/localization.md)若要了解如何将本地化的字符串文件添加到应用程序项目。


## <a name="testing-accessibility"></a>测试辅助功能

请按照[这些步骤](http://developer.android.com/training/accessibility/testing.html#how-to)启用 TalkBack 和资源管理器通过触摸辅助功能在 Android 设备上的进行测试。

你可能需要安装[TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)从 Google Play，如果未出现在**设置 > 可访问性**。


## <a name="related-links"></a>相关链接

- [跨平台可访问性](~/cross-platform/app-fundamentals/accessibility.md)
- [Android 可访问性 Api](http://developer.android.com/guide/topics/ui/accessibility/index.html)
