---
title: "iOS 本地化"
description: "本文档介绍 iOS SDK 的本地化功能以及如何使用 Xamarin 访问它们。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: ea91dbcf7148651cb5d10acae4ada8bb6758c39e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="ios-localization"></a>iOS 本地化

_本文档介绍 iOS SDK 的本地化功能以及如何使用 Xamarin 访问它们。_

请参阅[国际化编码](encodings.md)有关包括字符集/代码页必须处理非 Unicode 数据的应用程序中的说明。

## <a name="ios-platform-features"></a>iOS 平台功能

本部分介绍一些在 iOS 中的本地化功能。 跳到[下一节](#basics)若要查看特定代码和示例。

### <a name="language"></a>语言

用户选择在其语言**设置**应用。 此设置会影响的语言字符串和显示的操作系统以及检测语言设置的应用程序的图像。

这是其中用户将决定是否需要查看英语、 西班牙语、 日语、 法语或在其应用中显示其他语言。

在 iOS 7 中的受支持语言的实际列表： 英语 （美国），英语 （英国）、 中文 （简体）、 中文 （繁体）、 法语、 德语、 意大利语、 日语、 朝鲜语、 西班牙语、 阿拉伯语、 加泰罗尼亚语、 克罗地亚语、 捷克语、 丹麦语、 荷兰语、 芬兰语、 希腊语、 希伯来语匈牙利语、 印度尼西亚语、 马来语、 挪威语、 波兰语、 葡萄牙语、 葡萄牙语 （巴西）、 罗马尼亚语、 俄语、 斯洛伐克语、 瑞典语、 泰语、 土耳其语、 乌克兰语、 越南语，英语 （澳大利亚），西班牙语 （墨西哥）。

可以通过访问的第一个元素查询当前语言`PreferredLanguages`数组：

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

此值将是语言代码如`en`为英语，`es`为西班牙语，`ja`代表日语，等等。_返回的值被限制为支持 （使用回退规则以确定最佳匹配） 的应用程序的本地化信息之一。_

应用程序代码不总是需要检查此值的 Xamarin，并且这两个 iOS 提供有助于自动为用户的语言中提供正确的字符串或资源的功能。 本文档的其余部分中介绍这些功能。

> [!NOTE]
> **注意：**在 iOS 9 之前, 的建议的代码是`var lang = NSLocale.PreferredLanguages [0];`。
>
> 请参阅在 iOS 9-中更改此代码返回的结果[技术注意 TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html)有关详细信息。
>
> 你可以继续使用`NSLocale.PreferredLanguages [0]`来确定用户选择的实际值 （而不考虑本地化你的应用程序支持）。

### <a name="locale"></a>区域设置

用户选择在其区域设置**设置**应用。 此设置影响日期、 时间、 数字和货币的格式设置的方式。

这允许用户选择他们是否看到 12 小时或 24 小时时间格式，其小数分隔符是否逗号或一个点和、 月份和日期显示中的每年的顺序。

可以使用 Xamarin 访问这两个 Apple 的 iOS 类 (`NSNumberFormatter`) 以及 System.Globalization 中的.NET 类。 开发人员应评估这是更好地适合其需求，因为没有在每个可用的不同功能。 具体而言，如果您是检索并显示在应用内购买价格使用 StoreKit 你肯定应该返回的价格信息使用 Apple 的格式设置类。

可以通过以下任一方法查询当前区域设置：

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

第一个值可以缓存由操作系统，并因此可能不会始终反映用户的当前所选的区域设置。 第二个值用于获取当前所选的区域设置。


### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS 生成`NSCurrentLocaleDidChangeNotification`用户更新后，其区域设置。 应用程序可以侦听此通知时它们正在运行，并对此 UI 进行相应的更改。

<a name="basics" />

## <a name="localization-basics-in-ios"></a>在 iOS 中的本地化基础知识

IOS 的以下功能可轻松利用 Xamarin 向用户显示有关提供本地化的资源中。 请参阅[TaskyL10n 示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)若要了解如何实现这些想法。

### <a name="infoplist"></a>Info.plist

在开始之前，配置**Info.plist**文件具有以下项：

- `CFBundleDevelopmentRegion` -应用程序 （通常语言读出由开发人员，但在情节提要和字符串和图像资源使用） 的默认语言。 在示例中， **en** （对于英语） 指定的。
- `CFBundleLocalizations` -支持的应用程序，还使用语言代码等其他本地化信息的数组**es** （西班牙语） 和**PT-PT** （葡萄牙语使用葡萄牙）。

```xml
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
<key>CFBundleLocalizations</key>
<array>
  <string>de</string>
  <string>es</string>
  <string>ja</string>
  ...
</array>
```

### <a name="localizedstring-method"></a>LocalizedString 方法

`NSBundle.MainBundle.LocalizedString`方法查找已存储在的本地化文本**.strings**项目中的文件。 按语言中，使用的特殊命名目录中进行组织这些文件**.lproj**后缀。

#### <a name="strings-file-locations"></a>.strings 文件位置

- **Base.lproj**是包含默认语言资源的目录。
  它通常位于项目根目录中 (但可以放置在**资源**文件夹)。
- **<language>.lproj**目录创建时为每个支持的语言，通常**资源**文件夹。

可以有许多不同的**.strings**每个语言目录中的文件：

- **Localizable.strings** -本地化文本的主列表。
- **InfoPlist.strings** -某些特定的密钥允许在此文件中转换等应用程序名称。
- **< 情节提要名称 >.strings** -可选文件，其中包含用户界面元素在情节提要中的翻译。

**生成操作**这些文件应**捆绑资源**。

#### <a name="strings-file-format"></a>.strings 文件格式

本地化的字符串值的语法是：

```csharp
/* comment */
"key"="localized-value";
```

应转义字符串中的以下字符：

* `\"`  引号
* `\\`  反斜杠
* `\n`  换行符

这是一个示例**es/Localizable.strings** (ie。西班牙语） 文件中的示例：

```csharp
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>图像

若要本地化在 iOS 中的映像：

1. 指在代码中，映像，例如：

  ```csharp
  UIImage.FromBundle("flag");
  ```

2. 将默认图像文件放置**flag.png**中**Base.lproj** （本机开发语言目录）。

3. （可选） 将放在映像的本地化的版本**.lproj**文件夹的每种语言 （如。 **es.lproj**， **ja.lproj**)。 使用相同的文件名**flag.png**每个语言目录中。

如果图像不存在为特定的语言，iOS 将回退到默认的本机语言文件夹，并从那里加载映像。


#### <a name="launch-images"></a>启动映像

使用标准命名约定为启动映像 （和 XIB 或对 iPhone 6 型号的情节提要） 时将它们中放置**.lproj**为每种语言的目录。

```csharp
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>应用程序名称

放置**InfoPlist.strings**文件中**.lproj**目录可让你重写从应用程序的某些值**Info.plist**，包括应用程序名称：

```csharp
"CFBundleDisplayName" = "LeónTodo";
```

你可以使用其他键[本地化应用程序特有字符串](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21)是：

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright


<!--
## App icon

Does not seem to be possible (although it definitely used to be!).
-->

### <a name="localization-native-development-region"></a>本地化本机开发区域

默认字符串 (位于**Base.lproj**文件夹) 将被认为在回退语言。 这意味着，如果转换请求在代码中，找不到当前的语言中， **Base.lproj**文件夹将搜索要使用的默认字符串 （如果不找到任何匹配，转换标识符字符串本身是显示）。

开发人员可以选择不同的语言进行通过设置 plist 项作为备用`CFBundleDevelopmentRegionKey`。 其值应设置为本机开发语言的语言代码。 此屏幕截图显示一个 plist 在 Xamarin Studio 中使用本机开发区域设置为西班牙语 (es):

![](images/cfbundledevelopmentregion.png "InfoPList.strings 属性")

如果不使用在情节提要并在整个代码的默认语言是英语，则将此值以反映在整个应用程序代码使用的本机语言的设置。

### <a name="dates-and-times"></a>日期和时间

但也可以使用内置的.NET 日期和时间函数 (以及当前`CultureInfo`) 若要格式化的区域设置日期和时间，这会忽略特定于区域设置的用户设置 （这可以单独设置从语言）。

使用 iOS`NSDateFormatter`以生成与用户的区域设置首选项匹配的输出。 下面的示例代码演示了基本的日期和时间格式设置选项：

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

在美国英语的的结果：

```csharp
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

在西班牙西班牙语的结果：

```csharp
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

请参阅 Apple[日期格式化程序](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html)文档以了解更多信息。 当测试区分区域设置的日期和时间格式，检查同时**iPhone 语言**和**区域**设置。

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>右到左 (RTL) 布局

iOS 提供了大量功能有助于构建 RTL 识别应用程序：

* 使用**自动布局**`leading`和`trailing`控件对齐属性 (对应于*左*和*右*为英语，例如，但是反转 RTL 语言）。
  [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md)控件很适合用于对需要 RTL 注意的控件进行布局。
* 使用`TextAlignment = UITextAlignment.Natural`文本对齐方式 (这将是*左*对于大多数语言但*右*为 RTL)。
* `UINavigationController` 自动翻转后退按钮，并反转轻扫方向。

下面的屏幕截图演示[本地化**Tasky**示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)阿拉伯语和希伯来语 （尽管已在字段中输入英语）：

[ ![](images/rtl-ar-sml.png "阿拉伯语的本地化")](images/rtl-ar.png "Arabic") 

[ ![](images/rtl-he-sml.png "希伯来语的本地化")](images/rtl-he.png "Hebrew")

iOS 自动反转`UINavigationController`，和其他控件放置在`UIStackView`或与自动布局对齐。
使用本地化 RTL 文本**.strings** LTR 文本的方式相同的文件。


<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>本地化代码中的用户界面

[Tasky （在代码中本地化）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)示例演示如何本地化应用程序代码 （而不是 XIBs 或情节提要） 的用户界面内置其中。

### <a name="project-structure"></a>项目结构



![](images/solution-code.png "资源树")

### <a name="localizablestrings-file"></a>Localizable.strings 文件

如上所述， **Localizable.strings**文件格式包含的键 / 值对，其中键是一个用户选定的字符串，指示

西班牙语 (**es**) 本地化信息的示例如下所示：

```csharp
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>执行本地化

在应用程序代码中，只要用户界面的显示文本设置 （无论它位于标签的文本，或输入的占位符，等等） 的代码使用 iOS`LocalizedString`函数可检索要显示的正确转换：

```csharp
var localizedString = NSBundle.MainBundle.LocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>本地化情节提要 Ui

示例[Tasky （本地化情节提要）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)演示如何在情节提要中的控件上的文本进行本地化。

### <a name="project-structure"></a>项目结构

**Base.lproj**目录包含情节提要，并还应该包含任何应用程序中使用的图像。

其他语言目录包含**Localizable.strings**引用在代码中，任何字符串资源的文件以及**MainStoryboard.strings**包含中的文本的翻译文件情节提要。

![](images/solution-storyboard.png "资源树")

语言目录应包含的任何映像的已本地化，重写中存在的一个副本**Base.lproj**。

### <a name="object-id--localization-id"></a>对象 ID / 本地化 ID

当创建和编辑在情节提要中的控件，选择每个控件，然后检查要使用用于本地化的 ID:

* 在适用于 Mac 的 Visual Studio，它具有位于属性填充并调用**本地化 ID**。
* 在 Xcode 中，调用**对象 ID**。

它是一个字符串值，通常采用的格式类似**"NF3 h8 xmR"**:

![](images/xs-designer-localization-id.png "情节提要本地化的 Xcode 视图")

在使用此值**.strings**文件以将已转换的文本自动分配给每个控件。

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

情节提要转换文件的格式是类似于**Localizable.strings**文件，只不过密钥 （在左侧的值） 不能是用户定义，但改为必须具有非常特定的格式： `ObjectID.property`。

在示例中**Mainstoryboard.strings**下面可以看到`UITextField`具有`placeholder`可以进行本地化; 的 text 属性`UILabel`具有`text`属性; 和`UIButton`s 默认文本使用设置`normalTitle`:

```csharp
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> **使用情节提要具有大小类**可能会导致未出现的翻译。 这可能与[此问题](http://stackoverflow.com/questions/24989208/xcode-6-does-not-localize-interface-builder)其中 Apple 的文档指出： 本地化的情节提要或 XIB 将正确不本地化如果满足所有以下三种情况： 情节提要或 XIB 使用大小类。 基本地化和 build 目标设置为世界。 生成面向 iOS 7.0。
解决方法是为两个相同的文件复制你的情节提要字符串文件： **MainStoryboard~iphone.strings**和**MainStoryboard~ipad.strings**:

> ![](images/xs-dup-strings.png "字符串文件")


<!--
# Native Formatting

Xamarin.iOS applications can take advantage of the .NET framework's formatting options for localizing
numbers and dates.

### Date string formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html#//apple_ref/doc/uid/TP40002369-SW1


NSDateFormatter formatter = new NSDateFormatter ();
formatter.DateFormat = "MMMM/dd/yyyy";
NSString dateString = new NSString (formatter.ToString (d));


### Number formatting

https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfNumberFormatting10_4.html#//apple_ref/doc/uid/TP40002368-SW1

-->

<a name="appstore" />

## <a name="app-store-listing"></a>应用商店列表

遵循上的 Apple 的常见问题[应用存储本地化](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization)输入你的应用已在销售每个国家/地区的翻译。 请注意您的应用程序还包含本地化才会显示翻译其警告**.lproj**目录的语言。

<!--

Once you’ve entered your application into iTunes Connect the default language
metadata and screenshots will appear as shown:

![]( "itunes connect 1")

Use the language list on the right to select other languages to provide
translated application name, description, search keywords, URLs and screenshots.
The complete list of languages is shown in this screenshot:

![]( "itunes connect 2")
-->

## <a name="summary"></a>摘要

本文介绍如何本地化 iOS 应用程序使用的内置资源处理和情节提要功能的基础知识。

你可以在中了解更多有关 i18n 和 L10n iOS、 Android 和跨平台应用 （包括 Xamarin.Forms）[本跨平台指南](~/cross-platform/app-fundamentals/localization.md)。


## <a name="related-links"></a>相关链接

- [Tasky （在代码中本地化） （示例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky （本地化情节提要） （示例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Apple 本地化指南](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [跨平台本地化概述](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms Localization](~/xamarin-forms/app-fundamentals/localization.md)
- [Android 本地化](~/android/app-fundamentals/localization.md)
