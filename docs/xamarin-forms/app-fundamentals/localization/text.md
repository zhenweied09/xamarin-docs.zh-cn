---
title: 字符串和映像本地化
description: 可以使用.NET 资源文件本地化 Xamarin.Forms 应用。
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 09fe3587e4e435383822e50bd12616747b807f82
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108452"
---
# <a name="localization"></a>本地化

_可以使用.NET 资源文件本地化 Xamarin.Forms 应用。_

## <a name="overview"></a>概述

用于本地化.NET 应用程序使用的内置机制[RESX 文件](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx)中的类和`System.Resources`和`System.Globalization`命名空间。 包含已翻译的字符串的 RESX 文件嵌入在 Xamarin.Forms 程序集，以及编译器生成的类，提供强类型化访问翻译。 然后可以在代码中检索已翻译的文本。

### <a name="sample-code"></a>代码示例

有与此文档关联的两个示例：

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization)介绍的概念非常简单演示。 如下所示的代码段都来自此示例。
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized)是使用这些本地化技术的基本工作应用。

#### <a name="shared-projects-are-not-recommended"></a>不建议使用共享的项目

TodoLocalized 示例包括[共享项目演示](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/)但是，由于生成系统的限制的资源文件不会收到 **。 designer.cs**生成的文件，这将导致无法访问在代码中强类型已翻译的字符串。

此文档的其余部分与使用 Xamarin.Forms.NET 标准库模板的项目。

## <a name="globalizing-xamarinforms-code"></a>全球化 Xamarin.Forms 代码

**全球化**应用程序是使其"全球通用。"的过程 这意味着编写能够显示不同的语言的代码。

全球化应用程序的关键部分之一构建用户界面，以便有没有*硬编码*文本。 相反，向用户显示的任何内容应检索从一组已被翻译为所选语言的字符串。

本文档中，我们将介绍如何使用 RESX 文件来存储这些字符串和检索它们的显示，具体取决于用户的首选项。

这些示例针对英语、 法语、 西班牙语、 德语、 中文、 日语、 俄语和葡萄牙语 （巴西） 语言。 应用程序可以转换为较多或少所需的语言。

> [!NOTE]
> 在通用 Windows 平台上 RESW 文件应该用于推送通知本地化，而不是 RESX 文件。 有关详细信息，请参阅[UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)。

### <a name="adding-resources"></a>添加资源

全球化 Xamarin.Forms.NET Standard 库应用程序的第一步添加用于将使用的所有文本都存储在应用程序的 RESX 资源文件。 我们需要添加包含默认的文本的 RESX 文件，然后添加我们想要支持每种语言的其他 RESX 文件。

#### <a name="base-language-resource"></a>基本语言资源

基本资源 (RESX) 文件将包含默认语言字符串 （这些示例假定的默认语言是英语）。 通过右键单击项目，然后选择将文件添加到 Xamarin.Forms 公共代码项目**添加 > 新建文件...**.

选择有意义的名称，例如**AppResources**然后按**确定**。

[![添加资源文件](text-images/resx-new-file-sml.png "新建文件对话框")](text-images/resx-new-file.png#lightbox "新建文件对话框")

两个文件将添加到项目：

* **AppResources.resx**可翻译字符串的 XML 格式的存储位置的文件。
* **AppResources.designer.cs**声明的分部类，以包含对 RESX XML 文件中创建的所有元素的引用的文件。

在解决方案树将显示为相关的文件。 RESX 文件*应*进行编辑，以便添加新的可翻译字符串; **。 designer.cs**文件应*不*进行编辑。

![](text-images/appresources-tree.png "AppResources.resx 文件")

##### <a name="string-visibility"></a>字符串可见性

默认情况下时生成强类型引用为字符串，它们将为`internal`对程序集。 这是因为 RESX 文件的默认生成工具生成 **。 designer.cs**文件具有`internal`属性。

选择**AppResources.resx**文件，并显示**属性**板以查看此生成工具的配置。 下面显示的屏幕截图**自定义工具： ResXFileCodeGenerator**。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "AppResources.Resx 属性窗口")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "属性面板来显示 AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)

-----

若要使强类型化的字符串属性`public`，你必须手动将配置更改为**自定义工具： PublicResXFileCodeGenerator**，如以下屏幕截图中所示：


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "AppResources.Resx 属性窗口")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "属性面板来显示 AppResources.Resx")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "属性面板来显示 AppResources.Resx")](text-images/xs-resx-public.png#lightbox)

-----

此更改是可选的并仅需要你想要跨不同的程序集引用本地化的字符串 （例如，如果将 RESX 文件中的不同程序集放置到你的代码）。 此主题的示例使字符串`internal`因为它们同一程序集中 Xamarin.Forms.NET 标准库使用位置定义。

只需在基的 RESX 文件上设置自定义工具，如上所示;不需要设置*任何*以下各节所述的特定于语言的 RESX 文件的生成工具。

##### <a name="editing-the-resx-file"></a>编辑 RESX 文件

遗憾的是没有任何内置的 RESX 编辑器在 Visual Studio for mac。 添加新的可翻译字符串需要新的 XML 添加`data`为每个字符串的元素。 每个`data`元素可以包含以下：

* `name` （必需） 的属性是此翻译字符串的键。 它必须是有效C#属性名称-以便允许使用任何空格或特殊字符。
* `value` 元素 （必需），它是应用程序中显示的实际字符串。
* `comment` 元素 （可选） 可以包含说明如何使用此字符串翻译的说明。
* `xml:space` （可选） 若要控制如何保留在字符串中的间距的属性。

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

编写的应用程序，如每个段向用户显示的文本应将添加到基本 RESX 资源文件中的新`data`元素。 建议您包括`comment`s 尽可能多地以确保高质量翻译。

> [!NOTE]
> Visual Studio （包括免费的 Community 版本） 包含基本的 RESX 编辑器。 如果你有权访问的 Windows 计算机，可以在方便地添加和编辑 RESX 文件中的字符串。

#### <a name="language-specific-resources"></a>特定于语言的资源

通常情况下，默认文本字符串的实际译文就不会发生之前已写入大型应用程序块 （在这种情况下默认 RESX 文件将包含大量字符串）。 它仍是在开发周期的早期添加特定于语言的资源 （可选） 使用的机器翻译文本，以帮助测试本地化代码填充一个好办法。

我们想要支持每种语言添加另一个 RESX 文件。
特定于语言的资源文件必须遵循特定的命名约定： 基本资源文件 （例如使用相同的文件名。 **AppResources**) 后跟一个句点 （.） 和语言代码。 简单的示例包括：

* **AppResources.fr.resx**的法语语言翻译。
* **AppResources.es.resx** -西班牙语翻译。
* **AppResources.de.resx**的德语语言翻译。
* **AppResources.ja.resx** -日语语言翻译。
* **AppResources.zh Hans.resx** -中文 （简体） 语言翻译。
* **AppResources.zh Hant.resx** -中文 （繁体） 语言翻译。
* **AppResources.pt.resx** -葡萄牙语语言翻译。
* **AppResources.pt BR.resx** -巴西葡萄牙语语言翻译。

常规模式是使用两个字母的语言代码，但有 （如中文版） 的一些示例，其中使用不同的格式，以及 （如葡萄牙语 （巴西）） 的其他示例中有四个字符的区域设置标识符是必需。

这些特定于语言的资源文件*不这样做*需要 **。 designer.cs**分部类，以便它们可作为常规 XML 文件，添加，使用**生成操作： EmbeddedResource**设置。 此屏幕截图显示了一个包含特定于语言的资源文件的解决方案：

![](text-images/appresources-langs.png "特定于语言的资源文件")

在应用程序开发和基本的 RESX 文件已添加文本时，您应它发送到每个转换的转换器`data`元素并返回一个特定于语言的资源文件 （使用显示的命名约定） 包含在应用程序中。 机器翻译的一些示例如下所示：

**AppResources.es.resx （西班牙语）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx （日语）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt BR.resx （巴西葡萄牙语）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

仅`value`元素需要更新通过转换器-`comment`不能进行转换。 请记住： 编辑 XML 文件进行转义保留的字符喜欢`<`， `>`，`&`与`&lt;`， `&gt;`，并且`&amp;`如果它们出现在`value`或`comment`。

<a name="incode" />

### <a name="using-resources-in-code"></a>在代码中使用的资源

RESX 资源文件中的字符串可用于在用户界面代码中使用`AppResources`类。 `name`分配给每个字符串在 RESX 文件将成为该类，如下所示，可以在 Xamarin.Forms 代码中引用的属性：

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

在 iOS、 Android 和通用 Windows 平台 (UWP) 将呈现为你的用户界面所期望的只不过现在就可以将翻译为多种语言的应用，因为正在从资源加载文本，而不是硬编码。 下面是在转换之前每个平台上显示 UI 的屏幕截图：

![](text-images/simple-example-english.png "在转换之前的跨平台 Ui")

### <a name="troubleshooting"></a>疑难解答

#### <a name="testing-a-specific-language"></a>测试特定语言

它可能比较棘手，若要在模拟器或设备切换到不同的语言，尤其是在开发时想要快速测试不同的区域性。

您可以强制加载的设置的特定语言`Culture`中此代码片段所示：

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

这种方法 – 上直接设置区域性`AppResources`类 – 也可用于实现在您的应用程序 （而不是使用设备的区域设置） 语言选择器。

#### <a name="loading-embedded-resources"></a>加载嵌入资源

下面的代码段时，尝试调试嵌入的资源 （如 RESX 文件） 的问题。 将此代码添加到你的应用 （应用程序生命周期中及早），将会列出嵌入程序集中，显示完整的资源标识符的所有资源：

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

在中**AppResources.Designer.cs**文件 (围绕*第 33 行*)，完整*资源管理器名称*（例如指定。 `"UsingResxLocalization.Resx.AppResources"`) 类似于下面的代码：

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

检查**应用程序输出**的结果的上面显示的调试代码，以确认正确列出了资源 （即`"UsingResxLocalization.Resx.AppResources"`).

如果没有，则`AppResources`类将不能加载其资源。
检查以下项目，其中资源找不到解决的问题：

* 项目的默认命名空间匹配中的根命名空间**AppResources.Designer.cs**文件。
* 如果**AppResources.resx**文件位于一个子目录，则子目录名称应为命名空间的一部分*和*资源标识符的一部分。
* **AppResources.resx**文件具有**生成操作： EmbeddedResource**。
* **项目选项 > 源代码 >.NET 命名策略 > 使用 Visual Studio 样式资源名称**勾选了。 如果您愿意可以 untick 这，但是引用 RESX 资源时使用的命名空间将需要更新整个应用。

#### <a name="doesnt-work-in-debug-mode-android-only"></a>不能在调试模式 (仅限 Android)

如果使用已翻译的字符串在你的 Android 版本生成，但不是进行调试时，右键单击**Android 项目**，然后选择**选项 > 生成 > Android 生成**并确保**快速程序集部署**不勾选了。 此选项会导致加载资源出现问题，如果要测试本地化应用程序不应使用。

### <a name="displaying-the-correct-language"></a>显示正确的语言

到目前为止，我们探讨了如何编写代码，以便可以提供翻译，但不是如何实际使其显示。 Xamarin.Forms 代码可以充分利用。NET 的资源加载正确的语言翻译，但我们需要查询来确定用户所选的语言的每个平台上的操作系统。

由于需要一些特定于平台的代码来获取用户的语言首选项，使用[依赖关系服务](~/xamarin-forms/app-fundamentals/dependency-service/index.md)公开 Xamarin.Forms 应用中的该信息并为每个平台实现。

首先，定义一个接口来公开用户的首选的区域性，类似于下面的代码：

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

第二种，使用[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) Xamarin.Forms 中`App`类来调用该接口并将我们 RESX 资源区域性设置为正确的值。 请注意，我们无需手动将此值设置为通用 Windows 平台，因为资源框架会自动识别这些平台上所选的语言。

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

资源`Culture`需要应用程序首次加载，以便使用正确的语言字符串时设置。 （可选） 可能会更新此值根据可能会引发 iOS 或 Android，如果用户更新其语言首选项应用运行时的特定于平台的事件。

为实现`ILocalize`接口所示[特定于平台的代码](#Platform-specific_Code)下面一节。 这些实现充分利用此`PlatformCulture`帮助器类：

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

检测要显示哪种语言的代码必须是特定于平台的因为 iOS、 Android 和 UWP 所有略有不同的方式公开此信息。 有关代码`ILocalize`依赖关系服务是提供下面为每个平台，以及其他特定于平台的要求，以确保本地化的文本能够正确显示。

特定于平台的代码还必须处理情况下，操作系统允许用户配置不支持的区域设置标识符。NET 的`CultureInfo`类。 在这些情况下必须编写自定义代码来检测到不受支持的区域设置进行替代最佳。NET 兼容区域设置。

#### <a name="ios-application-project"></a>iOS 应用程序项目

iOS 用户从日期和时间格式设置区域性中分别选择其首选的语言。 若要加载了正确的资源进行本地化的 Xamarin.Forms 应用，我们只需查询`NSLocale.PreferredLanguages`数组中的第一个元素。

以下实现`ILocalize`依赖关系服务应位于 iOS 应用程序项目中。 因为 iOS 而不是短划线 （这是.NET 标准表示形式） 使用下划线代码会将下划线替换实例化之前`CultureInfo`类：

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
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
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
> `try/catch`中的块，`GetCurrentCultureInfo`方法模拟完全匹配找不到，查找接近的匹配项，只需根据语言 （区域设置中的字符的第一个块） 通常用于区域设置说明符 – 回退行为。
>
> Xamarin.Forms 中，对于某些区域设置中在 iOS 中有效，但不是对应于有效`CultureInfo`在.NET 中，并会尝试处理这上面的代码。
>
> 例如，iOS**设置 > 常规语言&amp;区域**屏幕，可设置你的手机**语言**到**英语**但**区域**到**西班牙**，这会导致的区域设置字符串`"en-ES"`。 当`CultureInfo`创建失败，代码将回退使用只是前两个字母来选择显示语言。
>
> 开发人员应修改`iOSToDotnetLanguage`和`ToDotnetFallbackLanguage`方法来处理其支持的语言所需的特定用例。


有一些系统定义的用户界面元素的自动转换为 iOS，如**完成**按钮`Picker`控件。 若要强制 iOS 这些元素需要我们指明我们在支持哪种语言翻译**Info.plist**文件。 可以添加这些值通过**Info.plist > 源**如下所示：

![本地化 Info.plist 中的键](text-images/info-plist.png "本地化 Info.plist 中的密钥")

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

实现依赖关系服务和更新后**Info.plist**，iOS 应用程序将能够显示本地化的文本。

> [!NOTE]
> 请注意，Apple 将葡萄牙语稍有不同于您所料。
> 从[其 docs](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2): _"pt 作为语言 ID 为使用葡萄牙语因为它用于在巴西和 PT-PT-语言 ID 为葡萄牙语因为它会用葡萄牙"_。
> 这意味着当葡萄牙语语言选择在非标准的区域设置的回退语言将葡萄牙语 （巴西） 在 iOS 上，除非编写代码来更改此行为 (如`ToDotnetFallbackLanguage`上面)。

有关 iOS 本地化的详细信息，请参阅[iOS 本地化](~/ios/app-fundamentals/localization/index.md)。

#### <a name="android-application-project"></a>Android 应用程序项目

Android 公开通过当前所选的区域设置`Java.Util.Locale.Default`，并且还使用下划线分隔符而不是短划线 （它将替换下面的代码）。 将此依赖关系服务实现添加到 Android 应用程序项目：

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
> `try/catch`中的块，`GetCurrentCultureInfo`方法模拟完全匹配找不到，查找接近的匹配项，只需根据语言 （区域设置中的字符的第一个块） 通常用于区域设置说明符 – 回退行为。
>
> Xamarin.Forms 中，对于某些区域设置在 Android 中有效，但不是对应于有效`CultureInfo`在.NET 中，并会尝试处理这上面的代码。
>
> 开发人员应修改`iOSToDotnetLanguage`和`ToDotnetFallbackLanguage`方法来处理其支持的语言所需的特定用例。

此代码添加到 Android 应用程序项目后，它将能够自动显示已翻译的字符串。

> [!NOTE]
>️**警告：** 如果使用已翻译的字符串在你的 Android 版本生成，但不是进行调试时，右键单击**Android 项目**，然后选择**选项 > 生成 > Android构建**，并确保**快速程序集部署**不勾选了。 此选项会导致加载资源出现问题，如果要测试本地化应用程序不应使用。

有关 Android 本地化的详细信息，请参阅[Android 本地化](~/android/app-fundamentals/localization.md)。

#### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台 (UWP) 项目不需要依赖关系服务。 相反，此平台自动资源的区域性设置正确。

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

展开.NET Standard 库项目中的属性节点并双击**AssemblyInfo.cs**文件。 将以下行添加到要将非特定语言资源程序集语言设置为英语的文件：

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

这会通知资源管理器的应用程序的默认区域性，因此确保语言中性 RESX 文件中定义的字符串 (**AppResources.resx**) 应用程序运行在一个英语区域设置时，会显示。

### <a name="example"></a>示例

更新的特定于平台的项目如下所示更高版本和已翻译的 RESX 文件重新编译应用程序之后, 将可以在每个应用更新后的翻译。 下面是示例代码转换为简体中文屏幕截图：

![](text-images/simple-example-hans.png "跨平台 Ui 翻译成中文 （简体）")

有关 UWP 本地化的详细信息，请参阅[UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)。

## <a name="localizing-xaml"></a>本地化 XAML

当生成标记的 XAML 中的 Xamarin.Forms 用户界面将类似于以下形式，与字符串嵌入直接在 XML 中：

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

理想情况下，我们无法将直接在 XAML 中，我们可以通过创建来执行此操作中的用户界面控件转换*标记扩展*。 公开 RESX 资源到 XAML 标记扩展的代码如下所示。 此类应添加到 Xamarin.Forms 常见代码 （以及 XAML 页面中）：

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

下面的列表内容说明了上面的代码中的重要元素：

* 此类命名`TranslateExtension`，但按照惯例，我们可以引用是作为**Translate**我们标记中。
* 此类应实现`IMarkupExtension`，所需为其 Xamarin.Forms 工作。
* `"UsingResxLocalization.Resx.AppResources"` 是我们 RESX 的资源的资源标识符。 它是我们默认命名空间、 资源文件所在的文件夹和默认的 RESX 文件名组成。
* `ResourceManager`使用创建类`IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)`若要确定当前的程序集将加载资源，并缓存在静态`ResMgr`字段。 它将作为创建`Lazy`类型，以便其创建推迟到中的第一次使用`ProvideValue`方法。
* `ci` 使用依赖关系服务从本机操作系统中获取用户的所选的语言。
* `GetString` 是从资源文件中检索实际的已翻译的字符串的方法。 在通用 Windows 平台上，`ci`将为 null 因为`ILocalize`这些平台上不实现接口。 这相当于调用`GetString`仅带第一个参数的方法。 相反，资源框架会自动识别区域设置，将从相应的 RESX 文件中检索的已翻译的字符串。
* 错误处理已包括在内，以帮助调试缺少的资源通过引发异常 (在`DEBUG`仅模式下)。

以下 XAML 代码段演示如何使用标记扩展。 有两个步骤以使其正常运行：

1. 声明自定义`xmlns:i18n`命名空间的根节点中。 `namespace`和`assembly`完全-在此示例中它们完全相同，但可能不同项目中的项目设置必须匹配。
2. 使用`{Binding}`通常会包含要调用的文本的属性的语法`Translate`标记扩展。 资源键是唯一的必需的参数。

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

## <a name="localizing-platform-specific-elements"></a>在本地化特定于平台的元素

虽然我们可以处理的 Xamarin.Forms 代码中的用户界面转换，但是有一些元素必须在每个特定于平台的项目中完成的。 本部分将介绍如何本地化：

* Application Name
* 图像

示例项目包括名为本地化的图像**flag.png**中, 引用了C#，如下所示：

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

标志图像也引用中 XAML 如下：

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

只要实现如下所述的项目结构，所有平台将自动都解析到本地化版本的映像，这些图像引用。

### <a name="ios-application-project"></a>iOS 应用程序项目

iOS 使用某一命名标准称为本地化项目或 **.lproj**目录，用于包含图像和字符串资源。 这些目录可以包含在应用中，使用的图像的本地化的版本以及**InfoPlist.strings**可用于本地化的应用程序名称的文件。 有关 iOS 本地化的详细信息，请参阅[iOS 本地化](~/ios/app-fundamentals/localization/index.md)。

#### <a name="images"></a>图像

此屏幕截图显示了具有特定于语言的 iOS 示例应用程序 **.lproj**目录。 西班牙语目录称为**es.lproj**，包含默认图像的本地化的版本，以及**flag.png**:

![](text-images/ios-resources.png "iOS 本地化项目目录")

每个语言目录包含一份**flag.png**，该语言的本地化。 如果提供没有图像，则操作系统将默认为默认语言的目录中的图像。 有关完整的 Retina 支持，应提供**@2x**并**@3x**每个映像的副本。

#### <a name="app-name"></a>应用程序名称

内容**InfoPlist.strings**只是一个单个键-值来配置应用程序名称：

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

运行应用程序时，应用程序名称和映像已同时本地化：

![](text-images/ios-imageicon.png "iOS 示例应用程序文本和映像本地化")

### <a name="android-application-project"></a>Android 应用程序项目

Android 遵循不同的方案用于存储使用不同的本地化的图像**drawable**并**字符串**带有语言代码后缀的目录。 当需要 （例如 ZH-TW 或 PT） 的四个字母区域设置代码时，请注意，Android 需要额外**r**前面 dash/以下区域设置代码 （例如。 zh rTW 或 pt rBR）。 有关 Android 本地化的详细信息，请参阅[Android 本地化](~/android/app-fundamentals/localization.md)。

#### <a name="images"></a>图像

此屏幕截图显示了 Android 示例某些本地化的绘图和字符串：

![](text-images/android-resources.png "Android 本地化绘图和字符串目录")

请注意，Android 不使用-Zh-hans 和简体和繁体中文; Zh-hant 代码相反，它仅支持特定国家/地区代码 ZH-CN 和 ZH-TW。

若要支持高密度屏幕的不同分辨率的图像，创建与其他语言文件夹`-*dpi`后缀，如**绘图 es mdpi**，**绘图 es xdpi**， **绘图 es xxdpi**，等等。请参阅[提供的替代 Android 资源](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)有关详细信息。

#### <a name="app-name"></a>应用程序名称

内容**strings.xml**只是一个单个键-值来配置应用程序名称：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

更新**MainActivity.cs**的 Android 应用程序项目中，以便`Label`引用 XML 的字符串。

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

现在，应用程序本地化的应用程序名称和图像。 下面是结果 （在西班牙语） 的屏幕截图：

![](text-images/android-imageicon.png "Android 示例应用程序文本和映像本地化")

### <a name="universal-windows-platform-application-projects"></a>通用 Windows 平台应用程序项目

通用 Windows 平台的强大简化了映像和应用程序名称的本地化的资源基础结构。 有关 UWP 本地化的详细信息，请参阅[UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)。

#### <a name="images"></a>图像

可以通过将它们放在特定于资源的文件夹中，本地化图像，如以下屏幕截图中所示：

![](text-images/uwp-image-folder-structure.png "UWP 映像本地化文件夹结构")

在运行时的 Windows 资源基础结构将选择合适的映像，根据用户的区域设置。

## <a name="summary"></a>总结

可以使用 RESX 文件和.NET 全球化类本地化 Xamarin.Forms 应用程序。 除少量特定于平台的代码来检测用户首选哪种语言，本地化工作的大部分常见的代码中集中管理。

若要充分利用 iOS 和 Android 中提供的多分辨率支持特定于平台的方式通常处理映像。

## <a name="related-links"></a>相关链接

- [RESX 本地化示例](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [TodoLocalized 示例应用](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [跨平台本地化](~/cross-platform/app-fundamentals/localization.md)
- [iOS 本地化](~/ios/app-fundamentals/localization/index.md)
- [Android 本地化](~/android/app-fundamentals/localization.md)
- [UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)
- [使用 CultureInfo 类 (MSDN)](http://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [查找并使用为特定区域性 (MSDN) 资源](http://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
