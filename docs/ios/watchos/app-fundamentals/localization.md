---
title: 使用 watchOS Xamarin 的本地化
description: 本文档介绍如何本地化 watchOS 使用 Xamarin 生成的应用。 它讨论监视应用程序，监视扩展，在代码中，字符串情节提要文本、 测试和的详细信息。
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 4f158f1c8699ad5090eb7fade8af8918c8881d95
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790774"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>使用 watchOS Xamarin 的本地化

_调整 watchOS 面向多个语言版本的应用_

![](localization-images/both-languages-sml.png "Apple Watch 显示本地化的内容")

已经进行了本地化 watchOS 应用，使用标准 iOS 方法：

- 使用**本地化 ID**情节提要元素
- **.strings**情节提要，与关联的文件和
- **Localizable.strings**在代码中使用的文本的文件。

默认情节提要和资源位于**基**目录，和特定于语言的翻译和其他资源存储在 **.lproj**目录。
iOS 和监视操作系统自动将使用用户的语言选择加载正确的字符串和资源。

因为 Apple Watch 应用程序必须具有两个部分-Watch 应用和监视扩展的资源所需在两个位置，具体取决于如何使用它们的本地化字符串。

本地化的文本和资源将*不同*watch 应用和监视扩展中。

## <a name="watch-app"></a>Watch 应用

监视应用程序包含描述应用程序的用户界面的情节提要。 任何控件 (如`Label`和`Image`) 支持本地化具有**本地化 ID**。

每个特定于语言的 **.lproj**目录应包含 **.strings**翻译具有每个元素的文件 (使用**本地化 ID**)，以及映像引用的情节提要。

## <a name="watch-extension"></a>监视扩展

监视扩展是应用程序代码在其中运行。 从代码将向用户显示任何文本需要扩展中，不能在监视应用程序进行本地化。

扩展还应该包含特定于语言的 **.lproj**目录，但 **.strings**文件仅需要在代码中使用的文本的翻译。

## <a name="globalizing-the-watch-solution"></a>全球化监视解决方案

全球化是使应用程序可本地化的过程。
对于这意味着设计具有记住的不同文本长度情节提要的监视应用程序，确保每个屏幕布局调整适当地根据什么文本显示。 你还需要确保可以使用转换监视扩展代码中引用的任何字符串`LocalizedString`方法。

### <a name="watch-app"></a>Watch 应用

默认情况下监视应用程序未配置用于本地化。 你需要移动默认情节提要文件并创建你的翻译某些其他目录：

1. 创建**Base.lproj**目录和移动**Interface.storyboard**到其中。

2. 创建 **<language>.lproj**为你希望支持每种语言的目录。

3. **.Lproj**目录应包含**Interface.strings**文本文件 （文件名应匹配 storboard 的名称）。 你可以根据需要将需要本地化这些目录中的任何映像。

（仅英语和西班牙语语言已添加文件） 进行这些更改之后，监视应用程序项目如下所示：

  ![](localization-images/watchapp-solution.png "监视应用程序项目与英语和西班牙语语言文件")

#### <a name="storyboard-text"></a>情节提要文本

当您编辑情节提要时，选择每个元素和通知**本地化 ID**出现在**属性**填充：

  [![](localization-images/storyboard-sml.png "在属性板中显示的本地化 ID")](localization-images/storyboard.png#lightbox)

在**Base.lproj**文件夹中，创建键-值对，如下所示，其中键通过形成**本地化 ID**并将其属性名称在控件上，联接由句点 (`.`)。

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

请注意，在此示例中，**本地化 ID**可以是简单的数字字符串 （例如。 "0"、"1"，等等) 或更复杂字符串 （如"AgC-eL-Hgc")。 `Label` 控件具有`Text`属性和`Button`具有`Title`属性，用于反映它们本地化的值设置的方式请务必使用小写的属性名称，如上面的示例中所示。

当监视在呈现时情节提要时，正确的值将自动中提取并根据用户所选的语言显示。

#### <a name="storyboard-images"></a>情节提要图像

示例解决方案还包括**gradient@2x.png**每个语言文件夹中的映像。 此映像可以是不同的每种语言 （如。 它可能嵌入文本，需要转换时，或使用本地化插图）。

只需设置图像的**映像**将根据用户所选的语言 watch 上呈现情节提要和正确的图像中的属性。

![](localization-images/storyboard-image.png "在情节提要中设置映像映像属性")

注意： 由于所有 Apple 观察都具有 Retina 显示，仅**@2x**的映像的版本是必需的。 不需要指定**@2x**在情节提要。

### <a name="watch-extension"></a>监视扩展

监视扩展需要类似的目录结构来支持本地化，但是没有任何情节提要。 在扩展中的本地化的字符串是仅那些 C# 代码所引用的。

![](localization-images/watchextension-solution.png "监视扩展目录结构，以支持本地化")

#### <a name="strings-in-code"></a>在代码中的字符串

**Localizable.strings**文件具有与它是否与情节提要关联略有不同的结构。 在这种情况下，我们可以选择任何"密钥"字符串;Apple 的建议是使用反映将以默认语言显示的实际文本的密钥：

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

`NSBundle.MainBundle.LocalizedString`方法用于将字符串解析为其已翻译的对应项，如下面的代码中所示。

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>在代码中的映像

可以通过两种方式设置由代码填充的映像。

1. 你可以更改`Image`中监视应用程序中，例如控件的值设置为图像的字符串名称的已存在。

  ```csharp
  displayImage.SetImage("gradient"); // image in Watch App (as shown above)
  ```

2. 你可以将映像从扩展中移动到监视使用`FromBundle`，则应用将自动选择的用户的语言选择了正确的图像。 示例解决方案中没有映像**language@2x.png**用每个语言文件夹，并显示在`DetailController`使用下面的代码：

  ```csharp
  using (var image = UIImage.FromBundle ("language")) {
    displayImage.SetImage (image);
  }
  ```

  请注意，不需要指定**@2x**时引用图像的文件名。

第二种方法也是适用如果从远程服务器以在监视; 上的呈现下载映像但是在这种情况下你应该确保你下载的映像已正确本地化根据用户的首选项。

## <a name="localization"></a>本地化

翻译后已配置你的解决方案，将需要处理你 **.strings**文件和你想要支持每种语言的映像。

你可以创建任意多个 **.lproj**作为你的目录需要 （一个用于每个受支持的语言）。 如使用语言代码名为**en**， **es**， **de**，**日本**， **PT-BR**，（对于英语等西班牙语、 德语、 日语和葡萄牙语 （巴西） 分别)。

附加的示例用 （计算机生成） 翻译来演示如何本地化 watchOS 应用。

### <a name="watch-app"></a>Watch 应用

这些值用于将监视应用程序的情节提要中定义的用户界面翻译。 *密钥*值是每个情节提要控件的组合**本地化 ID**和要进行转换的属性。

建议添加注释，以便转换器知道了转换应包含的文件的原始文本。

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

情节提要 （機器翻譯） 西班牙语字符串如下所示。 最好将注释添加到每个行，因为很难知道什么**本地化 ID**否则引用：

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>监视扩展

在代码中使用这些值来转换之前显示给用户的信息。 *密钥*处于选定状态，开发人员时它们你编写代码，并且通常会包含要转换的实际字符串。

#### <a name="eslprojlocalizablestrings-file"></a>es.lproj/Localizable.strings 文件

（機器翻譯） Spansish 语言字符串：

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>正在测试

若要更改语言首选项的方法不同之间的模拟器和物理设备。

### <a name="simulator"></a>模拟器

在模拟器中，选择要测试使用 iOS 的语言**设置**应用 （模拟器主屏幕中的灰色齿轮图标）。

  ![](localization-images/sim-settings-sml.png "IOS 设置应用程序本地化设置")

### <a name="watch-device"></a>监视设备

当测试监视时，更改中的监视的语言**Apple Watch**配对的 iPhone 上的应用程序。

  ![](localization-images/phone-settings-sml.png "更改配对的 iPhone 上的 Apple Watch 应用中的监视的语言")



## <a name="related-links"></a>相关链接

- [WatchLocalization （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchLocalization/)
