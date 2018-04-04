---
title: MonoTouch.Dialog Json Markup
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 02c95f06571a3c242481769846d7cab51450f2ca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="monotouchdialog-json-markup"></a>MonoTouch.Dialog Json Markup

本页介绍 MonoTouch.Dialog 的接受的 Json 标记[JsonElement](https://developer.xamarin.com/api/type/MonoTouch.Dialog.JsonElement/)

让我们从一个示例开始。 下面是可以传递到 JsonElement 完整 Json 文件。

```csharp
{     
  "title": "Json Sample",
  "sections": [ 
      {
          "header": "Booleans",
          "footer": "Slider or image-based",
          "id": "first-section",
          "elements": [
              { 
                  "type" : "boolean",
                  "caption" : "Demo of a Boolean",
                  "value"   : true
              }, {
                  "type": "boolean",
                  "caption" : "Boolean using images",
                  "value"   : false,
                  "on"      : "favorite.png",
                  "off"     : "~/favorited.png"
              }, {
                      "type": "root",
                      "title": "Tap for nested controller",
                      "sections": [ {
                         "header": "Nested view!",
                         "elements": [
                           {
                             "type": "boolean",
                             "caption": "Just a boolean",
                             "id": "the-boolean",
                             "value": false
                           },
                           {
                             "type": "string",
                             "caption": "Welcome to the nested controller"
                           }
                         ]
                       }
                     ]
                   }
          ]
      }, {
          "header": "Entries",
          "elements" : [
              {
                  "type": "entry",
                  "caption": "Username",
                  "value": "",
                  "placeholder": "Your account username"
              }
          ]
      }
  ]
}
```

上面的标记产生以下 UI:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "创建按给定的标记的 UI")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

在树中的每个元素可以包含属性`"id"`。 它有可能在运行时引用各个部分或使用 JsonElement 索引器的元素。 如：

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>根元素语法

根元素包含以下值：

-  `title`
-  `sections`（可选）


根元素可以在节内显示为要创建嵌套的控制器的元素。 在这种情况下，额外属性`"type"`必须设置为 `"root"`

 <a name="url" />


### <a name="url"></a>URL

如果`"url"`设置属性，如果用户点击此 RootElement，代码将从指定的 url 请求的文件并将内容显示的新信息。 可以使用此函数创建从服务器基于用户点击扩展的用户界面。

 <a name="group" />


### <a name="group"></a>组

如果设置，这将设置的根元素 groupname。 组名称用于选取一个摘要，它显示为从一个元素中的嵌套元素的根元素的值。 这是一个复选框的值或单选按钮的值。

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

标识在嵌套元素中选择的单选项

 <a name="title" />


### <a name="title"></a>标题

如果存在，则它将用于 RootElement 的标题

 <a name="type" />


### <a name="type"></a>类型

必须设置为`"root"`时这将显示在 （这用于嵌套控制器） 部分。

 <a name="sections" />


### <a name="sections"></a>节

这是一个 Json 数组的各个部分

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>部分语法

部分包含：

-  `header`（可选）
-  `footer`（可选）
-  `elements` 数组


 <a name="header" />


### <a name="header"></a>标题

如果存在，标头文本将显示为部分中的标题。

 <a name="footer" />


### <a name="footer"></a>页脚

如果存在，页脚显示底部的部分。

 <a name="elements" />


### <a name="elements"></a>元素

这是元素的数组。 每个元素必须包含至少一个键，`"type"`用于识别要创建的元素的类型的密钥。
某些元素共享一些常见的属性，例如`"caption"`和`"value"`。 这些是受支持的元素的列表：

-  `string` 元素 （同时具有和没有样式）
-  `entry` 行 （常规或密码）
-  `boolean` （使用开关或映像） 的值


字符串元素可以用作按钮通过提供一种方法要在用户点击该单元格或访问器上, 时调用

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>呈现元素

呈现元素基于 C# StringElement 和 StyledStringElement 和它们可能会使以各种方式的信息并且可以以各种方式呈现。 最简单元素可以创建如下：

```csharp
{
        "type": "string",
        "caption": "Json Serializer",
}
```

这将显示一个简单的字符串与所有默认设置： 字体、 背景、 文本颜色和效果。 可以挂接到这些元素的操作，并使它们通过设置的行为类似于按钮`"ontap"`属性或`"onaccessorytap"`属性：

```csharp
{
    "type":    "string",
        "caption": "View Photos",
        "ontap:    "Acme.PhotoLibrary.ShowPhotos"
}
```

上述可调用类"Acme.PhotoLibrary"中的"ShowPhotos"方法。 `"onaccessorytap"`非常相似，但它将仅调用如果用户点击上而不是对单元格的点击附件。 若要启用此功能，还必须设置访问器：

```csharp
{
    "type":     "string",
        "caption":  "View Photos",
        "ontap:     "Acme.PhotoLibrary.ShowPhotos",
        "accessory: "detail-disclosure",
        "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

呈现元素可以同时显示两个字符串，一个是标题，另一个是值。 如何呈现这些字符串取决于该样式，则可以设置这使用`"style"`属性。 默认值将显示标题，在左侧和右侧的值。 有关更多详细信息的样式，请参阅部分。 颜色使用跟表示红、 绿、 蓝方和可能 alpha 值的值的十六进制数字的 '#' 符号进行编码。 可以采用缩写形式 （3 个或 4 十六进制数字） 表示 RGB 或 RGBA 值编码内容。 或表示 RGB 或 RGBA 值的长格式 （6 或 8 位）。 简短版本是一种速记两次编写相同的十六进制数字。 因此，"#1bc"常量是为红色的该 = 0x11，绿色 = 0xbb 和蓝色 = 0xcc。 如果不存在 alpha 值，颜色是不透明的。 示例如下：

```csharp
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>accessory

确定类型的附件，显示在呈现元素中，可能的值有：

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


如果值不存在，则不显示任何附件

 <a name="background" />


### <a name="background"></a>背景

后台属性设置单元格的背景色。 值是图像的 URL （在这种情况下，将调用异步图像下载程序和下载图像后，将会更新后台） 也可以是使用颜色语法指定一种颜色。

 <a name="caption" />


### <a name="caption"></a>标题

要显示在呈现元素的主要字符串。 字体和颜色可进行自定义设置`"textcolor"`和`"font"`属性。 呈现样式由`"style"`属性。

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>颜色和 detailcolor

要用于主文本或详细的文本的颜色。

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>detailfont 和字体

要用于标题或详细信息的文本的字体。 字体规范的格式不 （可选） 跟短划线和点大小的字体名称。
以下是有效的字体规范：

-  "Helvetica"
-  "Helvetica-14"


 <a name="linebreak" />


### <a name="linebreak"></a>linebreak

确定如何分解行。 可能的值为：

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


同时`character-wrap`和`word-wrap`可以连同使用`"lines"`属性设置为零，以将呈现元素转换为多行元素。

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>ontap 和 onaccessorytap

这些属性必须指向采用对象作为参数的应用程序中的静态方法名称。 当你创建层次结构使用的 JsonDialog.FromFile 或 JsonDialog.FromJson 方法您可以传递一个可选对象值。 此对象值然后传递到你的方法。 你可以使用这要传递给你静态方法的一些背景信息。 例如：

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines" />


### <a name="lines"></a>文本行

如果设置为零，它将使元素自动调整大小具体取决于包含字符串的内容。 对于这种方式，你必须设置`"linebreak"`属性`"character-wrap"`或`"word-wrap"`。

 <a name="style" />


### <a name="style"></a>样式

该样式确定将用于呈现内容的单元格样式的种类与 UITableViewCellStyle 枚举值的对应关系。
可能的值为：

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` ： 使用副标题文本。


 <a name="subtitle" />


### <a name="subtitle"></a>副标题

要用于副标题的值。 这是一种快捷方式将样式设置为`"subtitle"`并设置`"value"`为字符串的属性。
此命令的作用它们都具有单个条目。

 <a name="textcolor" />


### <a name="textcolor"></a>textcolor

要使用的文本的颜色。

 <a name="value" />


### <a name="value"></a>值

要显示在呈现元素的辅助值。 此布局受到`"style"`设置。 字体和颜色可进行自定义设置`"detailfont"`和`"detailcolor"`。

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>布尔元素

布尔元素应将类型设置为`"bool"`，可以包含`"caption"`以显示与`"value"`设置为 true 或 false。 如果`"on"`和`"off"`属性被设置，它们被假定为映像。 映像是解决相对于应用程序中的当前工作目录。 如果你想要引用相对于捆绑的文件，则可以使用`"~"`作为快捷方式来表示应用程序捆绑包目录。 例如`"~/favorite.png"`将捆绑文件中包含 favorite.png。 例如：

```csharp
{ 
    "type" : "boolean",
    "caption" : "Demo of a Boolean",
    "value"   : true
},

{
    "type": "boolean",
    "caption" : "Boolean using images",
    "value"   : false,
    "on"      : "favorite.png",
    "off"     : "~/favorited.png"
}
```

 <a name="type" />


### <a name="type"></a>类型

可以将类型设置为`"boolean"`或`"checkbox"`。 如果设置为布尔值它将使用 UISlider 或映像 (如果这两个`"on"`和`"off"`设置)。 如果设置为复选框，它将使用一个复选框。 `"group"`属性可以用于标记为属于特定组中的布尔元素。 这是如果包含根还具有有用`"group"`作为根的属性将汇总属于同一个组具有所有布尔值 （或复选框） 的计数结果。

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>条目元素

使用项元素来允许用户输入数据。 项元素的类型是`"entry"`或`"password"`。 `"caption"`属性设置为文本，以在右侧，显示和`"value"`设置为初始值，以设置的项。 `"placeholder"`用于针对空条目 （它显示为灰色） 向用户显示提示。 下面是一些可能的恶意活动：

```csharp
{
        "type": "entry",
        "caption": "Username",
        "value": "",
        "placeholder": "Your account username"
}, {
        "type": "password",
        "caption": "Password",
        "value": "",
        "placeholder": "You password"
}, {
        "type": "entry",
        "caption": "Zip Code",
        "value": "01010",
        "placeholder": "your zip code",
        "keyboard": "numbers"
}, {
        "type": "entry",
        "return-key": "route",
        "caption": "Entry with 'route'",
        "placeholder": "captialization all + no corrections",
        "capitalization": "all",
        "autocorrect": "no"
}
```

 <a name="autocorrect" />


### <a name="autocorrect"></a>autocorrect

确定要使用的项的自动更正样式。 可能的值为 true 或 false (或字符串`"yes"`和`"no"`)。

 <a name="capitalization" />


### <a name="capitalization"></a>大小写

要使用的项的大小写样式。 可能的值为：

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>标题

要使用的项的标题

 <a name="keyboard" />


### <a name="keyboard"></a>键盘

要用于数据输入的键盘类型。 可能的值为：

-  `ascii`
-  `decimal`
-  `default`
-  `email`
-  `name`
-  `numbers`
-  `numbers-and-punctuation`
-  `twitter`
-  `url`


 <a name="placeholder" />


### <a name="placeholder"></a>占位符

当项具有空值显示提示文本。

 <a name="return-key" />


### <a name="return-key"></a>返回密钥

用于返回密钥的标签。 可能的值为：

-  `default`
-  `done`
-  `emergencycall`
-  `go`
-  `google`
-  `join`
-  `next`
-  `route`
-  `search`
-  `send`
-  `yahoo`


 <a name="value" />


### <a name="value"></a>值

条目的初始值

 <a name="Radio_Elements" />


## <a name="radio-elements"></a>单选元素

单选元素具有类型`"radio"`。 为选定的项的选取通过`radioselected`其包含的根元素上的属性。
此外，如果值设置为`"group"`的属性，此单选按钮属于该组。

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>日期和时间元素

元素类型`"datetime"`，`"date"`和`"time"`用于呈现日期与时间、 日期或时间。 标题和一个值，这些元素将作为参数。 可以在.NET DateTime.Parse 函数支持的任何格式写入值。 示例:

```csharp
"header": "Dates and Times",
"elements": [
        {
                "type": "datetime",
                "caption": "Date and Time",
                "value": "Sat, 01 Nov 2008 19:35:00 GMT"
        }, {
                "type": "date",
                "caption": "Date",
                "value": "10/10"
        }, {
                "type": "time",
                "caption": "Time",
                "value": "11:23"
                }                       
]
```

 <a name="Html/Web_Element" />


## <a name="htmlweb-element"></a>Html/Web 元素

你可以创建单元格，当点击将嵌入指定的 URL 的内容呈现 UIWebView 本地或远程使用`"html"`类型。 此元素的仅有两个属性是`"caption"`和`"url"`:

```csharp
{
        "type": "html",
        "caption": "Miguel's blog",
        "url": "http://tirania.org/blog" 
}
```
