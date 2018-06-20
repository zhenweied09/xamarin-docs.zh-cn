---
title: Xamarin.iOS 的本地化
description: 本文档介绍 iOS 本地化功能以及如何在 Xamarin.iOS 应用程序中使用这些功能。 它讨论语言、 区域设置、 字符串文件、 启动映像和的详细信息。
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/28/2017
ms.openlocfilehash: 7f05243196a9b916ac5c7b73df957262604ccb11
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268805"
---
# <a name="localization-in-xamarinios"></a>Xamarin.iOS 的本地化

_本文档介绍 iOS SDK 的本地化功能以及如何使用 Xamarin 访问它们。_

请参阅[国际化编码](encodings.md)有关包括字符集/代码页必须处理非 Unicode 数据的应用程序中的说明。

## <a name="ios-platform-features"></a>iOS 平台功能

本部分介绍一些在 iOS 中的本地化功能。 跳到[下一节](#basics)若要查看特定代码和示例。

### <a name="language"></a>语言

用户选择在其语言**设置**应用。 此设置会影响语言字符串和由操作系统和应用程序中显示的图像。 

若要确定在应用程序使用的语言，获取的第一个元素`NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

此值将是语言代码如`en`为英语，`es`为西班牙语，`ja`代表日语，等等。返回的值被限制为支持 （使用回退规则以确定最佳匹配） 的应用程序的本地化信息之一。

应用程序代码不总是需要检查此值 – Xamarin，并且这两个 iOS 提供有助于自动为用户的语言中提供正确的字符串或资源的功能。 本文档的其余部分中介绍这些功能。

> [!NOTE]
> 使用`NSLocale.PreferredLanguages`以确定用户的语言首选项，而不考虑支持的应用程序本地化信息。 在 iOS 9; 中更改此方法返回的值请参阅[技术注意 TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html)有关详细信息。

### <a name="locale"></a>区域设置

用户选择在其区域设置**设置**应用。 此设置影响日期、 时间、 数字和货币的格式设置的方式。

这允许用户选择他们是否看到 12 小时制或 24 小时时间格式，其小数分隔符是否逗号或一个点和、 月份和日期显示中的每年的顺序。

可以使用 Xamarin 访问这两个 Apple 的 iOS 类 (`NSNumberFormatter`) 以及 System.Globalization 中的.NET 类。 开发人员应评估这是更好地适合其需求，因为没有在每个可用的不同功能。 具体而言，如果您是检索并显示使用 StoreKit 的应用内购买价格则应使用 Apple 的格式设置类，其返回的价格信息。

可通过两种方法之一查询当前区域设置：

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

第一个值可以缓存由操作系统，并因此可能不会始终反映用户的当前所选的区域设置。 第二个值用于获取当前所选的区域设置。

> [!NOTE]
> Mono （.NET 运行时基于 Xamarin.iOS） 和 Apple 的 iOS Api 不支持完全相同的语言/区域组合集。
> 因此，就可以在 iOS 中选择的语言/区域组合**设置**应用程序未映射到 Mono 中的有效值。 例如，将 iPhone 的语言为英语和其区域设置为西班牙将导致以下 Api 以生成不同的值：
> 
> - `CurrentThead.CurrentCulture`: EN-US (Mono API)
> - `CurrentThread.CurrentUICulture`: EN-US (Mono API)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (Apple API)
>
> 由于 Mono 使用`CurrentThread.CurrentUICulture`若要选择资源和`CurrentThread.CurrentCulture`设置日期和货币的格式，基于 Mono 的本地化 （例如，.resx 文件） 可能会不产生预期的结果，这些语言/区域组合。 在这些情况下，依赖于 Apple 的 Api，用于本地化根据需要。

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS 生成`NSCurrentLocaleDidChangeNotification`用户更新后，其区域设置。 应用程序可以侦听此通知时它们正在运行并可以对此 UI 进行相应的更改。

<a name="basics" />

## <a name="localization-basics-in-ios"></a>在 iOS 中的本地化基础知识

IOS 的以下功能可轻松利用 Xamarin 向用户显示有关提供本地化的资源中。 请参阅[TaskyL10n 示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)若要了解如何实现这些想法。

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>在 Info.plist 中指定默认和支持的语言

在[技术的问答 QA1828: iOS 如何确定你应用语言为](https://developer.apple.com/library/content/qa/qa1828/_index.html)，Apple 介绍 iOS 如何选择要在应用程序中使用的语言。 以下因素会影响显示的语言：

- 用户的首选语言 (在中找到**设置**应用)
- 与应用程序 （.lproj 文件夹） 的本地化信息捆绑在一起
- `CFBundleDevelopmentRegion` (**Info.plist**值，该值指定应用程序的默认语言)
- `CFBundleLocalizations` (**Info.plist**指定所有受支持的本地化信息的数组)

技术在问答中，所示`CFBundleDevelopmentRegion`表示应用程序的默认区域和语言。 如果应用程序显式不支持任何用户的首选语言，它将使用此字段所指定的语言。 

> [!IMPORTANT]
> iOS 11 严格比以前版本的操作系统适用此语言选择机制。 因此，未显式声明其支持本地化信息 – 通过包括.lproj 文件夹或设置一个值的任何 iOS 11 应用`CFBundleLocalizations`– 比在 iOS 10 中可能会显示在 iOS 11 中的另一种语言。

如果`CFBundleDevelopmentRegion`不中指定**Info.plist**文件，Xamarin.iOS 生成工具当前使用的默认值为`en_US`。 虽然这可能会更改的未来版本中，则意味着默认语言是英语。

若要确保你的应用程序选择所需的语言，请执行以下步骤：

- 指定的默认语言。 打开**Info.plist**并用**源**视图，以设置的值`CFBundleDevelopmentRegion`钥; 在 XML 中，其外观应与下类似：

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

此示例使用"es"若要指定，当无用户的首选语言都受支持，默认为西班牙语。

- 声明所有受支持的本地化信息。 在**Info.plist**，使用**源**视图设置为一个数组`CFBundleLocalizations`钥; 在 XML 中，其外观应与下类似：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

使用.NET 机制，如.resx 文件必须提供这些已本地化的 Xamarin.iOS 应用程序**Info.plist**以及值。

有关这些详细信息**Info.plist**密钥，看一看 Apple 的[信息属性列表密钥引用](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)。

### <a name="localizedstring-method"></a>LocalizedString 方法

`NSBundle.MainBundle.LocalizedString`方法查找已存储在的本地化文本 **.strings**项目中的文件。 按语言中，使用的特殊命名目录中进行组织这些文件 **.lproj**后缀。

#### <a name="strings-file-locations"></a>.strings 文件位置

- **Base.lproj**是包含默认语言资源的目录。
  它通常位于项目根目录中 (但可以放置在**资源**文件夹)。
- **<language>.lproj**目录创建时为每个支持的语言，通常**资源**文件夹。

可以有许多不同的 **.strings**每个语言目录中的文件：

- **Localizable.strings** – 本地化文本的主列表。
- **InfoPlist.strings** – 某些特定的密钥允许在此文件中转换等应用程序名称。
- **< 情节提要名称 >.strings** – 可选文件，其中包含用户界面元素在情节提要中的翻译。

**生成操作**这些文件应**捆绑资源**。

#### <a name="strings-file-format"></a>.strings 文件格式

本地化的字符串值的语法是：

```console
/* comment */
"key"="localized-value";
```

应转义字符串中的以下字符：

* `\"`  引号
* `\\`  反斜杠
* `\n`  换行符

这是一个示例**es/Localizable.strings** (ie。西班牙语） 文件中的示例：

```console
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

3. （可选） 将放在映像的本地化的版本 **.lproj**文件夹的每种语言 （如。 **es.lproj**， **ja.lproj**)。 使用相同的文件名**flag.png**每个语言目录中。

如果图像不存在为特定的语言，iOS 将回退到默认的本机语言文件夹，并从那里加载映像。


#### <a name="launch-images"></a>启动映像

使用标准命名约定为启动映像 （和 XIB 或对 iPhone 6 型号的情节提要） 时将它们中放置 **.lproj**为每种语言的目录。

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>应用程序名称

放置**InfoPlist.strings**文件中 **.lproj**目录可让你重写从应用程序的某些值**Info.plist**，包括应用程序名称：

```console
"CFBundleDisplayName" = "LeónTodo";
```

你可以使用其他键[本地化应用程序特有字符串](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21)是：

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

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

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

在西班牙西班牙语的结果：

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

请参阅 Apple[日期格式化程序](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html)文档以了解更多信息。 当测试区分区域设置的日期和时间格式，检查同时**iPhone 语言**和**区域**设置。

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>右到左 (RTL) 布局

iOS 提供了大量功能有助于构建 RTL 识别应用程序：

* 使用自动布局`leading`和`trailing`控件对齐 （其中对应于左侧和右侧为英语，但对于 RTL 语言反转） 的属性。
  [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md)控件很适合用于对需要 RTL 注意的控件进行布局。
* 使用`TextAlignment = UITextAlignment.Natural`文本对齐方式 （这将处于对于大多数语言而不适合 RTL）。
* `UINavigationController` 自动翻转后退按钮，并反转轻扫方向。

下面的屏幕截图演示[本地化 Tasky 示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)阿拉伯语和希伯来语 （尽管已在字段中输入英语）：

[![](images/rtl-ar-sml.png "阿拉伯语的本地化")](images/rtl-ar.png#lightbox "Arabic") 

[![](images/rtl-he-sml.png "希伯来语的本地化")](images/rtl-he.png#lightbox "Hebrew")

iOS 自动反转`UINavigationController`，和其他控件放置在`UIStackView`或与自动布局对齐。
使用本地化 RTL 文本 **.strings** LTR 文本的方式相同的文件。

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>本地化代码中的用户界面

[Tasky （在代码中本地化）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)示例演示如何本地化应用程序代码 （而不是 XIBs 或情节提要） 的用户界面内置其中。

### <a name="project-structure"></a>项目结构

![](images/solution-code.png "资源树")

### <a name="localizablestrings-file"></a>Localizable.strings 文件

如上所述， **Localizable.strings**文件格式的键 / 值对组成。 密钥描述字符串的意图，值为应用程序中使用的已翻译的文本。

西班牙语 (**es**) 本地化信息的示例如下所示：

```console
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

* 在适用于 Mac 的 Visual Studio，位于**属性填充**称为**本地化 ID**。
* 在 Xcode 中，调用**对象 ID**。

此字符串值通常具有"NF3-h8-xmR"，例如一个窗体，如下面的屏幕截图中所示：

![](images/xs-designer-localization-id.png "情节提要本地化的 Xcode 视图")

在使用此值 **.strings**文件以将已转换的文本自动分配给每个控件。

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

情节提要转换文件的格式是类似于**Localizable.strings**文件，只不过密钥 （在左侧的值） 不能是用户定义，但改为必须具有非常特定的格式： `ObjectID.property`。

在示例中**Mainstoryboard.strings**下面可以看到`UITextField`具有`placeholder`可以进行本地化; 的 text 属性`UILabel`具有`text`属性; 和`UIButton`s 默认文本使用设置`normalTitle`:

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> 使用情节提要具有大小类可能会导致不会显示应用程序中的翻译。 [Apple 的 Xcode 发行说明](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html)指示，情节提要或 XIB 将不本地化正确是否三个事项均成立： 它使用大小类、 基本地化和 build 目标设置为通用，并且生成面向 iOS 7.0。 解决方法是为两个相同的文件复制你的情节提要字符串文件： **MainStoryboard~iphone.strings**和**MainStoryboard~ipad.strings**，如以下屏幕截图中所示：
> 
> ![](images/xs-dup-strings.png "字符串文件")

<a name="appstore" />

## <a name="app-store-listing"></a>应用商店列表

遵循上的 Apple 的常见问题[应用存储本地化](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization)输入你的应用已在销售每个国家/地区的翻译。 请注意您的应用程序还包含本地化才会显示翻译其警告 **.lproj**目录的语言。

## <a name="summary"></a>总结

本文介绍如何本地化 iOS 应用程序使用的内置资源处理和情节提要功能的基础知识。

你可以在中了解更多有关 i18n 和 L10n iOS、 Android 和跨平台应用 （包括 Xamarin.Forms）[本跨平台指南](~/cross-platform/app-fundamentals/localization.md)。

## <a name="related-links"></a>相关链接

- [Tasky （在代码中本地化） （示例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky （本地化情节提要） （示例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Apple 本地化指南](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [跨平台本地化概述](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms 本地化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Android 本地化](~/android/app-fundamentals/localization.md)
