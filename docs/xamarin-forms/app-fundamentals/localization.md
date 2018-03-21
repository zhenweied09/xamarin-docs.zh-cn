---
title: "本地化"
description: "可以使用.NET 资源文件本地化 Xamarin.Forms 应用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: e04ea24883bdf1e29a538aaff92c555df8e1755f
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2018
---
# <a name="localization"></a>本地化

_可以使用.NET 资源文件本地化 Xamarin.Forms 应用。_

## <a name="overview"></a>概述

本地化.NET 应用程序使用的内置机制[RESX 文件](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx)和中的类`System.Resources`和`System.Globalization`命名空间。 包含已翻译的字符串的 RESX 文件都嵌入到 Xamarin.Forms 程序集，以及一个编译器生成的类，提供对翻译强类型访问。 然后可以在代码中检索的翻译文本。

### <a name="sample-code"></a>代码示例

有与此文档关联的两个示例：

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization)是很简单的示例所述的概念。 下面所示的代码片段是所有与此示例。
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized)是基本正在运行的应用使用这些本地化技术。

#### <a name="shared-projects-are-not-recommended"></a>建议不要使用共享的项目

TodoLocalized 示例包括[共享项目演示](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/)但是由于生成系统的限制的资源文件不会获得**。 designer.cs**生成这违反能够访问的文件转换字符串[在代码中强类型](~/xamarin-forms/app-fundamentals/localization.md)。

此文档的其余部分都与使用 Xamarin.Forms PCL 模板项目。

## <a name="globalizing-xamarinforms-code"></a>全球化 Xamarin.Forms 代码

**全球化**应用程序是使其"全球通用。"的过程 这意味着编写代码，它能够显示不同的语言。

全球化应用程序的要点之一生成用户界面，以便有没有*硬编码*文本。 相反，向用户显示的任何内容应检索从一组的已转换为所选语言的字符串。

在本文档中，我们将查看如何使用 RESX 文件来存储这些字符串和检索它们的显示，具体取决于用户的首选项。

这些示例目标英语、 法语、 西班牙语、 德语、 中文、 日语、 俄语和葡萄牙语 （巴西） 语言。 应用程序可以转换为所需的尽可能少或任意多个语言。

### <a name="adding-resources"></a>将资源添加

全球化 Xamarin.Forms PCL 应用程序的第一步添加可用于将使用的所有文本都存储在应用程序的 RESX 资源文件。 我们需要添加包含默认的文本，RESX 文件，然后添加我们想要支持每种语言的其他 RESX 文件。

#### <a name="base-language-resource"></a>基本语言资源

基本资源 (RESX) 文件将包含 （的示例假定英语为默认语言） 的默认语言字符串。 将文件添加到 Xamarin.Forms 常见代码项目中，右键单击项目并选择**添加 > 新文件...**.

选择有意义的名称，例如**AppResources**按**确定**。

[![将资源文件添加](localization-images/resx-new-file-sml.png "新建文件对话框")](localization-images/resx-new-file.png#lightbox "新建文件对话框")

两个文件将添加到项目中：

* **AppResources.resx**可以翻译字符串以 XML 格式的存储位置的文件。
* **AppResources.designer.cs**声明的分部类，以包含对 RESX XML 文件中创建的所有元素的引用的文件。

解决方案树将显示作为相关的文件。 RESX 文件*应*编辑添加新的可以翻译字符串; **。 designer.cs**文件应*不*对其进行编辑。

![](localization-images/appresources-tree.png "AppResources.resx 文件")

##### <a name="string-visibility"></a>字符串可见性

默认情况下对字符串的强类型引用生成时，将会`internal`对程序集。 这是因为 RESX 文件的默认生成工具生成**。 designer.cs**文件`internal`属性。

选择**AppResources.resx**文件，并显示**属性**板以查看其中此生成工具是配置。 下面显示的屏幕截图**自定义工具： ResXFileCodeGenerator**。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](localization-images/vs-resx-internal-sml.png "AppResources.Resx 的属性窗口")](localization-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](localization-images/xs-resx-internal-sml.png "AppResources.Resx 的属性填充")](localization-images/xs-resx-internal.png#lightbox)

-----

若要使强类型字符串属性`public`，你必须手动将配置更改为**自定义工具： PublicResXFileCodeGenerator**，下面的屏幕截图中所示：


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](localization-images/vs-resx-public-sml.png "AppResources.Resx 的属性窗口")](localization-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](localization-images/xs-resx-internal-sml.png "AppResources.Resx 的属性填充")](localization-images/xs-resx-internal.png#lightbox)


[![](localization-images/xs-resx-public-sml.png "AppResources.Resx 的属性填充")](localization-images/xs-resx-public.png#lightbox)

-----

此更改是可选的并且仅需要如果你想要跨不同的程序集引用本地化的字符串 （例如，如果将这些 RESX 文件置于不同的程序集到你的代码）。 本主题的示例离开字符串`internal`因为它们在同一 Xamarin.Forms PCL 程序集使用这些定义。

你只需以在基的 RESX 文件上设置自定义工具; 如上所示不需要设置*任何*以下各节所述的特定于语言的 RESX 文件上的生成工具。

##### <a name="editing-the-resx-file"></a>编辑 RESX 文件

遗憾的是没有任何内置 RESX 编辑器在 Visual Studio for mac。 添加新的可以翻译字符串，则需要添加新的 XML`data`每个字符串的元素。 每个`data`元素可以包含以下：

* `name` 属性 （必需） 是针对此可以翻译字符串的密钥。 它必须是有效的 C# 属性名-因此允许不包含空格或特殊字符。
* `value` 元素 （必需）、 实际应用程序中显示的字符串。
* `comment` （可选） 的元素可以包含说明为说明了如何使用此字符串转换器。
* `xml:space` （可选） 若要控制字符串中的间距的保存方式的属性。

一些示例`data`元素如下所示：

```xml
<data name="NotesLabel" xml:space="preserve">
    <value>Notes:</value>
    <comment>label for input field</comment>
</data>
<data name="NotesPlaceholder" xml:space="preserve">
    <value>eg. buy milk</value>
    <comment>example input for notes field</comment>
</data>
<data name="AddButton" xml:space="preserve">
    <value>Add new item</value>
</data>
```

写入应用程序，应在新到基 RESX 资源文件中添加文本向用户显示的每个段`data`元素。 建议您包括`comment`s 尽可能多地以确保高质量的翻译。

> [!NOTE]
> Visual Studio （包括免费的社区版） 包含基本的 RESX 编辑器。 如果你有权访问的 Windows 计算机，可以是一种简便方式添加和编辑 RESX 文件中的字符串。

#### <a name="language-specific-resources"></a>特定于语言的资源

通常情况下，直到应用程序的大量都已写入 （在这种情况下，默认 RESX 文件将包含大量字符串），则不会进行默认文本字符串的实际转换。 它仍是在开发周期的初期添加特定于语言的资源使用机器翻译文本，以帮助本地化代码进行测试 （可选） 填充一个好办法。

一个附加的 RESX 文件添加我们想要支持每种语言。
特定于语言的资源文件必须遵循特定的命名约定： 使用相同的文件名，如基资源文件 （如。 **AppResources**) 后跟一个句点 （.），然后选择的语言代码。 简单的示例包括：

* **AppResources.fr.resx** -法语语言翻译。
* **AppResources.es.resx** -西班牙语语言翻译。
* **AppResources.de.resx** -德语语言翻译。
* **AppResources.ja.resx**的日语语言翻译。
* **AppResources.zh Hans.resx** -中文 （简体） 语言翻译。
* **AppResources.zh Hant.resx** -中文 （繁体） 语言翻译。
* **AppResources.pt.resx** -葡萄牙语语言翻译。
* **AppResources.pt BR.resx** -巴西葡萄牙语语言翻译。

常规模式是使用两个字母的语言代码，但有 （例如中文） 的一些示例，其中使用另一种格式，以及 （如葡萄牙语 （巴西）） 的其他示例要求四个字符的区域设置标识符的位置。

这些特定于语言的资源文件*不这样做*需要**。 designer.cs**分部类，以便它们都可添加为正则 XML 文件，与**生成操作： EmbeddedResource**设置。 此屏幕截图显示一个包含特定于语言的资源文件的解决方案：

![](localization-images/appresources-langs.png "特定于语言的资源文件")

在开发应用程序和基 RESX 文件已添加文本时，你应将它发送到将将每个转换的转换器`data`元素并返回特定于语言的资源文件 （使用显示的命名约定） 要包括在应用程序。 一些机转换的示例如下所示：

**AppResources.es.resx （西班牙语）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx (Japanese)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx (Brazilian Portuguese)**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

仅`value`元素需要更新通过转换器-`comment`不能转换。 请记住： 时编辑 XML 文件进行转义保留的字符，例如`<`， `>`，`&`与`&lt;`， `&gt;`，和`&amp;`如果它们出现在`value`或`comment`。

<a name="incode" />

### <a name="using-resources-in-code"></a>代码中使用的资源

RESX 资源文件中的字符串将可用于在用户界面代码使用`AppResources`类。 `name`分配给每个字符串中 RESX 文件将成为这如下所示，可以在 Xamarin.Forms 代码中引用该类上的属性：

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

预期上 iOS、 Android 和 Windows 平台呈现为你的用户界面的一样，只不过现在可以将应用程序翻译成多个语言，因为正在从资源加载文本，而不是硬编码。 下面是在转换之前每个平台上显示用户界面屏幕截图：

![](localization-images/simple-example-english.png "在转换之前的跨平台用户界面")

### <a name="troubleshooting"></a>疑难解答

#### <a name="testing-a-specific-language"></a>测试特定的语言

它可能会很棘手，若要切换到不同的语言，在你想要快速测试不同的区域性时的开发期间 particulary 的模拟器或设备。

你可以强制一种特定的语言，以通过设置加载`Culture`此代码段中所示：

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

这种方法 – 直接在上设置的区域性`AppResources`类 – 也可以用于实现在您的应用程序 （而不是使用设备的区域设置） 语言选择器。

#### <a name="loading-embedded-resources"></a>加载嵌入资源

尝试调试嵌入的资源 （例如 RESX 文件） 的问题时，下面的代码段非常有用。 将此代码添加到你的应用 （在早期的应用程序生命周期中），将会列出嵌入到程序集中，显示完整的资源标识符的所有资源：

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly; // "EmbeddedImages" should be a class in your app
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

在**AppResources.Designer.cs**文件 (解决*行 33*)，完整*资源管理器名称*（如指定 `"UsingResxLocalization.Resx.AppResources"`) 类似于下面的代码：

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

检查**应用程序输出**的结果的上面显示的调试代码，以确认正确的资源列出 （例如。`"UsingResxLocalization.Resx.AppResources"`).

如果没有，则`AppResources`类将不能加载其资源。
请检查以下内容以解决问题，无法找到资源：

* 项目的默认命名空间匹配的根命名空间中**AppResources.Designer.cs**文件。
* 如果**AppResources.resx**文件位于一个子目录，则子目录名称应为命名空间的一部分*和*的资源标识符的一部分。
* **AppResources.resx**文件具有**生成操作： EmbeddedResource**。
* **项目选项 > 源代码 >.NET 命名策略 > 使用 Visual Studio 样式资源名称**勾选了。 如果你愿意，可以 untick 这，但是需要引用 RESX 资源时使用的命名空间将整个应用更新。

#### <a name="doesnt-work-in-debug-mode-android-only"></a>在调试模式下 (仅 Android) 不起作用

如果使用已翻译的字符串在版本 Android 生成，但不是在调试时，右键单击**Android 项目**和选择**选项 > 生成 > Android 生成**и · ± ј**快速程序集部署**不勾选了。 此选项会导致加载资源出现问题，如果要测试本地化应用程序不应使用。

### <a name="displaying-the-correct-language"></a>显示正确的语言

到目前为止，我们探讨了如何编写代码，以便可以提供翻译，但不是如何实际使它们显示。 Xamarin.Forms 代码可以充分利用。NET 的资源加载正确的语言翻译，但我们需要查询来确定用户选定的语言的每个平台上的操作系统。

由于获取用户的语言首选项需要一些特定于平台的代码，使用[依赖服务](~/xamarin-forms/app-fundamentals/dependency-service/index.md)公开 Xamarin.Forms 应用中的信息并为每个平台实现它。

首先，定义一个接口，以便公开用户的首选的区域性，类似于下面的代码：

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

其次，使用[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) Xamarin.Forms 中`App`类来调用接口并将我们 RESX 资源区域性设置为正确的值。 请注意，我们不需要手动设置此值为 Windows Phone 和通用 Windows 平台上，因为资源 framework 自动在这些平台上识别所选的语言。

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

资源`Culture`需要应用程序首次加载，以便使用正确的语言字符串时设置。 （可选） 可能会更新可能会引发 iOS 或 Android，如果用户更新其语言首选项应用运行时的特定于平台的事件根据此值。

实现`ILocalize`接口所示[特定于平台的代码](#Platform-specific_Code)下面一节。 这些实现充分利用此`PlatformCulture`帮助器类：

```csharp
public class PlatformCulture
{
    public PlatformCulture (string platformCultureString)
    {
        if (String.IsNullOrEmpty(platformCultureString))
        {
            throw new ArgumentException("Expected culture identifier", "platformCultureString"); // in C# 6 use nameof(platformCultureString)
        }
        PlatformString = platformCultureString.Replace("_", "-"); // .NET expects dash, not underscore
        var dashIndex = PlatformString.IndexOf("-", StringComparison.Ordinal);
        if (dashIndex > 0)
        {
            var parts = PlatformString.Split('-');
            LanguageCode = parts[0];
            LocaleCode = parts[1];
        }
        else
        {
            LanguageCode = PlatformString;
            LocaleCode = "";
        }
    }
    public string PlatformString { get; private set; }
    public string LanguageCode { get; private set; }
    public string LocaleCode { get; private set; }
    public override string ToString()
    {
        return PlatformString;
    }
}
```

<a name="Platform-specific_Code" />

### <a name="platform-specific-code"></a>特定于平台的代码

要检测要显示的语言必须是特定于平台的因为 iOS、 Android 和 Windows 平台所有略有不同的方式公开此信息的代码。 代码`ILocalize`依赖服务是提供下面为每个平台，以及其他特定于平台的要求，以确保本地化的文本正确地呈现。

特定于平台的代码还必须处理其中操作系统使用户能够配置不支持的区域设置标识符的情况。NET 的`CultureInfo`类。 在这些情况下必须编写自定义代码来检测不受支持的区域设置进行替代最佳。NET 兼容的区域设置。

#### <a name="ios-application-project"></a>iOS 应用程序项目

iOS 用户从日期和时间格式设置区域性分别选择其首选的语言。 若要加载正确的资源来本地化我们只需查询的 Xamarin.Forms 应用`NSLocale.PreferredLanguages`的第一个元素的数组。

以下实现`ILocalize`依赖服务应放在 iOS 应用程序项目。 因为 iOS 而不是短划线 （这是.NET 标准表示形式） 使用下划线代码会将下划线替换实例化之前`CultureInfo`类：

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.iOS.Localize))]

namespace UsingResxLocalization.iOS
{
public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo ()
        {
            var netLanguage = "en";
            if (NSLocale.PreferredLanguages.Length > 0)
            {
                var pref = NSLocale.PreferredLanguages [0];
                netLanguage = iOSToDotnetLanguage(pref);
            }
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string iOSToDotnetLanguage(string iOSLanguage)
        {
            var netLanguage = iOSLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage (PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "pt":
                    netLanguage = "pt-PT"; // fallback to Portuguese (Portugal)
                    break;
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> `try/catch`块以`GetCurrentCultureInfo`方法模拟完全匹配找不到，查找接近的匹配项，只需根据语言 （区域设置中的字符的第一个块） 通常用于区域设置说明符 – 回退行为。
>
> Xamarin.Forms，这对于某些区域设置中在 iOS 中有效，但并不对应到有效`CultureInfo`中.NET 和上面尝试处理这种情况的代码。
>
> 例如，iOS**设置 > 常规语言&amp;区域**屏幕可让你设置你的手机**语言**到**英语**但**区域**到**西班牙**，这将导致的区域设置字符串`"en-ES"`。 当`CultureInfo`创建失败，代码回退使用刚刚前两个字母来选择显示语言。
>
> 开发人员应修改`iOSToDotnetLanguage`和`ToDotnetFallbackLanguage`方法以处理其支持的语言所需的特定用例。


有一些系统定义的用户界面的元素来自动如转换由 iOS**完成**按钮上`Picker`控件。 若要强制转换我们需要指示哪种语言中我们支持这些元素的 iOS **Info.plist**文件。 你可以将这些值添加通过**Info.plist > 源**如下所示：

![在 Info.plist 中的本地化键](localization-images/info-plist.png "Info.plist 中的本地化键")

或者，打开**Info.plist**文件在 XML 编辑器中，并直接编辑的值：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

一旦实现依赖服务并更新**Info.plist**，iOS 应用程序将能够显示本地化的文本。

> [!NOTE]
> 请注意，Apple 将葡萄牙语略有不同比你预期。
> 从[其文档](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"使用 pt 作为语言 ID 为葡萄牙语它用作巴西和 PT-PT 中的语言 ID 葡萄牙语因为中葡萄牙使用的该"_。
> 这意味着当葡萄牙语语言选择在非标准的区域，回退语言将葡萄牙语 （巴西） 在 iOS 上除非编写代码来更改此行为 (如`ToDotnetFallbackLanguage`上面)。

#### <a name="android-application-project"></a>Android 应用程序项目

Android 公开通过当前所选的区域设置`Java.Util.Locale.Default`，并使用下划线分隔符而不是短划线 （它将替换下面的代码）。 将此依赖关系服务实现添加到 Android 应用程序项目：

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.Android.Localize))]

namespace UsingResxLocalization.Android
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale(CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo()
        {
            var netLanguage = "en";
            var androidLocale = Java.Util.Locale.Default;
            netLanguage = AndroidToDotnetLanguage(androidLocale.ToString().Replace("_", "-"));
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string AndroidToDotnetLanguage(string androidLanguage)
        {
            var netLanguage = androidLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (androidLanguage)
            {
                case "ms-BN":   // "Malaysian (Brunei)" not supported .NET culture
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "in-ID":  // "Indonesian (Indonesia)" has different code in  .NET
                    netLanguage = "id-ID"; // correct code for .NET
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage(PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> `try/catch`块以`GetCurrentCultureInfo`方法模拟完全匹配找不到，查找接近的匹配项，只需根据语言 （区域设置中的字符的第一个块） 通常用于区域设置说明符 – 回退行为。
>
> Xamarin.Forms，这对于某些区域设置中在 Android 中有效，但并不对应到有效`CultureInfo`中.NET 和上面尝试处理这种情况的代码。
>
> 开发人员应修改`iOSToDotnetLanguage`和`ToDotnetFallbackLanguage`方法以处理其支持的语言所需的特定用例。


一旦此代码已添加到 Android 应用程序项目，它将能够自动显示已翻译的字符串。

> [!NOTE]
>️**警告：**如果使用已翻译的字符串在版本 Android 生成，但不是在调试时，右键单击**Android 项目**和选择**选项 > 生成 > Android生成**并确保**快速程序集部署**不勾选了。 此选项会导致加载资源出现问题，如果要测试本地化应用程序不应使用。

#### <a name="windows-application-projects"></a>Windows 应用程序项目

Windows 8.1 和通用 Windows 平台 (UWP) 项目不需要的依赖关系服务 – 这些平台自动正确设置资源的区域性。

实现本文档下文所述的 XAML 标记扩展可能需要`ILocalize`下面显示了 Windows Phone 的实现。

##### <a name="windows-phone-80"></a>Windows Phone 8.0

但不用于`App`类中，以下是有关其 Windows Phone 实现`ILocalize`依赖服务。 将此类添加到 Windows Phone 应用项目;如果实现 XAML 标记扩展将在后面说明，将需要：

```csharp
[assembly: Dependency(typeof(UsingResxLocalization.WinPhone.Localize))]

namespace UsingResxLocalization.WinPhone
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci) { }
        public System.Globalization.CultureInfo GetCurrentCultureInfo ()
        {
            return System.Threading.Thread.CurrentThread.CurrentUICulture;
        }
    }
}

```

Windows Phone 8.0 项目必须正确配置为要显示的本地化文本。
必须在项目选项中选择支持的语言*和* **WMAppManifest.xml**文件。
如果未更新这些设置将不加载本地化的 RESX 资源。

##### <a name="project-options"></a>项目选项

右键单击 Windows Phone 项目并选择**属性**。 在**应用程序**选项卡上刻度**支持区域性**了应用程序支持：

[![](localization-images/winphone-projectproperties-sml.png "项目属性-支持的区域性")](localization-images/winphone-projectproperties.png#lightbox "项目属性-支持的区域性")

##### <a name="wmappmanifestxml"></a>WMAppManifest.xml

展开 Windows Phone 项目中的属性节点并双击**WMAppManifest.xml**文件。 单击**打包**选项卡上和刻度支持的应用程序的所有语言。

[![](localization-images/winphone-wmappmanifest-sml.png "WMAppManifest.xml-支持的语言")](localization-images/winphone-wmappmanifest.png#lightbox "WMAppManifest.xml-支持的语言")

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

展开可移植类库 (PCL) 项目中的属性节点并双击**AssemblyInfo.cs**文件。 将以下行添加到要将非特定语言资源程序集语言设置为英语的文件：

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

这可告知资源管理器的应用程序的默认区域性，因此确保语言中性 RESX 文件中定义的字符串 (**AppResources.resx**) 应用程序运行在一个英语区域设置时，将显示。

### <a name="example"></a>示例

在更新的特定于平台的项目如下所示上述并重新应用程序编译的已翻译 RESX 文件之后, 更新的翻译将在每个应用可用。 下面是转换为简体中文的示例代码的屏幕快照：

![](localization-images/simple-example-hans.png "转换为中文 （简体） 的跨平台用户界面")

## <a name="localizing-xaml"></a>本地化 XAML

当生成标记的 XAML 中的 Xamarin.Forms 用户界面将与类似此操作，请使用字符串嵌入直接在 XML 中：

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

理想情况下，我们无法转换直接在 XAML 中，我们可以通过创建中执行此操作的用户界面控件*标记扩展*。 公开 RESX 资源到使用 XAML 的标记扩展的代码所示。 此类应添加到 Xamarin.Forms 通用代码 （以及 XAML 页面中）：

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in XAML
    [ContentProperty("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci = null;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(
            () => new ResourceManager(ResourceId, IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly));

        public string Text { get; set; }

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public object ProvideValue(IServiceProvider serviceProvider)
        {
            if (Text == null)
                return string.Empty;

            var translation = ResMgr.Value.GetString(Text, ci);
            if (translation == null)
            {
#if DEBUG
                throw new ArgumentException(
                    string.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
#else
                translation = Text; // HACK: returns the key, which GETS DISPLAYED TO THE USER
#endif
            }
            return translation;
        }
    }
}
```

下面的列表内容说明了在上面的代码的重要元素：

* 此类命名`TranslateExtension`，而是通过约定我们可以指就**翻译**我们标记中。
* 此类应实现`IMarkupExtension`，所需为其 Xamarin.Forms 工作。
* `"UsingResxLocalization.Resx.AppResources"` 是我们 RESX 资源的资源标识符。 它包含我们默认命名空间、 资源文件所在的文件夹和默认的 RESX 文件名。
* `ResourceManager`类创建使用`IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)`来确定当前的程序集加载资源，并缓存在静态中`ResMgr`字段。 它创建为`Lazy`类型，以便其创建将推迟，直到它首先用`ProvideValue`方法。
* `ci` 使用依赖项服务从本机操作系统中获取用户的选择的语言。
* `GetString` 是从资源文件中检索实际的已翻译的字符串的方法。 在 Windows Phone 8.1 和通用 Windows 平台上，`ci`将为 null 因为`ILocalize`接口未实现在这些平台上。 这是等效于调用`GetString`仅带第一个参数的方法。 相反，资源框架会自动识别区域设置，并将从相应的 RESX 文件中检索已翻译的字符串。
* 错误处理已包含在内，以帮助调试缺少的资源通过引发异常 (在`DEBUG`仅限模式)。

以下 XAML 代码段演示如何使用标记扩展。 有两个步骤即可使它正常：

1. 声明自定义`xmlns:i18n`在根节点的命名空间。 `namespace`和`assembly`完全-在此示例中它们是相同但可以是不同项目中的项目设置必须匹配。
2. 使用`{Binding}`上将通常包含要调用的文本的属性的语法`Translate`标记扩展。 资源键是唯一的必需的参数。

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        x:Class="UsingResxLocalization.FirstPageXaml"
        xmlns:i18n="clr-namespace:UsingResxLocalization;assembly=UsingResxLocalization">
    <StackLayout Padding="0, 20, 0, 0">
        <Label Text="{i18n:Translate NotesLabel}" />
        <Entry Placeholder="{i18n:Translate NotesPlaceholder}" />
        <Button Text="{i18n:Translate AddButton}" />
    </StackLayout>
</ContentPage>
```

下面更详细的语法也是有效的标记扩展：

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>本地化特定于平台的元素

尽管我们可以满足 Xamarin.Forms 代码中的用户界面的转换，但有一些必须在每个特定于平台的项目中的元素。 本部分将介绍如何本地化：

* Application Name
* 图像

示例项目包括已本地化的图像调用**flag.png**中, 引用了该 C#，如下所示：

```csharp
var flag = new Image();
switch (Device.RuntimePlatform)
{
    case Device.iOS:
    case Device.Android:
        flag.Source = ImageSource.FromFile("flag.png");
        break;
    case Device.UWP:
        flag.Source = ImageSource.FromFile("Assets/Images/flag.png");
        break;
}
```

在如下的 XAML 中还引用标志映像：

```xaml
<Image>
  <Image.Source>
    <OnPlatform x:TypeArguments="ImageSource">
        <On Platform="iOS, Android" Value="flag.png" />
        <On Platform="UWP" Value="Assets/Images/flag.png" />
    </OnPlatform>
  </Image.Source>
</Image>
```

所有平台将自动都解决类似这样的映像，本地化版本的映像引用，前提执行如下所述的项目结构。

### <a name="ios-application-project"></a>iOS 应用程序项目

iOS 使用名为本地化 Projects 命名标准或**.lproj**目录，用于包含映像和字符串资源。 这些目录中可以包含在应用中，使用的图像的本地化的版本以及**InfoPlist.strings**可用来本地化应用程序名称的文件。

#### <a name="images"></a>图像

此屏幕快照显示 iOS 示例应用程序与特定于语言的**.lproj**目录。 西班牙语目录调用**es.lproj**，包含默认的映像的本地化的版本，以及**flag.png**:

![](localization-images/ios-resources.png "iOS 本地化项目目录")

每个语言目录包含一份**flag.png**、 针对该语言本地化。 如果提供没有图像，则操作系统将默认为默认语言目录中的图像。 有关完整 Retina 支持，还应提供 **@2x** 和 **@3x** 的每个图像的副本。

#### <a name="app-name"></a>应用程序名称

内容**InfoPlist.strings**是不仅仅是一个键-值来配置的应用程序名称：

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

当运行应用程序时，应用程序名称和映像已同时本地化：

![](localization-images/ios-imageicon.png "iOS 示例应用程序文本和图像本地化")

### <a name="android-application-project"></a>Android 应用程序项目

Android 遵循用于存储使用不同的本地化的图像的不同方案**可绘制**和**字符串**带有语言代码后缀的目录。 （如 ZH-TW 或 PT-BR） 需要四个字母区域设置代码时，请注意，Android 需要附加**r**前面 dash/以下区域设置代码 （如。 中文 rTW 或 pt rBR）。

#### <a name="images"></a>图像

此屏幕快照显示 Android 示例，其中具有某些本地化 drawables 和字符串：

![](localization-images/android-resources.png "Android 本地化 Drawables 和字符串目录")

请注意，Android 不会使用中文 Hans 和简体中文和繁体中文; 此不同代码相反，它仅支持特定国家/地区代码 ZH-CN 和 ZH-TW。

若要为高密度屏幕支持不同的分辨率的图像，创建与其他语言文件夹`-*dpi`后缀，例如**drawables es mdpi**， **drawables es xdpi**， **drawables es xxdpi**等。请参阅[提供的替代项 Android 资源](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)有关详细信息。

#### <a name="app-name"></a>应用程序名称

内容**strings.xml**是不仅仅是一个键-值来配置的应用程序名称：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

更新**MainActivity.cs** Android 应用程序项目中，以便`Label`引用 XML 的字符串。

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

应用程序现在本地化的应用程序名称和映像。 下面是结果的屏幕快照中 (西班牙语）：

![](localization-images/android-imageicon.png "Android 示例应用程序文本和图像本地化")

### <a name="windows-phone-80-application-project"></a>Windows Phone 8.0 应用程序项目

Windows Phone 不具有一种简单内置方法的选择特定的已本地化的图像或本地化的应用程序名称。

#### <a name="images"></a>图像

若要避开此限制示例提供了一项建议如何实现已本地化映像加载使用[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)为`Image`控件。

自定义呈现器代码如下所示-如果源是`FileImageSource`然后再提取文件名生成已本地化的图像使用的路径`CurrentUICulture`。 某些语言需要特殊处理，以便按预期方式; 工作回退在示例中默认值是使用两个字母的语言代码将只在少数特殊情况下：

```csharp
using System.IO;
using Xamarin.Forms;
using Xamarin.Forms.Platform.WinPhone;

[assembly: ExportRenderer(typeof(Image), typeof(UsingResxLocalization.WinPhone.LocalizedImageRenderer))]
namespace UsingResxLocalization.WinPhone
{
    public class LocalizedImageRenderer : ImageRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Image> e)
        {
            base.OnElementChanged(e);

            if (e.NewElement != null)
            {
                var s = e.NewElement.Source as FileImageSource;
                if (s != null)
                {
                    var fileName = s.File;
                    string ci = System.Threading.Thread.CurrentThread.CurrentUICulture.ToString();
                    // you might need some custom logic here to support particular cultures and fallbacks
                    if (ci == "pt-BR") {
                        // use the complete string 'as is'
                    } else if (ci == "zh-CN") {
                         // we could have named the image directories differently,
                         // but this keeps them consisent with RESX file naming
                        ci = "zh-Hans";
                    } else if (ci == "zh-TW" || ci == "zh-HK") {
                        ci = "zh-Hant";
                    } else {
                        // for all others, just use the two-character language code
                        ci = System.Threading.Thread.CurrentThread.CurrentUICulture.TwoLetterISOLanguageName;
                    }
                    e.NewElement.Source = Path.Combine("Assets/" + ci + "/" + fileName);
                }
            }
        }
    }
}
```

此代码适用于下面所示的目录结构中的本地化映像。 你建议修改代码以满足你特定的本地化要求 （例如处理更具体的区域设置中，和故障时映像不可用）：

![](localization-images/winphone-resources.png "WinPhone 本地化映像目录结构")

Windows Phone 现在本地化映像。 此处是 （在西班牙语和简体中文） 结果的屏幕快照：

![](localization-images/winphone-image-sml.png "WinPhone 示例应用程序文本和图像本地化")

#### <a name="app-name"></a>应用程序名称

请参阅 Microsoft 的文档以[本地化的 Windows Phone 8.0 应用标题](http://msdn.microsoft.com/library/windows/apps/ff967550(v=vs.105).aspx)。

### <a name="windows-phone-81-and-universal-windows-platform-application-projects"></a>Windows Phone 8.1 和通用 Windows 平台应用程序项目

Windows Phone 8.1 和通用 Windows 平台都具有简化的映像和应用程序名称的本地化资源基础结构。

#### <a name="images"></a>图像

可以将它们放置在特定资源的文件夹中，按本地化映像，如下面的屏幕截图中所示：

![](localization-images/uwp-image-folder-structure.png "WinPhone 8.1 和 UWP 映像本地化文件夹结构")

在运行时的 Windows 资源基础结构将选择适当的映像，根据用户的区域设置。

#### <a name="app-name"></a>应用程序名称

请参阅 Microsoft 的文档以[Windows 8.1 应用商店应用： 本地化描述你的应用到用户的信息](https://msdn.microsoft.com/library/windows/apps/hh454044.aspx)和[从应用程序清单中加载字符串](https://msdn.microsoft.com/library/windows/apps/xaml/hh965323.aspx#loading_strings_from_the_app_manifest.)。

## <a name="summary"></a>总结

可以使用 RESX 文件和.NET 全球化类本地化 Xamarin.Forms 应用程序。 除了特定于平台的代码来检测用户希望哪种语言的少量，大部分本地化工作集中在通用代码。

若要利用 iOS 和 Android 中提供的多分辨率支持的特定于平台的方式通常处理图像。 Windows Phone 需要一些自定义代码，若要将映像本地化的跨平台友好方式;提供的示例代码是为了添加此功能。


## <a name="related-links"></a>相关链接

- [RESX 本地化示例](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [TodoLocalized 示例应用程序](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [跨平台本地化](~/cross-platform/app-fundamentals/localization.md)
- [iOS 本地化](~/ios/app-fundamentals/localization/index.md)
- [Android 本地化](~/android/app-fundamentals/localization.md)
- [使用 CultureInfo 类 (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [查找和使用特定区域性 (MSDN) 为资源](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
